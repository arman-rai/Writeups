```
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
7680/tcp open  pando-pub
8080/tcp open  http-proxy

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: GAIA
|   NetBIOS_Domain_Name: GAIA
|   NetBIOS_Computer_Name: GAIA
|   DNS_Domain_Name: GAIA
|   DNS_Computer_Name: GAIA
|   Product_Version: 10.0.17763
|_  System_Time: 2025-09-19T02:24:08+00:00
| ssl-cert: Subject: commonName=GAIA
| Not valid before: 2025-09-18T02:18:46
|_Not valid after:  2026-03-20T02:18:46
|_ssl-date: 2025-09-19T02:24:30+00:00; 0s from scanner time.
7680/tcp open  pando-pub?
8080/tcp open  http-proxy
| fingerprint-strings:
|   FourOhFourRequest:
|     HTTP/1.1 404 Not Found
|     Content-Type: text/html
|     Content-Length: 177
|     Connection: Keep-Alive
|     <HTML><HEAD><TITLE>404 Not Found</TITLE></HEAD><BODY><H1>404 Not Found</H1>The requested URL nice%20ports%2C/Tri%6Eity.txt%2ebak was not found on this server.<P></BODY></HTML>
|   GetRequest:
|     HTTP/1.1 401 Access Denied
|     Content-Type: text/html
|     Content-Length: 144
|     Connection: Keep-Alive
|     WWW-Authenticate: Digest realm="*ThinVNC*", qop="auth", nonce="A6CyKeNr5kAo2EEC42vmQA==", opaque="SvQIfgNvGiJdaw1ATibMuJTBIndMaYBWph"
|_    <HTML><HEAD><TITLE>401 Access Denied</TITLE></HEAD><BODY><H1>401 Access Denied</H1>The requested URL requires authorization.<P></BODY></HTML>
| http-auth:
| HTTP/1.1 401 Access Denied\x0D
|_  Digest nonce=QeD4M+Nr5kBI10EC42vmQA== qop=auth opaque=sbHfcNUccCvMOMpcvN8lPoUXQKFYlXfIns realm=ThinVNC
|_http-title: 401 Access Denied
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.80%I=7%D=9/19%Time=68CCBE60%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,179,"HTTP/1\.1\x20401\x20Access\x20Denied\r\nContent-Type:\x20
SF:text/html\r\nContent-Length:\x20144\r\nConnection:\x20Keep-Alive\r\nWWW
SF:-Authenticate:\x20Digest\x20realm=\"ThinVNC\",\x20qop=\"auth\",\x20nonc
SF:e=\"A6CyKeNr5kAo2EEC42vmQA==\",\x20opaque=\"SvQIfgNvGiJdaw1ATibMuJTBInd
SF:MaYBWph\"\r\n\r\n<HTML><HEAD><TITLE>401\x20Access\x20Denied</TITLE></HE
SF:AD><BODY><H1>401\x20Access\x20Denied</H1>The\x20requested\x20URL\x20\x2
SF:0requires\x20authorization\.<P></BODY></HTML>\r\n")%r(FourOhFourRequest
SF:,111,"HTTP/1\.1\x20404\x20Not\x20Found\r\nContent-Type:\x20text/html\r\
SF:nContent-Length:\x20177\r\nConnection:\x20Keep-Alive\r\n\r\n<HTML><HEAD
SF:><TITLE>404\x20Not\x20Found</TITLE></HEAD><BODY><H1>404\x20Not\x20Found
SF:</H1>The\x20requested\x20URL\x20nice%20ports%2C/Tri%6Eity\.txt%2ebak\x2
SF:0was\x20not\x20found\x20on\x20this\x20server\.<P></BODY></HTML>\r\n");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

### Gaining Foothold via CVE-2019-17662

The sources confirm that the older version of ThinVNC running on port 8080 was highly susceptible to vulnerabilities.

The exploit leveraged was related to **CVE-2019-17662**. This vulnerability consisted of a path traversal flaw combined with ThinVNC's failure to secure its access credentials, which were stored in **plaintext**.

The exploit script related to **CVE-2019-17662** was cloned and executed, successfully allowing the extraction of credentials.

### Initial Login (VNC) and Transition to RDP

Once the credentials were obtained:

1. **VNC Access:** The credentials were used to bypass the **HTTP Basic Auth** on port 8080, granting initial user access to the desktop via the web-based ThinVNC interface.
2. **RDP Transition:** Because the ThinVNC interface was inconvenient to use, and assuming the user credentials were the same, the user utilized the RDP service (port 3389) for a more stable connection. This connection was established using the `xfreerdp` tool, supplying the retrieved username and password.

### Privilege Escalation

After successfully logging in via RDP (corresponding to your "Then logged in used blah blah" phase), the next steps detailed in the sources focused on privilege escalation:

- The PrintSpooler service was targeted due to its history of vulnerabilities, specifically using the **PrintNightmare** exploit.
- A PowerShell version of the exploit was executed (using the RDP shared `/tmp` directory via `\\tsclient\share`).
- **PrintNightmare** successfully created a new administrative user named `adm1n` with the password `P@ssw0rd`.
- This new administrator account was then used to launch a high-integrity command prompt.
- Finally, with administrative privileges, the tool **Mimikatz** was used to dump password hashes from the machine, including the **NTLM password hash** for the built-in Administrator account.


https://github.com/calebstewart/CVE-2021-1675#
https://github.com/peass-ng/PEASS-ng/tree/master/winPEAS
https://github.com/gentilkiwi/mimikatz/tree/master/mimikatz
