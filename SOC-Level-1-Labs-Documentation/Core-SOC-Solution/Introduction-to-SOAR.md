# 🛡️ Introduction to SOAR (TryHackMe Walkthrough)

**Lab Title:** Introduction to SOAR (Security Orchestration, Automation, and Response)  
**Category:** SOC Level 1 / Incident Response & Security Automation  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
As cyber threats grow in speed and complexity, traditional Security Operations Center (SOC) teams face massive operational bottlenecks, including alert fatigue, manual processes, and disconnected security tools. **SOAR (Security Orchestration, Automation, and Response)** addresses these challenges by integrating security tools, automating repetitive tasks via predefined playbooks, and standardizing incident response workflows. This walkthrough explores the core pillars of SOAR, traditional SOC challenges, playbook design, and practical threat intelligence automation.

---

## ⚡ Task 1: Introduction to SOAR

### 1. What is SOAR?
SOAR is a comprehensive technology solution designed to help organizations and SOC teams streamline, optimize, and automate security operations, incident management, threat intelligence, and vulnerability response.

### 2. The 3 Pillars of SOAR
* **1. Orchestration:** Connecting all tools via API (SIEM + EDR + Firewall) into a single collaborative workspace.
* **2. Automation:** Executing scripts/playbooks without the need for manual human clicks.
* **3. Response:** Rapid and standardized reaction during incidents (Containment & Remediation).

### 3. Comparison: SIEM vs. EDR vs. SOAR
| Tool | Primary SOC Role | Response Action |
| :--- | :--- | :--- |
| **SIEM** | Collects logs from the entire network and analyzes them (Central Log Hub) | Generates an Alert |
| **EDR** | Monitors and isolates the endpoint itself | Blocks process / Isolates computer |
| **SOAR** | Conductor connecting SIEM, EDR, Email, Firewall, and Slack | Automates the entire workflow (Playbook) |

---

## 📊 Task 2: Traditional SOC and Challenges

### 1. Traditional SOC Capabilities
* **Monitoring & Detection:** Scanning and anomaly detection (primarily via SIEM).
* **Recovery & Remediation:** EDR, Firewall, IAM for stopping threats.
* **Threat Intelligence:** TIP Feeds (IPs, Hashes, Domains).
* **Communication:** Ticketing / IT Coordination.

### 2. The Four Major Challenges
1. **Alert Fatigue:** Thousands of alerts daily overloading analysts.
2. **Manual Processes:** Loss of valuable time on repetitive manual tasks.
3. **Disconnected Tools:** Tools that do not communicate with each other.
4. **Impaired Communication:** Difficulties in coordination between SOC, IT department, and management.

### ❓ Task 2 Quiz Answer
* **Question:** How would you describe the experience of an overload of security events being triggered within a SOC?  
  * **Answer:** `Alert Fatigue`

---

## ⚙️ Task 3: Overcoming SOC Challenges with SOAR

### 1. The Power of SOAR
SOAR unifies tools through API integrations and predefined **Playbooks**. This enables the elimination of manual workflows, reduction of **MTTR (Mean Time to Respond)**, and prevention of human errors.

### 2. Levels of Automation in Playbooks
* **Fully Automated:** Execution without human intervention for safe actions (e.g., blocking a known phishing URL).
* **Semi-Automated (Human-in-the-Loop):** Performing the investigation automatically, but with a mandatory approval click from the analyst before critical remediations.
* **Manual Execution:** Manually triggering actions from the SOAR dashboard.

### ❓ Task 3 Quiz Answers
* **Question:** The act of connecting and integrating security tools and systems into seamless workflows is known as?  
  * **Answer:** `Orchestration`
* **Question:** What do we call a predefined list of actions to handle an incident?  
  * **Answer:** `Playbook`

---

## 🛠️ Task 4 & 5: Building SOAR Playbooks & Automation Workflow

