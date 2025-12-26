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

*— Namura | Cybersecurity Practitioner*  
*Date: December 26, 2025*

---

Would you like this as a **fillable PDF template**, **Notion database**, or integrated with your **n8n automation workflow** for client onboarding? I can also generate a **checklist version** for your daily use.