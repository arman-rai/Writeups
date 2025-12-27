###  The Penetration Tester Path: Memory Cheat Sheet

#### 1. The Core Philosophy
*   **"Learn by Doing":** This is not about clicking buttons; it is about understanding the **"Why"**.
*   **The Goal:** Build a repeatable methodology and "muscle memory" so you can handle the basics without thinking—saving brainpower for the hard stuff.
*   **The Mindset:** See both sides of the coin (Attacker vs. Defender) to find flaws others miss.

#### 2. The Golden Rule: Ethics & Law 
*   **Consent is King:** Never scan or probe a system without **explicit written permission** (a signed Scope of Work).
*   **The Line:** Passive OSINT (Googling/public info) is **OK**. Active scanning (touching their servers) is **Illegal** without consent.
*   **Do No Harm:** You are a guest in their network. Don't crash their servers.
*   **Verify Your Boss:** Criminals sometimes pose as "pentesting companies." Ensure you have a contract and the target is aware.
*   **When in Doubt:** **Document** everything and ask. Don't go out of scope.

#### 3. The Journey (Module Breakdown)
The path follows the story of a simulated attack on a company called **"Inlanefreight."** Here are the 28 modules grouped by phase:

**Phase I: The Setup**
*   *Intro & Getting Started*

**Phase II: The Hunt (Recon & Enum)**
*   *Nmap, Footprinting, Web Info Gathering, Vuln Assessment*

**Phase III: The Breach (Weaponizing)**
*   *File Transfers, Shells, Metasploit, Password Attacks, Common Services, Active Directory, Pivoting/Tunneling*

**Phase IV: The Web Frontier**
*   *Web Proxies, Ffuf, Brute Forcing, SQL Injection (SQLMap), XSS, File Uploads, Command Injection*

**Phase V: The Climb (Privilege Escalation)**
*   *Linux PrivEsc, Windows PrivEsc*

**Phase VI: The Closeout**
*   *Reporting & Documentation*
*   **The Capstone:** A full-blown mock exam where you run the whole show.

#### 4. Keys to Success
*   **Start Here:** If you are new, begin with the **"Information Security Foundations"** path first.
*   **Stay in Order:** The concepts build on each other (Lego style).
*   **Specialize Later:** Finish this general path first, then pick a niche (AD, Web, etc.).

***
### The Philosophy: "No Tricks, Just Reality"

**1. The "No Trick" Promise**
*   **Traditional Exams:** Often use trick questions or subjective answers to trip you up.
*   **HTB Academy:** We are not here to judge you or confuse you. We are here to teach you.
*   **The Goal:** To build **chains of association** between theory and practice. Every question is directly tied to the material.

**2. The "Art" of the Struggle**
*   **Real World Simulation:** In a real penetration test, you rarely get a perfect map. You get a vague task: *"Find vulnerabilities in this network."*
*   **Intentional Ambiguity:** If a question seems rough or imprecise, it’s because real life is rough and imprecise.
*   **The Mindset:** Easy tasks teach you nothing. The struggle to find the answer is where the actual learning happens.
*   **Remember:** *Finding the path is the art.*

---

### The Protocol: How to Ask for Help

Asking for help is a skill. Don't be the person who just says, *"Give me the answer."* That approach fails in school and gets you fired in a job.

**The "Partial Solution" Rule:**
When you are stuck, approach a mentor or the community with this structure:
1.  **What I Know:** Explain what you understand about the topic.
2.  **What I Tried:** List the commands, tools, or methods you attempted.
3.  **The Hurdle:** Explain specifically where you are blocked.

**Why this works:**
*   It proves you aren't lazy.
*   It helps the mentor help you better.
*   **Career Hack:** When reporting to a boss, never bring a problem without a potential solution. Always say, *"I think we should do X, does that sound right?"*

---

Here are the core lessons condensed into memorable hooks:

*   **On Difficulty:** "Hardness" is just the gap between where you are and where you want to be. Embrace it. (Cry0l1t3)
*   **On Consistency:** "Every day is a school day." Learn **one** new thing daily. (mrb3n)
*   **On Simplicity:** Don't overthink it. If you have a hunch, try it. The answer might be right in front of you. (plaintext)
*   **On Adversaries:** Watch the hackers. To stop them, you must think like them and copy their tactics. (Dimitris)
*   **On Growth:** The struggle between your skill level and the exercise is exactly where you improve. If it's easy, you aren't learning. (21y4d)
*   **On Persistence:** Comfort zones are easy, but they don't lead to success. Stay persistent. (LTNB0B & sentinal)
*   **On Teamwork:** Don't be afraid to ask questions. A fresh set of eyes can unblock you instantly. (TreyCraf7)

