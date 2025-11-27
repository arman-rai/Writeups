This was a fun one, I did it mainly using msfconsole
we could use the PoC and do it but I used the metasploit 
On Poc we create a .msi file that can help us pop s nt-auth shell which is always escalated 

found these on nmap 

```
Automatically increasing ulimit value to 5000.
Open 10.201.71.22:49671
Open 10.201.71.22:47001
Open 10.201.71.22:135
Open 10.201.71.22:5985
Open 10.201.71.22:49668
Open 10.201.71.22:445
Open 10.201.71.22:49670
Open 10.201.71.22:49667
Open 10.201.71.22:3389
Open 10.201.71.22:7680
Open 10.201.71.22:49672
Open 10.201.71.22:139
Open 10.201.71.22:49664
Open 10.201.71.22:49666
Starting Script(s)

Scanning cyberlens.thm (10.201.71.22) [14 ports]
Discovered open port 49664/tcp on 10.201.71.22
Discovered open port 7680/tcp on 10.201.71.22
Discovered open port 5985/tcp on 10.201.71.22
Discovered open port 49671/tcp on 10.201.71.22
Discovered open port 49672/tcp on 10.201.71.22
Discovered open port 135/tcp on 10.201.71.22
Discovered open port 47001/tcp on 10.201.71.22
Discovered open port 445/tcp on 10.201.71.22
Discovered open port 3389/tcp on 10.201.71.22
Discovered open port 139/tcp on 10.201.71.22
Discovered open port 49666/tcp on 10.201.71.22
Discovered open port 49670/tcp on 10.201.71.22
Discovered open port 49668/tcp on 10.201.71.22
Discovered open port 49667/tcp on 10.201.71.22
Completed Connect Scan at 17:42, 0.50s elapsed (14 total ports)

Scanned at 2025-08-28 17:42:12 +0545 for 69s

PORT      STATE SERVICE       REASON  VERSION
135/tcp   open  msrpc         syn-ack Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack
3389/tcp  open  ms-wbt-server syn-ack Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: CYBERLENS
|   NetBIOS_Domain_Name: CYBERLENS
|   NetBIOS_Computer_Name: CYBERLENS
|   DNS_Domain_Name: CyberLens
|   DNS_Computer_Name: CyberLens
|   Product_Version: 10.0.17763
|_  System_Time: 2025-08-28T11:58:12+00:00
| ssl-cert: Subject: commonName=CyberLens
| Issuer: commonName=CyberLens
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-08-27T11:55:45
| Not valid after:  2026-02-26T11:55:45
| MD5:   81ca 4218 2887 3ff1 9777 b61a 7c82 f304
| SHA-1: 2083 6ac0 2e7d d56d ee81 333b 4059 bb27 5abc 11e0
| -----BEGIN CERTIFICATE-----
| MIIC1jCCAb6gAwIBAgIQaQs6FTv0GahGMFWX2FhfODANBgkqhkiG9w0BAQsFADAU
| MRIwEAYDVQQDEwlDeWJlckxlbnMwHhcNMjUwODI3MTE1NTQ1WhcNMjYwMjI2MTE1
| NTQ1WjAUMRIwEAYDVQQDEwlDeWJlckxlbnMwggEiMA0GCSqGSIb3DQEBAQUAA4IB
| DwAwggEKAoIBAQC+4QWpKWQw/+vaGQlX5pkenVabrNQNvYpR1kEFhQoO1+og7mrf
| IHpm8N8/1d3LYimw5RKd2jiRMdFJTZT2we4ogoKkgMyJxSC8zYnwJdy8UvXJwbvC
| WlAz2rKdNO/9ET7Z5xqotyw8dsFxhZ9t8RZUtBNBzzm8mudycIDVKcDepQSjblUj
| sjudT5iZ6R2NiUnj/K1gNLwysTghSBqBJHNKKEhc+bUP1unq4+bKAdcDJLLL1UoH
| FxNltMHYKfJD/Iw8b4YGfvqFFJNyXZ3AmKT56ney6MOr9O8dDIgdOP+J4OQrynoC
| 6Zc9OTvNxHedrffSVvrKOVVTJtLFqAGOXr/hAgMBAAGjJDAiMBMGA1UdJQQMMAoG
| CCsGAQUFBwMBMAsGA1UdDwQEAwIEMDANBgkqhkiG9w0BAQsFAAOCAQEAYGIvpYcu
| C11wNTfEFRPjksnjM1taGcN0yJHUxLb4VpHoY15QmxhPt/9mTiZ22m+wgqydEkWT
| nzFyZbNqOIBzbjQc1PjMzMc/yCBPjRZFXax65d3tg2gcbO3PzZ4fIoQP3PG+o8+L
| RVWrVlfMgKcLtUE+5CMNDoEo3PoP5ueUzIBB0qZZ2JqlaHulxCPS4DROVLkoDTY0
| 46BaiEnuZIAUwEv2EH++6L/stRtAN/xKYtimBk1bVvCsw1n/uOnvLpgrJ2Xdf3eP
| Hy5KWrlNICjWFCT5r3RqHlcs7mKmh8z3kURaiYV+6mvnoAYP8+XZpTHTcbFQ/p78
| WXmAnNUaooUYnw==
|_-----END CERTIFICATE-----
|_ssl-date: 2025-08-28T11:58:20+00:00; -1s from scanner time.
5985/tcp  open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
7680/tcp  open  pando-pub?    syn-ack
47001/tcp open  http          syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 0s, deviation: 0s, median: -1s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 46632/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 36369/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 8959/udp): CLEAN (Failed to receive data)
|   Check 4 (port 41591/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-08-28T11:58:14
|_  start_date: N/A


```

