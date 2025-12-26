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

