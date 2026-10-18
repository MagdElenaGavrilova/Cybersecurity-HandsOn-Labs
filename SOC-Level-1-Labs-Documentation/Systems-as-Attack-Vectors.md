# 🛡️ Systems as Attack Vectors (TryHackMe Walkthrough)

**Lab Title:** Systems as Attack Vectors  
**Category:** SOC Level 1 / Threat Intelligence & System Security  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
While social engineering targets human behavior, **systems as attack vectors** focus on technical flaws, software vulnerabilities, and structural misconfigurations. Attackers continuously scan the internet for unpatched servers, weak default credentials, and exposed cloud assets to compromise enterprise environments without human intervention. This lab explores system-level attacks, zero-days, misconfigurations, and how SOC analysts collaborate with IT teams to mitigate these risks.

---

## 🏗️ Task 2: Definition of Systems

### 1. The Castle Analogy (Continued)
If the previous room focused on guarding the castle gate against social engineering, what happens if the main gate's lock is cheap and broken? An attacker can slip directly into the fortress unseen. In cybersecurity, attackers frequently target unsecure systems directly, bypassing user interaction entirely.

### 2. What is a System and Why is it a Target?
A **system** can be a physical server, an endpoint workstation (laptop/desktop), or a cloud platform (e.g., Microsoft 365) storing critical data, emails, or applications.
* Compromising a single user's email inbox yields access to one account.
* Compromising an entire mail server grants control over thousands of accounts simultaneously.

### ❓ Task 2 Questions & Answers
* **Question:** Can cyber attacks happen without victim intervention?  
  * **Answer:** `Yea`
* **Question:** Can a breach of just a single system lead to disastrous consequences?  
  * **Answer:** `Yea`

> 🛡️ **SOC Analyst Insight — Hardening & Vulnerability Management:**  
> System defense in a SOC relies on two core pillars: **Vulnerability Management** (regular scanning and patching) and **Hardening** (securing configurations, closing unnecessary ports, and enforcing least privilege). While humans can be tricked, an unconfigured or unpatched server is an open door that automated scanner tools find in seconds.

---

## ⚡ Task 3: Attacks on Systems

### 1. Attacker Objectives
Upon breaching a target system, attackers pursue various malicious goals:
* **Data Exfiltration:** Stealing sensitive corporate data or intellectual property.
* **Ransomware Deployment:** Encrypting critical assets and demanding financial extortion.
* **Data Wiping:** Irreversible destruction of information to sabotage operations.

