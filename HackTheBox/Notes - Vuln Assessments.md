## Detailed Briefing Document: Security Assessments and Standards

This briefing document provides a comprehensive overview of various security assessments, their purposes, methodologies, and the importance of adhering to relevant standards. It also clarifies key cybersecurity terms like vulnerability, threat, risk, and asset management, drawing directly from the provided source material.

### 1. The Imperative of Security Assessments

Every organization, regardless of its size, industry, or security maturity, must conduct regular security assessments. The fundamental purpose of these assessments is "to find and confirm vulnerabilities are present, so we can work to patch, mitigate, or remove them." The types of assessments chosen depend on an organization's "compliance requirements and risk tolerance, face different threats, and have different business models." All organizations are obligated to "stay on top of both legacy and recent vulnerabilities and have a system for detecting and mitigating risks to their systems and data."

### 2. Core Security Assessment Types

The source details several key types of security assessments:

#### 2.1. Vulnerability Assessment

- **Purpose:** To identify and categorize security weaknesses based on a specific security standard, essentially "going through a checklist." They aim to "understand, identify, and categorize the risk for the more apparent issues present in an environment without actually exploiting them to gain further access."
- **Appropriateness:** "Vulnerability assessments are appropriate for all organizations and networks." They are particularly beneficial for organizations with lower security maturity levels, as a penetration test might uncover too many issues, overwhelming staff.

1. **Methodology (8 Steps):Conduct Risk Identification And Analysis:** Identify all IT assets and assign risks.
2. **Develop Vulnerability Scanning Policies:** Create and get approval for official scanning procedures.
3. **Identify The Type Of Scans:** Determine appropriate scan types (network, host-based, wireless, application) based on system software.
4. **Configure The Scan:** Define target IPs, port ranges, protocols, aggressiveness, time, and notifications.
5. **Perform The Scan:** The tool fingerprints and enumerates targets.
6. **Evaluate And Consider Possible Risks:** Understand potential impact on system availability during scans.
7. **Interpret The Scan Results:** Prioritize vulnerabilities with public exploits.
8. **Create A Remediation & Mitigation Plan:** Prioritize mitigation and ensure close communication between information security and IT staff.

- **Key Distinction from Pentesting:** Vulnerability assessments focus on identifying vulnerabilities and providing remediation steps without performing extensive manual exploitation. Assessors "will show evidence that the vulnerability exists and is not a false positive... but will not seek to perform privilege escalation, lateral movement, post-exploitation, etc."
- **Benefits:** "Cost-effective method of identifying low hanging vulnerabilities" and requires a lower skillset.
- **Limitations:** "May not identify vulnerabilities requiring manual inspection," "Potential false positives," and "Generic recommendations that may not be relevant."

#### 2.2. Penetration Test (Pentest)

- **Purpose:** To determine "if and how they can penetrate a network" by simulating cyberattacks with legal consent. The goal is "to see if certain kinds of exploits are possible."
- **Appropriateness:** Most suitable for organizations with a "medium or high security maturity level." It's crucial that pentests are conducted "after some vulnerability assessments have been conducted successfully and with fixes."
- **Legal Framework:** Requires a "lengthy legal document with the target company that describes what they're allowed to do and what they're not allowed to do."
- **Types of Pentesting (based on knowledge level):Black Box:** Performed with "no knowledge of a network's configuration or applications," simulating an external attacker.
- **Grey Box:** Conducted with "a little bit of knowledge," akin to an employee outside the IT department.
- **White Box:** Testers are given "full access to all systems, configurations, build documents, etc., and source code," aiming to find as many flaws as possible.
- **Specializations of Pentesters:Application Pentesters:** Assess web, thick-client, API, and mobile applications, often skilled in source code review.
- **Network or Infrastructure Pentesters:** Evaluate all aspects of a computer network, including devices, workstations, servers, and applications, requiring strong networking, OS, and scripting knowledge.
- **Physical Pentesters:** Leverage physical security weaknesses (e.g., "Can you tailgate someone into the data center?").
- **Social Engineering Pentesters:** Test human beings for susceptibility to scams like phishing or vishing.
- **Benefits:** "Provides an in-depth analysis of vulnerabilities and overall organisational security" and offers "logical and realistic recommendations tailored to the target organisation." It can also "illustrate a real-life attack chain that an attacker could utilize to access an organization's environment."
- **Limitations:** "Cost significantly more time and money" and "Requires a more in-depth security knowledge."

#### 2.3. Vulnerability Assessments vs. Penetration Tests

While both aim to improve security, they are distinct:

FeatureVulnerability AssessmentPenetration Test**Methodology**Looks for vulnerabilities against standards (checklist), validates critical findings.Simulates cyberattacks to see if and how a network can be penetrated, includes manual & automated techniques.**Exploitation**Minimal to no manual exploitation.Actively exploits vulnerabilities to gain access, escalate privileges, etc.**Cost/Expertise**Cost-effective, requires less specialized skill.Significantly more time and money, requires in-depth security knowledge.**Output**Identification of commonly known issues, generic recommendations.In-depth analysis, tailored, realistic recommendations, shows attack chains.**Maturity Level**Appropriate for all, especially lower maturity.Appropriate for medium to high security maturity, after successful VAs.Organizations can perform both in the same year, as they "can complement each other." Regular internal vulnerability scans are crucial even for organizations receiving annual or semi-annual penetration tests.

### 3. Other Security Assessment Types

Beyond vulnerability assessments and penetration tests, organizations can utilize:

#### 3.1. Security Audits

