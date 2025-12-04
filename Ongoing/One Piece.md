
```
PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             187 Jul 26  2020 welcome.txt
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:192.168.134.56
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 01:18:18:f9:b7:8a:c3:6c:7f:92:2d:93:90:55:a1:29 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC45MSZ6fV/xyKjd0Vlj750dJSO5TPl1lrNfd+t+qc4LIKnaMoUsyIuxlnTOSQ0yHhGCxRYaDheybyGr1JqQrFazro9bL5cr3o0LQYLgTWbTcVAgkByqDvblrqUj1c6O4R0Z3BoppqzBgXIsUJFw96HAiYzVJCh9RN2rGnAHmqy8lIS/Z56pFlmiEOc3/W1ccnA/ABAIWkX25Kpxz+QE1eMEWEswLG57qmG8nt0qkOT6hQ9sskVW/ADnUmY3rO/dsP7TXh/IvI1slb6HALUlQXXfGUp/2CwOS7SfIthom8HJ3s7STVVOiAQM6xw6USA9QFLObcUSV0qHpXzJnyQtqtl
|   256 cc:02:18:a9:b5:2b:49:e4:5b:77:f9:6e:c2:db:c9:0d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLQ8y5fOAYcijtTXLprC5JojtRJvMIvbUGGFTMN5eYol3XZucpVKnt/fyLV/5x1jWXsnQixuE2QMCJ6hNRGwHgw=
|   256 b8:52:72:e6:2a:d5:7e:56:3d:16:7b:bc:51:8c:7b:2a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIWb4BgTYBRRA6bswNkUVwbviPydKMyyWsLyspHwzc/B
80/tcp open  http    syn-ack Apache httpd 2.4.29 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: C31581B251EA41386CB903FC27B37692
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: New World
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

On the Source code of the landing page, this was encoded:
`Nami ensures there are precisely 3472 possible places where she could have lost it.` <== This was an OSINT Task, lol

On FTP, there are some files
```.notes.txt
Hagrid told me that spells names are not good since they will not "rock you"
Hermonine loves historical text editors along with reading old books.
```

```welcome.txt
Welcome to Zou. It is an island located on the back of a massive, millennium-old elephant named Zunesha that roams the New World.
Except this, there is not much to say about this island.
```

There seems to be a directory which I didn't see on the first login named `.the_whale_tree`
```.secret_room.txt
Inuarashi: You reached the center of the Whale, the majestic tree of Zou.
Nekomamushi: We have hidden this place for centuries.
Inuarashi: Indeed, it holds a secret.
Nekomamushi: Do you see this red stele ? This is a Road Poneglyph.
Luffy: A Road Poneglyph ??
Inuarashi: There are four Road Poneglyphs around the world. Each of them gives one of the key to reach Laugh Tale and to find the One Piece.
Luffy: The One Piece ?? That's my dream ! I will find it and I will become the Pirate King !!!
Nekomamushi: A lot have tried but only one succeeded over the centuries, Gol D Roger, the former Pirate King.
Inuarashi: It is commonly known that both Emperors, Big Mom and Kaido, own a Road Poneglyph but no one knows where is the last one.
Nekomamushi: The other issue is the power of Big Mom and Kaido, they are Emperor due to their strength, you won't be able to take them down easily.
Luffy: I will show them, there can be only one Pirate King and it will be me !!
Inuarashi: There is another issue regarding the Road Poneglyph.
Nekomamushi: They are written in an ancient language and a very few people around the world can actually read them.
```

Steganography on one of the `.road_poneglyph.jpg` 
```
FUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWSALJNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LIKFUWS2LJNEAWS2LJNFUQC4LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAXC2LJNFUQC2LJNFUWSALRNFUWS2IBNFUWS2LJAFYWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBOFUWS2LJAFUWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNEAWS2LJNFUQC4LJNFUWSALJNFUWS2IBOFUWS2LIKFUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBOFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWSALJNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWSALRNFUWS2IBNFUWS2LJAFUWS2LJNEAXC2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LIKFUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAXC2LJNFUQC4LJNFUWSALJNFUWS2IBNFUWS2LJAFYWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNEAWS2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBOFUWS2LIKFUWS2LJNEAWS2LJNFUQC4LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUQC4LJNFUWSALJNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNEAWS2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFYWS2LJNEAWS2LJNFUQC4LJNFUWSALJNFUWS2IBOFUWS2LIKFUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2IBNFUWS2LJAFYWS2LJNEAWS2LJNFUQC2LJNFUWQULJNFUWS2IBNFUWS2LJAFYWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LJAFUWS2LJNBIWS2LJNFUQC2LJNFUWSALRNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUQC4LJNFUWSALRNFUWS2CRNFUWS2LJAFUWS2LJNEAXC2LJNFUQC4LJNFUWSALJNFUWS2IBNFUWS2LJAFYWS2LJNEAWS2LJNFUFC2LJNFUWSALJNFUWS2IBOFUWS2LJAFUWS2LJNEAWS2LJNFUQC2LJNFUWSALJNFUWS2IBNFUWS2LIK
road_poneglyphe1.txt (END)
```
and got this:  3HTXi9i2T2
nothing interesting imo and I got stuck

Got to know that we have to find this repo for moving on
https://github.com/1FreyR/LogPose/blob/master/LogPose.txt

and I got nothing on fuzzing subdomains and directories heck even files, I think somethings wrong with my machine
Imma try this after some time

Tried fuzzing again with extensions html, php and txt
got a directory named `/dr3ssr0s4.html`

Got some cryptic data on /images/rabbit_hole.png:
```
6b 65 79 3a 69 6d 20 6f 6e 20 6f 74 69 20 6f 74 69
m5.J`/{{#F%&!5GI}+n<a
Lhtttavbsw ql gbbzy gfivwwvz
```
which meant
```
key:im on oti oti
??
```

Searched up the source code again and found `/king_kong_gun.jpg` on the .css file 
and it had a comment : ` Doflamingo is /ko.jpg`

got another image on `/ko.jpg`
on strings, we got 
