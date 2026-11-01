# 🛡️ Eviction: Threat Intelligence & MITRE ATT&CK Analysis

*Unearth the monster from under your bed.*

---

## 📌 1. Scenario Overview

* **Real-world APT Group:** APT28 (also known as Fancy Bear, Sednit, Sofacy).
* **Primary Tool:** MITRE ATT&CK Navigator (using a mapped layer of techniques for APT28 to answer all questions).
* **Analyst Objective (Sunny):** Uncover Tactics, Techniques, and Procedures (TTPs) to prevent the theft of intellectual property from E-corp.

---

## ❓ Lab Questions & Analysis

### ❓ Question 1
**What is a technique used by the APT to both perform recon and gain initial access?**

* **Analysis:** Comparing the horizontal columns **Reconnaissance** and **Initial Access** in MITRE ATT&CK Navigator for APT28. The same technique related to sending malicious links via email appears in both phases.
* **🎯 Answer:** Spearphishing Link (or Spearphishing link) `(ATT&CK ID: T1598.003 / T1566.002)`

---

### ❓ Question 2
**Sunny identified that the APT might have moved forward from the recon phase. Which accounts might the APT compromise while developing resources?**

* **Analysis:** Focusing on the **Resource Development** tactic. To send credible phishing emails and set up the attack infrastructure, APT28 compromises existing email accounts.
* **🎯 Answer:** Email Accounts `(ATT&CK ID: T1586.002)`

---

### ❓ Question 3
**E-corp has found that the APT might have gained initial access using social engineering to make the user execute code for the threat actor. Sunny wants to identify if the APT was also successful in execution. What two techniques of user execution should Sunny look out for?** *(Answer format: `<technique 1> and <technique 2>`)*

* **Analysis:** Examining the **Execution** tactic, specifically the sub-techniques under **User Execution** (T1204). When a user is manipulated via social engineering, they are induced to click a link or open a malicious file attachment.
* **🎯 Answer:** Malicious File and Malicious Link (or malicious file and malicious link) `(ATT&CK ID: T1204.001 and T1204.002)`

---

### ❓ Question 4
**If the above technique was successful, which scripting interpreters should Sunny search for to identify successful execution?** *(Answer format: `<technique 1> and <technique 2>`)*

* **Analysis:** Under the **Execution** tactic, in the section for **Command and Scripting Interpreter** (T1059), we look for the primary scripting interpreters used by APT28 to execute commands.
* **🎯 Answer:** PowerShell and Windows Command Shell (or powershell and windows command shell) `(ATT&CK ID: T1059.001 and T1059.003)`

---

### ❓ Question 5
**While looking at the scripting interpreters identified in Q4, Sunny found some obfuscated scripts that changed the registry. Assuming these changes are for maintaining persistence, which registry keys should Sunny observe to track these changes?**

* **Analysis:** Moving to the **Persistence** tactic. The technique that utilizes the Windows Registry to automatically execute scripts upon system boot is **Registry Run Keys**.
* **🎯 Answer:** Registry Run Keys `(ATT&CK ID: T1547.001)`

---

### ❓ Question 6
**Sunny identified that the APT executes system binaries to evade defences. Which system binary's execution should Sunny scrutinize for proxy execution?**

* **Analysis:** In the **Defense Evasion** tactic, we look for the technique **Signed Binary Proxy Execution**. APT28 frequently uses the legitimate Windows system binary `rundll32.exe` to execute malicious DLL files without triggering alerts.
* **🎯 Answer:** Rundll32 (or rundll32.exe) `(ATT&CK ID: T1218.011)`

---

### ❓ Question 7
**Sunny identified tcpdump on one of the compromised hosts. Assuming this was placed there by the threat actor, which technique might the APT be using here for discovery?**

* **Analysis:** In the **Discovery** column, the `tcpdump` tool is used to intercept and capture network traffic in order to discover other devices and protocols within the network.
* **🎯 Answer:** Network Sniffing (or Network sniffing) `(ATT&CK ID: T1040)`

---

### ❓ Question 8
**It looks like the APT achieved lateral movement by exploiting remote services. Which remote services should Sunny observe to identify APT activity traces?**

* **Analysis:** Reviewing the **Lateral Movement** section. APT28 moves across the network by exploiting server protocols used for resource sharing and administration—primarily SMB and Windows Admin Shares.
* **🎯 Answer:** SMB/Windows Admin Shares (or SMB/windows admin shares) `(ATT&CK ID: T1021.002)`

---

### ❓ Question 9
**It looked like the primary goal of the APT was to steal intellectual property from E-corp's information repositories. Which information repository can be the likely target of the APT?**

* **Analysis:** In the **Collection** tactic under **Data from Information Repositories** (T1213), the Microsoft repository targeted by APT28 to exfiltrate corporate documents is SharePoint.
* **🎯 Answer:** SharePoint (or Sharepoint) `(ATT&CK ID: T1213.002)`

---

### ❓ Question 10
**Although the APT had collected the data, it could not connect to the C2 for data exfiltration. To thwart any attempts to do that, what types of proxy might the APT use?** *(Answer format: `<technique 1> and <technique 2>`)*

* **Analysis:** In the **Command and Control (C2)** column under the **Proxy** technique (T1090), APT28 utilizes external servers and multi-layered routing (such as Tor or redirected VPNs) to conceal C2 traffic.
* **🎯 Answer:** External Proxy and Multi-hop Proxy (or external proxy and multi-hop proxy) `(ATT&CK ID: T1090.002 and T1090.003)`

---

## 🔤 Key Concepts & MITRE ATT&CK Mapping Summary

`APT28` | `Spearphishing Link` | `Email Accounts` | `User Execution` | `PowerShell` | `Cmd.exe` | `Registry Run Keys` | `Rundll32` | `Network Sniffing` | `SMB / Admin Shares` | `SharePoint` | `Multi-hop Proxy`