- **Purpose:** "Typically requirements from outside the organization," mandated by government agencies or industry associations "to assure that an organization is compliant with specific security regulations."
- **Example:** PCI DSS compliance for entities handling credit card data, where non-compliance can lead to fines and inability to accept payments.
- **Organizational Responsibility:** Organizations must perform vulnerability assessments proactively to ensure compliance before a "surprise security audit."

#### 3.2. Bug Bounties

- **Purpose:** Invite "members of the general public... to find security vulnerabilities in their applications."
- **Appropriateness:** Suited for "Larger companies with large customer bases and high security maturity" who can manage bug reports and endure external scrutiny. Payments can range from "a few hundred dollars to hundreds of thousands of dollars."

#### 3.3. Red Team Assessment

- **Purpose:** An "evasive black box pentesting, simulating all kinds of cyber attacks from the perspective of an external threat actor." These assessments typically have a specific "end goal (i.e., reaching a critical server or database, etc.)"
- **Reporting:** Assessors "only report the vulnerabilities that led to the completion of the goal, not as many vulnerabilities as possible as with a penetration test."
- **Internal Red Teams:** Perform "more targeted penetration tests with an insider's knowledge of its network" and should engage in continuous campaigns based on new threats or specific vulnerability types.
- **Relationship to Pentesting:** A red team consists of "offensive security professionals who have considerable experience with penetration testing."

#### 3.4. Purple Team Assessment

- **Purpose:** A collaborative approach where "offensive and defensive security specialists work together with a common goal, to improve the security of their network."
- **Collaboration:** Red teams identify problems, and blue teams (defensive specialists in SOCs or CSIRTs) learn and fix them. The blue team is "involved at every step" and may "even play a role in designing campaigns."

### 4. Key Cybersecurity Terminology

A clear understanding of fundamental terms is essential for effective cybersecurity:

- **Vulnerability:** "A weakness or bug in an organization's environment, including applications, networks, and infrastructure, that opens up the possibility of threats from external actors." They are often registered in MITRE's Common Vulnerability Exposure (CVE) database and assigned a Common Vulnerability Scoring System (CVSS) score (0-10) based on factors like attack vector, complexity, and impact on confidentiality, integrity, and availability.
- **Threat:** "A process that amplifies the potential of an adverse event, such as a threat actor exploiting a vulnerability." The likelihood of exploitation drives threat concerns.
- **Exploit:** "Any code or resources that can be used to take advantage of an asset's weakness." Many are publicly available on platforms like Exploit-db or GitHub.
- **Risk:** "The possibility of assets or data being harmed or destroyed by threat actors." As per ISO 31000, "Risk is the effect of uncertainty on objectives."
- **Relationship:** "Threat plus Vulnerability equals Risk." A risk matrix (Likelihood x Impact) helps prioritize remediation efforts, where "a vulnerability with a high likelihood of being exploited and the highest impact on an organization would represent the highest risk and would want to be prioritized for remediation."

### 5. Asset Management

Effective cybersecurity begins with thorough asset management. "If you want to protect something, you must first know what you are protecting!"

- **Asset Inventory:** A critical component of vulnerability management, it must include "information technology, operational technology, physical, software, mobile, and development assets." This inventory should be "thorough and complete," documenting all data storage (on-premises, cloud, SaaS applications), applications, and networking devices.
- **Dynamic Nature:** Organizations frequently add or remove assets, and these changes "must be thoroughly noted in the data asset inventory" to ensure continuous protection.

### 6. Assessment Standards

Adherence to specific standards is crucial for assessments to be "accredited and accepted by governments and legal authorities." These standards enhance efficiency and reduce the likelihood of attacks.

#### 6.1. Compliance Standards

Organizations must adhere to regulatory compliance bodies' information security standards to maintain accreditation.

- **PCI DSS (Payment Card Industry Data Security Standard):** Mandates requirements for organizations handling credit card data, including internal and external scanning and proper segmentation of Cardholder Data Environments (CDE). Goals include: "Build and maintain a secure network, protect cardholder data, maintain a vulnerability management program, implement strong access control measures, regularly monitor and test networks, maintain an information security policy."
- **HIPAA (Health Insurance Portability and Accountability Act):** Protects patient data, requiring risk assessment and vulnerability identification for accreditation.
- **FISMA (Federal Information Security Management Act):** Safeguards government operations, requiring documented vulnerability management programs for IT systems.
- **ISO 27001:** A worldwide standard for information security management, requiring quarterly external and internal scans and maintenance of an effective Information Security Management System.
- **Important Note:** "Although compliance is essential, it should not drive a vulnerability management program. Vulnerability management should consider the uniqueness of an environment and the associated risk appetite to an organization."

#### 6.2. Penetration Testing Standards

Pentests must be conducted with clear rules, guidelines, a defined scope, and a legal contract to minimize harm.

- **PTES (Penetration Testing Execution Standard):** Outlines the phases of a penetration test: "Pre-engagement Interactions, Intelligence Gathering, Threat Modeling, Vulnerability Analysis, Exploitation, Post Exploitation, Reporting."
- **OSSTMM (Open Source Security Testing Methodology Manual):** Provides guidelines across five channels: "Human Security, Physical Security, Wireless Communications, Telecommunications, Data Networks."
- **NIST (National Institute of Standards and Technology):** Offers a Penetration Testing Framework with phases: "Planning, Discovery, Attack, Reporting."
- **OWASP (Open Web Application Security Project):** The leading organization for defining testing standards and classifying risks to web applications, maintaining guides like the Web Security Testing Guide (WSTG) and Mobile Security Testing Guide (MSTG).