### 2. Three Main Attack Vectors Against Systems
1. **Human-Led Attacks:** Users inadvertently initiate system compromise via infected USBs, pirated software, or credential reuse. *(Statistic: 81% of breaches involve compromised credentials).*
2. **Software Vulnerabilities:** Flaws in code exploited by attackers. Over 40,000 vulnerabilities were published in recent years, with hundreds actively exploited in the wild.
3. **Supply Chain Attacks:** Compromising a trusted third-party library or software update (e.g., SolarWinds, 3CX, or TryHackMe's Lottie Player incident) to infect all downstream users simultaneously.

### ❓ Task 3 Questions & Answers
* **Question:** What is the term for a security flaw that can be exploited to breach a system?  
  * **Answer:** `vulnerability`
* **Question:** What is the name of the attack when malware comes from a trusted app or library?  
  * **Answer:** `supply chain attack`

> 🛡️ **SOC Analyst Insight — Software Bill of Materials (SBOM):**  
> To track supply chain risks, SOC and security teams utilize an **SBOM (Software Bill of Materials)**—an inventory of all third-party libraries used in corporate apps. When critical vulnerabilities emerge (like Log4j), analysts instantly identify affected assets and trigger emergency responses.

---

## 🐛 Task 4: Vulnerabilities & Zero-Days

### 1. Understanding Software Vulnerabilities
Software bugs often remain undiscovered for years (e.g., the Linux *Shellshock* flaw existed since 1992 before discovery in 2014).
* **Zero-Day Vulnerabilities:** The most dangerous scenario where attackers discover a flaw before the vendor or defenders do. No official patch exists during this window, forcing SOC analysts to rely on behavioral detection.

### 2. The CVE Life Cycle & Response
Public vulnerabilities receive a **CVE (Common Vulnerabilities and Exposures)** ID, initiating a race between attackers developing exploits and defenders applying patches.
* **Interim Mitigation Strategies:** While waiting for patches, SOC analysts deploy workarounds, restrict network access to trusted IPs, or configure WAF/IPS rules to block exploit signatures.

### ❓ Task 4 Questions & Answers
* **Question:** What is the CVE for the critical SharePoint vulnerability dubbed "ToolShell"?  
  * **Answer:** `CVE-2025-53770`
* **Question:** How would you respond to a detected vulnerability on your system?  
  * **Answer:** `patch`

> 💡 **Zero-Day vs. Zero-Trust Summary:**  
> * **Zero-Day:** A threat/flaw unknown to the vendor with no patch available.  
> * **Zero-Trust:** A defense architecture built on the principle *"Never trust, always verify"* regardless of network location.

---

## ⚙️ Task 5: Misconfigurations & Botnets

### 1. Misconfigurations vs. Vulnerabilities
Unlike code bugs, **misconfigurations** are human errors during setup (e.g., leaving default passwords like `admin` or setting cloud storage buckets to public). 
* Real-world impacts include exposed databases containing millions of user records and unsecured IoT devices hijacked into botnets.

### 2. Botnets and IoT Threats
* **Bot:** Any compromised device (PC, router, smart fridge/IoT) controlled remotely by a threat actor (Zombie).
* **Botnet:** A network of thousands or millions of compromised devices orchestrated by a Command and Control (C2) server. Used for massive **DDoS attacks**, spam campaigns, or crypto-mining.

### ❓ Task 5 Questions & Answers
* **Question:** Can a system patch or software update fix the misconfigurations?  
  * **Answer:** `Nay`
* **Question:** Which activity involves an authorized cyber attack to detect the misconfigurations?  
  * **Answer:** `penetration testing`

> 🛡️ **SOC Analyst Insight — Cloud Security Posture Management (CSPM):**  
> To combat cloud misconfigurations, organizations deploy CSPM tools (such as Nessus or Cloud Security scanners) that automatically alert teams if an administrator accidentally exposes an AWS S3 bucket to the public internet.

---

## 🛠️ Task 6 & 7: Practice & Conclusion

### Summary of System Protections
| Defensive Control | Description |
| :--- | :--- |
| **Patch Management** | Regular tracking and updating of vulnerable systems. |
| **IT Training** | Educating IT staff to prevent default configurations and loose permissions. |
| **Network Protection** | Restricting system access strictly to authorized IPs and trusted users. |
| **Antivirus / EDR** | Endpoint detection software to block and alert on malicious payloads. |

> 🛡️ **SOC Analyst Insight — Remediation Planning:**  
> The bridge between the SOC and IT administrators is the **Remediation Plan**. The SOC identifies and prioritizes risks based on severity, handing IT a clear, actionable roadmap of required patches and configuration hardenings.

---

## 📚 Recommended Threat Intelligence Resources
* **The DFIR Report:** Deep-dive incident response case studies.
* **CISA Known Exploited Vulnerabilities (KEV) Catalog:** Official tracking of actively exploited CVEs.
* **BleepingComputer:** Essential portal for supply chain and ransomware news.
* **CheckPoint Live Cyber Threat Map:** Real-time global attack visualization.

---

## 🔤 Key Concepts & Terminology Summary
`Vulnerability` | `Zero-Day` | `CVE` | `Misconfiguration` | `Botnet` | `Supply Chain Attack` | `Remediation Plan` | `Hardening`
