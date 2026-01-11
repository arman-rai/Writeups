```nmap
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 6.7.1
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: AnotherCTF
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

creds:
```
wpuser:
```

did a wpscan and as given, we found a CVE for XSS and SSRF
CVE-2018-20463 and CVE-2018-20462
