# 🛡️ Introduction to SIEM (TryHackMe Walkthrough)

**Lab Title:** Introduction to SIEM (Security Information and Event Management)  
**Category:** SOC Level 1 / Log Management & Threat Detection  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
Enterprise environments generate millions of events every second across hundreds of computers, servers, and network devices. Investigating security breaches manually across distributed systems is impossible. **SIEM (Security Information and Event Management)** acts as the "brain" of the SOC, centralizing log collection, normalizing disparate data formats, correlating cross-system activities, and generating real-time alerts. This lab explores log sources, ingestion methods, the SIEM workflow, and SOC alert triage.

---

## 🪵 Task 1 & 2: Log Sources (Host-Centric vs. Network-Centric)

### 1. Understanding Log Classifications
* **Host-Centric Logs (Local):** Events occurring inside an operating system (e.g., user authentications, process executions, Registry modifications).
* **Network-Centric Logs (Traffic):** Events capturing device communication across the network (e.g., web traffic, VPN sessions, firewall logs).

### ❓ Key Concepts & Questions
* **Question:** Is Registry-related activity host-centric or network-centric?  
  * **Answer:** `host-centric`
* **Question:** Is VPN-related activity host-centric or network-centric?  
  * **Answer:** `network-centric`

---

## 🧠 Task 3: The 5 Superpowers of a SIEM

### 1. Core Functions of a SIEM Solution
1. **Centralized Log Collection:** Automatically aggregates logs from thousands of endpoints using lightweight **Agents / Forwarders** or **Syslog**.
2. **Normalization & Parsing:** Translates chaotic, vendor-specific raw logs into a unified, standardized schema so analysts can read them seamlessly.
3. **Correlation:** Linking seemingly unrelated low-severity events (e.g., unusual login + PowerShell execution + data staging) into a unified attack narrative.
4. **Real-time Alerting:** Continuously scanning logs against detection rules to trigger instant notifications for the SOC team.
5. **Dashboards & Visualization:** Providing actionable insights through graphs, charts, and metrics.

---

## 📥 Task 4: Log Sources & Ingestion Methods

### 1. Key Log File Paths in Linux
* `/var/log/httpd` or `/var/log/apache` ➡️ Web server access and error logs.
* `/var/log/auth.log` or `/var/log/secure` ➡️ Authentication and SSH login attempts.
* `/var/log/cron` ➡️ Automated background task execution logs.
* `/var/log/kern` ➡️ Operating system kernel-level events.

### 2. Windows & Ingestion Mechanisms
* **Windows Event Viewer:** Centralized logging using unique **Event IDs** (e.g., `4624` for successful logon, `4625` for failed logon).
* **Ingestion Methods:** Agents/Forwarders, Syslog, Manual Uploads, and Port-Forwarding.

### ❓ Key Questions & Answers
* **Question:** In which location within a Linux environment are HTTP logs stored?  
  * **Answer:** `/var/log/httpd` *(or `/var/log/apache`)*

---

## 🚨 Task 5 & 6: Alerting Process and Analysis (Triaging)

### 1. Key Windows Event IDs for Detection
* **Event ID 104:** Log cleared (indicates an adversary attempting to erase forensic tracks).
* **Event ID 4688:** A new process has been created (e.g., tracking reconnaissance commands like `whoami`).

### 2. Alert Classification in the SOC
* **True Positive (TP):** A genuine security incident. *Action:* Isolate infected host, block malicious IPs.
* **False Positive (FP):** Benign activity triggering an alert. *Action:* Perform **Rule Tuning** to prevent future noise.

### ❓ Task 5 & 6 Questions & Answers
* **Question:** Which Event ID is generated when event logs are removed?  
  * **Answer:** `104`
* **Question:** What type of alert may require tuning?  
  * **Answer:** `False Positive`

---

## 🔤 Key Concepts & Terminology Summary
`SIEM` | `Normalization` | `Parsing` | `Correlation` | `Agent / Forwarder` | `Event Viewer` | `True Positive` | `False Positive` | `Rule Tuning`