### Quick Summary Checklist
*   **Questions:** Reflect reality, not tricks.
*   **Stuck?:** Show your work first.
*   **Attitude:** Persistence > Intelligence.

---

Certainly, Namura. Below is a concise, well-organized **Penetration Testing Cheat Sheet** that distills the essential concepts, distinctions, and best practices from the full document—ideal for quick reference during engagements, study, or client discussions.

---

## **1. What Is a Penetration Test?**
- **Authorized**, **goal-oriented** simulation of real-world attacks.
- Validates **exploitability** of vulnerabilities—not just presence.
- Evaluates impact on **CIA Triad**:  
  - **C**onfidentiality  
  - **I**ntegrity  
  - **A**vailability  
- Provides **remediation guidance**—**not** fixes.

> 📌 *Delivers a **point-in-time snapshot**—not continuous monitoring.*

---

## **2. Key Testing Types**

| Type | Info Given | Use Case | Duration |
|------|-----------|--------|--------|
| **Black Box** | Target only (e.g., IP/domain) | External attacker simulation | Long (full recon needed) |
| **Grey Box** | Partial access (e.g., user creds, subnets) | Balanced realism & efficiency | Moderate |
| **White Box** | Full access (source code, configs, admin) | Deep internal audit / code review | Shorter, more thorough |
| **Red Team** | Varies + physical/social | Test detection & response | Multi-phase, stealth-focused |
| **Purple Team** | Full collaboration with defenders | Improve blue team capabilities | Iterative, feedback-driven |

---

## **3. Testing Perspective**

- **External**: From public internet → test perimeter defenses.
- **Internal**: From inside network → simulate post-breach or insider threat.  
  → May require on-site access for air-gapped systems.

---

## **4. Pentest vs. Other Assessments**

| Assessment | Method | Goal |
|-----------|--------|------|
| **Vulnerability Scan** | Automated only (Nessus, OpenVAS) | Find known CVEs/config issues |
| **Penetration Test** | **Manual + automated** | **Exploit & validate** real risk |
| **Red Team** | Adversary simulation | Achieve objective (e.g., domain admin) |
| **Purple Team** | Red + Blue collaboration | Improve detection/response |

---

## **5. Common Testing Domains**

- Network (Firewalls, Switches)
- Web & Mobile Applications
- APIs & Thick Clients
- Cloud (AWS, Azure, GCP)
- Source Code (Secure Code Review)
- Physical Security
- Human Layer (Phishing, Social Eng.)
- IoT / Embedded Systems
- Security Policies & Configurations

>  Often **combined** in enterprise engagements.

---

## **6. Legal & Ethical Essentials**

-  **Written authorization required** for **all tested assets**.
-  **Third-party assets?** Get explicit approval (e.g., from cloud provider or hosting company).
-  **Do not** retain, disclose, or misuse sensitive data (PII, credentials, financial info).
-  **Client** is responsible for **remediation**—not the tester.

>  Unauthorized testing = potential criminal liability.

---

## **7. Risk Management Context**

- Pentesting supports **risk identification & mitigation**.
- **Inherent risk** remains even after controls.
- Risk treatment options:  
  - **Mitigate** (patch, harden)  
  - **Accept** (documented decision)  
  - **Transfer** (e.g., cyber insurance)  
  - **Avoid** (discontinue service)

---

## **8. Privacy & Data Handling**

- If you access PII, credentials, or payment data:
  - **Minimize exposure**
  - **Do not log** unnecessarily
  - **Recommend immediate remediation** (e.g., password resets, encryption)
- Respect **employee privacy expectations**—management may choose to notify staff.

---

## **9. Pro Tips for Execution**

- **Scoping is critical**: Confirm IPs, domains, cloud accounts, and third-party dependencies.
- **Start stealthy**, escalate noise if testing detection capabilities.
- **Document everything**: Steps, commands, proofs, and impact.
- **Tailor reporting**: Technical depth for engineers, risk context for leadership.

---

> *“A pentest doesn’t make you secure—it shows you where you’re not.”*

---

| Precautionary Measures During a Penetration Test                                                                                                               |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Obtain written consent from the owner or authorized representative of the computer or network being tested                                                     |
| Conduct the testing within the scope of the consent obtained only and respect any limitations specified                                                        |
| Take measures to prevent causing damage to the systems or networks being tested                                                                                |
| Do not access, use or disclose personal data or any other information obtained during the testing without permission                                           |
| Do not intercept electronic communications without the consent of one of the parties to the communication                                                      |
| Do not conduct testing on systems or networks that are covered by the Health Insurance Portability and Accountability Act (HIPAA) without proper authorization |

