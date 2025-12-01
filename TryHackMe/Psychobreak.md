suru ma ta nmap nai hane

eta uta web ma gaye

dev tools hane

gardai gaye

euta ma ftp wala thiyo

ftp bata download gare

.wav file lai strings ma convert gare

euta file thiyo tyas ma dictionary ra checker file thiyo

brute hane

ssh login bhetiyo

ogin hane flag paye

root garna sabai try gare

cron tab bata bhayo

teslai append garera file ma rev shell hande

bhayo

yo ruvik lai ni hataidye













```
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 62 ProFTPD 1.3.5a
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 44:2f:fb:3b:f3:95:c3:c6:df:31:d6:e0:9e:99:92:42 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDtgGI2Qpv+ora/iClEVeJSyw673ED4ciilMWv/Cw2NtVl9oB8A5rKktZYnJDw5sYZOvXimjb20Rk6a742anZZA87PM3StTZy8ZAMDEwdt8omaz5zy1c+HcJi4jjUIzPAZK10iKJ0JnyZ3eZZgEXALsU1zTi6U8Wn+6pixB9yRzAV8FVd/UThmC8vkiyNbNJUF6tgP+paajOIq2KzcmYrn8zZFL79EjDUUqSx72/wc/VUYyNArVGtVmOuvW1TBQwnpUv3zNQL1sabfiRzmgWB4unfHCVbj8autfHOfHSpMxC5QOuOJRTdhak6MUlHbjSXBF5MU1OP4mNTIoh/+e8k17
|   256 92:24:36:91:7a:db:62:d2:b9:bb:43:eb:58:9b:50:14 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCE8pJD7f5qX4X2kInnJf/m5wbTLOFA3I49Hyi2MrHxg3jREHseTbpqk00Xmy7F2+8Z8ljTdJwD9aafUAPgXxes=
|   256 34:04:df:13:54:21:8d:37:7f:f8:0a:65:93:47:75:d0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPxHqNM/ISBztZhs47D+flKJiTqFqt5kJrFDoeNyO8Zb
80/tcp open  http    syn-ack ttl 62 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Welcome To Becon Mental Hospital
|_http-server-header: Apache/2.4.18 (Ubuntu)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.4
OS details: Linux 4.4
```

On Source code got /sadistRoom and then got the keys
532219a04ab7a02b56faafbec1a4c1ea

and then inputted the code to get to the /lockerRoom 
then there is this 
Tizmg_nv_zxxvhh_gl_gsv_nzk_kovzhv => Grant_me_access_to_the_map_please

and got to the map
also got SafeHeaven/ and abandonedRoom/

on fuzzing SafeHeaven got SafeHeaven/keeper/
and got a OSINT challange which led to St. Augustine Lighthouse

and got the keeper key
48ee41458eb0b43bf82b986cecf3af01

Got these on ?shell=ls..
680e89809965ec41e64dc7e447f175ab be8bc662d1e36575a52da40beba38275 index.php

```
namura@pop-os ~/tryhackme $ cd psycho/
namura@pop-os ~/tryhackme/psycho $ ls
namura@pop-os ~/tryhackme/psycho $ ls -la
total 36
drwxrwxr-x 2 namura namura  4096 Nov 30 11:25 .
drwxrwxr-x 5 namura namura  4096 Nov 30 11:25 ..
-rw-rw-r-- 1 namura namura 26484 Nov 30 11:25 helpme.zip
namura@pop-os ~/tryhackme/psycho $ unzip helpme.zip
Archive:  helpme.zip
  inflating: helpme.txt
  inflating: Table.jpg
namura@pop-os ~/tryhackme/psycho $ ls -la
total 68
drwxrwxr-x 2 namura namura  4096 Nov 30 11:25 .
drwxrwxr-x 5 namura namura  4096 Nov 30 11:25 ..
-rw-r--r-- 1 namura namura   191 Jul  7  2020 helpme.txt
-rw-rw-r-- 1 namura namura 26484 Nov 30 11:25 helpme.zip
-rw-r--r-- 1 namura namura 26093 Jul  7  2020 Table.jpg
namura@pop-os ~/tryhackme/psycho $ cat helpme.
cat: helpme.: No such file or directory
namura@pop-os ~/tryhackme/psycho $ cat helpme.txt

From Joseph,

Who ever sees this message "HELP Me". Ruvik locked me up in this cell. Get the key on the table and unlock this cell. I'll tell you what happened when I am out of
this cell.

namura@pop-os ~/tryhackme/psycho $ open Table.jpg
namura@pop-os ~/tryhackme/psycho


namura@pop-os ~/tryhackme/psycho $ file Table.jpg
Table.jpg: Zip archive data, at least v2.0 to extract, compression method=deflate
namura@pop-os ~/tryhackme/psycho $ steghide extract -sf Table.jpg
Enter passphrase:
steghide: the file format of the file "Table.jpg" is not supported.
namura@pop-os ~/tryhackme/psycho $ steghide extract -sf Table.jpg
Enter passphrase:
steghide: the file format of the file "Table.jpg" is not supported.
namura@pop-os ~/tryhackme/psycho $ mv Table.jpg Table.zip
namura@pop-os ~/tryhackme/psycho $ file Table.zip
Table.zip: Zip archive data, at least v2.0 to extract, compression method=deflate
namura@pop-os ~/tryhackme/psycho $ unzip Table.zip
Archive:  Table.zip
  inflating: Joseph_Oda.jpg
  inflating: key.wav
namura@pop-os ~/tryhackme/psycho $ ls -la
total 124
drwxrwxr-x 2 namura namura  4096 Nov 30 11:28 .
drwxrwxr-x 5 namura namura  4096 Nov 30 11:25 ..
-rw-r--r-- 1 namura namura   191 Jul  7  2020 helpme.txt
-rw-rw-r-- 1 namura namura 26484 Nov 30 11:25 helpme.zip
-rw-r--r-- 1 namura namura 25399 Jul  7  2020 Joseph_Oda.jpg
-rw-r--r-- 1 namura namura 26844 Jul  7  2020 key.wav
-rw-r--r-- 1 namura namura 26093 Jul  7  2020 Table.zip
namura@pop-os ~/tryhackme/psycho $ open Joseph_Oda.jpg
namura@pop-os ~/tryhackme/psycho
namura@pop-os ~/tryhackme/psycho $ exiftool Joseph_Oda.jpg
ExifTool Version Number         : 12.40
File Name                       : Joseph_Oda.jpg
Directory                       : .
File Size                       : 25 KiB
File Modification Date/Time     : 2020:07:07 22:52:08+05:45
File Access Date/Time           : 2020:07:07 22:53:14+05:45
File Inode Change Date/Time     : 2025:11:30 11:28:00+05:45
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 350
Image Height                    : 490
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 350x490
Megapixels                      : 0.172
namura@pop-os ~/tryhackme/psycho $ strings Joseph_Oda.jpg | less
namura@pop-os ~/tryhackme/psycho $ audacity key.wav


```

got a code using 
https://morsecode.world/international/decoder/audio-decoder-adaptive.html

the file thankyou.txt was steg hidden and got the creds
joseph:intotheterror445 

got some files
cracked it using a simple checker and then got the encrypted value
used this https://www.dcode.fr/multitap-abc-cipher
and got password to kidman
