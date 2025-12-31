https://security.appspot.com/vsftpd.html
https://en.wikipedia.org/wiki/Fail2ban


**FTP and TFTP Enumeration – Revision Notes**

### Overview
The **File Transfer Protocol (FTP)** is an application-layer protocol (TCP/IP stack) used for transferring files between client and server. It operates over **TCP** and uses **two channels**:
- **Control channel** (port **21**): for sending commands and receiving status codes.
- **Data channel** (port **20** in active mode): for actual file transfers.

#### Active vs. Passive FTP
- **Active mode**: Server initiates data connection *to* client → often blocked by client-side firewalls.
- **Passive mode (PASV)**: Server provides a port; client initiates *both* control and data connections → firewall-friendly, commonly used.

FTP transmits data in **plaintext**, making it susceptible to sniffing. Credentials and files are unencrypted unless **FTPS (FTP over SSL/TLS)** is configured.

---

### Anonymous FTP
Many servers allow **anonymous login** (username: `anonymous`, password: any or blank). Though often restricted, misconfigurations can permit:
- Directory listing
- File download
- File upload (if `anon_upload_enable=YES`)

> **Always test for anonymous access** during enumeration—it’s a common low-hanging fruit.

---

### TFTP (Trivial File Transfer Protocol)
- Simpler, **connectionless** protocol using **UDP port 69**.
- **No authentication**, **no encryption**, and **no directory listing**.
- **Unreliable** (relies on application-layer retransmission).
- Typically used only in **trusted local networks** (e.g., PXE boot, router config backups).

**Common TFTP commands**:
- `get` – download file  
- `put` – upload file  
- `connect` – set remote host  
- `status`, `verbose`, `quit`

---

### FTP Server Configuration (vsFTPd Example)
The default config file: `/etc/vsftpd.conf`

#### Key Security Settings:
| Setting | Purpose |
|--------|--------|
| `anonymous_enable=YES` | Allows anonymous access |
| `anon_upload_enable=YES` | Permits file uploads by anonymous users |
| `anon_mkdir_write_enable=YES` | Allows directory creation |
| `write_enable=YES` | Enables write commands (STOR, DELE, MKD, etc.) |
| `chroot_local_user=YES` | Restricts local users to their home directory |
| `hide_ids=YES` | Masks UIDs/GIDs as “ftp” in listings (security through obscurity) |
| `ls_recurse_enable=YES` | Allows recursive directory listing (`ls -R`) |

#### User Access Control
- `/etc/ftpusers`: Lists users **denied FTP access** (e.g., `root`, `guest`).

---

### Enumeration & Interaction Techniques

#### 1. **Manual FTP Client**
```bash
ftp 10.129.14.136
Name: anonymous
Password: anything
```
Useful commands:
- `ls` / `ls -R` – list files (recursively if enabled)
- `get <file>` – download
- `put <file>` – upload
- `status` – view current session settings
- `debug` / `trace` – enable verbose protocol output

#### 2. **Bulk Download with wget**
```bash
wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136
```
Recursively mirrors the entire accessible FTP directory.

#### 3. **Nmap Enumeration**
Use Nmap with **NSE scripts** to automate detection:
```bash
sudo nmap -sV -p 21 -sC -A 10.129.14.136
```
Key NSE scripts:
- `ftp-anon.nse` – tests anonymous login and lists contents
- `ftp-syst.nse` – runs `STAT` to reveal server version and config
- `ftp-brute.nse` – brute-forces credentials (use cautiously)
- `ftp-vsftpd-backdoor.nse` – checks for known exploits

> Nmap script database: `/usr/share/nmap/scripts/`
> Update with: `sudo nmap --script-updatedb`

#### 4. **Raw Interaction (Telnet / Netcat)**
```bash
telnet 10.129.14.136 21
# or
nc -nv 10.129.14.136 21
```
Manual command entry (e.g., `USER anonymous`, `PASS x`, `LIST`).

#### 5. **FTPS (FTP over SSL/TLS)**
If FTP uses encryption, use **OpenSSL**:
```bash
openssl s_client -connect 10.129.14.136:21 -starttls ftp
```
- Reveals **SSL certificate details**
- May disclose internal hostnames (e.g., `master.inlanefreight.htb`) and admin emails

---

### Security Implications

- **Anonymous write access** → potential for web shell upload if FTP syncs with web root.
- **Sensitive files** (e.g., `Important Notes.txt`, contracts, credentials) often stored carelessly.
- **FTP logs** can be leveraged for **log poisoning → RCE** in LFI scenarios.
- **Clear-text protocol** → credentials exposed on shared networks.

> **Internal ≠ secure**: Misconfigured internal FTP servers are frequent pivot points in penetration tests.

---

### Summary Checklist for FTP Enumeration
✅ Scan for port 21 (and non-standard ports)  
✅ Test anonymous login  
✅ List and download all accessible files  
✅ Check for upload permissions  
✅ Use Nmap NSE scripts (`ftp-anon`, `ftp-syst`)  
✅ Inspect SSL cert if FTPS is used  
✅ Search for credentials, config files, or secrets in downloaded content  
✅ Assess integration with web servers (potential RCE via upload)

Understanding and exploiting FTP/TFTP is not just about file access—it’s often a gateway to deeper system compromise.