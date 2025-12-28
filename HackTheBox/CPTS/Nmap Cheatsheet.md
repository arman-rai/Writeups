# NMAP BUG BOUNTY CHEATSHEET

## HOST DISCOVERY
```bash
# Ping sweep (stealthy)
nmap -sn 192.168.1.0/24 -oA hosts

# No ping (assume all hosts up)
nmap -Pn target.com

# Custom host discovery (bypass firewalls)
nmap -sn -PE -PP -PS22,80,443,31337 target.com
```

## PORT SCANNING
```bash
# Top ports (fast reconnaissance)
nmap --top-ports 100 -T4 -oA top100 target.com

# Full TCP scan (comprehensive)
nmap -p- -T4 -v --max-retries 1 --max-scan-delay 20 --open -oA full_tcp target.com

# UDP scan (often overlooked attack surface)
nmap -sU -sV --top-ports 20 --max-retries 1 -T4 -oA udp_scan target.com

# Stealth scans (when needed)
nmap -sS -f -D RND:5 -T2 -p 80,443,8080 target.com  # Fragmented decoy scan
nmap -sA -p 1-1000 target.com  # ACK scan to map firewall rules
```

## SERVICE ENUMERATION
```bash
# Version detection with aggressive timing
nmap -sV -sC -p- -T4 --script-timeout 15s --max-rtt-timeout 100ms -oA services target.com

# Web server deep dive
nmap -p 80,443,8000-8100 -sV --script http-*,ssl-* -oA web_scan target.com

# Critical services scan
nmap -p 21,22,23,25,110,139,143,445,3306,3389,5432,5900,6379 -sV -sC --script vuln -oA critical_services target.com
```

## FIREWALL EVASION
```bash
# Source port trickery (bypass weak firewall rules)
nmap -sS -g 53 --source-port 53 -p 22,443 target.com  # Pretend to be DNS

# Decoy scan (hide among other IPs)
nmap -D RND:10 -sS -T2 -p 80,443 target.com

# Fragment packets (bypass IDS)
nmap -f -sS -p 80,443 target.com

# Idle scan (completely blind scanning)
nmap -sI zombie.com target.com
```

## NSE SCRIPTING (BUG BOUNTY GOLD)
```bash
# Web application scanning
nmap -p 80,443 --script http-vuln*,http-sql-injection,http-xssed,http-shellshock,http-git,http-backup-finder -oA web_vulns target.com

# CMS detection and enumeration
nmap -p 80,443 --script http-wordpress*,http-drupal*,http-joomla*,http-magento* -oA cms_scan target.com

# SSL/TLS vulnerabilities
nmap -p 443 --script ssl-enum-ciphers,ssl-heartbleed,ssl-poodle,ssl-dh-params -oA ssl_scan target.com

# Misconfiguration checks
nmap -p 21 --script ftp-anon,ftp-bounce target.com
nmap -p 22 --script ssh-auth-methods,ssh-brute,ssh-known-hosts target.com
nmap -p 3306 --script mysql-empty-password,mysql-brute target.com
```

## PERFORMANCE OPTIMIZATION
```bash
# Fast internal network scan
nmap -p- -T5 --min-rate 1000 --max-retries 0 --max-scan-delay 20 --open -oA fast_internal target.com

# Slow external scan (avoid triggering rate limits)
nmap -p- -T2 --max-rate 50 --min-hostgroup 1 --min-parallelism 1 --scan-delay 5s -oA slow_external target.com
```

## OUTPUT MANAGEMENT
```bash
# Generate all formats (normal, XML, grepable)
nmap -oA full_scan target.com

# Convert XML to HTML report
xsltproc full_scan.xml -o report.html

# Parse open ports from grepable output
grep "open" scan.gnmap | awk '{print $2}' | cut -d "/" -f1

# Extract discovered subdomains from NSE output
grep "Host script results" scan.nmap -A 10 | grep -E "hostname|vhost"
```

## BUG BOUNTY PRO TIPS
- Always scan non-standard ports (8000-10000 range often contains hidden admin panels)
- Use `-v -v` for verbose output to catch subtle service banners
- Chain Nmap with other tools: pipe open ports to nuclei, ffuf, or custom scripts
- For API-heavy targets: `nmap -p 80,443 --script http-open-proxy,http-cors target.com`
- Check for exposed databases: `nmap -p 27017,6379,9200 --script mongodb-databases,redis-info,elasticsearch* target.com`
- Always verify NSE findings manually before reporting
- Use `--script-args http.useragent="Mozilla/5.0"` to blend in with normal traffic
- Never use aggressive scans (`-T5`) on initial external recon - you'll get banned
- Store all scans with timestamped filenames for historical comparison
- For JavaScript-heavy apps: complement Nmap with subfinder/assetfinder for subdomain discovery