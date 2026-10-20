# 🛡️ Splunk: The Basics (TryHackMe Walkthrough)

**Lab Title:** Splunk: The Basics  
**Category:** SOC Level 1 / SIEM & Log Analysis  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
In a large enterprise environment, thousands of devices such as servers, firewalls, routers, and web applications generate millions of log entries every second. Manually inspecting individual servers during an investigation is impossible. **Splunk** acts as the "Google for your logs," centralizing, indexing, and enabling real-time search and correlation using its powerful **SPL (Search Processing Language)**. This walkthrough covers Splunk architecture, interface navigation, data ingestion, and practical log analysis.

---

## 🏗️ Task 3: Splunk Architecture

Splunk operates on a distributed three-tier architecture to process and store massive volumes of data efficiently:

1. **Splunk Forwarder (The Field Agent):**  
   A lightweight software agent installed on endpoints (servers, workstations) whose sole task is collecting local logs and securely forwarding them to the central Splunk instance with minimal CPU/RAM overhead.
2. **Splunk Indexer (The Processing Core):**  
   Receives raw logs from forwarders, parses and normalizes them into structured **field-value pairs**, and indexes/stores them on disk for lightning-fast retrieval.
3. **Splunk Search Head (The Analyst Interface):**  
   The primary web interface (accessed via the *Search & Reporting* app) where analysts write SPL queries, view dashboards, and investigate security incidents.

### ❓ Task 3 Question & Answer
* **Question:** Which component is used to collect and send data over the Splunk instance?  
  * **Answer:** `Forwarder`

---

## 🖥️ Task 4 & 5: Interface Navigation & Data Ingestion

### 1. Splunk Web Interface Overview
* **Splunk Bar (Top Navigation):** Includes *Messages*, *Settings* (for index and user management), *Activity*, *Help*, and app switching.
* **Apps Panel:** Displays installed apps, primarily **Search & Reporting**.
* **Add Data Tab:** Provides options to upload, monitor, or forward data.

### 2. Log Ingestion Methods (Add Data)
* **Upload:** Manual ingestion of log or CSV files for offline/quick analysis.
* **Monitor:** Collecting live data files and system ports from local or remote systems.
* **Forward:** Receiving data sent from Splunk Forwarders.

### ❓ Task 4 Question & Answer
* **Question:** In the Add Data tab, which option is used to collect data from files and ports?  
  * **Answer:** `Monitor`

---

## 🔍 Task 5: Practical VPN Log Analysis (SPL Queries)

By uploading a sample JSON-formatted VPN log file and creating an index named `VPN_Logs`, analysts can perform deep forensic queries using `| spath` to parse JSON fields.

### Key SPL Commands Used:
* `index=VPN_Logs` — Targets the specific log index.
* `| spath` — Automatically extracts and parses JSON fields into field-value pairs (`UserName`, `Source_ip`, `Source_Country`).
* `| search` — Filters events based on specific criteria.
* `| stats count` — Aggregates and counts matching events.

### Practical Investigation Findings & Answers:
1. **Total events present in the log file:**  
   * *SPL:* `index=VPN_Logs | stats count`  
   * *Answer:* `2862`
2. **Log events captured by user "Maleena":**  
   * *SPL:* `index=VPN_Logs | spath | search UserName="Maleena" | stats count`  
   * *Answer:* `60`
3. **Username associated with IP `107.14.182.38`:**  
   * *SPL:* `index=VPN_Logs | spath | search Source_ip="107.14.182.38" | stats values(UserName)`  
   * *Answer:* `Smith`
4. **Number of events originating from all countries except France:**  
   * *SPL:* `index=VPN_Logs | spath | search Source_Country!="France" | stats count`  
   * *Answer:* `2814`
5. **VPN events associated with IP `107.3.206.58`:**  
   * *SPL:* `index=VPN_Logs | spath | search Source_ip="107.3.206.58" | stats count`  
   * *Answer:* `14`

> 💡 **Pro-Tip for Splunk Analysts:**  
> Always verify the **Time Picker** setting (top right) when running queries. Setting it to **"All time"** ensures older log files are correctly retrieved and displayed.

---

## 🔤 Key Concepts & Terminology Summary
`SIEM` | `Splunk Forwarder` | `Indexer` | `Search Head` | `SPL` | `Field-Value Pairs` | `Data Ingestion` | `spath`