### 1. Practical Playbook Architectures
* **Phishing Playbook:** Automatically receives a suspicious email, checks links and attachments in a Sandbox, compares against Threat Intelligence, and performs deletion/blocking if malicious.
* **CVE Patching Playbook:** Pulls information about new vulnerabilities (NVD/CVE Feed), performs risk assessment, opens patching tickets, tests them, and develops custom alternative solutions (**Custom mitigation script**) if the patch is not sufficient.

### 2. Automation Workflow Table (Task 5 Configuration Matrix)
| Workflow Category | Function / Task | Setting | Configuration Rationale |
| :--- | :--- | :--- | :--- |
| **Case Management** | Create Case Ticket | 🟢 Automated | As soon as SIEM generates an alert, a ticket is immediately opened to prevent missing it. |
| | Assign Case Ticket | 🟢 Automated | Automatic assignment of the ticket to an on-duty analyst. |
| | Communicate Case Ticket | 🟢 Automated | Sending notifications to Slack/Email for quick coordination. |
| | Update Case Ticket | 🟢 Automated | All results from checks are automatically written to the ticket via API. |
| | Delete Case Ticket | 🔴 Manual | Ticket deletion must be strictly manual to preserve audit trails. |
| **Threat Intel Feeds** | Fetch New Incident Alerts | 🟢 Automated | Continuous ingestion of new Indicators of Compromise (IoCs) in real-time. |
| | Set Fetch Intervals | 🟢 Automated | Automatic maintenance of polling frequency (e.g., every 5 minutes). |
| | Failed Fetch Notifications | 🟢 Automated | Generating an alert to administrators if the threat feed fails. |
| | Discard Old Alerts | 🔴 Manual | Old alerts are not deleted automatically to allow Retroactive Threat Hunting. |
| **Data Extraction** | Extract Domains | 🟢 Automated | Extracting domains from logs and emails without manual copying. |
| | Extract URLs | 🟢 Automated | Automatic parsing of all suspicious links. |
| | Extract IPs | 🟢 Automated | Isolating internal and external IP addresses in seconds. |
| | Analyst Extraction | 🔴 Manual | Human analysis is needed for hidden, novel, or obfuscated artifacts (Zero-Day). |
| **Reputation Checks** | Reputation Results Output | 🟢 Automated | Sending extracted IPs/Domains to VirusTotal and returning the summary to the ticket. |
| | Sandbox Testing | 🔴 Manual | Sending files to a Sandbox requires a manual decision to avoid sharing sensitive data. |
| | Analyst Validation | 🔴 Manual | The final verdict (True Positive / False Positive) is confirmed by a human analyst. |
| **Course of Action (COA)**| Block Domains | 🟢 Automated | Automatically sending blocking commands after approval is received. |
| | Block IPs | 🟢 Automated | Closing network access for malicious IPs on Firewall devices. |
| | Block URLs | 🟢 Automated | Blocking links at the shield and DNS filter levels. |
| | Update Case Tickets | 🟢 Automated | Automatically updating the ticket with the status from network devices. |
| | Analyst Approve COA | 🔴 Manual | **Human-in-the-Loop control!** The analyst must manually approve the action to prevent business disruption. |

### ❓ Task 4 & 5 Quiz Answers & Flag
* **Question:** Is manual analysis vital within a SOAR workflow? Yay or Nay?  
  * **Answer:** `Yay`
* **Question:** From where is the CVE Patching playbook fetching the new CVEs?  
  * **Answer:** `Advisory lists`
* **Question:** In the CVE Patching playbook, if the assets are found vulnerable even after the patch is deployed, what does the SOC develop next?  
  * **Answer:** `Custom mitigation script`
* **Captured Lab Flag:** `THM{AUT0M@T1N6_S3CUR1T¥}`

---

## 🔤 Key Concepts & Terminology Summary
`SOAR` | `Orchestration` | `Automation` | `Response` | `Playbook` | `Alert Fatigue` | `MTTR` | `Human-in-the-Loop` | `API Connectors`
