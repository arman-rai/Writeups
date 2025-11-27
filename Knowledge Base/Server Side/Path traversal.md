## Path Traversal

- **What:** Also called _directory traversal_. Attackers use `../` (or variations) to traverse out of the intended directory, reading arbitrary files on the server. They can obtain source code, credentials, or system files. In Windows, backslashes (`..\`) or mixed-case (`../..//`) may be used.
    
- **Example payloads:**
    
    - Unix: `GET /image?file=../../../etc/passwd` retrieves `/etc/passwd`.
        
    - Windows: `filename=..\..\windows\win.ini` (or URL-encoded `..%5c..%5cwindows%5cwin.ini`).
        
    - Encodings: `%2e%2e/` (`../`), `%2e%2e%5c` (`..\`), `%252e%252e%255c` (double-encoded) to bypass filters.
        
    - Null byte trick: e.g. `file=secret.doc%00.pdf` can bypass naive extension checks.
        
- **Impact:** Attacker can read sensitive files (config, source, password hashes), and sometimes write files to modify behavior. Full system compromise is possible if write is allowed.
    
- **Bypasses:** Filters blocking `../` can often be evaded by alternative encodings (e.g. `%2e%2e/`, `....//`), numeric IPs (e.g. `017700000001` for `127.0.0.1`), or Unicode tricks.
    
- **Mitigation:** Never pass raw user input to filesystem calls. Use allow-lists (only permit expected filenames), canonicalize paths and verify they stay within a safe base directory. Store files outside the webroot if possible.

