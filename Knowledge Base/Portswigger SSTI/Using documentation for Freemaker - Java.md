Fuzzed all payloads on the query and got a probable template: Freemaker on Java

https://freemarker.apache.org/docs/dgui_template_overallstructure.html
https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html#freemarker-java
https://www.synack.com/exploits-explained/exploits-explained-discovering-a-server-side-template-injection-vuln-in-freemarker/

Did some digging and found that there was an option to change the template and tried SSTI on it and the final payload was:
```
 ${"freemarker.template.utility.Execute"?new()("rm morale.txt")}
```