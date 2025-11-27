```
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry
|_/admin/
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Welcome to GetSimple! - gettingstarted
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

http://gettingstarted.htb/data/users/admin.xml

| d033e22ae348aeb5660fc2140aec35850c4da997 | sha1 | admin |
| ---------------------------------------- | ---- | ----- |

```
FFUF
backups                 [Status: 301, Size: 326, Words: 20, Lines: 10]
admin                   [Status: 301, Size: 324, Words: 20, Lines: 10]
theme                   [Status: 301, Size: 324, Words: 20, Lines: 10]
plugins                 [Status: 301, Size: 326, Words: 20, Lines: 10]
data                    [Status: 301, Size: 323, Words: 20, Lines: 10]
server-status           [Status: 403, Size: 283, Words: 20, Lines: 10]
.hta                    [Status: 403, Size: 283, Words: 20, Lines: 10]
.htaccess               [Status: 403, Size: 283, Words: 20, Lines: 10]
.htpasswd               [Status: 403, Size: 283, Words: 20, Lines: 10]
index.php               [Status: 200, Size: 5485, Words: 422, Lines: 152]
robots.txt              [Status: 200, Size: 32, Words: 3, Lines: 2]
sitemap.xml             [Status: 200, Size: 431, Words: 7, Lines: 3]
```

getsimple cms 3.3.15
(multi/http/getsimplecms_unauth_code_exec)

got shell as www-data

https://gtfobins.github.io/gtfobins/php/#sudo

got root