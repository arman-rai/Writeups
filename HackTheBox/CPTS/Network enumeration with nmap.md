Enumeration is foundational—not about gaining access directly, but about discovering every possible attack vector.  
Tools alone are insufficient; understanding their output and knowing how to interpret and act on it is essential.  
Active interaction with services reveals more than passive scanning—observe responses, errors, banners, and behaviors.  
Deep knowledge of protocols, services, and their expected syntax enables effective probing and exploitation of anomalies.  
The goal is maximal information gathering: the more you know, the clearer the path to exploitation becomes.  
Precise information (like detailed directions) drastically reduces effort—vague data leads to wasted time.  
Access typically stems from two sources:  
1. Functions/resources that allow interaction or leak information.  
2. Information that unlocks further critical data (credentials, configs, version details, etc.).  
Misconfigurations—not just outdated software—are prime sources of vulnerabilities; overreliance on firewalls or patching is inadequate.  
"Enumeration is the key" is often misinterpreted: the issue isn’t missing tools, but lacking service-specific understanding.  
Investing time to learn how a service works (purpose, communication patterns, default behaviors) saves far more time than random tool spraying.  
Manual enumeration is irreplaceable: automated tools may miss filtered, slow-responding, or non-standard services due to timeouts or assumptions.  
A port marked “closed” by a scanner might still be open but unresponsive—manual verification prevents false negatives that block progress.

---

Network Mapper (Nmap) is a free and open-source utility for network discovery and security auditing, written primarily in C and C++, with components and scripting support in Lua (via the Nmap Scripting Engine) and integration possibilities in Python. It leverages raw IP packets to rapidly determine:

- Live hosts on a network  
- Open ports and associated services (including application name and version when possible)  
- Operating system and version  
- Presence and behavior of firewalls, packet filters, or IDS  

**Primary Use Cases**  
- Security auditing and vulnerability assessment  
- Penetration testing simulation  
- Firewall/IDS rule validation  
- Network inventory and mapping  
- Service and port discovery  
- Response analysis and baseline creation  

**Core Scanning Capabilities**  
1. **Host Discovery** – Identifies active devices on a network (e.g., via ICMP, ARP, or custom probes).  
2. **Port Scanning** – Determines open, closed, or filtered ports using various TCP/UDP/SCTP techniques:  
   - `-sS`: TCP SYN scan (stealthy, default for privileged users)  
   - `-sT`: TCP Connect scan (full handshake, used when raw packet access is unavailable)  
   - `-sU`: UDP scan (for connectionless services like DNS, SNMP)  
   - `-sN/-sF/-sX`: Null, FIN, and Xmas scans (stealth scans that exploit RFC-compliant stack behaviors)  
   - `-sI`: Idle scan (blind scan through a zombie host for complete anonymity)  
3. **Service and Version Detection** (`-sV`) – Probes open ports to identify service banners and software versions.  
4. **OS Detection** (`-O`) – Fingerprint operating systems based on TCP/IP stack characteristics.  
5. **Scriptable Interaction** – The **Nmap Scripting Engine (NSE)**, powered by Lua, enables advanced tasks like vulnerability detection, brute-forcing, and protocol exploitation via 100s of built-in scripts.  

**Basic Syntax**  
```bash
nmap [scan type] [options] <target>
```

**Example: TCP SYN Scan**  
```bash
sudo nmap -sS 127.0.0.1
```
- **Open port**: Target replies with SYN-ACK  
- **Closed port**: Target replies with RST  
- **Filtered port**: No response (likely due to firewall dropping packets)  

**Key Advantages**  
- **Flexible**: Adapts to networks with firewalls, NAT, or complex routing  
- **Fast**: Capable of scanning thousands of ports per second  
- **Portable**: Runs on Linux, Windows, macOS, and BSD variants  
- **Extensible**: NSE allows custom logic without modifying core code  
- **Well-documented**: Supported by a comprehensive book, man pages, and active community  

