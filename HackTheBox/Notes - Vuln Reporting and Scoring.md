## Briefing Document: Vulnerability Scoring, Assessment, and Disclosure

This document provides a detailed overview of key concepts and standards in vulnerability management, drawing from the provided source "Vulnerability Scoring, Assessment, and Disclosure." It covers methods for scoring vulnerabilities, languages for assessing system states, and the process of publicly cataloging security issues.

### 1. Vulnerability Scoring Systems

Two primary systems are discussed for evaluating the severity and risk of vulnerabilities: the Common Vulnerability Scoring System (CVSS) and Microsoft DREAD.

#### 1.1 Common Vulnerability Scoring System (CVSS)

CVSS is an industry standard for calculating severity ratings of vulnerabilities, used by many scanning tools. Understanding its derivation is crucial for manual calculation or justifying scores.

**1.1.1 CVSS Scoring Components:** The CVSS system categorizes severity based on **exploitability** and **impact**.

- **Exploitability Metrics:** Evaluate the technical means to exploit an issue.
- **Attack Vector:** How the attack is launched (e.g., network, local).
- **Attack Complexity:** The difficulty of exploiting the vulnerability.
- **Privileges Required:** Level of privilege an attacker needs.
- **User Interaction:** Whether user interaction is required for exploitation.
- **Impact Metrics:** Represent the repercussions of successful exploitation, based on the **CIA Triad**.
- **CIA Triad:** "Confidentiality, Integrity, and Availability."
- **Confidentiality:** "All About Access Control," ensuring data privacy and access only to those with a need to know. High severity example: attacker stealing passwords. Low severity example: attacker taking non-vital information.
- **Integrity:** "Is The Data Complete And Authentic," assuring data has not been tampered with and remains unaltered. High severity example: attacker modifying crucial business files. Low severity example: attacker cannot control modified files.
- **Availability:** "Always UP, Always ACCESSIBLE," ensuring users can reach resources when needed. High severity example: attacker causing environment to be completely unavailable. Low severity example: attacker cannot entirely deny access, some assets still accessible.

**1.1.2 CVSS Metric Groups:** CVSS scores are derived from three metric groups:

- **Base Metric Group:** Represents vulnerability characteristics, consisting of Exploitability and Impact Metrics.
- **Temporal Metric Group:** Details the availability of exploits or patches.
- **Exploit Code Maturity:** Probability of exploitation based on ease of techniques. Values include 'Not Defined,' 'High' (exploit consistently works, easily identifiable), 'Functional' (exploit code available), 'Proof-of-Concept' (PoC available, needs changes), and 'Unproven.'
- **Remediation Level:** Prioritization of a vulnerability. Values include 'Not Defined,' 'Unavailable' (no patch), 'Workaround' (unofficial solution), 'Temporary Fix' (official temporary solution), and 'Official Fix' (official patch released).
- **Report Confidence:** Validation and accuracy of technical details. Values include 'Not Defined,' 'Confirmed' (various sources confirm), 'Reasonable' (sources published, but incomplete details for reproduction), and 'Unknown.'
- **Environmental Metric Group:** Represents the significance of the vulnerability to a specific organization, considering the CIA triad.
- **Modified Base Metrics:** Allows organizations to alter base metrics if they deem a more significant risk to Confidentiality, Integrity, or Availability. Values include 'Not Defined,' 'High,' 'Medium,' and 'Low' to reflect the astronomical, significant, or minimal effects on the organization.

**1.1.3 CVSS Calculation:** The calculation of a CVSS v3.1 score incorporates all discussed metrics. The National Vulnerability Database provides a public calculator.

- **Example:** Windows Print Spooler Remote Code Execution Vulnerability has a CVSS Base Metrics score of 8.8.

#### 1.2 Microsoft DREAD

DREAD is a risk assessment system developed by Microsoft to evaluate the severity of security threats and vulnerabilities. It uses a 10-point scale based on five main factors:

- **D**amage Potential
- **R**eproducibility
- **E**xploitability
- **A**ffected Users
- **D**iscoverability

"The model is essential to Microsoft's security strategy and is used to monitor, assess and respond to security threats and vulnerabilities in Microsoft products."

### 2. Vulnerability Assessment Languages and Catalogs

Beyond scoring, standardized languages and catalogs facilitate the identification, assessment, and communication of vulnerabilities.

