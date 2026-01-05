
```
PORT     STATE SERVICE REASON  VERSION
22/tcp   open  ssh     syn-ack OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 3e:b8:18:ef:45:a8:df:59:bf:11:49:4b:1d:b6:b8:93 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCm8EM7a8N6rH7GEW1mRVsOF6JokkXtMyWfSqOINFhEeatr5sCxnPjl42Y5rTVXZ0+LYHh+BRsnyEMsQ5nwXR8ac6Pu4bJOSa9hWj7Di0v4fgLGI4V14eEc9o1NJJ9bYJabr2odtrno0b0XiVsG8B5itnRJAOLRDLhjRMowbhPdXCBpRHXxeQ7P1wrGPdwyskb4NHkpu0F/U8UzBsl58+pPbVgGOlwdxLLhwgmxke1K5FXVq1S4eONElltA8wzv1yxYUo5gDsidHNlufsLI8QaA2DeeM0/DVC/T+uYYIJ0dDiQD6D/vKWsyMFfz0v9BYMVMCUEmvO+kG3byyG7ZiTHt
|   256 0b:cf:f9:94:06:85:97:f6:bd:cc:33:66:4e:26:ea:27 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBFw0bTfMp/o+68OnA07grCXThkkXA2j484lrLhkP/jWYPFd9+F9AbJACOeDHQUiUR635QhjIYyDfpV8TmfhstTo=
|   256 60:ce:be:2d:1e:f0:18:00:30:70:ff:a2:66:d7:85:f7 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAq1UqpgXAgXQ91rTHu4IiV8DSvEReXSA+EVfVpE2/3i
7777/tcp open  cbt?    syn-ack
| fingerprint-strings:
|   GetRequest:
|     HTTP/1.1 200 OK
|     Server: Werkzeug/2.3.4 Python/3.11.0
|     Date: Mon, 05 Jan 2026 02:09:19 GMT
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 5688
|     Connection: close
|     <!DOCTYPE html>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <meta http-equiv="X-UA-Compatible" content="IE=edge">
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <meta name="description" content="SSTI is wonderful">
|     <meta name="author" content="Ayham Al-Ali">
|     <link rel="icon" href="favicon.ico">
|     <title>Super Secret TIp</title>
|     <!-- Bootstrap core CSS -->
|     <link href="/static/css/bootstrap.min.css" rel="stylesheet">
|     <!-- Custom styles for this template -->
|     <link href="/static/css/carousel.css" rel="stylesheet">
|     </head>
|     <!-- NAVBAR
|     ================================================== -->
|     <body>
|     <div class="navbar-wrapper">
|     <div class=
|   Socks5:
|     <!DOCTYPE HTML>
|     <html lang="en">
|     <head>
|     <meta charset="utf-8">
|     <title>Error response</title>
|     </head>
|     <body>
|     <h1>Error response</h1>
|     <p>Error code: 400</p>
|     <p>Message: Bad request syntax ('
|     ').</p>
|     <p>Error code explanation: 400 - Bad request syntax or unsupported method.</p>
|     </body>
|_    </html>
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port7777-TCP:V=7.80%I=7%D=1/5%Time=695B1D4A%P=x86_64-pc-linux-gnu%r(Soc
SF:ks5,18B,"<!DOCTYPE\x20HTML>\n<html\x20lang=\"en\">\n\x20\x20\x20\x20<he
SF:ad>\n\x20\x20\x20\x20\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\
SF:x20\x20\x20\x20\x20\x20\x20<title>Error\x20response</title>\n\x20\x20\x
SF:20\x20</head>\n\x20\x20\x20\x20<body>\n\x20\x20\x20\x20\x20\x20\x20\x20
SF:<h1>Error\x20response</h1>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x2
SF:0code:\x20400</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Message:\x20Bad\x
SF:20request\x20syntax\x20\('\\x05\\x04\\x00\\x01\\x02\\x80\\x05\\x01\\x00
SF:\\x03'\)\.</p>\n\x20\x20\x20\x20\x20\x20\x20\x20<p>Error\x20code\x20exp
SF:lanation:\x20400\x20-\x20Bad\x20request\x20syntax\x20or\x20unsupported\
SF:x20method\.</p>\n\x20\x20\x20\x20</body>\n</html>\n")%r(GetRequest,16E7
SF:,"HTTP/1\.1\x20200\x20OK\r\nServer:\x20Werkzeug/2\.3\.4\x20Python/3\.11
SF:\.0\r\nDate:\x20Mon,\x2005\x20Jan\x202026\x2002:09:19\x20GMT\r\nContent
SF:-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x205688\r\nCon
SF:nection:\x20close\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en\">\n\x2
SF:0\x20<head>\n\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20\
SF:x20<meta\x20http-equiv=\"X-UA-Compatible\"\x20content=\"IE=edge\">\n\x2
SF:0\x20\x20\x20<meta\x20name=\"viewport\"\x20content=\"width=device-width
SF:,\x20initial-scale=1\">\n\n\x20\x20\x20\x20<meta\x20name=\"description\
SF:"\x20content=\"SSTI\x20is\x20wonderful\">\n\x20\x20\x20\x20<meta\x20nam
SF:e=\"author\"\x20content=\"Ayham\x20Al-Ali\">\n\x20\x20\x20\x20<link\x20
SF:rel=\"icon\"\x20href=\"favicon\.ico\">\n\n\x20\x20\x20\x20<title>Super\
SF:x20Secret\x20TIp</title>\n\n\x20\x20\x20\x20<!--\x20Bootstrap\x20core\x
SF:20CSS\x20-->\n\x20\x20\x20\x20<link\x20href=\"/static/css/bootstrap\.mi
SF:n\.css\"\x20rel=\"stylesheet\">\n\n\x20\x20\x20\x20<!--\x20Custom\x20st
SF:yles\x20for\x20this\x20template\x20-->\n\x20\x20\x20\x20<link\x20href=\
SF:"/static/css/carousel\.css\"\x20rel=\"stylesheet\">\n\x20\x20</head>\n<
SF:!--\x20NAVBAR\n==================================================\x20--
SF:>\n\x20\x20<body>\n\x20\x20\x20\x20<div\x20class=\"navbar-wrapper\">\n\
SF:x20\x20\x20\x20\x20\x20<div\x20class=");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

Usernames:
`Mustermann`

Got some fiiles on port 7777
```
cloud
debug
```

