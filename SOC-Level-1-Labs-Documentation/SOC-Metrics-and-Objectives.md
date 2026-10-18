# 🛡️ SOC Metrics and Objectives (TryHackMe Walkthrough)

**Lab Title:** SOC Metrics and Objectives  
**Category:** SOC Level 1 / Performance Tracking & Operations Management  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
To ensure a Security Operations Center (SOC) operates efficiently and protects the organization effectively, leadership (SOC Managers and CISOs) relies on Key Performance Indicators (KPIs) and operational metrics. This lab explores vital metrics such as **SLA (Service Level Agreement)**, **MTTD**, **MTTA**, **MTTR**, and **False Positive Rates**, examining how they impact analyst workload and incident response effectiveness.

---

## 📊 Task 1 & 2: Core SOC Metrics & Alert Fatigue

### 1. Key Performance Indicators (KPIs) Overview
* **SLA (Service Level Agreement):** The contractual timeframe within which an alert must be acknowledged and resolved based on its severity (e.g., Critical alerts addressed within 15 minutes).
* **MTTD (Mean Time to Detect):** The average time taken for the system to identify and generate an alert from the moment an attack starts.
* **MTTA (Mean Time to Acknowledge):** The average time from when an alert appears in the SIEM to when an L1 analyst claims/acknowledges the ticket.
* **MTTR (Mean Time to Respond / Remediate):** The average time required to investigate, contain, and fully neutralize a threat.
* **False Positive Rate (FPR):** The percentage of alerts that turn out to be benign activity rather than real attacks.

> 🏢 **In-House vs. Managed SOC (MSSP):**  
> * **In-House SOC:** Internal security team protecting corporate assets; metrics are used for internal optimization.  
> * **Managed SOC (MSSP):** External provider protecting multiple clients. SLAs are legally binding; failing to meet them results in severe financial penalties.

### 2. The Danger of "Alert Fatigue"
* **Alert Count & Noise:** Handling 200+ alerts daily leads to psychological fatigue, where analysts begin rubber-stamping tickets without proper inspection. Adversaries frequently leverage background noise to mask targeted attacks.
* **The "Zero Alerts" Myth:** Zero alerts for a week does *not* mean the network is secure—it usually indicates a broken SIEM, uncollected logs, or disabled detection rules.

### ❓ Task 2 Questions & Answers
* **Question:** Is zero alerts for one month a good sign for your SOC team? (Yea/Nay)  
  * **Answer:** `Nay`
* **Question:** What is the False Positive Rate if only 10 out of 50 alerts appear to be real threats?  
  * **Answer:** `80%` *(Calculation: 40 false alarms / 50 total = 0.8)*

---

⏱️ Task 3: SLAs and Time-Based Metrics

### 1. Understanding SLA Scenarios
* **Weekend Alert Scenario:** If a critical alert arrives on Saturday, but the SOC operates on an 8/5 schedule (Monday–Friday, business hours only), the alert will remain unacknowledged until **Monday**.
* **MTTR Calculation Example:**
  * MTTD = 12 mins | MTTA = 10 mins | Containment & Remediation time = 35 mins.  
  * Total Incident Workflow Time.

### ❓ Task 3 Questions & Answers
* **Question:** A critical alert arrives on Saturday, and the team works 8/5. When is it acknowledged?  
  * **Answer:** `Monday`

---

## 🛠️ Task 4 & 5: Practical Metric Problem-Solving (Scenarios)

### Scenario 01: Slow Containment Incident
* **Problematic Metric:** Time to Respond (MTTR) was too high, as analysts spent 5 hours trying to rotate compromised credentials and revoke MFA sessions in Entra ID.
* **Improvement Task:** Create a step-by-step workbook explaining credential rotation procedures and present it to the team.
* **Assign Task To:** The L2 analyst who handled the incident (to leverage lessons learned).

### Scenario 02: Delayed Detection
* **Problematic Metric:** Time to Detect (MTTD) was 20 minutes because detection rules were scheduled to run too infrequently.
* **Improvement Task:** Tune the SIEM and detection rules to run more frequently (e.g., every 5 minutes).
* **Assign Task To:** The dedicated SOC Engineer.

### Scenario 03: Alert Fatigue & High False Positives
* **Problematic Metric:** False Positive Rate is the core issue, with 95% of alerts stemming from IT system noise.
* **Improvement Task:** Schedule a meeting to implement the False Positive remediation process.
* **Assign Task To:** SOC Engineers to tune rules and exclude authorized IT administrative noise.

---

## 🔤 Key Concepts & Terminology Summary
`SLA` | `MTTD` | `MTTA` | `MTTR` | `False Positive Rate (FPR)` | `Alert Fatigue` | `Rule Tuning` | `MSSP`
