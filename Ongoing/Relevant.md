```
80/tcp    open  http          syn-ack Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
	135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  syn-ack Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2025-11-12T01:39:36+00:00
| ssl-cert: Subject: commonName=Relevant
| Issuer: commonName=Relevant
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-11T01:36:52
| Not valid after:  2026-05-13T01:36:52
| MD5:   9bae 60af 0e89 fad6 f2a4 0c2e 2d76 746f
| SHA-1: 0246 0506 f06c e605 a4c6 c33c 2c94 700a 3809 3402
| -----BEGIN CERTIFICATE-----
| MIIC1DCCAbygAwIBAgIQEty+viYR2pdHwilrYhMl5jANBgkqhkiG9w0BAQsFADAT
| MREwDwYDVQQDEwhSZWxldmFudDAeFw0yNTExMTEwMTM2NTJaFw0yNjA1MTMwMTM2
| NTJaMBMxETAPBgNVBAMTCFJlbGV2YW50MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAtxMz0gN74lTmBTGTrvsRU2IVJmgfaZkuvm8njhBSNhdNLJOVXzed
| ab6zNhlbvo+5hh2SnGjx7Kfs1F7G4l4YXPRbUo4p6eom01IUB7Ro+xXjq6sVsu2U
| XJkhZH22qI0VisPYixR4ZB2my8bmoDkjJo5O6/vuTTXBZbuKO3U94UlTB0z8djOP
| ROoeNrP+qvDF0vngdCIIVwqRWx+qyQfCiqKYtI7Yuqta0fycVapFYqvZktp/vrq4
| 4Ey3QQJkQ1GPkenfXjWYPw1KRY9g5svlxW1NTZIbyNPRuAEv+Lwgv2z4bn/sRDtH
| JnSurX/mnyuuM+IDHYgDALdZFE3B41nijwIDAQABoyQwIjATBgNVHSUEDDAKBggr
| BgEFBQcDATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBAIwQMdEyz1Zo
| l5a7cyAU2n98AyBxnZNt0exhC0Pl1euu1/7tjObHj1UpK2b8pkdK4kuoPOvbrjqD
| mym5JXyvq9I4tK7kqMK6ZJUkE15IrFqxRzCqAIAgHpQKL7sUGrfgytxCf1j7S9Iw
| CZkJ7wjv6dKRIwnZs4nU3IT0OLCeqzx8pv/BwZehZ3z6MB0c8ydFKNtsuef3JnCa
| WT7j1GMubockW3W+VLtJQlA0GSMVCX3v1yUHNUapGq0Qz48vMoDBjjTfHOB9BJ18
| Q0eruUtflWXTFUVP4ykg5i668eEjMf/seR6R9X50ybCyZ6FNtp1EuToUUfqUhfgS
| o6ya8XKakVA=
|_-----END CERTIFICATE-----
|_ssl-date: 2025-11-12T01:40:15+00:00; -1s from scanner time.
49663/tcp open  http          syn-ack Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
49666/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: Host: RELEVANT; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 0s, deviation: 0s, median: -1s
|_ms-sql-info: ERROR: Script execution failed (use -d to debug)
| p2p-conficker:
|   Checking for Conficker.C or higher...
|   Check 1 (port 41508/tcp): CLEAN (Timeout)
|   Check 2 (port 49615/tcp): CLEAN (Timeout)
|   Check 3 (port 22880/udp): CLEAN (Timeout)
|   Check 4 (port 55765/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_smb-os-discovery: ERROR: Script execution failed (use -d to debug)
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2025-11-12T01:39:37
```

Shares on SMB:
```
namura@pop-os ~/tryhackme $ smbclient -L 10.201.117.76 -U Anonymous -N

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        nt4wrksv        Disk
SMB1 disabled -- no workgroup available

```