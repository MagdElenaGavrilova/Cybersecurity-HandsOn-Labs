# 🛡️ Elastic Stack: The Basics (TryHackMe Walkthrough)

**Lab Title:** Elastic Stack: The Basics  
**Category:** SOC Level 1 / SIEM, Log Management & Kibana Analytics  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
While the Elastic Stack (ELK) was not originally designed as a traditional SIEM, its incredible speed, indexing capabilities (powered by Apache Lucene), and visualization engine have made it one of the most popular platforms for Security Operations Center (SOC) teams. ELK centralizes logs from network devices, operating systems, applications, and VPNs, enabling rapid search, data correlation, and real-time security monitoring. This walkthrough explores the core components of the ELK ecosystem, Kibana interface navigation, KQL syntax, and practical incident investigation using VPN telemetry.

---

## 🏗️ Task 1 & 2: Elastic Stack Overview & Core Components

### 1. What is the Elastic Stack?
The Elastic Stack is an open-source suite of products designed to aggregate, store, search, and visualize data in real-time. It consists of four core components:

* **Beats (Data Shippers):** Lightweight agents installed on endpoints to collect specific types of telemetry (e.g., `Winlogbeat` for Windows event logs, `Filebeat` for text/web logs, `Packetbeat` for network flows, `Metricbeat` for performance metrics).
* **Logstash (Processing Engine):** An ETL (Extract, Transform, Load) tool divided into three configuration blocks:
  1. *Input:* Defines where data comes from (listening ports, files, network streams).
  2. *Filter:* Cleans, parses, and normalizes logs into structured field-value pairs.
  3. *Output:* Defines where processed data is sent (typically Elasticsearch).
* **Elasticsearch (The Search Engine & Database):** A NoSQL full-text search and analytics engine optimized for storing and querying **JSON-formatted documents** at millisecond speeds.
* **Kibana (The Visualization Window):** A web-based Graphical User Interface (GUI) that interfaces directly with Elasticsearch, allowing analysts to perform discovery, search logs, and build interactive dashboards.

### 2. Modern Trends: Logstash vs. Elastic Agent
While traditional environments use the Beats -> Logstash -> Elasticsearch pipeline, modern enterprises increasingly deploy a unified **Elastic Agent** managed via Fleet to collect all telemetry directly, reducing memory and CPU overhead on endpoints.

### ❓ Task 2 Quiz Answers
* **Question:** Logstash is used to visualize the data. (yay / nay)  
  * **Answer:** `nay` *(Logstash processes data; Kibana visualizes it).*
* **Question:** Elasticsearch supports all data formats apart from JSON. (yay / nay)  
  * **Answer:** `nay` *(Elasticsearch primarily and natively works with JSON documents).*

---

## 🖥️ Task 3, 4 & 5: Lab Setup, Discover Tab & KQL (Kibana Query Language)

### 1. Navigating the Discover Tab
The Discover tab is the primary workspace for SOC analysts. Key features include:
* **Index Pattern:** Defines the target dataset (e.g., `vpn_connections` for January 2022 VPN telemetry).
* **Fields Pane (Left):** Displays parsed field-value pairs (`UserName`, `SourceIP`, `Source_Country`). Clicking fields allows filtering via positive (`+`) or negative (`-`) operators.
* **Timeline Histogram:** Displays log volume over time, essential for spotting traffic spikes, brute-force attacks, or data exfiltration.

### 2. Kibana Query Language (KQL) Fundamentals
KQL enables precise searching across massive datasets:
* **Free Text Search:** Searches terms globally across all fields (e.g., `"United States"`). Use wildcards like `United*` for partial matching.
* **Logical Operators:** Must be capitalized (`AND`, `OR`, `NOT`). Example: `Source_Country : "United States" AND (UserName : James OR UserName : Albert)`.
* **Field-Based Search:** Targets specific keys (e.g., `Source_ip : 238.163.231.224`).

### ❓ Practical Investigation Findings & Answers
1. **Total hits returned for `vpn_connections` (Dec 31, 2021 – Feb 2, 2022):** `2861`
2. **IP address with the maximum number of connections:** `238.163.231.224`
3. **User responsible for overall maximum traffic:** `James`
4. **Max hits Source IP when filtering for `UserName : Emanda`:** `107.14.1.247`
5. **IP address causing the traffic spike on Jan 11th:** `172.201.60.191`
6. **Connections from IP `238.163.231.224` excluding New York state:** `48`
7. **Records returned for US logs from users James or Albert:** `161`
8. **VPN connections observed for terminated user Johny Brown after Jan 1, 2022:** `1` *(Indicates compromised credentials).*

---

## 📊 Task 6 & 7: Visualizations and Dashboards

### 1. Creating Visualizations & Failed Connections Analysis
Visualizations transform raw telemetry into tables, bar charts, and pie charts. By querying failed VPN attempts (`action : failed`) over January 2022:
* **Total wrong VPN connection attempts in January:** `274`
* **User observed with the greatest number of failed attempts:** `Simon` *(Indicates a potential brute-force or password-guessing attack targeting his account).*

### 2. Dashboards (Single Pane of Glass)
Consolidating saved searches and individual visualizations into a central **Dashboard** provides the SOC team with a real-time overview of network health, active anomalies, and threat vectors.

---

## 🔤 Key Concepts & Terminology Summary
`Elasticsearch` | `Logstash` | `Beats` | `Kibana` | `JSON` | `Index Pattern` | `KQL` | `Timeline Histogram` | `Dashboard`
