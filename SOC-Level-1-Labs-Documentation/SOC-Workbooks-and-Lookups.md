# 🛡️ SOC Workbooks and Lookups (TryHackMe Walkthrough)

**Lab Title:** SOC Workbooks and Lookups  
**Category:** SOC Level 1 / Alert Triage & Incident Response  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
During a night shift, a Security Operations Center (SOC) analyst receives numerous alerts. Determining whether an alert represents a genuine attack (True Positive) or routine business activity (False Positive) requires more than just looking at logs—it requires contextual corporate data. This lab explores the use of **Identity Inventories**, **Asset Inventories**, **Network Diagrams**, and structured **SOC Workbooks (Playbooks)** to simplify and streamline L1 alert triage.

---

## 🔍 Task 2: Identity & Asset Inventories (Contextual Triage)

### 1. The Scenario Analysis
* **Alert Details:** User `G.Baker` logged into server `HQ-FINFS-02`, downloaded `Financial Report US 2024.xlsx`, and sent it to `R.Lund`.
* **Is this an attack?** To answer this, an L1 analyst must check internal inventories:
  * **Identity Inventory (e.g., BambooHR / Active Directory):** Reveals that `G.Baker` is the Chief Financial Officer (Europe/UK) and `R.Lund` is a US Financial Adviser based in Texas.
  * **Asset Inventory:** Reveals that `HQ-FINFS-02` is a Windows Server 2022 located in the UK Datacenter dedicated to storing financial records.
* **Verdict:** The CFO downloading a financial report from the corporate file server and sharing it with their US Financial Adviser is completely normal, expected business activity.

### ❓ Task 2 Questions & Answers
* **Question:** Looking at the identity inventory, what is the role of R.Lund at the company?  
  * **Answer:** `US Financial Adviser`
* **Question:** Checking the asset inventory, what data does the HQ-FINFS-02 server store?  
  * **Answer:** `financial records`
* **Question:** Finally, does the file sharing from the scenario look legitimate and expected? (Yea/Nay)  
  * **Answer:** `Yea`

> 🛡️ **SOC Analyst Insight — Context is King:**  
> Raw logs without contextual inventories lead to alert fatigue. Integrating HR systems (like BambooHR) and IT asset databases directly into your SIEM/SOAR workflow allows analysts to instantly verify if an action aligns with a user's job role.

---

## 🗺️ Task 3: Network Diagrams & True vs. False Positives

### 1. Reconstructing an Attack Timeline via Network Diagrams
1. **08:00 UTC:** External IP `103.61.240.174` attempts brute-force login on firewall port `TCP/10443` (VPN gateway).
2. **08:23 UTC:** Successful brute-force results in an internal VPN assignment: `10.10.0.53`.
3. **08:25 UTC:** IP `10.10.0.53` begins scanning the Database Subnet (`172.16.15.0/24`) and later pivots to the Office Subnet (`172.16.23.0/24`).

> 💡 **Why Network Diagrams Matter:** IP addresses alone provide no operational meaning. A network diagram maps subnets and device functions, instantly revealing that port `10443` hosts the corporate VPN (`vpn.tryhackme.thm`) and that the attacker breached the perimeter.

### 2. Alert Classification: True Positive (TP) vs. False Positive (FP)
* **True Positive (TP):** The alert indicates a genuine security incident (e.g., unauthorized VPN access followed by internal network scanning).
* **False Positive (FP):** The alert is triggered by legitimate user or administrative activity that mimics suspicious behavior.

### ❓ Task 3 Questions & Answers
* **Question:** According to the network diagram, which service is exposed on the TCP/10443 port?  
  * **Answer:** `VPN`
* **Question:** Now, which subnet would the server behind 172.16.15.99 IP belong to?  
  * **Answer:** `Database Subnet`
* **Question:** Finally, does the scenario look like a True Positive (TP) or False Positive (FP)?  
  * **Answer:** `True Positive`

---

## 📖 Task 4: SOC Workbooks (Playbooks / Runbooks)

### 1. What is a SOC Workbook?
A SOC Workbook is a step-by-step procedural guide created by senior analysts (L2/L3) to help L1 analysts investigate specific types of alerts consistently and without missing critical details.

### 2. The Three Core Phases of a Workbook
1. **Enrichment:** Gathering context about the user, host, IP, and threat intelligence (e.g., VirusTotal, AbuseIPDB, Identity Inventory).
2. **Investigation:** Analyzing SIEM logs, process trees, and timelines to establish a definitive verdict.
3. **Escalation / Response:** Taking containment actions (isolating hosts, revoking sessions) or escalating to Incident Response.

### ❓ Task 4 Questions & Answers
* **Question:** Which SOC role would use workbooks the most?  
  * **Answer:** `L1 Analyst`
* **Question:** What is the process of gathering user, host, or IP context using TI and lookups?  
  * **Answer:** `Enrichment`
* **Question:** Looking at the workbook example, what platform is used as an identity inventory source?  
  * **Answer:** `BambooHR`

> 🛡️ **SOC Analyst Insight — SOAR Automation:**  
> In modern enterprise SOCs, these manual workbook steps are frequently automated using **SOAR (Security Orchestration, Automation, and Response)** platforms. When an alert triggers, SOAR automatically executes the enrichment phase and presents the compiled intelligence directly to the L1 analyst.

---

## 🛠️ Practical Workbook Workflows Summary

### Common L1 Investigation Playbooks:
1. **Unusual Login Location:** Enrich IP via Threat Intel -> Check SIEM history for travel/VPN -> Verify with user if suspicious -> Reset password if compromised.
2. **Suspicious Email Attachment:** Extract file hash -> Analyze via Sandbox/VirusTotal -> Check if other users received it -> Purge email and isolate endpoint if malicious.
3. **Malware Endpoint Alert:** Identify host via Asset Inventory -> Analyze process execution tree (e.g., Word spawning PowerShell) -> Isolate machine via EDR -> Escalate to L2.

---

## 🔤 Key Concepts & Terminology Summary
`Identity Inventory` | `Asset Inventory` | `Network Diagram` | `True Positive (TP)` | `False Positive (FP)` | `SOC Workbook` | `Enrichment` | `SOAR`