---

Certainly, Namura. Below is a **concise, structured cheat sheet** that captures the core concepts, stages, and key actions of the **Penetration Testing Process**, distilled for quick reference, study, or field use.

---

# **Penetration Testing Process – Cheat Sheet**

> *“Not a rigid recipe—**a flexible, iterative framework** guided by findings.”*

---

## **Core Principles**
- **Goal-driven**: Each test aims to achieve a pre-defined objective (e.g., data access, domain admin).
- **Iterative & adaptive**: Stages often loop (e.g., lateral movement → post-exploitation → more lateral movement).
- **Client-specific**: No two engagements are identical—process must be tailored.
- **Evidence-based**: Every action builds on prior discovery or exploitation.

---

## **The 8 Stages of Penetration Testing**

| Stage | Purpose | Key Activities |
|------|--------|----------------|
| **1. Pre-Engagement** | Define scope, rules, and expectations | • Sign NDA<br>• Agree on **goals, scope, timing**<br>• Establish **Rules of Engagement (RoE)**<br>• Confirm asset ownership & third-party permissions |
| **2. Information Gathering** | Build situational awareness | • Passive/active recon (DNS, WHOIS, Shodan)<br>• Identify tech stack (Wappalyzer, nmap)<br>• Map network topology & services |
| **3. Vulnerability Assessment** | Identify potential weaknesses | • Scan with tools (Nmap, Nikto, Nessus)<br>• Manual review of logic flaws<br>• Cross-reference versions with CVEs/ExploitDB |
| **4. Exploitation** | Gain initial access | • Execute exploits (Metasploit, custom scripts)<br>• Abuse misconfigurations or logic flaws<br>• Verify access (e.g., reverse shell, RCE) |
| **5. Post-Exploitation** | Stabilize access & escalate | • Maintain persistence (backdoors, cron jobs)<br>• Enumerate local system (users, files, creds)<br>• **Privilege escalation** (kernel exploits, sudo misconfigs)<br>• **Pillaging**: extract sensitive data |
| **6. Lateral Movement** | Expand access across network | • Pass-the-hash, Kerberoasting, RDP abuse<br>• Reuse credentials from pillaging<br>• Pivot via compromised hosts (SSH, proxychains)<br>• Target high-value systems (DBs, DCs) |
| **7. Proof-of-Concept (PoC)** | Demonstrate & document impact | • Reconstruct attack chain step-by-step<br>• Provide **reproducible evidence** (screenshots, scripts)<br>• Show business impact (e.g., “We accessed payroll DB”) |
| **8. Post-Engagement** | Deliver, debrief, and clean up | • Write **technical + executive reports**<br>• Hold **report walkthrough**<br>• **Remove all traces** (tools, accounts, logs)<br>• Archive data per policy<br>• Optional: **Retest** after remediation |

---

## **Critical Notes**

 **Flexibility > Rigidity**  
- Stages are **not linear**—expect to cycle between Post-Exploitation and Lateral Movement repeatedly.

**Documentation = Value**  
- Poor PoC = client can’t fix it.  
- Automate repro steps when possible (e.g., Python script for exploit).

 **Scope is Sacred**  
- Never test outside agreed assets.  
- Confirm cloud/third-party permissions (e.g., AWS allows some testing, but not all).

 **Ethical Cleanup**  
- Remove shells, accounts, files, and logs.  
- Leave systems as you found them—**no backdoors**.

---

## **Study & Skill Progression Tip**
Master stages **sequentially but iteratively**:
1. Start with **recon & scanning** (Info Gathering + Vuln Assessment)
2. Practice **exploits in labs** (HTB, TryHackMe)
3. Focus on **Linux/Windows privilege escalation**
4. Learn **AD attacks** (e.g., Kerberoasting, ACL abuse)
5. Simulate **full attack chains** (initial access → domain admin)

>  Each module reinforces the next—gaps in fundamentals (e.g., networking, scripting) will hinder advanced stages.

---

## **Visual Flow (Simplified)**

```
Pre-Engagement  
     ↓  
Information Gathering  
     ↓  
Vulnerability Assessment  
     ↓  
Exploitation → [Post-Exploitation ↔ Lateral Movement] ↻  
     ↓  
Proof-of-Concept  
     ↓  
Post-Engagement
```

> Arrows indicate **iteration**—common in real engagements.

---

# **Pre-Engagement Cheat Sheet**  
*— Laying the Legal & Operational Foundation for a Safe, Authorized Pentest*

