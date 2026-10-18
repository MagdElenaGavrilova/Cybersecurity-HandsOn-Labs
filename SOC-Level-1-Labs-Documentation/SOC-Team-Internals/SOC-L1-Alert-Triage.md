# 🛡️ SOC L1 Alert Triage (TryHackMe Walkthrough)

**Lab Title:** SOC L1 Alert Triage  
**Category:** SOC Level 1 / Alert Management & Operational Workflow  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
Alert triage is the primary daily responsibility of a Junior (L1) SOC Analyst. Efficient triage determines whether an incident is caught during early staging or missed with catastrophic operational impacts. This walkthrough explores the lifecycle of an alert, essential properties, system interactions (SIEM, EDR, SOAR, ITSM), and a systematic prioritization approach for handling incoming alerts in high-volume environments.

---

## 📊 Events vs. Alerts & Ecosystem Architecture

### 1. Distinguishing Events from Alerts
* **Event:** Any recorded system or network activity (e.g., user login, file download, HTTP request). Enterprises generate millions of events daily.
* **Alert:** A notification triggered only when an event (or sequence of events) matches suspicious or anomalous logic defined by a detection rule.

### 2. SOC Platform Ecosystem
| Platform | Core Function | Examples |
| :--- | :--- | :--- |
| **SIEM** | Central log collection, correlation, and alerting engine | Splunk ES, Elastic SIEM |
| **EDR / NDR** | Host and network-level real-time threat detection | MS Defender for Endpoint, CrowdStrike |
| **SOAR** | Automated enrichment and orchestration across platforms | Splunk SOAR, Cortex XSOAR |
| **ITSM** | Ticketing and incident management | ServiceNow, Jira, TheHive |

### ❓ Task 2 Questions & Answers
* **Question:** What is the number of alerts you see in the SOC dashboard?  
  * **Answer:** `5`
* **Question:** What is the name of the most recent alert you see?  
  * **Answer:** `Double-Extension File Creation`

---

## 🔎 Alert Properties & Classification

### Key Alert Metadata Fields
1. **Alert Time:** Timestamp when the alert was triggered.
2. **Alert Severity:** Priority level assigned by engineers (`Low`, `Medium`, `High`, `Critical`).
3. **Alert Status:** Investigation stage (`New`, `In Progress`, `Closed`).
4. **Alert Verdict:** Final analyst determination (`True Positive` vs. `False Positive`).
5. **Assignee & Description:** Responsible analyst and context/playbook instructions.

### ❓ Task 3 Questions & Answers
* **Question:** What was the verdict for the "Unusual VPN Login Location" alert?  
  * **Answer:** `False Positive`
* **Question:** What user was mentioned in the "Unusual VPN Login Location" alert?  
  * **Answer:** `T.Ross`

---

## 🎯 Alert Prioritisation Methodology

When triaging high volumes of concurrent alerts, L1 analysts follow a strict 3-step prioritization rule:
1. **Filter:** Select only unassigned alerts with status `New` or `Awaiting Action`.
2. **Severity First:** Address alerts in order of business risk: **Critical** ➔ **High** ➔ **Medium** ➔ **Low**.
3. **Age Second:** For alerts of equal severity, always pick the **Oldest** alert first (to minimize attacker dwell time).

### ❓ Task 4 Questions & Answers
* **Question:** Should you first prioritise medium over low severity alerts?  
  * **Answer:** `Yea`
* **Question:** Should you first take the newest alerts and then the older ones?  
  * **Answer:** `Nay`
* **Question:** Assign yourself to the first-priority alert and change its status to In Progress.  
  * **Answer:** `Potential Data Exfiltration`

---

## 🔤 Key Concepts & Terminology Summary
`Alert Triage` | `Event vs Alert` | `Severity Prioritization` | `Oldest First Rule` | `True Positive` | `False Positive` | `SOAR` | `ITSM`
