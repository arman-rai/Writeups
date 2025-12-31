https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb/f210069c-7086-4dc2-885e-861d837df688
https://github.com/cddmp/enum4linux-ng
https://github.com/ShawnDEvans/smbmap
🌟 https://github.com/Pennyw0rth/NetExec
https://github.com/byt3bl33d3r/CrackMapExec

## Server Message Block (SMB) Enumeration 

### **Protocol Overview**
- **SMB (Server Message Block)** enables file, printer, and inter-process communication over a network.
- Native to **Windows**, but **Samba** allows Linux/Unix systems to participate in SMB networks.
- Operates over:
  - **TCP 139**: SMB over NetBIOS (legacy)
  - **TCP 445**: Direct SMB (modern)

### **SMB Versions**
| Version | OS Support | Security Notes |
|--------|-----------|----------------|
| **CIFS / SMB 1.0** | NT 4.0, Win 2000 | Insecure; no encryption; deprecated |
| **SMB 2.0/2.1** | Vista, Win 7, Server 2008/R2 | Improved signing, performance |
| **SMB 3.0+** | Win 8+, Server 2012+ | **AES encryption**, integrity, multichannel |

> **Best Practice**: Disable SMBv1; enforce SMB 3.x with signing/encryption.

---

### **Samba Configuration Risks**
Key dangerous settings in `/etc/samba/smb.conf`:
```ini
[notes]
  path = /mnt/notes/
  browseable = yes     # Share visible to all
  guest ok = yes       # Allows anonymous access
  writable = yes       # Permits file modification
  create mask = 0777   # Full permissions on new files
```
- **`map to guest = bad user`** converts invalid logins into guest sessions.
- Misconfigured shares are common in test/dev environments and often forgotten.

---

### **Enumeration Workflow**

#### **1. Share Discovery (Anonymous)**
```bash
smbclient -N -L //10.129.14.128
```
- `-N`: Null session (no credentials)
- Reveals all browsable shares (e.g., `notes`, `IPC$`)

#### **2. File Interaction**
```bash
smbclient //10.129.14.128/notes
smb: \> ls
smb: \> get sensitive.txt
smb: \> !cat sensitive.txt  # Execute local shell command
```

#### **3. RPC-Based Enumeration (`rpcclient`)**
```bash
rpcclient -U "" -N 10.129.14.128
```
Critical commands:
- `srvinfo` – OS and server type  
- `enumdomusers` – List users  
- `queryuser <RID>` – Detailed user info  
- `netshareenumall` – Full share list with paths  
- `querydominfo` – Workgroup/domain details  

**RID Cycling** (enumerate hidden users):
```bash
for i in $(seq 500 1100); do
  rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x' $i)" | grep "User Name"
done
```

#### **4. Automated Tools**

| Tool | Key Features |
|------|-------------|
| **`enum4linux-ng`** | All-in-one SMB enum; exports to JSON/YAML; checks SMB dialect, policies, users, shares, OS |
| **`CrackMapExec`** | Fast share/user enum; shows permissions (e.g., `READ,WRITE`) |
| **`SMBMap`** | Lists shares and access rights |
| **`samrdump.py` (Impacket)** | Extracts SAMR user data |

> **enum4linux-ng example**:
```bash
./enum4linux-ng.py 10.129.14.128 -A -oY smb_enum
```
- Auto-skips unavailable services (e.g., LDAP)
- Detects null session support, SMB dialects, password policies

#### **5. Nmap (Limited Use)**
```bash
nmap -sV -sC -p139,445 10.129.14.128
```
- Basic version and `smb2-security-mode` only
- **Not sufficient** for deep enumeration

---

### **Critical Findings to Report**
- **Anonymous access** to shares → data leakage or file upload
- **Writable shares** → potential for **web shell deployment** or **LFI/RFI abuse**
- **User enumeration** → enables password spraying / brute-force
- **Weak password policy**:  
  - Min length = 5  
  - No complexity required  
  - No account lockout (`lockout_threshold: None`)
- **SMB signing disabled** → enables **NTLM relay attacks**

---

### **Operational Notes**
- Always cross-validate tool output with manual commands (e.g., `rpcclient`, `smbclient`).
- Use `smbstatus` on the target (if accessible) to monitor active sessions.
- **Never assume** all shares are listed—test for hidden or non-browsable ones.
- Export results (e.g., with `enum4linux-ng -oY`) for integration into larger workflows.

---

### **Summary Checklist**
- Scan **TCP 139/445**  
- Test **null session** access  
- List and **download files** from open shares  
- Enumerate **users via RID cycling**  
- Check **password policy** and **SMB dialect**  
- Identify **writable shares**  
- Document **SMB signing status**  
- Use **multiple tools** + **manual verification**

SMB remains a high-value target in penetration tests—especially when convenience overrides secure configuration.

---