On investigating the http port I found a comment on port 61777 and I focused there
then I found that it had Apache Tika 1.17 Server and found some module on msfconsole then I ran it on port 61777
and boom RCE shell and then I did winpeas these were the interesting ones

```
C:\Windows\system32>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State   
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

C:\Windows\system32>whoami /groups
whoami /groups

GROUP INFORMATION
-----------------

Group Name                             Type             SID          Attributes                                        
====================================== ================ ============ ==================================================
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group
BUILTIN\Remote Desktop Users           Alias            S-1-5-32-555 Mandatory group, Enabled by default, Enabled group
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\INTERACTIVE               Well-known group S-1-5-4      Mandatory group, Enabled by default, Enabled group
CONSOLE LOGON                          Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group
LOCAL                                  Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group
NT AUTHORITY\NTLM Authentication       Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192                                                    


systeminfo

Host Name:                 CYBERLENS
OS Name:                   Microsoft Windows Server 2019 Datacenter
OS Version:                10.0.17763 N/A Build 17763
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Multiprocessor Free
Registered Owner:          EC2
Registered Organization:   Amazon.com
Product ID:                00430-00000-00000-AA344
Original Install Date:     3/17/2021, 2:59:06 PM
System Boot Time:          8/28/2025, 11:54:51 AM
System Manufacturer:       Xen
System Model:              HVM domU
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 79 Stepping 1 GenuineIntel ~2300 Mhz
BIOS Version:              Xen 4.11.amazon, 8/24/2006
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC) Coordinated Universal Time
Total Physical Memory:     4,096 MB
Available Physical Memory: 2,369 MB
Virtual Memory: Max Size:  4,800 MB
Virtual Memory: Available: 3,110 MB
Virtual Memory: In Use:    1,690 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\CYBERLENS
Hotfix(s):                 27 Hotfix(s) Installed.
                           [01]: KB4601555
                           [02]: KB4470502
                           [03]: KB4470788
                           [04]: KB4480056
                           [05]: KB4486153
                           [06]: KB4493510
                           [07]: KB4499728
                           [08]: KB4504369
                           [09]: KB4512577
                           [10]: KB4512937
                           [11]: KB4521862
                           [12]: KB4523204
                           [13]: KB4535680
                           [14]: KB4539571
                           [15]: KB4549947
                           [16]: KB4558997
                           [17]: KB4562562
                           [18]: KB4566424
                           [19]: KB4570332
                           [20]: KB4577586
                           [21]: KB4577667
                           [22]: KB4587735
                           [23]: KB4589208
                           [24]: KB4598480
                           [25]: KB4601393
                           [26]: KB5000859
                           [27]: KB5001568
Network Card(s):           1 NIC(s) Installed.
                           [01]: AWS PV Network Device
                                 Connection Name: Ethernet
                                 DHCP Enabled:    Yes
                                 DHCP Server:     10.201.0.1
                                 IP address(es)
                                 [01]: 10.201.71.22
                                 [02]: fe80::c1e9:b44a:fca0:7993
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.



C:\\Windows\\Temp\\winpeas.exe

\Users\CyberLens\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
 ⭐️ https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/index.html#alwaysinstallelevated
    AlwaysInstallElevated set to 1 in HKLM!
    AlwaysInstallElevated set to 1 in HKCU!

   Folder: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
    FolderPerms: CyberLens [Allow: AllAccess]
    File: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\desktop.ini (Unquoted and Space detected) - C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows,C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\desktop.ini 
    FilePerms: CyberLens [Allow: AllAccess]
    Potentially sensitive file content: LocalizedResourceName=@%SystemRoot%\system32\shell32.dll,-21787
   =================================================================================================


    Folder: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
    FolderPerms: CyberLens [Allow: AllAccess]
    File: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunHidden.vbs (Unquoted and Space detected) - C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows,C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunHidden.vbs 
    FilePerms: CyberLens [Allow: AllAccess]
   =================================================================================================


    Folder: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
    FolderPerms: CyberLens [Allow: AllAccess]
    File: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunWallpaperSetup.cmd (Unquoted and Space detected) - C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows,C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\RunWallpaperSetup.cmd 
    FilePerms: CyberLens [Allow: AllAccess]
    Potentially sensitive file content: @Echo Off
   =================================================================================================


    Folder: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
    FolderPerms: CyberLens [Allow: AllAccess]
    File: C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\startup.bat (Unquoted and Space detected) - C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows,C:\Users\CyberLens\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\startup.bat 
    FilePerms: CyberLens [Allow: AllAccess]
    Potentially sensitive file content: @echo off


����������͹ Searching executable files in non-default folders with write (equivalent) permissions (can be slow)
     File Permissions "C:\Apache24\bin\ab.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\abs.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\ApacheMonitor.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\htcacheclean.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\htdbm.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\htdigest.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\htpasswd.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\httpd.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\httxt2dbm.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\logresolve.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\openssl.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\rotatelogs.exe": Users [Allow: AllAccess]
     File Permissions "C:\Apache24\bin\wintty.exe": Users [Allow: AllAccess]
     File Permissions "C:\Users\CyberLens\AppData\Local\Temp\1TKXKE.exe": CyberLens [Allow: AllAccess]

```

Then, I tried the one with ⭐️ and then boom nt-authority
lol, it would have been harder with PoC i guess
done in around 20 mins~