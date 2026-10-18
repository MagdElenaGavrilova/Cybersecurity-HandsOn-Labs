# 🛡️ SOC L1 Alert Reporting and Escalation (TryHackMe Walkthrough)

**Lab Title:** SOC L1 Alert Reporting  
**Category:** SOC Level 1 / Incident Reporting & Crisis Communication  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
Properly reporting, escalating, and communicating about high-risk alerts is a core duty of an L1 SOC Analyst. Identifying a threat is only half the battle; accurately conveying the technical details to L2/L3 analysts, Incident Responders, and system owners ensures rapid containment. This walkthrough covers the **Alert Funnel**, the **Five Ws Reporting Framework**, hands-on alert escalation, and crisis communication protocols.

---

## 🌪️ Task 2: The Alert Funnel Framework

### The Three Phases of the L1 Triage Pipeline
1. **Alert Reporting:** Formally documenting findings, extracted IOCs, and raw log artifacts into a standardized report.
2. **Alert Escalation:** Transferring high-severity or complex True Positive alerts to L2/L3 analysts with elevated permissions for remediation.
3. **Communication:** Contacting internal stakeholders (IT, HR, System Owners) via out-of-band channels to verify business legitimacy.

### ❓ Task 2 Questions & Answers
* **Question:** What is the process of passing suspicious alerts to an L2 analyst for review?  
  * **Answer:** `Alert Escalation`
* **Question:** What is the process of formally describing alert details and findings?  
  * **Answer:** `Alert Reporting`

---

## ✍️ Task 3: The Five Ws Reporting Guide

When documenting an alert in the ticketing system or SIEM analyst comments, L1 analysts utilize the structured **Five Ws Framework**:

* **Who:** Impacted user, compromised account, or host system (`NT AUTHORITY\SYSTEM` on `DMZ-MSEXCHANGE-2013`).
* **What:** The specific action or malicious command executed (e.g., Active Directory discovery via reverse shell).
* **When:** Exact timestamp of event detection and duration.
* **Where:** Source IP, hostname, path, or network segment involved.
* **Why:** Final analyst verdict and reasoning (`True Positive` due to unauthorized execution of `revshell.exe`).

---

## 🚀 Task 4: Hands-On Escalation Case Study (Active Directory Discovery)

### Incident Investigation Analysis
* **Host Impacted:** `DMZ-MSEXCHANGE-2013` (Critical Exchange Server)
* **User Context:** `NT AUTHORITY\SYSTEM`
* **Malicious Execution Chain:**
  `w3wp.exe` *(Web Server process)* ➔ `C:\Users\Public\revshell.exe` *(Reverse Shell)* ➔ `cmd.exe` ➔ Reconnaissance commands (`whoami /priv`, `net group "Domain Admins" /domain`, `nltest /dclist:tryhackme.thm`)
* **Verdict:** **True Positive (Critical Threat)** — Requires immediate host isolation and escalation to L2 Incident Response.

```plaintext
[Five Ws Report Example for Escalation]

Who: NT AUTHORITY\SYSTEM on host DMZ-MSEXCHANGE-2013
What: Active Directory domain discovery activity initiated via a malicious reverse shell (revshell.exe)
When: Current Timestamp
Where: Windows Server 2012 R2 Exchange server originating from IIS web process compromise (w3wp.exe)
Why: True Positive. Highly malicious activity indicating advanced system compromise and active adversary recon.
