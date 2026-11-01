# ⛓️ Cyber Kill Chain: Understanding the Enemy (TryHackMe Walkthrough)

**Lab Title:** Cyber Kill Chain  
**Category:** SOC Level 1 / Threat Intelligence & Security Frameworks  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
The Cyber Kill Chain framework, originally developed by Lockheed Martin in 2011, is a strategic model used to identify and prevent network intrusions. It details the sequential phases an adversary (or Advanced Persistent Threat - APT) must execute to successfully compromise a target. Understanding this framework allows Blue Teams and SOC analysts to map out attacker methodologies, evaluate security controls, and interrupt the kill chain before critical objectives are reached.

---

## 🚀 Task 1: Introduction to Cyber Kill Chain

### 1. What is the Cyber Kill Chain?
* **Definition:** A strategic framework defining the step-by-step progression of a cyber attack.
* **Core Philosophy:** If a defender can detect and disrupt an attacker at *any* early stage of the chain (**"Break the Chain"**), the intrusion fails, and the organization is protected.

### 2. Two Key Benefits for SOC Analysts
1. **Adversary Insight:** Helps defenders understand attacker goals, tactics, and procedures (TTPs).
2. **Defensive Gap Analysis:** Identifies missing security controls and monitoring gaps across the enterprise architecture.

---

## 🕵️‍♂️ Task 2: Reconnaissance

### 1. Overview
Reconnaissance is the planning, research, and identification phase where an adversary gathers intelligence on the target organization’s infrastructure, technology stack, and employees before launching an attack.

### 2. The Two Types of Reconnaissance
* **Passive Reconnaissance:** Involves gathering information **without** direct interaction with the target's systems (e.g., WHOIS lookups, social media analysis, OSINT Framework queries). Highly safe for the attacker and extremely difficult for SOC teams to detect.
* **Active Reconnaissance:** Involves direct engagement with the target network (e.g., port scanning, ping sweeps, banner grabbing). Highly visible and easily flagged by perimeter security devices like Firewalls and IDS/IPS.

### 3. Key OSINT Tools
* **OSINT Framework:** A web-based directory/interface organizing open-source intelligence tools and resources by category.
* **Email Harvesting:** The systematic collection of employee email addresses from public sources to facilitate downstream phishing attacks. *(Tools: `theHarvester`, `Hunter.io`)*

### ❓ Task 2 Questions & Answers
* **Question 1:** What is the name of the Intel Gathering Tool that is a web-based interface to the common tools and resources for open-source intelligence?  
  * **Answer:** `OSINT Framework`
* **Question 2:** What is the definition for the email gathering process during the stage of reconnaissance?  
  * **Answer:** `Email harvesting`

---

## ☣️ Task 3: Weaponization

### 1. Overview
Weaponization pairs the intelligence gathered during reconnaissance with malicious code to create a deliverable payload. The attack has not yet been launched against the target; rather, the weapon is being crafted.

### 2. Key Terminology & Tactics
* **Malware:** Software designed to disrupt, damage, or gain unauthorized access to a system.
* **Exploit:** Code designed to take advantage of a specific software vulnerability or system flaw.
* **Payload:** The malicious code executed on the target system once the exploit succeeds.
* **Malicious Macros:** Automated scripts (written in VBA) embedded inside Microsoft Office documents that execute malicious commands upon user activation.

### ❓ Task 3 Questions & Answers
* **Question: What is the term for automated scripts embedded in Microsoft Office documents that can be used to perform tasks or exploited by attackers for malicious purposes?**  
  * **Answer:** `macros` (or `macro`)

---

## 🚚 Task 4: Delivery

### 1. Overview
Delivery is the phase where the adversary transmits the weaponized payload to the targeted environment or user.

### 2. Primary Delivery Vectors
1. **Phishing Email:** The most common vector; delivering payloads via malicious email attachments (e.g., malicious Excel/Word files) or hyperlinks.
2. **USB Drops:** Physical social engineering where infected thumb drives are deliberately left in public spaces near the target organization to exploit human curiosity.
3. **Watering Hole Attack:** Compromising a legitimate, frequently visited third-party website utilized by the target group to silently infect users via drive-by downloads.

### ❓ Task 4 Questions & Answers
* **Question: What do you call an attack targeting a specific group by infecting their frequently visited website?**  
  * **Answer:** `Watering hole attack` (or `watering hole`)

---

## 💥 Task 5: Exploitation

