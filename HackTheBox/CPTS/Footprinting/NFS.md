### **Overview**
- **NFS (Network File System)** is a protocol developed by Sun Microsystems for sharing file systems over a network, primarily used in **Linux/Unix environments**.
- Unlike SMB, NFS uses **ONC-RPC (Open Network Computing Remote Procedure Call)** over **port 111 (rpcbind)** and **port 2049 (nfs)**.
- **NFSv2/v3**: Client-based authentication (relies on IP/UID/GID); **no user-level auth**.
- **NFSv4+**: Supports **Kerberos**, **ACLs**, **stateful operations**, and uses **only port 2049**, simplifying firewall traversal.

---

### **Key NFS Versions**
| Version | Features |
|--------|---------|
| **NFSv2** | UDP-only, legacy, limited error reporting |
| **NFSv3** | TCP/UDP, larger files, async writes, better error handling |
| **NFSv4** | **User authentication (Kerberos)**, firewall-friendly (port 2049 only), ACLs, stateful |
| **NFSv4.1** | Adds **pNFS** (parallel NFS) for clustered storage and **session trunking** |

> **Security Note**: NFSv2/v3 trust client-reported UIDs/GIDs—**only safe in trusted networks**.

---

### **Configuration: `/etc/exports`**
Controls which directories are shared and with what permissions.

**Basic Syntax**:
```bash
/path/to/share  client(options)
```

**Common Options**:
| Option | Description |
|-------|------------|
| `rw` | Read-write access |
| `ro` | Read-only |
| `sync` / `async` | Synchronous (safe) vs asynchronous (fast) writes |
| `no_subtree_check` | Disables subtree verification (improves performance) |
| `root_squash` | Maps root UID/GID to anonymous user (default, secure) |
| `no_root_squash` | **Dangerous**: Allows remote root to retain UID 0 → full file control |

**Example**:
```bash
/mnt/nfs  10.129.14.0/24(rw,sync,no_subtree_check,no_root_squash)
```

> **Critical Risk**: `no_root_squash` enables **privilege escalation**—an attacker with root on their machine can create SUID binaries or read sensitive files.

---

### **Enumeration Steps**

#### **1. Scan for NFS Services**
```bash
nmap -p 111,2049 -sV -sC 10.129.14.128
```
- **Port 111**: `rpcbind` → reveals registered RPC services
- **Port 2049**: `nfs` → actual file sharing

Use NSE scripts for deeper insight:
```bash
nmap --script nfs* -p 111,2049 10.129.14.128
```
Key scripts:
- `nfs-showmount`: Lists exported shares
- `nfs-ls`: Lists directory contents
- `nfs-statfs`: Shows disk usage

#### **2. List Available Shares**
```bash
showmount -e 10.129.14.128
```
Output:
```
Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

#### **3. Mount the Share**
```bash
mkdir /tmp/nfs-mount
sudo mount -t nfs 10.129.14.128:/mnt/nfs /tmp/nfs-mount -o nolock
```
- `-o nolock`: Bypasses `nlockmgr` (useful if port not open)

#### **4. Inspect Files and Permissions**
```bash
ls -l /tmp/nfs-mount      # Shows usernames/groups
ls -n /tmp/nfs-mount      # Shows raw UIDs/GIDs
```
- **UID 0 = root** → if `no_root_squash` is set, you can **modify any file**
- Look for:
  - SSH private keys (`id_rsa`)
  - Backup scripts
  - Configuration files
  - `flag.txt` (in CTFs)

> **Privilege Escalation Example**:  
> If you have SSH access as a low-priv user and find a writable NFS share with `no_root_squash`, you can:
> 1. Create a SUID binary on the NFS share
> 2. Execute it to gain root

#### **5. Unmount**
```bash
sudo umount /tmp/nfs-mount
```

---

### **Dangerous Misconfigurations**
| Setting | Risk |
|--------|------|
| `no_root_squash` | Remote root = local root → **full system compromise** |
| `rw` + broad subnet (e.g., `0.0.0.0/0`) | Allows anyone to modify files |
| `insecure` | Allows non-privileged ports (>1024) → easier client spoofing |
| Missing `root_squash` | Default should be enabled; disabling it is high-risk |

---

### **Summary Checklist**
-  Scan **ports 111 & 2049**  
-  Run `showmount -e` to list exports  
-  Use `nmap --script nfs*` for quick content preview  
-  **Mount** share locally for full inspection  
-  Check for **SSH keys**, **credentials**, **flags**, and **SUID opportunities**  
-  Verify if `no_root_squash` is enabled → **critical finding**  
-  Always **unmount** after enumeration  

NFS is often overlooked but can provide direct paths to privilege escalation—especially when administrators prioritize convenience over secure UID/GID handling.

---
