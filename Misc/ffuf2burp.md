# FFUF Operational Cheat Sheet

### 1. The Pipeline: FFUF to Burp

**Goal:** Send _only_ the valid findings to Burp for manual analysis (Sitemap population & Repeater usage). Keeps your proxy history clean.

Bash

```
ffuf -u http://target.com/FUZZ -w wordlist.txt -replay-proxy http://127.0.0.1:8080 -mc 200,301
```

### 2. The Extraction: Burp to FFUF

**Goal:** Fuzz complex, authenticated requests without typing headers manually.

1. **Burp:** Right-click request -> "Copy to file" (or copy/paste to `req.txt`).
    
2. **Edit:** Replace the target value (Cookie, JSON param, etc.) with `FUZZ`.
    
3. **Clean:** Remove `Accept-Encoding: gzip` line to see cleartext responses in terminal.
    

Bash

```
ffuf -request req.txt -w wordlist.txt
```

### 3. Multi-Input Fuzzing (Clusterbomb)

**Goal:** Fuzz two parameters at once (e.g., specific usernames against specific IDs).

- **Setup:** Edit `req.txt` to have two placeholders, e.g., `USER` and `ID`.
    

Bash

```
# -mode clusterbomb tries every combination (slow, thorough)
# -mode pitchfork maps line-by-line (fast, requires equal length lists)
ffuf -request req.txt -w users.txt:USER -w ids.txt:ID -mode clusterbomb
```

### 4. Smart Filtering (Auto-Calibration)

**Goal:** Stop manually guessing file sizes (`-fs`) for soft 404s. Let ffuf detect the "normal" fail state.

Bash

```
# -ac sends random strings to learn what a 404 looks like
ffuf -u http://target.com/FUZZ -w wordlist.txt -ac
```

### 5. Stealth & Evasion (Blue Team Avoidance)

**Goal:** Avoid WAF bans and stay under the radar.

- `-rate 50`: Cap at 50 requests/second (Default is usually too fast).
    
- `-p 0.1-1.0`: Add a random delay between 0.1s and 1.0s per request.
    
- `-H "User-Agent: Mozilla/5.0..."`: **Always** change this. The default `ffuf/2.1.0` agent is flagged instantly.
    

### 6. Extension Hunting (The "High Value" Scan)

**Goal:** Find config backups (`.bak`, `.old`) or exposed source code (`.swp`, `.zip`).

Bash

```
# Don't use this with massive wordlists; use raft-small-words
ffuf -u http://target.com/FUZZ -w raft-small-words.txt -e .bak,.old,.conf,.swp,.zip -mc 200
```