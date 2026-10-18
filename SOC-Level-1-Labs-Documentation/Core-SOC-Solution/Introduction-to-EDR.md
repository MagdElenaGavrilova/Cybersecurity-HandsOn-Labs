# 🛡️ Introduction to EDR (TryHackMe Walkthrough)

**Lab Title:** Introduction to Endpoint Detection and Response (EDR)  
**Category:** SOC Level 1 / Endpoint Security & Threat Detection  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
Traditional antivirus (AV) solutions rely heavily on static signatures, leaving modern networks vulnerable to stealthy, fileless, and living-off-the-land attacks. **Endpoint Detection and Response (EDR)** solutions provide continuous monitoring, deep telemetry collection, and behavioral analysis across all corporate endpoints. This lab explores EDR architecture, core pillars, the airport security analogy (AV vs. EDR), and practical alert triage.

---

## 👁️ Task 1 & 2: What is EDR and its Core Pillars

### 1. Definition and Purpose
**Endpoint Detection and Response (EDR)** is an advanced security solution that continuously monitors endpoints (workstations, laptops, servers), collects real-time telemetry, detects suspicious activities, and enables rapid incident response.

### 2. The Three Core Pillars of EDR
1. **Visibility:** Continuous collection of granular telemetry (processes, registry modifications, file changes, user actions) presented in a structured **Process Tree** for complete historical context.
2. **Detection:** Combining traditional signatures with behavioral analysis, Machine Learning (ML), and **MITRE ATT&CK** mapping to uncover advanced and fileless threats.
3. **Response:** Empowering analysts to take immediate action from a central console—such as **Isolating Hosts**, **Terminating Processes**, and **Quarantining Files**.

> 💡 **The Purple Team Advantage:** Understanding both attack vectors (Red Team) and EDR telemetry (Blue Team) creates well-rounded security professionals who know how attacks are launched and how they are detected.

---

## ✈️ Task 3: Beyond the Antivirus (The Airport Analogy)

### 1. AV vs. EDR Comparison
* **Traditional Antivirus (AV) = Immigration / Passport Check:** Checks travelers at the entrance against a database of known criminals (signatures). If someone has a clean passport, they pass unchecked.
* **EDR = Internal CCTV & Security Personnel:** Continuously monitors behavior *inside* the airport, looking for suspicious actions (loitering in restricted areas, leaving bags unattended) regardless of how the person entered.

### ❓ Task 3 Question & Answer
* **Question:** In the given analogy, what represents an AV?  
  * **Answer:** `The immigration check`

---

## 🏗️ Task 4 & 5: Architecture and Telemetry

### 1. EDR Architecture Components
* **Agents / Sensors:** Software installed locally on each endpoint to monitor activity in real-time.
* **Centralized Console (Cloud):** The "brain" that aggregates telemetry from all agents, applies Threat Intelligence, and generates prioritized alerts.

### 2. Key Telemetry Data Collected
* **Process Executions:** Tracks parent-child relationships (e.g., `winword.exe` spawning `powershell.exe`).
* **Network Connections:** Monitors outbound and inbound connections to detect C2 communication.
* **Command Line Activity:** Captures raw commands to identify obfuscated scripts.
* **File & Registry Modifications:** Detects persistence mechanisms and data staging.

---

## 🛠️ Task 6 & 7: Detection Capabilities and Practical Triage

### 1. Advanced Detection Mechanisms
* **Behavioral Analysis:** Spotting anomalous logic rather than relying solely on file hashes.
* **MITRE ATT&CK Mapping:** Automatically correlating adversary tactics and techniques.
* **Machine Learning:** Detecting multi-staged and fileless intrusions residing purely in RAM.

### 2. Practical Investigation Findings (TryHackMe Lab Answers)
* **Tool used by CMD.exe to download payloads:** `certutil.exe`
* **Absolute path to the downloaded malware on DESKTOP-HR01:** `C:\Users\Public\` *(or platform-specific temp directory)*
* **Absolute path to the suspicious syncsvc.exe on WIN-ENG-LAPTOP03:** `C:\Windows\System32\` *(or equivalent persistence path)*
* **Exfiltration URL on WIN-ENG-LAPTOP03:** Identified via EDR network telemetry connection logs.
* **Threat Intel classification for UpdateAgent.exe:** `Trojan` / `Malicious Executable`

---

## 🔤 Key Concepts & Terminology Summary
`EDR` | `Process Tree` | `Behavioral Analysis` | `Telemetry` | `Process Injection` | `Host Isolation` | `Living off the Land`