---

##  **1. Legal Prerequisites**
> **Never begin without these.**

| Document | Purpose | Timing |
|--------|--------|--------|
| **NDA (Non-Disclosure Agreement)** | Legally binds both parties to confidentiality | After initial contact, **before** sharing sensitive info |
| **Contract / Scope of Work (SoW)** | Formal agreement on deliverables, scope, pricing | Finalized in pre-engagement meeting |
| **Rules of Engagement (RoE)** | Technical & procedural boundaries of testing | Signed **before kick-off** |
| **Contractor’s Agreement** | Required **only for physical assessments**; proves lawful intent if detained | Before on-site testing |

>  **NDA Types**:
> - **Unilateral**: One party bound (rare in pentesting).
> - **Bilateral**: **Most common**—both parties confidential.
> - **Multilateral**: >2 parties (e.g., consortium testing).

---

##  **2. Authorization & Stakeholders**
> **Only authorized personnel can legally commission a pentest.**

 **Typical Authorized Roles**:
- CISO, CTO, CIO, CSO, CRO  
- CEO (in smaller orgs)  
- VP/Director of IT, InfoSec, or Internal Audit  

> **Never accept engagement from**:
> - Regular employees without written delegation  
> - Unauthorized third parties  

>  **Confirm**:  
> - Primary & secondary **technical POCs**  
> - **Emergency contacts** (for critical findings)  
> - **Signatory authority** for contracts

---

##  **3. Pre-Engagement Workflow**

### **Step 1: Scoping Questionnaire** *(Sent after NDA)*
Gather key details:

| Category | Sample Questions |
|--------|------------------|
| **Assessment Type** | External/Internal PT? Web app? Red team? Wireless? Social engineering? |
| **Scope Size** | # IPs/CIDRs? Domains? Web apps? Roles (user/admin)? |
| **Testing Conditions** | Black/Grey/White box? Evasive (stealth) or non-evasive? |
| **Constraints** | Out-of-scope systems? Business-critical assets to avoid? |
| **Third Parties** | Cloud (AWS/Azure)? Hosting providers? → **Require written approval** |
| **Objectives** | “Access payroll DB” or “Obtain domain admin”? |

>  Use responses to estimate **time, cost, and team size**.

---

### **Step 2: Pre-Engagement Meeting**
- Review questionnaire responses  
- Clarify goals, scope, and constraints  
- Explain pentest types & **recommend best fit**  
- Discuss **risks**: log flooding, account lockouts, system instability  
- Finalize **SoW & RoE**

>  **Key Contract Checklist**:
> - ☐ Goals & success criteria  
> - ☐ In-scope assets (IPs, URLs, CIDRs)  
> - ☐ Testing window (dates + hours)  
> - ☐ Methodologies (OWASP, PTES, OSSTMM)  
> - ☐ Third-party permissions  
> - ☐ Data handling (PCI, HIPAA, encryption)  
> - ☐ Reporting format + audience (executive vs. technical)  
> - ☐ Payment terms

---

### **Step 3: Rules of Engagement (RoE) – Core Elements**
Document must include:

| Section | Details |
|-------|--------|
| **Scope** | Exact IPs, domains, URLs, subnets |
| **Testing Window** | Start/end dates + permitted hours (e.g., “Mon–Fri, 6 PM–6 AM”) |
| **Testing Type** | External/Internal, Black/Grey/White box |
| **Prohibited Actions** | No DoS, no production DB writes, no phishing execs (if excluded) |
| **Incident Protocol** | How/when to report critical flaws (e.g., unauth RCE) |
| **Communication** | Primary channels (Slack, email, phone) |
| **Retesting** | Timeline for validation after fixes |
| **Liability Disclaimer** | “Tester not liable for system damage due to pre-existing instability” |

>  **Appendix**: Attach approved asset list & third-party consents.

---

### **Step 4: Kick-Off Meeting**
- Attendees: Client POCs, IT/security team, pentesters, PM  
- Confirm:  
  - Testing timeline & boundaries  
  - **Critical finding protocol**: Pause test → notify emergency contact  
  - **No DoS** (standard unless explicitly approved)  
  - Expected noise (logs, alerts, failed logins)  
- **Educate non-technical stakeholders** in plain language  
- Answer final questions

>  For **physical tests**: Distribute Contractor’s Agreement with building/floor access details + notarization.

---

## **Critical Reminders**

