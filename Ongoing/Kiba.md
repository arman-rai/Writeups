```
PORT     STATE SERVICE   REASON  VERSION
22/tcp   open  ssh       syn-ack OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 9d:f8:d1:57:13:24:81:b6:18:5d:04:8e:d2:38:4f:90 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDdVrdscXW6Eaq1+q+MgEBuU8ngjH5elzu6EOX2UJzNKcvAgxLrV0gCtWb4dJiJ2TyCLmA5lr0+8/TCInbcNfvXbmMEjxv0H3mi4Wjc/6wLECBXmEBvPX/SUyxPQb9YusTj70qGxgyI6SCB13TKftGeHOn2YRGLkudRF5ptIWYZqRnwlmYDWvuEBotWyUpfC1fGEnk7iH6gr3XJ8pwhY8wOojWaXEPsSZux3iBO52GuHILC14OiR/rQz9jxsq4brm6Zk/RhPCt1Ct/5ytsPzmUi7Nvwz6UoR6AeSRSHxOCnNBRQc2+5tFY7JMBBtvOFtbASOleILHkmTJBuRK3jth5D
|   256 e1:e6:7a:a1:a1:1c:be:03:d2:4e:27:1b:0d:0a:ec:b1 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBD2fQ/bb8Gwa5L5++T3T5JC7ZvciybYTlcWE9Djbzuco0f86gp3GOzTeVaDuhOWkR6J3fwxxwDWPk6k7NacceG0=
|   256 2a:ba:e5:c5:fb:51:38:17:45:e7:b1:54:ca:a1:a3:fc (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJk7PJIcjNmxjQK6/M1zKyptfTrUS2l0ZsELrO3prOA0
80/tcp   open  http      syn-ack Apache httpd 2.4.18 ((Ubuntu))
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
5601/tcp open  esmagent? syn-ack
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe:
|     HTTP/1.1 400 Bad Request
|   FourOhFourRequest:
|     HTTP/1.1 503 Service Unavailable
|     retry-after: 30
|     content-type: text/html; charset=utf-8
|     cache-control: no-cache
|     content-length: 30
|     Date: Thu, 25 Sep 2025 07:15:14 GMT
|     Connection: close
|     Kibana server is not ready yet
|   GetRequest, HTTPOptions:
|     HTTP/1.1 503 Service Unavailable
|     retry-after: 30
|     content-type: text/html; charset=utf-8
|     cache-control: no-cache
|     content-length: 30
|     Date: Thu, 25 Sep 2025 07:15:05 GMT
|     Connection: close
|_    Kibana server is not ready yet
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port5601-TCP:V=7.80%I=7%D=9/25%Time=68D4EBF8%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,E0,"HTTP/1\.1\x20503\x20Service\x20Unavailable\r\nretry-after:
SF:\x2030\r\ncontent-type:\x20text/html;\x20charset=utf-8\r\ncache-control
SF::\x20no-cache\r\ncontent-length:\x2030\r\nDate:\x20Thu,\x2025\x20Sep\x2
SF:02025\x2007:15:05\x20GMT\r\nConnection:\x20close\r\n\r\nKibana\x20serve
SF:r\x20is\x20not\x20ready\x20yet")%r(HTTPOptions,E0,"HTTP/1\.1\x20503\x20
SF:Service\x20Unavailable\r\nretry-after:\x2030\r\ncontent-type:\x20text/h
SF:tml;\x20charset=utf-8\r\ncache-control:\x20no-cache\r\ncontent-length:\
SF:x2030\r\nDate:\x20Thu,\x2025\x20Sep\x202025\x2007:15:05\x20GMT\r\nConne
SF:ction:\x20close\r\n\r\nKibana\x20server\x20is\x20not\x20ready\x20yet")%
SF:r(RTSPRequest,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(RPCChe
SF:ck,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(DNSVersionBindReq
SF:TCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(DNSStatusRequest
SF:TCP,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(Help,1C,"HTTP/1\
SF:.1\x20400\x20Bad\x20Request\r\n\r\n")%r(SSLSessionReq,1C,"HTTP/1\.1\x20
SF:400\x20Bad\x20Request\r\n\r\n")%r(TerminalServerCookie,1C,"HTTP/1\.1\x2
SF:0400\x20Bad\x20Request\r\n\r\n")%r(TLSSessionReq,1C,"HTTP/1\.1\x20400\x
SF:20Bad\x20Request\r\n\r\n")%r(Kerberos,1C,"HTTP/1\.1\x20400\x20Bad\x20Re
SF:quest\r\n\r\n")%r(SMBProgNeg,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:\r\n")%r(X11Probe,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(Fo
SF:urOhFourRequest,E0,"HTTP/1\.1\x20503\x20Service\x20Unavailable\r\nretry
SF:-after:\x2030\r\ncontent-type:\x20text/html;\x20charset=utf-8\r\ncache-
SF:control:\x20no-cache\r\ncontent-length:\x2030\r\nDate:\x20Thu,\x2025\x2
SF:0Sep\x202025\x2007:15:14\x20GMT\r\nConnection:\x20close\r\n\r\nKibana\x
SF:20server\x20is\x20not\x20ready\x20yet")%r(LPDString,1C,"HTTP/1\.1\x2040
SF:0\x20Bad\x20Request\r\n\r\n")%r(LDAPSearchReq,1C,"HTTP/1\.1\x20400\x20B
SF:ad\x20Request\r\n\r\n")%r(LDAPBindReq,1C,"HTTP/1\.1\x20400\x20Bad\x20Re
SF:quest\r\n\r\n")%r(SIPOptions,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:\r\n")%r(LANDesk-RC,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n")%r(
SF:TerminalServer,1C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

```
namura@pop-os ~ $ whatweb http://10.201.80.217:5601/
http://10.201.80.217:5601/ [302 Found] Country[RESERVED][ZZ], IP[10.201.80.217], Kibana, RedirectLocation[/app/kibana], UncommonHeaders[kbn-name,kbn-xpack-sig]
http://10.201.80.217:5601/app/kibana [200 OK] Bootstrap, Country[RESERVED][ZZ], HTML5, IP[10.201.80.217], Kibana[6.5.4], Script, Title[Kibana], UncommonHeaders[kbn-name,kbn-xpack-sig], X-UA-Compatible[IE=edge]

```
## CVE-2019-7609