---
- Always begin internal network penetration tests with **host discovery** to identify live systems before deeper enumeration.  
- The most common and efficient method is **ICMP echo request** (`-PE`), but Nmap defaults to **ARP ping** on local subnets (Layer 2) because it’s faster and more reliable—ARP replies confirm host presence without relying on ICMP settings.  
- Use `-sn` to perform **ping scan only** (no port scanning); this initiates host discovery across a range, list, or single IP.  
- Save all scan results consistently using `-oA <basename>` to generate normal, XML, and grepable output files—essential for reporting, comparison, and audit trails.  
- When scanning a **network range**: `nmap -sn 10.129.2.0/24 -oA tnet`  
- When scanning from an **IP list**: `nmap -sn -iL hosts.lst -oA tnet`  
- For **specific IPs**: list them individually (`10.129.2.18 10.129.2.19`) or as a range (`10.129.2.18-20`)  
- To force **ICMP-only probing** and bypass ARP (e.g., for testing or non-local networks), use:  
  `nmap -sn -PE --disable-arp-ping <target>`  
- Use `--reason` to understand **why** Nmap marked a host as up (e.g., `arp-response`, `echo-reply`).  
- Use `--packet-trace` to **observe raw packet flow**—critical for verifying scan behavior and debugging false negatives.  
- Be aware: hosts may **appear offline** not because they’re down, but because firewalls **block ICMP** or **ignore unsolicited probes**—alternative discovery methods (e.g., TCP ACK, SYN to common ports) may be needed in restricted environments.  
- Host discovery is **context-dependent**: on local Ethernet, ARP is king; over routed networks or through firewalls, protocol-based probes (ICMP, TCP, UDP) become necessary.  
- Never assume absence of response equals absence of host—always correlate with other data sources and consider evasion-aware scanning techniques if initial results seem incomplete.
 
---

https://nmap.org/book/man-port-scanning-techniques.html

- Port state interpretation is critical:  
  - **open**: Service is actively accepting connections (TCP SYN→SYN-ACK, UDP reply, or SCTP INIT→INIT-ACK).  
  - **closed**: Port is reachable but no service listens (TCP RST response, ICMP port unreachable for UDP).  
  - **filtered**: Firewall/filter blocks or drops probes—no definitive response (no reply or non-port-unreach ICMP errors).  
  - **unfiltered**: Only in ACK scans—port is reachable but state (open/closed) undetermined.  
  - **open|filtered**: No response in UDP/SCTP scans; could be open with silent service or filtered.  
  - **closed|filtered**: Exclusive to idle scans—ambiguous due to zombie host behavior.  

- **SYN scan (`-sS`)** is default for privileged users:  
  - Sends SYN, interprets SYN-ACK (open), RST (closed), or no reply (filtered).  
  - Fast, stealthy (no full connection), but requires raw socket access (root/Admin).  

- **Connect scan (`-sT`)** is fallback for unprivileged users:  
  - Completes full TCP handshake via OS `connect()` call.  
  - Accurate but noisy—logs appear on target; less stealthy but more “polite” to services.  

- **Filtered ports** arise from firewall rules:  
  - **Dropped packets**: No response → Nmap retries (visible via `--packet-trace` showing multiple SENT, no RCVD).  
  - **Rejected packets**: ICMP type 3/code 3 (“port unreachable”) → still marked *filtered* because the service isn’t directly responding.  

- **UDP scanning (`-sU`)** is inherently slow and ambiguous:  
  - Empty or protocol-specific probes sent; response only if service is configured to reply (e.g., DNS, NetBIOS).  
  - **Closed**: ICMP port unreachable (type 3/code 3).  
  - **Open**: Direct UDP response from service.  
  - **open|filtered**: No response (silent service or dropped by firewall).  
  - Rate-limiting (e.g., Linux ICMP throttling) drastically slows scans.  

- **Version detection (`-sV`)** goes beyond port state:  
  - Actively interrogates open ports with protocol-specific probes.  
  - Extracts service name, version, OS hints (e.g., “Samba smbd 3.X–4.X”), and metadata (e.g., workgroup).  
  - May establish full connections and send application-layer payloads.  
  - Results should be verified; false positives occur—submit corrections via https://nmap.org/submit/.  

- Always disable extraneous discovery when analyzing specific behaviors:  
  - `-Pn`: Skip host discovery (assume host up).  
  - `-n`: Disable DNS resolution (speed + clarity).  
  - `--disable-arp-ping`: Force IP-layer probes (bypass ARP on local nets).  
  - `--packet-trace`: Observe raw packet flow for validation.  
  - `--reason`: Understand *why* a port is in a given state (e.g., “syn-ack”, “port-unreach”, “no-response”).  

