# 🛡️ Humans as Attack Vectors (TryHackMe Walkthrough)

**Lab Title:** Humans as Attack Vectors  
**Category:** SOC Level 1 / Incident Response & Threat Detection  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
While modern enterprise networks are protected by advanced firewalls, EDR solutions, and SIEM platforms, **human beings remain the primary target for malicious actors**. Attackers frequently leverage social engineering to manipulate human psychology—bypassing perimeter security without exploiting software vulnerabilities. This lab explores why humans are targeted, common social engineering vectors, and how a Security Operations Center (SOC) mitigates and detects these threats.

---

## 🎯 Task 2: The Human Element

### 1. Direct Attacks vs. Social Engineering
Attackers constantly evaluate the path of least resistance:
* **Option A:** Spend weeks attempting to discover zero-day exploits or brute-force enterprise firewalls.
* **Option B:** Send a targeted phishing email to an administrative user and obtain valid access credentials.

> 💡 **Analogy:** A corporate network is like a fortified medieval castle with high stone walls and heavy iron gates. While an attacker can attempt to batter down the gates, it is significantly easier to trick the gatekeeper into opening the door. In cybersecurity, **humans are often considered the "weakest link"** in the defense chain.

### 2. Attack Vectors & Threat Scenarios

Attackers seek **access** above all else—whether to compromise specific targets or to harvest credentials at scale.

| Target Role / Scenario | Attacker's Next Objective / Impact |
| :--- | :--- |
| **HR Manager's Google Account** | Exfiltration and sale of sensitive employee database & PII |
| **High-Net-Worth Individual (Malware Execution)** | Account takeover and unauthorized wire transfers via online banking |
| **IT Administrator's VPN Account** | Unrestricted access to the core corporate network & domain controller |
| **Government Official (Social Engineering)** | Espionage, classified data theft, and pivot to further targets |

### ❓ Task 2 Questions & Answers
* **Question:** What or who is the weakest link in cyber security?  
  * **Answer:** `Humans`
* **Question:** What do attackers seek when targeting humans in a cyberattack?  
  * **Answer:** `access`

> 🛡️ **SOC Analyst Insight — Defense in Depth:**  
> To minimize the risk of human error, modern SOC environments enforce the **Principle of Least Privilege (PoLP)** and **Defense in Depth**. Even if a user falls victim to social engineering, their restricted permissions prevent full system compromise. Enforcing **Multi-Factor Authentication (MFA)** across all VPN and email logins drastically reduces the success rate of credential-harvesting attacks.

---

## 🎣 Task 3: Attacks on Humans

### 1. What is Social Engineering?
Social engineering relies on psychological manipulation rather than technical exploits. To maximize success, attackers design campaigns to be:
1. **Trustworthy:** Posing as legitimate entities (IT Support, HR, Bank, Executives).
2. **Emotional:** Triggering urgency, fear, curiosity, or desire to act quickly without thinking.

### 2. Common Social Engineering Vectors
* **Phishing Attacks:** The most prevalent threat vector (~3.4 billion malicious emails daily). Example: Fake alert emails prompting users to log in to spoofed portals.
* **Malware Downloads:** Tricking users via fake CAPTCHA prompts, malicious QR codes (Quishing), and SEO poisoning.
* **Deepfakes & AI Impersonation:** Generative AI used to replicate voice or video of C-level executives (e.g., a $25M fraud incident involving a fake deepfake CEO video call).
* **Impersonation (Vishing / Pretexting):** Posing as IT helpdesk personnel via phone to request access or force password resets, often leading to Ransomware deployments.
* **Other Vectors:** USB Drop Campaigns, Physical Tailgating, and Insider Threats.

### ❓ Task 3 Questions & Answers
* **Question:** What is the name of an attack tactic that manipulates human psychology?  
  * **Answer:** `social engineering`
* **Question:** Which social engineering method is about pretending to be someone else?  
  * **Answer:** `impersonation`

> 🛡️ **SOC Analyst Insight — Attack Trends:**  
> The vast majority of corporate security breaches do not originate from sophisticated zero-day exploits, but rather from basic phishing or impersonation calls. Alongside technical monitoring (SIEM/EDR), organizational resilience relies heavily on continuous **Security Awareness Training** and strict out-of-band verification procedures for sensitive tasks.

---

## 🏰 Task 4: Defending Humans

### 1. Core Defensive Pillars: Mitigation vs. Detection
* **Mitigation:** Proactive measures aimed at preventing an attack or reducing its blast radius (e.g., spam filters, MFA, policy enforcement).
* **Detection:** Reactive capability handled by SOC Analysts to identify, triage, and respond to threats that bypass preventive controls.

### 2. Mitigation Controls Framework
+-----------------------------------+
                  |   Human Risk Mitigation Controls  |
                  +-----------------------------------+
                                    |
    +-------------------------------+-------------------------------+
    |                               |                               |
    v                               v                               v
+-------------------+       +-----------------------+       +-------------------+
| Security Awareness|       | Email & Web Security  |       | Zero Trust & MFA  |
|     Training      |       |        Filters        |       |    Enforcement    |
+-------------------+       +-----------------------+       +-------------------+
### ❓ Task 4 Questions & Answers
* **Question:** Which process is aimed at preventing or reducing the chance of an attack?  
  * **Answer:** `mitigation`
* **Question:** Which mitigation measure is about training employees in cyber security?  
  * **Answer:** `security awareness training`

> 🛡️ **SOC Analyst Insight — Building a "Human Firewall":**  
> Combining regular **Security Awareness Training** with simulated **Phishing Exercises** builds an organizational "Human Firewall". Data shows that consistent testing reduces user click-through rates on malicious links from over 30% to under 3%. This drastically lowers incident volume, allowing SOC Analysts to focus on high-priority, complex threats.

---

## 🛠️ Key Concepts & Terminology Summary
`Social Engineering` | `Phishing` | `Impersonation` | `Defense in Depth` | `Least Privilege` | `Security Awareness Training` | `Human Firewall`