### 1. Overview
Exploitation triggers the malicious payload on the target system, leveraging software, hardware, or human vulnerabilities to execute arbitrary code.

### 2. Key Concepts
* **Zero-Day:** An exploit targeting a software vulnerability completely unknown to the vendor and public, lacking any official patch or defense.
* **Known CVEs:** Exploitation of publicly documented vulnerabilities that administrators failed to patch promptly.
* **Lateral Movement & Privilege Escalation:** Post-exploitation activities where an attacker expands access rights and pivots across internal network nodes.

### ❓ Task 5 Questions & Answers
* **Question: What is the term for a cyber attack that exploits a software vulnerability that is unknown by software vendors?**  
  * **Answer:** `zero-day` (or `zero-day exploit`)

---

## 💾 Task 6: Installation

### 1. Overview
Installation establishes **Persistence** on the compromised system, ensuring that the attacker retains access even across reboots, credential updates, or initial remediation attempts.

### 2. Key Techniques & Anti-Forensics
* **Web Shell:** A malicious script (`.php`, `.asp`, `.jsp`) uploaded to a web server, providing remote web-based administrative command execution.
* **Registry Run Keys & Startup Folders:** Modifying Windows system registries or startup directories so payloads execute automatically upon user login.
* **Timestomping:** An anti-forensics technique where an attacker alters file timestamps (Modified, Accessed, Created - MACB) to mimic legitimate operating system files, confounding Blue Team investigators.

### ❓ Task 6 Questions & Answers
* **Question 1:** What technique is used to modify file time attributes to hide new or changes to existing files?  
  * **Answer:** `timestomping`
* **Question 2:** What malicious script can be planted by an attacker on the web server to maintain access to the compromised system and enables the web server to be accessed remotely?  
  * **Answer:** `web shell`

---

## 🕹️ Task 7: Command & Control (C2 / C&C)

### 1. Overview
Command & Control establishes an outbound communication channel, allowing the attacker to remotely manage, control, and exfiltrate data from the compromised infrastructure.

### 2. Key Concepts & Techniques
* **C2 Beaconing:** The malware periodically checks in with the external adversary server at regular time intervals to request instructions.
* **DNS Tunneling:** A sophisticated C2 technique packing malicious command traffic and stolen data inside standard DNS queries. Because outbound DNS traffic is rarely blocked, this effectively bypasses standard egress filters.
* **Fallback Channels:** Secondary, backup communication protocols or servers hardcoded into malware (e.g., shifting from standard HTTP to DNS or leveraging public social media APIs) to ensure continuous persistence if the primary C2 channel is detected and blocked by a SOC team.

### ❓ Task 7 Questions & Answers
* **Question: What is the C2 communication where the victim makes regular DNS requests to a DNS server and domain which belong to an attacker?**  
  * **Answer:** `DNS Tunneling`

---

## 🏁 Task 8: Actions on Objectives

### 1. Overview
The final stage of the Cyber Kill Chain, where the adversary executes their primary strategic goals within the compromised network.

### 2. Core Activities
* **Data Exfiltration:** Aggregating and secretly transferring sensitive intellectual property or customer records out of the environment.
* **Defense Sabotage:** Deleting Volume Shadow Copies (VSS) or system backups to prevent system recovery.
* **Ransomware Deployment:** Encrypting organizational infrastructure to demand financial extortions.

### ❓ Task 8 Questions & Answers
* **Question: What technology is included in Microsoft Windows that can create backup copies or snapshots of files or volumes on the computer, even when they are in use?**  
  * **Answer:** `Shadow Copy` (also known as Volume Shadow Copy Service or VSS)

---

## 🎯 Task 9: Case Study - The Target Breach (2013)

### 1. Attack Breakdown Mapped to the Kill Chain
* **Recon & Delivery:** Attackers compromised a third-party HVAC vendor and utilized spearphishing emails to breach the corporate network perimeter.
* **Exploitation:** Leveraged administrative PowerShell scripts for malicious execution.
* **Installation:** Executed Dynamic Linker Hijacking to install Point-of-Sale (POS) RAM-scraping malware on checkout registers.
* **Command & Control (C2):** Maintained communications via configured fallback channels.
* **Actions on Objectives:** Harvested and exfiltrated payment card records belonging to 40 million customers.

### 🔑 Task 9 Walkthrough & Answers
* **Flag Answer:**  
  ```text
  THM{7HR347_1N73L_12_4w35om3}
