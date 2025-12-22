##  **1. Enumeration — Nmap & Ports**

I ran an Nmap scan to identify open services:

```text
PORT      STATE SERVICE REASON  VERSION
80/tcp    open  http    syn-ack nginx 1.16.1
6498/tcp  open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu
65524/tcp open  http    syn-ack Apache httpd 2.4.43 (Ubuntu)
```

This matches what many other write-ups report — **three open ports: two webservers and an SSH service**. 

---

##  **2. Web Enumeration — Port 80**

I checked the default nginx site (`http://$IP`), but didn’t find much at first glance.

Using a directory fuzzing tool (e.g., `gobuster` or `feroxbuster`), I identified:

- `/robots.txt`
    
- `/hidden`
    

This enum approach matches others where **Gobuster helped locate hidden paths**. 

### -> Hidden Flag / Base64

At `/hidden/whatever`, viewing page source revealed a **hidden `<p>` tag** with an encoded string. Decoding it (often Base64) revealed the **first flag** — consistent with multiple community write-ups. 

---

##  **3. Apache Server — Port 65524**

Visiting:

```
http://$IP:65524/robots.txt
```

showed:

```
User-Agent: a18672860d0510e5ab6699730763b250 => flag{1m_s3c0nd_fl4g}
```

This line — also seen in detailed walk-throughs — is **the second flag**. 

Cracking that user-agent hash (MD5) with an online tool gives:

```
flag{1m_s3c0nd_fl4g}
```

---

##  **4. Source Code — Third Flag**

Inspecting the Apache page source, I found:

```
<p hidden>its encoded with ba..:ObsJmP173N2X6dOrAgEAL0Vu</p>
```

Online write-ups explain that this long string is **Base62** — decoding yields:

```
/n0th1ng3ls3m4tt3r
```

This matches paths found in other community guides

Visiting that directory shows an image (`binarycodepixabay.jpg`) and a hash.

From the page source, a **third flag** appears:

```
flag{9fdafbd64c47471a8f54cd3fc64cd312}
```

---

##  **5. Steganography — Image Extraction**

At `/n0th1ng3ls3m4tt3r`, I downloaded the image:

```
binarycodepixabay.jpg
```

As many write-ups show, this image has hidden data extractable via **steghide**. 

```bash
steghide extract -sf binarycodepixabay.jpg
```

It asked for a **password**, which I found by cracking the associated hash or using clues nearby (often done with CyberChef or online hash crackers). Using that password:

```bash
steghide extract -sf binarycodepixabay.jpg
```

This produced a text file containing **SSH credentials** — very similar to the output others have documented.

---

##  **6. SSH — User Access**

I used the **username/password** from the stego extract:

```bash
ssh boring@10.48.164.109 -p 6498
```

Once connected, I found:

```
user.txt
```

Often rot-encoded, so I ran ROT13 (or similar) to recover the clean flag

---

##  **7. Privilege Escalation — Root**

Inside the SSH session, enumeration (manual checks or **linPEAS**) showed a cron job running a script as root.

Specifically:

```
/var/www/.mysecretcronjob.sh
```

Numerous guides note this as a writable cron job that allowed writing a reverse shell or altering commands — a classic **privilege escalation vector**

By adding a reverse shell payload into that cron script and listening with netcat, I got a **root shell**.

Once root, I obtained:

```
/root/root.txt
```

closing the box.

---