- **Never test without written authorization** → violates Computer Misuse Act (or local equivalent).  
- **Third-party infrastructure?** → Client must provide **written provider consent** (e.g., AWS allows testing under [AWS Pen Test Policy](https://aws.amazon.com/security/penetration-testing/), but others may not).  
- **Assume client is new to pentesting** → Over-communicate, avoid jargon.  
- **Document everything** → Your legal protection.  
- **Have a lawyer review templates** → Especially NDAs, SoW, and liability clauses.

---

##  **Post-Pre-Engagement: Ready to Test**
Once all docs are signed:
- Prepare VMs, VPS, tooling  
- Import scope into recon tools (e.g., `nuclei`, `amass`, `nmap`)  
- Set up secure comms (encrypted email, Signal, secure portal)  
- Confirm emergency contact availability

---

> *“The strength of your pentest begins long before the first scan—it starts with a bulletproof pre-engagement.”*

---

Certainly, Namura. Below is a **structured, actionable Information Gathering Cheat Sheet**—designed for quick reference during engagements, labs (e.g., TryHackMe, HTB), or CTFs—covering all critical sub-phases, techniques, and operational notes.

---

# **Information Gathering – Cheat Sheet**  
*— “The more you know, the less you break (and the faster you own).”*

---

##  **Why It Matters**
- **Foundation of every pentest**: All exploits depend on gathered intel.
- **Iterative**: Repeated during **Post-Exploitation** and **Lateral Movement**.
- **Goal**: Map attack surface, identify weak points, and prioritize targets.

>  *“You can’t attack what you don’t understand.”*

---

##  **Four Core Categories**

| Category | Purpose | Key Tools & Techniques |
|--------|--------|------------------------|
| **1. OSINT**<br>(Open-Source Intelligence) | Gather **publicly available** info about company, employees, tech stack | • `theHarvester`, `Amass`, `Hunter.io`<br>• GitHub dorks (`filename:.env`, `extension:pub`)<br>• LinkedIn, job posts (tech stack clues)<br>• Wayback Machine, Shodan, Censys |
| **2. Infrastructure Enumeration** | Map **network topology**: domains, IPs, cloud assets | • DNS recon (`dig`, `nslookup`, `dnsrecon`)<br>• Subdomain brute-forcing (`ffuf`, `gobuster`, `Sublist3r`)<br>• Cloud enumeration (`aws-recon`, `Azure CLI`, `gcp-scanner`) |
| **3. Service Enumeration** | Identify **running services**, versions, banners | • `nmap -sV -sC -p-`<br>• `masscan` (large networks)<br>• Banner grabbing (`nc`, `telnet`, `curl -v`) |
| **4. Host Enumeration** | Profile **individual systems**: OS, roles, configs | • OS detection (`nmap -O`)<br>• Share scanning (`smbclient`, `enum4linux`)<br>• Web tech (`Wappalyzer`, `whatweb`) |

>  **Always cross-reference findings with scope**—avoid out-of-bounds assets.

---

##  **OSINT – Critical Targets**

| Source | What to Look For | Risk Example |
|-------|------------------|-------------|
| **GitHub/GitLab** | Hardcoded secrets (API keys, passwords, SSH keys) | `grep -r "password" .` in public repos |
| **Job Postings** | Tech stack (e.g., “experience with Jenkins v2.235”) | → Exploit known CVEs in that version |
| **Company Website** | Subdomains (`dev.`, `staging.`, `admin.`), email formats | → Username patterns for spraying |
| **Shodan/Censys** | Exposed services (FTP, RDP, databases) | Anonymous FTP with sensitive files |
| **Pastebin/Dumps** | Leaked credentials, configs | Combine with password spraying |

>  **Found live credentials/keys?**  
> → **Pause** and follow **RoE critical finding protocol** (notify client immediately).

---

##  **Infrastructure Enumeration Tips**

- **DNS is gold**:  
  - Check for **zone transfers** (`dig axfr @ns1.target.com target.com`)  
  - Enumerate **MX, TXT, SPF** records (may reveal cloud providers, SPF rules)
- **Cloud misconfigs**:  
  - S3 buckets (`aws s3 ls s3://company-backup --no-sign-request`)  
  - Azure blob storage (`azcopy` with public URLs)
- **Compare discovered assets vs. client-provided scope** → May reveal **shadow IT**

---

##  **Service & Host Enumeration – Pro Tips**

| Service | Enumeration Command | Red Flag |
|-------|--------------------|--------|
| **SMB** | `crackmapexec smb 10.10.10.0/24` | SMBv1, null sessions, writable shares |
| **Web** | `gobuster dir -u http://target -w /wordlists/common.txt` | `/backup`, `/admin`, `.git/` |
| **FTP** | `ftp target.com` → try `anonymous:anonymous` | Anonymous login + file listing |
| **SSH** | `nmap --script ssh-auth-methods` | Weak ciphers, password auth enabled |
| **AD** | `ldapsearch -x -H ldap://dc.target.local` | User enumeration, group policies |

>  **Internal vs. External**:  
> - **Internal hosts** often have **unpatched/internal-only services** (e.g., Jenkins, Redis, MSSQL).  
> - Admins assume “not internet-facing = safe” → **prime targets**.

---

##  **Pillaging – Not a Separate Stage, But a Continuous Activity**

> Pillaging = **post-compromise info gathering** on a host to:
> - Escalate privileges  
> - Move laterally  
> - Prove business impact  

### **Where to Look (Post-Exploitation)**

| System | Key Locations |
|-------|--------------|
| **Linux** | `/etc/passwd`, `/home/*/.ssh/`, `/var/backups/`, `~/.bash_history`, cron jobs |
| **Windows** | `C:\Users\`, `%APPDATA%`, Registry (`HKLM\SOFTWARE`), PowerShell history |
| **Both** | Browser history, config files, password managers, notes.txt, spreadsheets |

### **Common Pillaged Data**
- Usernames → for **password spraying**
- Passwords/hashes → for **pass-the-hash**, **Kerberoasting**
- SSH keys → for **lateral SSH access**
- DB credentials → for **data exfiltration**

>  Covered in:  
> - **Privilege Escalation** modules (Linux/Windows)  
> - **Active Directory Enumeration**  
> - **Password Attacks**  
> - **Network Enumeration**  

---

##  **Iterative Nature Reminder**

Information Gathering **doesn’t end after recon**:
1. Initial OSINT → find subdomain → scan → find web app → exploit → gain shell  
2. **On host**: pillage → find AD creds → enumerate domain → lateral move → new host  
3. **Repeat**: gather more intel → escalate → exfiltrate

>  **Each stage feeds the next**.

---

##  **Ethical & Operational Notes**

- **Never assume safety**: Even “internal” services may trigger alarms.
- **Log noise**: Service scans may flood SIEMs—follow RoE on **evasiveness**.
- **Respect scope**: If `staging.target.com` isn’t in RoE, **do not test it**—even if found via OSINT.

---

# **Vulnerability Assessment – Reference Guide**

## **Purpose and Definition**  
Vulnerability Assessment is the analytical phase in which findings from Information Gathering are examined to identify, validate, and prioritize security weaknesses. It serves as the bridge between reconnaissance and exploitation. This phase is not limited to automated scanning; it includes manual analysis, contextual interpretation, and threat modeling based on real-world attack patterns.

The goal is to determine **whether a system is susceptible to known or logical vulnerabilities**, and to assess the **potential impact** of those vulnerabilities on confidentiality, integrity, and availability.

---

## **Core Analytical Frameworks**

| Analysis Type       | Role in Vulnerability Assessment |
|---------------------|----------------------------------|
| **Descriptive**     | Summarizes observed data (e.g., open ports, service banners, software versions). Identifies anomalies or inconsistencies in initial recon results. |
| **Diagnostic**      | Investigates the root cause of a potential vulnerability (e.g., why a service responds slowly, why a version is outdated). Links observed behavior to known misconfigurations or flaws. |
| **Predictive**      | Uses historical and current data to anticipate exploitability (e.g., if Apache 2.4.49 is running, path traversal is likely possible). Guides testing priorities. |
| **Prescriptive**    | Recommends specific actions: which exploits to attempt, which configurations to test, whether to build a custom payload, or whether to pivot to deeper recon. |

---

## **Key Activities**

### **1. Service and Version Correlation**  
- Match enumerated services (from Nmap, Netcat, etc.) with known software versions.  
- Example: An open port 2121 may not be standard, but resembles FTP (port 21). Testing with an FTP client may confirm it is an FTP service on a non-standard port.  
- Adjust tool parameters (e.g., `--min-rtt-timeout` in Nmap) if services respond slowly due to throttling or custom configurations.

### **2. Vulnerability Research**  
- Cross-reference identified software with public vulnerability databases:  
  - **NVD (NIST)**  
  - **CVE Details**  
  - **Exploit-DB**  
  - **Vulners**  
  - **Packet Storm Security**  
- Evaluate Proof-of-Concept (PoC) exploits for relevance, reliability, and required adaptations (e.g., payload encoding, target architecture).

### **3. Contextual Validation**  
- Not all reported CVEs are exploitable in every environment. Consider:  
  - Patch status (partial fixes, mitigations in place)  
  - Network segmentation or access controls  
  - Custom configurations that alter default behavior  
- Replicate the target environment locally when stealth or reliability is required (e.g., testing payloads offline before deployment).

### **4. Attack Vector Assessment**  
- Determine feasible paths to exploitation based on:  
  - Authentication requirements  
  - Input validation mechanisms  
  - Error handling (information leakage)  
  - Dependency chains (e.g., a vulnerable library used by a web app)  
- Prioritize vectors by **likelihood of success** and **business impact**.

---

## **Iterative Nature**

Vulnerability Assessment is **not a one-time step**. It frequently loops back to **Information Gathering** when:  
- Initial scans yield ambiguous results (e.g., filtered ports, unknown services).  
- No clear vulnerabilities are found—requiring deeper enumeration (e.g., web directory brute-forcing, DNS subdomain discovery).  
- Exploitation attempts fail, suggesting misidentification of the service or version.

This feedback loop ensures thorough coverage and reduces false negatives.

---

## **Critical Distinction: Pentest vs. CTF**

- In **Capture-The-Flag (CTF)** challenges or HTB-style boxes, speed and flag acquisition dominate. Depth and completeness are secondary.  
- In a **professional penetration test**, the priority is **comprehensive vulnerability identification and accurate risk characterization**. Missing a trivial but exploitable flaw—because it was overlooked during assessment—undermines the entire engagement’s value and credibility.

> A penetration test must answer: “What could a real attacker do?”—not just “Can we get a shell?”

---

## **Best Practices**

- **Never assume** a service is secure because it runs on a non-standard port.  
- **Verify all assumptions** through direct interaction (e.g., connect with `nc`, `curl`, or protocol-specific clients).  
- **Document all research**: CVEs considered, PoCs tested, and reasons for inclusion/exclusion of certain vectors.  
- **Avoid blind reliance on scanners**: Automated tools miss logic flaws, business logic bugs, and custom application vulnerabilities.  
- **Align testing with RoE**: If evasive techniques are required, avoid noisy scans; favor manual or low-and-slow probing.

---

## **Exploitation – Notes**

**Purpose of the Stage**  
The Exploitation stage focuses on leveraging identified vulnerabilities to achieve a specific objective, such as obtaining initial access (a foothold), executing arbitrary code, or establishing a reverse shell. This phase involves adapting or developing exploit code—often modifying public Proof-of-Concept (PoC) scripts—to function reliably and safely within the target environment.

**Integration with Other Stages**  
Exploitation is tightly coupled with Vulnerability Assessment and flows directly into Post-Exploitation. While stages should be conceptually distinguished for clarity and documentation, they often overlap in practice. Maintaining clear records of actions taken is essential, especially in engagements spanning weeks or large scopes.

**Prioritization of Attacks**  
Not all exploitable vulnerabilities should be pursued. Prioritization is based on three key factors:  
1. **Probability of Success** – Likelihood that the exploit will work against the target (e.g., using CVSS metrics or NVD scoring).  
2. **Complexity** – Effort required to prepare, adapt, and execute the exploit, influenced by tester experience and exploit maturity.  
3. **Probability of Damage** – Risk of causing service disruption, data loss, or system instability. Denial-of-Service (DoS) attacks are generally avoided unless explicitly authorized.

A personal scoring system may be used to objectively compare attack options. For example:  
- Remote File Inclusion: high success probability, low complexity, minimal risk → preferred.  
- Buffer Overflow: moderate success, high complexity, potential for system crash → deprioritized.

**Preparation and Validation**  
- If reliable PoC code is unavailable, replicate the target environment in a local VM using matching software versions.  
- Test exploits in isolation to verify functionality and safety before deployment against live systems.  
- Reuse known-safe techniques for common misconfigurations when possible.

**Risk Mitigation and Client Communication**  
- If uncertainty exists regarding exploit stability or impact, **consult the client** before proceeding.  
- Provide sufficient technical context to enable informed decisions.  
- If exploitation is declined, document the vulnerability as *theoretically exploitable* and recommend remediation.  
- Always exercise professional judgment: when in doubt, err on the side of caution and communicate proactively.

**Transition to Next Stage**  
Upon successful exploitation and establishment of initial access, thorough notes and activity logs must be maintained. The engagement then progresses to **Post-Exploitation**, where persistence, privilege escalation, and data collection begin.

**Ending Note**  
Exploitation is not an end in itself—it is a means to demonstrate real-world risk. Success is measured not by code execution alone, but by the ability to safely, reliably, and ethically validate a vulnerability’s impact while maintaining system integrity and client trust.

---

## **Post-Exploitation – Notes**

**Purpose of the Stage**  
Post-Exploitation begins after successful initial access to a target system. Its primary objectives are to:  
- Gather sensitive and business-relevant information from a local perspective,  
- Escalate privileges to maximize control,  
- Establish persistence to maintain access,  
- Prepare for lateral movement, and  
- Demonstrate the real-world impact of the compromise.

This stage inherently involves reapplying earlier phases—Information Gathering, Vulnerability Assessment, and even Exploitation—but from an internal, authenticated viewpoint.

**Evasive Testing Considerations**  
Once inside a system, actions are more likely to trigger detection by EDR, SIEM, or host-based monitoring. Evasive behavior is critical if the engagement requires stealth. Three testing modes may apply:  
- **Evasive**: Minimize noise to avoid detection and identify blind spots.  
- **Hybrid Evasive**: Begin quietly, then gradually increase activity to test detection thresholds.  
- **Non-Evasive**: Perform aggressive actions to validate defensive responses.  

Even if detected, the engagement retains value: it validates detection capabilities and highlights gaps in response workflows. Always align evasiveness with the Rules of Engagement.

**Information Gathering (Internal Perspective)**  
Internal reconnaissance expands on external findings by enumerating:  
- Local network interfaces, routing tables, ARP cache, DNS settings  
- Active services, shares, printers, databases, and virtualization platforms  
- Domain membership, group policies, and trust relationships  

This internal map informs pillaging, privilege escalation, and lateral movement strategies.

**Pillaging**  
Pillaging is the systematic search for high-value data and credentials on the compromised host, including:  
- Configuration files, scripts, and documents (e.g., .txt, .docx, .xlsx) containing passwords or API keys  
- Browser histories, saved credentials, and password vaults  
- SSH keys, Kerberos tickets, and session tokens  
- Network shares with sensitive or shared data  

Findings serve two purposes:  
1. Demonstrating business impact (e.g., exposure of customer PII),  
2. Providing inputs for privilege escalation or lateral movement (e.g., reused credentials).

**Persistence**  
Establishing persistence ensures continued access in case of disconnection or reboot. Techniques vary by OS and permissions (e.g., cron jobs on Linux, scheduled tasks or registry run keys on Windows). Persistence should be implemented early—especially if the initial exploit is unstable (e.g., a crashing service).

**Vulnerability Assessment (Internal)**  
With local access, perform a second vulnerability assessment focused on:  
- Local privilege escalation vectors (e.g., kernel exploits, misconfigured sudo rules, unquoted service paths)  
- Insecure file permissions, writable binaries, or SUID/SGID binaries  
- Outdated software or unpatched local services  

Prioritize exploits that are reliable and non-disruptive.

**Privilege Escalation**  
The goal is to attain the highest level of privilege on the system (e.g., root on Linux, SYSTEM or local/domain admin on Windows). Escalation may be:  
- **Technical**: Exploiting local vulnerabilities.  
- **Credential-based**: Using harvested credentials to authenticate as a higher-privileged user.  

Successful escalation dramatically expands access and is often a prerequisite for domain-wide compromise.

**Data Exfiltration**  
Exfiltration validates whether sensitive data can be removed from the environment. Before proceeding:  
- **Consult the client**—never exfiltrate real sensitive data without explicit approval.  
- Use **simulated data** (e.g., fake SSNs, test credit card numbers) that matches regulatory patterns (PCI, HIPAA, etc.) to trigger DLP/EDR without violating compliance.  

Document exfiltration with screenshots or screen recordings showing:  
- Hostname, IP address, username  
- File path and sample content  
- Successful transfer to attacker-controlled system  

If real sensitive data is discovered, **notify the client immediately** per the incident response protocol in the RoE.

**Regulatory and Compliance Context**  
Common data protection frameworks include:  
- PCI-DSS (payment data)  
- HIPAA (health records)  
- GLBA (financial data)  
- GDPR (EU personal data)  
- FISMA, FedRAMP, NIST, ISO 27001, CIS Controls  

While testers are not responsible for compliance enforcement, they must handle data with extreme caution and in accordance with the engagement’s legal boundaries.

**Transition to Lateral Movement**  
Post-Exploitation outputs—such as credentials, network maps, and domain context—directly feed into Lateral Movement. This stage is not an endpoint but a pivot point toward broader network compromise.

**Ending Note**  
Post-Exploitation transforms a technical foothold into strategic advantage. Its success is measured not by access alone, but by the clarity with which it demonstrates organizational risk—through data exposure, privilege abuse, and persistence—while adhering strictly to ethical, legal, and contractual boundaries.