## Authentication Vulnerabilities

- **What:** Flaws in the login process or account management. If authentication is bypassed or credentials guessed, an attacker can impersonate users. Broken authentication is critical – compromise can reveal an entire user’s data or, if admin, the whole system.
    
- **Common issues:**
    
    - _Brute-force attacks:_ Automated guessing of usernames/passwords (often using wordlists). Common usernames (`admin`, `user`, email formats) and weak passwords (`Password1`, `123456`) are tried.
    - _Username enumeration:_ The app leaks valid usernames via different errors, status codes, or response times. For example, valid vs invalid login might return different messages or codes.
    - _Default/weak credentials:_ Built-in accounts (e.g. `admin:admin`, `root:root`) or reused breached credentials (credential stuffing).
    - _2FA/MFA flaws:_ Predictable one-time codes (`000000`, `123456`), rate-limit bypass, or mis-implementation allowing code reuse or bypass.
    - _Broken session management:_ Poor password reset (e.g. unvalidated email links), session fixation, missing logout.

- **Techniques/Payloads:**
    
    - Automate login attempts using tools (Burp Intruder, Hydra) with common username/password lists.
    - Try default or easy credentials: e.g. `admin:password`, `user:123456`, etc.
    - Check account lockout: intersperse correct logins to reset counters, as even some defenses reset after successful login.
    - In HTTP Basic Auth endpoints, use `curl -u user:pass` with guesses.
    - For brute-forcing, use logic: `' OR '1'='1` is SQL injection (handled separately), but password guesses like `Password123!` or variants using personal info.
- **Impact:** Attacker gains account access. A low-privileged breach may still reveal private data or give further access; admin compromise yields full control.