- Port selection strategies:  
  - `--top-ports=N`: Scan N most common ports from Nmap’s database.  
  - `-F`: Fast scan (top 100 ports).  
  - `-p 22,80,443` or `-p 1-1000`: Custom lists or ranges.  
  - `-p-`: Full scan (65,535 ports)—comprehensive but slow.  

- Understanding *how* results are derived—not just what they are—enables accurate threat modeling, evasion of false negatives, and informed exploitation decisions.

---

- Always save Nmap scan results for documentation, comparison, and reporting—never rely solely on terminal output.  
- Nmap supports three primary output formats:  
  - **Normal (`-oN`)**: Human-readable `.nmap` file—mirrors terminal output with minimal formatting. Ideal for quick review.  
  - **Grepable (`-oG`)**: Simplified `.gnmap` format with consistent delimiters—optimized for scripting and `grep`/`awk` parsing (e.g., `grep "open" file.gnmap`).  
  - **XML (`-oX`)**: Structured `.xml` output containing full scan metadata (reasons, timing, host details)—designed for programmatic processing and integration with other tools.  
- Use **`-oA <basename>`** to save all three formats simultaneously (e.g., `target.nmap`, `target.gnmap`, `target.xml`).  
- Output files are saved in the current working directory unless a full path is specified.  
- **XML output enables rich reporting**:  
  - Convert to **HTML** using `xsltproc`:  
    ```bash
    xsltproc target.xml -o target.html
    ```  
  - Produces a clean, shareable report suitable for non-technical stakeholders.  
- **Grepable format structure**:  
  - Each line starts with `Host: <IP>`  
  - Open ports listed as `Ports: <port>/open/<proto>//<service>///`  
  - Closed/filtered ports summarized in `Ignored State`  
- **Normal format** includes command line, timestamps, and narrative summaries—useful for audit trails.  
- **XML format** preserves every detail: port states, service detection confidence, MAC/vendor info, timing stats—essential for forensic analysis or automated workflows.  
- Never discard raw output—future phases (e.g., vulnerability mapping, exploitation planning) often require re-examining original scan context.  
- Refer to official documentation for advanced output control: https://nmap.org/book/output.html

---

- **Service version detection (`-sV`)** is critical for vulnerability mapping—exact versions enable precise exploit selection and source code analysis.  
- Start with a **lightweight port scan** (e.g., top ports or common ranges) before launching a full `-p- -sV` scan to reduce noise and avoid triggering defenses.  
- **Full port scans are slow**—use real-time progress monitoring:  
  - Press **Spacebar** during a scan to show immediate stats.  
  - Use `--stats-every=5s` (or `1m`, etc.) for periodic automated updates.  
  - Increase verbosity with `-v` or `-vv` to see **open ports as they’re discovered**, aiding early triage.  
- Nmap performs **banner grabbing** by default during `-sV`: it connects to open ports and reads initial service responses (e.g., `220 inlane ESMTP Postfix (Ubuntu)`).  
- **Banners often contain more detail than Nmap reports**:  
  - Nmap uses **signature matching** (based on known responses) to normalize and classify services.  
  - This normalization may **omit distribution-specific info** (e.g., “(Ubuntu)”) even when present in raw banners.  
- **Manual verification is essential**:  
  - Use `nc`, `telnet`, or `openssl s_client` to manually connect and inspect raw banners.  
  - Capture traffic with `tcpdump` to observe the full exchange, including **PSH-ACK packets carrying banner data**.  
- The **three-way handshake precedes banner transmission**:  
  1. Client → Server: **SYN**  
  2. Server → Client: **SYN-ACK**  
  3. Client → Server: **ACK**  
  4. Server → Client: **PSH-ACK + banner payload**  
  5. Client → Server: **ACK** (confirmation)  
- Some services **delay or suppress banners** until valid input is received—Nmap’s `-sV` may send protocol-specific probes (e.g., `HELP`, `EHLO`) to trigger responses.  
- **Custom banners or version obfuscation** can mislead automated scans—always cross-check with manual interaction or alternative tools.  
- If Nmap’s version detection is inaccurate or incomplete, **submit corrections** via https://nmap.org/submit/ to improve the global fingerprint database.  
- Combine `-sV` with other discovery phases:  
  - Host discovery (`-sn`) → Port scan (`-p-`) → Version detection (`-sV`) → OS detection (`-O`) → Scripting (`--script`)  
