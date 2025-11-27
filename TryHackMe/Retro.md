```
PORT     STATE SERVICE       REASON  VERSION
80/tcp   open  http          syn-ack Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
3389/tcp open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: RETROWEB
|   NetBIOS_Domain_Name: RETROWEB
|   NetBIOS_Computer_Name: RETROWEB
|   DNS_Domain_Name: RetroWeb
|   DNS_Computer_Name: RetroWeb
|   Product_Version: 10.0.14393
|_  System_Time: 2025-10-09T03:24:26+00:00
| ssl-cert: Subject: commonName=RetroWeb
| Issuer: commonName=RetroWeb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-10-08T03:20:45
| Not valid after:  2026-04-09T03:20:45
| MD5:   7c7e edd2 688e 4e70 20ba 3abf 4a04 7058
| SHA-1: 85ce 9a04 e4b5 3ee5 309c 925b 0654 59fe 34da 275e
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQQZHluLTfCaRB2IXIcQIMdDANBgkqhkiG9w0BAQsFADAT
| MREwDwYDVQQDEwhSZXRyb1dlYjAeFw0yNTEwMDgwMzIwNDVaFw0yNjA0MDkwMzIw
| NDVaMBMxETAPBgNVBAMTCFJldHJvV2ViMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAsE61DJs1ueJZoynjJwIpzMAd4CzMFK2X56zhJ1NR+i7abKRuCPQ8
| OhF7/Ro8kvXcq+CN9og/NWLd+9e/9Z1DIbhjDcETt/OAHKcAjI56UFswyLINiUhB
| Qb7T8GM5SRncT7qYb9PLauPsFGoKU7fnUJR+UkuBGbMDoxvx+i30aR2aqd5KnDw3
| 0MQCBzbEDpU5ocSZjjTti9wVZYRzwYf5xeWY9xWImha5NCxlz77XBHUmmdl6jNlM
| hROkeMaaBvAHqOQKXO6Hf9FMn/RjvYRmsp7UI8gWeUfm5aaipTXFO8QOqB53rRDz
| oWH9EK3gmXi3OnIYmE2oTCcIYHWhyZ7bBQIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBAA4cq2h5wGV+
| 016EyAcSck7eMczR1M2CRU59mIxOYcgqE0zNpEbz/yoCBJEWsAgq0NNr3rJcpT1j
| bZXhWijbvqDJATgoRXeOeqP2i87ET4Le48ymIpzMtp3eyurfuSv6Faox8fu5E6CS
| HJA3botLeukAdOf4c8Ws6yyDqhn7eD2olMAX+Hc+02NtMOt2cxf3jkeM6V5O4SU9
| TSfohbMAigbZymEUVfd65I5KmwkHyfyPPoGl1AUwgIDQjSIWgWXba4cgWUPyM41z
| QVFKlmCzf721kgkfTwVUYEpzSJZl8u/CnxX5alJNo3msMlrhKcqWer9il+Ktkfa7
| 6bfLs13nHgQ=
|_-----END CERTIFICATE-----
|_ssl-date: 2025-10-09T03:24:31+00:00; -1s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s

```

After some recon I found the username `wade` and password I thought `parzival` which turned out to be correct lol

Then got in via RDP and got user flag, did some recon, 
On the Chrome bookmark there was a CVE marked which turned out to be the one 
ez privesc ever

I think I have already done this somewhere~