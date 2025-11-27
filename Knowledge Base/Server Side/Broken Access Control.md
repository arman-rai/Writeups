## Broken Access Control (Authorization Flaws)

- **What:** Access control enforces _who_ can do _what_. A broken control lets users perform actions beyond their rights, violating least privilege. Examples: non-admin accessing admin pages (vertical escalation) or one user reading another’s data (horizontal escalation).
    
- **Types:**
    
    - _Vertical:_ Ordinary user reaches admin functionality. E.g. browsing to `/admin` or submitting `?admin=true` when only admins should.
    - _Horizontal:_ User A accesses User B’s resources by tampering with IDs or credentials. E.g. `/account?user=123` changed to `user=124` (IDOR).
    - _Parameter-based:_ Role/privilege in client-side input. E.g. `?role=1` or `?admin=true`. If the app trusts these values, switching them grants higher rights.
    - _Platform issues:_ Undocumented URLs, alternate HTTP methods, or headers (like `X-Original-URL`) can bypass front-end checks.
        
- **Example payloads/techniques:**
    
    - Visit protected URLs directly: e.g. `GET /admin` or adding `?admin=true`.
    - Tamper parameters: Change `userId=100` to another (IDOR).
    - Exploit open redirects or misconfigured headers (e.g. `X-Original-URL: /admin`) to reach admin functions.
    
- **Impact:** Unauthorized access to data or functions. Non-admins may gain full admin control or view sensitive data. Often leads to data leaks or account takeover.
    
- **Mitigation:** Enforce server-side checks for every action. Never rely on client-side role parameters. Validate session user identity on each request to ensure only permitted operations are allowed.


# [[IDOR]]