- Remember: **version accuracy directly impacts exploit reliability**—never assume a service is “just Apache”; the patch level (e.g., `2.4.29`) may determine whether a public exploit succeeds or crashes the service.

---

- The **Nmap Scripting Engine (NSE)** extends Nmap beyond port scanning by enabling **Lua-based scripts** to interact with services, extract intelligence, and test for vulnerabilities.  
- NSE scripts are organized into **14 functional categories**:  
  - `auth`: credential testing  
  - `broadcast`: host discovery via broadcast  
  - `brute`: credential brute-forcing  
  - `default`: safe, commonly useful scripts (run with `-sC`)  
  - `discovery`: service enumeration  
  - `dos`: DoS vulnerability checks (use cautiously)  
  - `exploit`: active exploitation of known flaws  
  - `external`: integrates third-party services  
  - `fuzzer`: protocol fuzzing for crash detection  
  - `intrusive`: potentially disruptive scripts  
  - `malware`: detects signs of infection  
  - `safe`: non-intrusive, read-only probes  
  - `version`: enhances service detection  
  - `vuln`: identifies known vulnerabilities  

- **Script invocation syntax**:  
  - Run **default scripts**: `nmap -sC <target>`  
  - Run **all scripts in a category**: `nmap --script <category> <target>` (e.g., `--script vuln`)  
  - Run **specific scripts**: `nmap --script script1,script2 <target>` (e.g., `--script banner,smtp-commands`)  

- **Example**:  
  ```bash
  sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
  ```  
  - `banner`: reveals raw service banner (`220 inlane ESMTP Postfix (Ubuntu)`)  
  - `smtp-commands`: lists supported SMTP verbs (`VRFY`, `STARTTLS`, etc.), aiding user enumeration  

- The **aggressive scan (`-A`)** combines:  
  - OS detection (`-O`)  
  - Version detection (`-sV`)  
  - Default NSE scripts (`-sC`)  
  - Traceroute (`--traceroute`)  
  - Useful for rapid reconnaissance but **noisier** and **less stealthy**  

- **Vulnerability assessment** with `--script vuln`:  
  - Cross-references detected software (e.g., `Apache 2.4.29`, `WordPress 5.3.4`) against public CVE databases  
  - Reports specific vulnerabilities (e.g., `CVE-2019-0211`, `CVE-2018-1312`) with severity scores and links  
  - May reveal exposed paths (`/wp-login.php`), version disclosures (`readme.html`), and valid usernames (`admin`)  

- **Key NSE capabilities demonstrated**:  
  - **Banner extraction** (often more detailed than `-sV` alone)  
  - **Application fingerprinting** (e.g., WordPress version via static files)  
  - **User enumeration** (via `http-wordpress-users`)  
  - **Automated CVE mapping** (via `vulners` script)  

- **Always verify NSE results**:  
  - False positives occur; manual validation is essential  
  - Intrusive or exploit scripts may **disrupt services**—use only with permission  
  - Safe scripts (`safe`, `discovery`, `version`) are preferred in early phases  

- Full script documentation and library reference: https://nmap.org/nsedoc/index.html  
- Report incorrect OS/version/script results to improve community database: https://nmap.org/submit/

---

- **Scan performance is critical** when targeting large networks or operating under bandwidth constraints—speed must be balanced against accuracy.  
- **Round-Trip Time (RTT) timeouts** control how long Nmap waits for responses:  
  - Default: conservative (e.g., 100–1000 ms initial/max RTT).  
  - Optimizing with `--initial-rtt-timeout 50ms --max-rtt-timeout 100ms` can **reduce scan time significantly** (e.g., 39s → 12s), but risks **missing slow or distant hosts** due to premature timeouts.  
- **Retries (`--max-retries`)** determine how many times Nmap resends probes for unresponsive ports:  
  - Default: **10 retries** (robust but slow).  
  - Reducing to `--max-retries 0` skips non-responsive ports immediately—**speeds up scans** but may **miss filtered or rate-limited services** (e.g., 23 → 21 open ports detected).  
