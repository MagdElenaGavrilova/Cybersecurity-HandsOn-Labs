# 🛡️ MITRE: Exploring Cybersecurity Resources (TryHackMe Walkthrough)

**Lab Title:** MITRE: Exploring Cybersecurity Resources  
**Category:** SOC Level 1 / Threat Intelligence & Security Frameworks  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
MITRE is a not-for-profit organization that manages research and development across various domains, most notably cybersecurity. The resources developed by MITRE serve as the backbone of modern security operations, providing a standardized taxonomy and shared knowledge base for both offensive (Red Teams) and defensive (Blue Teams) operations. This walkthrough explores the MITRE ATT&CK® framework, the Cyber Analytics Repository (CAR), the D3FEND defensive framework, and advanced frameworks like ATLAS and AADAPT.

---

## 🧭 Task 1: Introduction to MITRE

### 1. What is MITRE?
* **Definition:** A not-for-profit organization managing research and development in cybersecurity, AI, healthcare, and aerospace systems.
* **Mission:** "To solve problems for a safer world."
* **Cybersecurity Impact:** MITRE provides foundational frameworks that enable security professionals to understand adversary behavior, build robust detection rules, and fortify defensive perimeters.

### 2. Learning Objectives
* Understand the purpose and structure of the **MITRE ATT&CK®** framework.
* Explore how security professionals apply ATT&CK in daily workflows.
* Utilize Cyber Threat Intelligence (CTI) and the ATT&CK Matrix for threat profiling.
* Discover auxiliary MITRE frameworks, including **CAR (Cyber Analytics Repository)** and **D3FEND**.

### 💡 Why MITRE is Revolutionary in Industry
1. **Common Taxonomy:** Before MITRE ATT&CK, various security vendors used proprietary names for identical malware families, threat groups, and attack phases. MITRE created a universal dictionary.
2. **Theory to Practice:** MITRE frameworks map directly into SIEM tools (such as Elastic and Splunk) to build precise detection rules (e.g., mapping `T1078 - Valid Accounts`).

---

## 🛡️ Task 2: MITRE ATT&CK® Framework

### 1. Structure and Evolution
* **Origins (2013):** Developed to document and categorize standard Tactics, Techniques, and Procedures (TTPs) utilized by Advanced Persistent Threat (APT) groups.
* **TTP Breakdown:**
  * **Tactic:** The adversary's goal or objective (The *Why*).
  * **Technique:** How the adversary achieves their goal (The *How*).
  * **Sub-technique:** A more specific implementation of a technique.
  * **Procedure:** The exact execution steps or implementation details.
* **Matrix Scope:** Originally focused on Windows, the framework expanded to Enterprise (macOS, Linux, Cloud), Mobile, and Industrial Control Systems (ICS).

### ❓ Task 2 Questions & Answers
* **Question 1:** What Tactic does the Phishing technique belong to in the ATT&CK Matrix?  
  * **Answer:** `Initial Access` *(Phishing - T1566 is used by attackers to gain a foothold in the network).*
* **Question 2:** Which ID is associated with the Create Account technique?  
  * **Answer:** `T1136` *(Used for establishing persistence; sub-techniques include T1136.001 for Local accounts).*

---

## 👥 Task 3: MITRE ATT&CK in Operation

### How Different Teams Use ATT&CK
| Role / Team | Primary Goal | Application of ATT&CK |
| :--- | :--- | :--- |
| **CTI Teams** | Gather and analyze threat data | Mapping observed threat actor behaviors to ATT&CK TTPs to build actionable profiles |
| **SOC Analysts** | Investigate and triage security alerts | Correlating activities with tactics and techniques to provide rich context and prioritize incidents |
| **Detection Engineers** | Design and improve detection systems | Mapping SIEM/EDR rules to ATT&CK coverage matrices to ensure robust detection |
| **Incident Responders** | Handle and remediate active breaches | Mapping incident timelines to tactics and techniques for clear attack visualization |
| **Red & Purple Teams** | Simulate adversary behavior | Designing adversary emulation plans aligned with real-world groups |

### Case Study: Mustang Panda (Group ID: G0129)
* **Origin/Focus:** A prominent APT group targeting government entities, NGOs, and non-profits.
* **Mapped TTPs:** Uses Phishing for *Initial Access*, Scheduled Tasks for *Persistence*, File Obfuscation for *Defense Evasion*, and Ingress Tool Transfer for *Command and Control (C2)*.

### ❓ Task 3 Questions & Answers
* **Question 1:** In which country is Mustang Panda based?  
  * **Answer:** `China`
* **Question 2:** Which ATT&CK technique ID maps to Mustang Panda’s Reconnaissance tactics?  
  * **Answer:** `T1598` *(Active Scanning)*
* **Question 3:** Which software is Mustang Panda known to use for Access Token Manipulation?  
  * **Answer:** `Cobalt Strike`

---

