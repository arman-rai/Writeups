## HTTP Pentest Cheatsheet

### Recon & Enumeration

- **Ports & Services**: `nmap -p 80,443,8080,8000 --script http* <IP>`
- **Identify Software**: banner grabbing, Wappalyzer, whatweb
- **Public Exploits**: check Exploit-DB, searchsploit, CVEs
- **HackTricks**: reference for software-specific tricks

### Content Discovery

- **Directories/Files**:
    - `feroxbuster -u http://target -r -x php,asp,aspx,jsp,txt,conf`
    - `ffuf -u http://target/FUZZ -w /usr/share/seclists/.../directory-list-2.3-big.txt`
- **Crawling**: use `crawl.py`, Burp crawler
- **Wordlists**: SecLists (`directory-list-2.3-big.txt`, `raft-*`)

### Subdomains & Virtual Hosts

- `ffuf -u http://target -H "Host: FUZZ.target.com" -w subdomains.txt`
- Check DNS records + vhost fuzzing

### Authentication Testing

- **Default creds**: admin/admin, root/root, etc.
- **Username enumeration**: login, forgot password, blog authors, error messages
- **Dictionary/brute-force**: `hydra`, `ffuf` with known usernames
- **Register new user**: check privilege escalatio
- **Session cookies**: inspect JWTs, tamper, replay

### Web Vulnerabilities

- **SQL Injection**:
    - Test parameters with payloads (payloadbox repo)
    - Extract DB info, run `sqlmap` if manual injection confirmed
    - Goal: file read/write, RCE, webshell upload
- **File Upload**:
    - Upload polyglot/webshell, bypass filters
    - Fuzz directories to locate uploaded file
- **Command Injection / SSTI**: test all inputs for code eval
    
- **SSRF**:
    - Test URL fields, force request to your host
    - Pivot to `127.0.0.1`, internal IPs, other ports (API, metadata)
- **LFI/RFI**:
    - Path traversal → `/etc/passwd`, configs, logs
    - PHP wrappers: `php://filter`, `expect://`
    - Log poisoning → reverse shell
- **Config File Hunting**:
    - `.git` + `git-dumper` → grep for creds
    - Known paths: `config.php`, `.env`, `web.config`, etc.
    - If SMB/FTP/file-read available, search for DB/SSH credentials

### Post-Exploit Moves

- **WebDAV**: connect with `cadaver`, upload webshell
- **Pivoting**: once webshell gained, enumerate local files and services
- **Escalation**: re-use DB/FTP/SSH creds found in configs

---

