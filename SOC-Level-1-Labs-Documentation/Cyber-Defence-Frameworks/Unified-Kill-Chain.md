# 🔗 Unified Kill Chain (TryHackMe Walkthrough)

**Lab Title:** Unified Kill Chain  
**Category:** SOC Level 1 / Threat Intelligence & Attack Frameworks  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
To build a robust cybersecurity posture, security teams must fully understand the behaviors, objectives, and methodologies of advanced cyber threats (such as APT groups). The **Unified Kill Chain (UKC)** is an advanced framework that maps out the entire lifecycle of a cyber attack across 18 distinct phases. Unlike older linear frameworks (like Lockheed Martin's Kill Chain), the UKC is cyclic and designed to complement modern intelligence methodologies like MITRE ATT&CK.

---

## 🚀 Task 1: Introduction to Unified Kill Chain (UKC)

### 1. Core Purpose
* **Understanding Threats:** To analyze adversary behaviors, objectives, and methodologies from start to finish.
* **Incident Reconstruction:** Blue teams use the UKC framework to reconstruct security incidents: *How did the adversary get in (Inception), how did they spread (Propagation), where did they hide (Persistence), and what did they steal (Actions on Objectives)?*

### 2. Key Learning Objectives
* Understand why threat frameworks are vital for modern defense.
* Analyze adversary motivation, methods, and tactics using UKC.
* Explore the 18 distinct phases of the Unified Kill Chain.
* Learn how UKC integrates with and complements the MITRE ATT&CK matrix.

---

## 🧠 Task 3: What is "Threat Modelling"?

### 1. Definition & Core Steps
Threat modeling is a structured process used to identify, assess, and mitigate security risks within systems and applications. It relies on 4 core steps:
1. **Asset Identification:** Determining what needs protection (critical systems, applications, and sensitive data).
2. **Vulnerability Assessment:** Identifying weaknesses and potential exploitation vectors.
3. **Action Plan:** Creating a strategic plan to secure the environment.
4. **Preventive Policies:** Implementing security controls (such as a Secure Software Development Life Cycle - SDLC, or security awareness training).

### 2. Key Terminology & Frameworks
* **Asset:** The technical term for any piece of software or hardware in IT.
* **Popular Frameworks:** STRIDE, DREAD, and CVSS.

### ❓ Task Question & Answer
* **Question:** What is the technical term for a piece of software or hardware in IT (Information Technology)?  
  * **Answer:** `asset`

---

## 🗺️ Task 4: Introducing the Unified Kill Chain (UKC)

### 1. Background & Philosophy
* **Creator & Year:** Developed by Paul Pols in 2017 (and updated in 2022).
* **Purpose:** Designed to complement older frameworks (Lockheed Martin Kill Chain, MITRE ATT&CK) rather than compete with them.
* **The Cyclic Nature:** Unlike legacy frameworks that assume a strict, one-way linear attack, the UKC recognizes real-world adversary behavior where attackers constantly loop back through phases (e.g., performing fresh reconnaissance after a lateral pivot).

### ❓ Task Questions & Answers
* **Question 1:** In what year was the Unified Kill Chain framework released?  
  * **Answer:** `2017`
* **Question 2:** According to the Unified Kill Chain, how many phases are there to an attack?  
  * **Answer:** `18`
* **Question 3:** What is the name of the attack phase where an attacker employs techniques to evade detection?  
  * **Answer:** `Defense Evasion`
* **Question 4:** What is the name of the attack phase where an attacker employs techniques to remove data from a network?  
  * **Answer:** `Data Exfiltration` (or `Exfiltration`)
* **Question 5:** What is the name of the attack phase where an attacker achieves their objectives?  
  * **Answer:** `Objectives`

---

## 🛡️ Task 5: Initial Foothold & First 8 Phases of UKC

### 1. Overview
The initial phases focus on gaining access to a system or network environment by gathering intelligence, building infrastructure, and executing initial exploits.

### 2. First 8 Phases Covered
1. **Reconnaissance:** Gathering target information via active or passive methods.
2. **Weaponization:** Preparing and setting up attack infrastructure and payloads.
3. **Social Engineering:** Manipulating human behavior to compromise security (e.g., phishing).
4. **Exploitation:** Triggering system vulnerabilities for code execution.
5. **Persistence:** Establishing long-term access via backdoors or malicious services.
6. **Defense Evasion:** Techniques used to bypass security controls (AV, WAF, IDS).
7. **Command & Control (C2):** Setting up external communication channels to manage compromised hosts.
8. **Pivoting:** Using an initial compromised host as a staging node to attack isolated internal systems.

### ❓ Task Questions & Answers
* **Question 1:** What is an example of a tactic to gain a foothold using emails?  
  * **Answer:** `phishing`
* **Question 2:** Impersonating an employee to request a password reset is a form of what?  
  * **Answer:** `Social Engineering`
* **Question 3:** An adversary setting up the Command & Control server is what phase of the Unified Kill Chain?  
  * **Answer:** `Weaponization`
* **Question 4:** Exploiting a vulnerability present on a system is what phase of the Unified Kill Chain?  
  * **Answer:** `Exploitation`
* **Question 5:** Moving from one system to another is an example of?  
  * **Answer:** `Pivoting`
* **Question 6:** Leaving behind a malicious service that allows the adversary to log back into the target is what?  
  * **Answer:** `Persistence`

---

## 🌐 Task 6: Through (Network Propagation Phase)

### 1. Overview
Once an initial foothold is secured, the adversary begins expanding access, escalating privileges, and moving horizontally across the internal network.

### 2. Key Phases Covered
* **Pivoting:** Tunneling traffic through compromised nodes.
* **Discovery:** Mapping internal users, network shares, and configurations.
* **Privilege Escalation:** Elevating access to Administrative, SYSTEM, or Root levels.
* **Execution:** Running malicious code or scheduled tasks locally or remotely.
* **Credential Access:** Stealing hashes, passwords, or tickets (e.g., via *Mimikatz* or keyloggers).
* **Lateral Movement:** Jumping across internal systems using stolen credentials.

### ❓ Task Questions & Answers
* **Question 1:** As a SOC analyst, you pick up an alert pointing to failed logins from an administrator account. What phase of the Unified Kill Chain would an attacker be seeking to achieve?  
  * **Answer:** `Privilege Escalation`
* **Question 2:** Mimikatz, a known post-exploitation tool, was detected on the IT Manager's workstation. The Security logs show that the tool was attempting to dump OS and user secrets. Which Unified Kill Chain phase does this activity correspond to?  
  * **Answer:** `Credential Access`

---

## 🎯 Task 7: Out (Action on Objectives)

### 1. Overview
The final stage of the attack lifecycle where the adversary accesses critical assets to fulfill their strategic goals, directly targeting the **CIA Triad** (Confidentiality, Integrity, Availability).

### 2. Key Phases Covered
* **Collection:** Gathering valuable target data prior to theft.
* **Exfiltration:** Transferring compressed and encrypted data out of the network to external C2 servers.
* **Impact:** Disrupting, altering, or destroying systems and data (e.g., via Ransomware or sabotage).
* **Objectives:** Accomplishing the overarching socio-technical goal (financial extortion, espionage, or reputational damage).

### ❓ Task Questions & Answers
* **Question 1:** While monitoring the network as a SOC analyst, you observe a big traffic spike. Most of the network traffic is sent to an unknown, suspicious IP address. What Unified Kill Chain phase could describe this activity?  
  * **Answer:** `Exfiltration`
* **Question 2:** Personally identifiable information (PII) has been released to the public by an adversary. Your organisation is facing reputational losses and scrutiny for the breach. What part of the CIA triad would be affected by this action?  
  * **Answer:** `Confidentiality`

---

## 💻 Task 8: Practical Exercise

### Practical Simulation & Flag
By mapping adversary actions to their correct Unified Kill Chain phases on the interactive web application, the final objective flag is revealed.

* **Captured Flag:** `THM{UKC_SCENARIO}`

---

## 🔤 Key Concepts & Terminology Summary
`Unified Kill Chain` | `Threat Modelling` | `Asset` | `Reconnaissance` | `Weaponization` | `Social Engineering` | `Exploitation` | `Persistence` | `Defense Evasion` | `C2` | `Pivoting` | `Discovery` | `Privilege Escalation` | `Execution` | `Credential Access` | `Lateral Movement` | `Collection` | `Exfiltration` | `Impact` | `Objectives`