## 🔍 Task 4: ATT&CK for Threat Intelligence (Practical Exercise)

### Scenario Simulation
Acting as a security analyst in the aviation sector migrating infrastructure to the Cloud, we utilize MITRE ATT&CK and the Navigator tool to research aviation threats and identify defensive gaps.

### ❓ Task 4 Questions & Answers
* **Question 1:** Which APT group has targeted the aviation sector and has been active since at least 2013?  
  * **Answer:** `APT33` (or `G0064`)
* **Question 2:** Which ATT&CK sub-technique used by this group is a key area of concern for companies using Office 365?  
  * **Answer:** `Cloud Accounts` (or `T1078.004`)
* **Question 3:** According to ATT&CK, what tool is linked to the APT group and the sub-technique you identified?  
  * **Answer:** `Ruler` *(Software ID: S0358)*
* **Question 4:** Which mitigation strategy advises removing inactive or unused accounts to reduce exposure to this sub-technique?  
  * **Answer:** `User Account Management` *(M1018)*
* **Question 5:** What Detection Strategy ID would you implement to detect abused or compromised cloud accounts?  
  * **Answer:** `DET0546`

---

## 📊 Task 5: Cyber Analytics Repository (CAR)

### 1. What is CAR?
* **Definition:** A knowledge base of validated, operational detection analytics developed by MITRE, paired with the ATT&CK framework.
* **Components:** Contains a data model defined via **pseudocode**, direct implementations for SIEM tools (Splunk, EQL, LogPoint), and occasional unit tests.
* **Value for Defenders:** Translates abstract ATT&CK TTPs into functional detection logic and queries that can be deployed directly into organizational SIEMs.

### ❓ Task 5 Questions & Answers
* **Question 1:** Which ATT&CK Tactic is associated with `CAR-2019-07-001`?  
  * **Answer:** `Defense Evasion`
* **Question 2:** What is the Analytic Type for Access Permission Modification?  
  * **Answer:** `Situational Awareness`

---

## 🛡️ Task 6: MITRE D3FEND Framework

### 1. Countering ATT&CK
* **MITRE ATT&CK:** Focuses on the offensive side (The Adversary's Move / How attacks happen).
* **MITRE D3FEND:** Focuses on the defensive side (The Defender's Countermeasure / How to stop attacks).
* **Definition:** D3FEND (Detection, Denial, and Disruption Framework Empowering Network Defense) structures defensive techniques and establishes a common taxonomy for security controls.
* **Digital Artifacts:** D3FEND maps defenses to underlying digital artifacts (processes, files, network traffic), explaining how countermeasures operate and what telemetry must be collected.

### ❓ Task 6 Questions & Answers
* **Question 1:** Which sub-technique of User Behavior Analysis would you use to analyze the geolocation data of user logon attempts?  
  * **Answer:** `User Geolocation Logon Pattern Analysis`
* **Question 2:** Which digital artifact does this sub-technique rely on analyzing?  
  * **Answer:** `Network Traffic`

---

## 🧪 Task 7: Other MITRE Projects (Emulation, Caldera, AADAPT & ATLAS)

### 1. Specialized Frameworks
* **Emulation Plans:** Maintained by the Center for Threat Informed Defense (CTID), providing step-by-step guides to replicate real-world APT attacks.
* **Caldera:** An automated adversary emulation system that leverages ATT&CK to test and validate organizational defenses in controlled environments.
* **AADAPT (Adversarial Actions in Digital Asset Payment Technologies):** A specialized framework targeting threats against blockchain networks, smart contracts, and crypto wallets.
* **ATLAS (Adversarial Threat Landscape for Artificial-Intelligence Systems):** A matrix and knowledge base focused entirely on security threats, vulnerabilities, and mitigations targeting Artificial Intelligence and Machine Learning systems.

### ❓ Task 7 Questions & Answers
* **Question 1:** What technique ID is associated with Scrape Blockchain Data in the AADAPT framework?  
  * **Answer:** `ADT3025`
* **Question 2:** Which tactic does LLM Prompt Obfuscation belong to in the ATLAS framework?  
  * **Answer:** `Defense Evasion`

---

## 🏁 Task 8: Conclusion

### Key Takeaways
* **MITRE ATT&CK®:** Universal matrix for adversary TTPs.
* **CAR:** Practical pseudocode and SIEM queries for detection engineering.
* **MITRE D3FEND:** Defensive countermeasure mapping.
* **ATLAS & AADAPT:** Advanced threat landscapes for AI and Blockchain technologies.
* **Caldera & CTID:** Automated and manual adversary emulation frameworks.

> 💡 *“The more information we have as security professionals, the better equipped we are to fight back!”*

---

## 🔤 Key Concepts & Terminology Summary
`MITRE ATT&CK` | `Tactic` | `Technique` | `Sub-technique` | `CAR` | `D3FEND` | `ATLAS` | `AADAPT` | `Caldera` | `TTPs`