- **Packet rate control (`--min-rate <N>`)** forces Nmap to send at least *N* packets per second:  
  - Example: `--min-rate 300` reduced scan time from **29.8s to 8.7s** with **no loss in accuracy** (23 ports in both cases)—ideal in **white-box** or **trusted environments** with known network capacity and no IDS interference.  
- **Timing templates (`-T 0–5`)** offer predefined performance/stealth trade-offs:  
  - `-T0` (**paranoid**) / `-T1` (**sneaky**): Extremely slow; evades basic IDS (serial scanning, long delays).  
  - `-T2` (**polite**): Reduces bandwidth impact; useful on congested or fragile networks.  
  - `-T3` (**normal**): Default; balanced speed and reliability.  
  - `-T4` (**aggressive**): Assumes fast, reliable network—recommended for local/internal scans.  
  - `-T5` (**insane**): Maximizes speed (low timeouts, minimal retries, high parallelism); risks **inaccuracy** or **detection**, but effective in permissive environments (e.g., **32s → 18s**, full port coverage retained).  
- **Key trade-off**: **Speed vs. completeness**—aggressive tuning may skip hosts, miss filtered ports, or trigger defenses. Always validate critical findings with slower, more thorough scans if needed.  
- **Best practices**:  
  - Use `-T4` for internal networks with good connectivity.  
  - Avoid `-T5` or `--max-retries 0` in black-box engagements unless speed is prioritized over coverage.  
  - In white-box tests with network knowledge, combine `--min-rate`, adjusted RTT, and `-T4` for optimal throughput.  
- Reference full timing details:  
  - [Timing Templates](https://nmap.org/book/performance-timing-templates.html)  
  - [Performance Tuning](https://nmap.org/book/man-performance.html)

---

- **Firewalls** filter traffic based on rules—dropping (no response) or rejecting (explicit error like ICMP type 3/code 3 or TCP RST)—to block unauthorized access.  
- **IDS/IPS** systems passively monitor (IDS) or actively block (IPS) traffic using signature- or pattern-based detection (e.g., recognizing Nmap scans). Unlike firewalls, they’re harder to detect because they don’t alter packet flow visibly.  
- **Filtered ports** often indicate firewall interference; use **ACK scan (`-sA`)** to map firewall rules:  
  - Open/closed ports return **RST** → marked `unfiltered`  
  - No response or ICMP errors → marked `filtered`  
  - Firewalls often allow ACK packets (assuming established sessions), making `-sA` useful for probing rule sets.  
- **Evading detection and bypassing restrictions**:  
  - **Decoy scan (`-D RND:N` or `-D IP1,IP2,...`)**: Spoofs source IPs in IP header; your real IP is hidden among decoys. Critical that decoys appear “alive” to avoid triggering anti-spoofing (e.g., SYN flood) defenses.  
  - **Source IP spoofing (`-S <IP>`)**: Manually set source IP to test if firewall rules are IP-based (e.g., internal IPs allowed). Requires proper interface (`-e`) and often root.  
  - **Source port manipulation (`--source-port 53`)**: Abuse trusted ports like **53/TCP** (used for DNS zone transfers). Firewalls may permit traffic from these ports, bypassing filters.  
- **Example**: A port appears `filtered` in a standard SYN scan, but becomes `open` when scanned from source port 53—indicating a firewall rule allowing DNS-related traffic.  
- **Detecting IPS presence**:  
  - Use multiple **VPS instances** with distinct IPs.  
  - If one IP gets blocked during scanning, it suggests active IPS. Switch to another IP and reduce scan aggressiveness.  
- **Operational security**:  
  - Combine evasion techniques cautiously: decoys + spoofed ports + timing templates (`-T2` or `-T3`).  
  - Avoid `-T4`/`-T5` in black-box tests—high packet rates trigger alerts.  
  - Always validate findings manually (e.g., `nc -p 53 <target> <port>`) to confirm bypass success.  
- **Limitations**:  
  - Spoofed packets often dropped by upstream ISPs or egress filters (BCP38).  
  - Modern stateful firewalls and next-gen IPS may still detect decoy scans via traffic correlation.  
- **Strategic takeaway**: Evasion isn’t about “hiding completely”—it’s about **blending in**, **exploiting trust assumptions**, and **adapting based on observed filtering behavior**.

---

