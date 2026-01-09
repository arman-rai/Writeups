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

This was similar as we had to escape two tags <option> and <select>