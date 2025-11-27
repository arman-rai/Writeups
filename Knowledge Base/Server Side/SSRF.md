## SSRF (Server-Side Request Forgery)

- **What:** SSRF tricks the server into making HTTP(S) requests to attacker-chosen URLs. Often the target is an internal resource that the attacker cannot reach directly.
    
- **Attack examples:**
    
    - **Local server:** Point to `http://127.0.0.1` or `http://localhost` to reach services on the same host. E.g. changing a URL parameter to `http://localhost/admin` reveals an admin interface.
        
    - **Internal network:** Use internal IPs (like `http://192.168.0.x:port`) to scan intranet services.
        
    - **Cloud metadata:** Common SSRF payload: `http://169.254.169.254/latest/meta-data/iam/security-credentials/` to fetch AWS instance credentials. This can leak AWS keys or other cloud secrets.
        
    - **External redirection:** Submit a URL on a legitimate domain that redirects to an internal IP (open redirect bypass).
        
- **Bypasses/Tricks:**
    
    - **IP encoding:** `2130706433` (decimal), `017700000001` (octal), or IPv6 (`::ffff:127.0.0.1`) all resolve to 127.0.0.1.
        
    - **Domain hijack:** Register a domain pointing to `127.0.0.1` (e.g. via DNS or burp collaborator) and use it.
        
    - **Obfuscation:** URL-encode or embed credentials in URL (`http://user@127.0.0.1`) or use fragments/hashes to confuse filters.
        
    - **Chained redirect:** Exploit an open-redirect on an allowed host: e.g. `stockApi=http://trusted.com/redirect?url=http://internal/admin` .
        
- **Detection:** Monitor application behavior after injecting URLs (use Burp Repeater). For blind SSRF (no response), use out-of-band (e.g. Burp Collaborator) to catch DNS/HTTP callbacks.
    
- **Impact:** Can expose internal data/services. For example, AWS SSRF can give IAM credentials for EC2. Attackers can scan internal networks (port scan via SSRF), or even execute code if the internal service is exploitable.
    

