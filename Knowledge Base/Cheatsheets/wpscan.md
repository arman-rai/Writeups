### 0. The Pre-requisite: The API Token

**No Tricks, Just Reality:** If you run `wpscan` without an API token, you are flying blind. You will see _versions_ of plugins, but you won't know if they are vulnerable because the tool can't query the WPVulnDB (WordPress Vulnerability Database).

- **Get the Token:** Register at `wpscan.com` (Free tier: 25 requests/day).
    
- **Config it:** Add it to your command or save it in `~/.wpscan/scan.yml`.
    

---

### 1. Basic Enumeration

Before you go aggressive, just see what the site reports back. This scans for version info, `robots.txt`, and basic headers.

Bash

```
# Basic scan with API token
wpscan --url http://target.htb --api-token <YOUR_TOKEN>

# Save output to a file (Always document your work!)
wpscan --url http://target.htb -o initial_scan.txt
```

> **The Coach says:** Always check the `robots.txt` manually (`curl http://target.htb/robots.txt`). Sometimes the sysadmin hides a specific plugin folder there that `wpscan` might miss if it's not in the common wordlist.

---

### 2. Aggressive Enumeration (Plugins & Themes)

WordPress sites are usually compromised through 3rd-party plugins, not the core core. By default, `wpscan` checks "popular" plugins. We need to go deeper.

|**Flag**|**Meaning**|**Context**|
|---|---|---|
|`-e vp`|Vulnerable Plugins|Only lists plugins with known CVEs (requires API token).|
|`-e ap`|All Plugins|Checks **thousands** of plugins. Noisy and slow. Use if `vp` finds nothing.|
|`-e vt`|Vulnerable Themes|Checks for themes with known exploits.|
|`--plugins-detection aggressive`|Aggressive Mode|Sends requests to standard plugin paths even if the homepage doesn't link to them.|

The "Standard" CTF/Pentest Command:

This is my go-to "loud" enumeration command. It checks Vulnerable Plugins (vp), Vulnerable Themes (vt), Users (u), and Config Backups (cb).

Bash

```
wpscan --url http://target.htb -e vp,vt,tt,cb,dbe,u,m --plugins-detection mixed --api-token $WPSCAN_API
```

- `tt`: Timthumbs (old image resizing script, historically vulnerable).
    
- `cb`: Config backups (looks for `wp-config.php.bak`).
    
- `dbe`: Database exports.
    

---

### 3. User Enumeration

You can't brute force if you don't know _who_ to attack. WordPress reveals users via `/?author=1` redirects, JSON API endpoints, or RSS feeds.

Bash

```
# Enumerate users (range 1-100 by default)
wpscan --url http://target.htb --enumerate u

# Enumerate a specific range of User IDs (e.g., 1 to 50)
wpscan --url http://target.htb --enumerate u1-50
```

> **Hypothesis Check:** If you find a user named `admin`, that's a mistake by the developer. If you find a user named `j.smith`, we now have a valid username for a targeted brute force.

---

### 4. Password Attacks (Brute Forcing)

**Operational Constraint:** Do not run this against a production Enterprise environment without explicit permission. You _will_ lock out accounts.

WPScan can attack the login portal (`wp-login.php`) or the XML-RPC interface (`xmlrpc.php`). XML-RPC is faster because it allows multiple password guesses in a single HTTP request (Multicall).

**Option A: The Login Page (Slower, Stealthier)**

Bash

```
wpscan --url http://target.htb --usernames admin --passwords /usr/share/wordlists/rockyou.txt
```

Option B: XML-RPC (Faster, Noisier)

If xmlrpc.php is enabled (check your initial scan), use this. It is significantly faster.

Bash

```
wpscan --url http://target.htb --usernames users.txt --passwords /usr/share/wordlists/rockyou.txt --password-attack xmlrpc
```

---

### 5. Evasion & Advanced Flags

If you are facing a WAF (Web Application Firewall) or need to route traffic through Burp Suite to analyze the requests.

- **Random Agent:** Standard `wpscan` user-agent is instantly blocked by most WAFs.
    
    Bash
    
    ```
    --random-user-agent
    ```
    
- **Proxying (Burp Suite):** This is vital. If `wpscan` reports a vulnerability, **don't trust it blindly.** Proxy it to Burp, see the request, and replay it manually to confirm.
    
    Bash
    
    ```
    --proxy http://127.0.0.1:8080
    ```
    
- **Throttle:** Slow down to avoid rate limits (milliseconds).
    
    Bash
    
    ```
    --throttle 500
    ```
    

---

### 6. Blue Team & Remediation

We always explain the fix. If you find these issues, here is what you tell the client:

- **Block User Enum:** "The application leaks usernames via the REST API. Consider using a security plugin to disable `/wp-json/wp/v2/users` access for unauthenticated users."
    
- **Disable XML-RPC:** "XML-RPC is rarely used by modern sites and allows for high-speed brute forcing. Disable it in `htacess` or via a plugin."
    
- **Version Hiding:** "The `readme.html` and version meta tags are visible. Remove these files to make automated reconnaissance harder."
    

---

### Next Step for You

You mentioned you are interested in XSS. A common vector in WordPress is Authenticated XSS via plugin settings.

Would you like me to show you how to use wpscan to find a specific plugin version, and then search searchsploit or Google to see if that version has a known XSS vulnerability?