#### 2.1 Open Vulnerability Assessment Language (OVAL)

OVAL is a publicly available information security international standard, co-supported by the U.S. Department of Homeland Security, used to "evaluate and detail the system's current state and issues."

**2.1.1 OVAL Goal and Structure:** OVAL aims for a three-step assessment process:

1. Identifying a system's configurations for testing.
2. Evaluating the current system's state.
3. Disclosing the information in a report. Information can be described as: Vulnerable, Non-compliant, Installed Asset, and Patched.

**2.1.2 OVAL Definitions:** OVAL definitions are recorded in XML format to identify software vulnerabilities, misconfigurations, and system information _without needing to exploit a system directly_. This allows organizations to correlate which systems need patching. The four main classes of OVAL definitions are:

- **OVAL Vulnerability Definitions:** Identifies system vulnerabilities.
- **OVAL Compliance Definitions:** Identifies if configurations meet policy requirements.
- **OVAL Inventory Definitions:** Evaluates if specific software is present.
- **OVAL Patch Definitions:** Identifies if a system has the appropriate patch.

**2.1.3 OVAL ID Format:** Unique format: "oval:Organization Domain Name:ID Type:ID Value" (e.g., oval:org.mitre.oval:obj:1116). ID Type categories include: definition (def), object (obj), state (ste), and variable (var). Scanners like Nessus can use OVAL for security compliance scanning templates.

#### 2.2 Common Vulnerabilities and Exposures (CVE)

CVE is a publicly available catalog of security issues sponsored by the DHS. Each issue is assigned a unique CVE ID by a CVE Numbering Authority (CNA) to standardize vulnerability identification.

**2.2.1 What Qualifies for a CVE ID?** CVE IDs are assigned to flaws that meet specific criteria:

1. **Independently Fixable:** The flaw can be fixed independently of other bugs.
2. **Affecting One Codebase:** Flaws impacting multiple products get separate CVEs.
3. **Acknowledged By The Vendor And Documented:** The vendor acknowledges the bug's negative security impact, or the negative impact _and_ its violation of the affected system's security policy are documented.

**2.2.2 Stages of Obtaining a CVE:** A multi-stage process ensures proper identification, vendor notification, and public disclosure:

- **Stage 1: Identify if CVE is Required and Relevant:** "A vulnerability in the context of the CVE Program is indicated by code that can be exploited, resulting in a negative impact to confidentiality, integrity, OR availability, and that requires a coding change, specification change, or specification deprecation to mitigate or address." Verify no existing CVE ID.
- **Stage 2: Reach Out to Affected Product Vendor:** Good faith effort to contact the vendor directly.
- **Stage 3: Identify if Request Should Be For Vendor CNA or Third Party CNA:** Contact the appropriate CNA (if the vendor is a participating CNA) or a third-party coordinator.
- **Stage 4: Requesting CVE ID Through CVE Web Form:** If other methods fail.
- **Stage 5: Confirmation of CVE Form:** Receive confirmation email.
- **Stage 6: Receival of CVE ID:** Upon approval, the CVE ID is issued (not public yet).
- **Stage 7: Public Disclosure of CVE ID:** Announced publicly once vendors and parties are aware to prevent duplication.
- **Stage 8: Announcing the CVE:** Researchers sharing multiple CVEs should ensure each indicates different vulnerabilities.
- **Stage 9: Providing Information to The CVE Team:** Provide additional details for the official CVE listing and the NVD.

**2.2.3 Responsible Disclosure:** A critical practice where a researcher works with a vendor to ensure a patch is available _before_ publicly announcing a vulnerability. This prevents "real threat actors (from leveraging) the issues for criminal use, also referred to as a zero day or an 0-day."

**2.2.4 Examples of CVEs:**

- **CVE-2020-5902:** "an unauthenticated, remote code execution vulnerability in the BIG-IP Traffic Management User Interface (TMUI)." Leads to complete system takeover.
- **CVE-2021-34527 (PrintNightmare):** "a remote code execution vulnerability within the Windows Print Spooler service." Requires authentication but allows complete system takeover from remote or local code execution, including domain controllers and workstations.

### 3. Next Steps

The document concludes by setting the stage for exploring practical vulnerability scanning tools like Nessus and OpenVAS, leveraging the concepts of assessment types, vulnerability scoring, and disclosu