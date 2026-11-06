# 🛡️ Phishing Analysis Tools

Learn the tools used to aid an analyst in investigating suspicious emails.

---

## 🛡️ Phishing Analysis Tools: Introduction (Task 1)

### 📌 1. Lesson Overview
In the previous two rooms of the phishing module, we mastered the foundational basics of:
- **Email Header Analysis** and **Body Analysis**.
- Investigating links and attached files by inspecting the **Email Source Code**.

#### What are we doing in this room?
We build upon that foundation by getting introduced to powerful tools that enable automated and deeper analysis, after which we will practically apply these skills by investigating three real phishing cases.

### 🎯 Learning Objectives:
- Understand what information needs to be collected during a complete email analysis.
- Get acquainted with tools that enable efficient and fast investigation of suspicious messages.
- Investigate IP addresses and URL links, including reputation lookups.
- Solve practical phishing cases to solidify our knowledge.

---

### 💡 3. Extra Context (Expert Notes for Notion)

#### Why do we need specific tools in a SOC instead of just manual analysis?
Manual analysis (such as reading Raw Headers and Base64 decoding) is great for understanding core principles. However, in a real Security Operations Center (SOC), analysts face dozens or hundreds of suspicious emails daily. Automated check tools make it possible to determine in seconds whether a URL has already been reported as malware or if an IP address originates from a known malicious network (botnet/VPN).

#### What is an Indicator of Compromise (IoC)?
Throughout the tasks in this room, we will learn how to extract IoCs (such as file hashes, malicious domains, IP addresses, and URLs), which are subsequently checked against specialized threat intelligence databases.

---

### 📊 Quick Overview

| Key Area | Description / Consideration |
| :--- | :--- |
| **Module** | **Phishing Analysis Tools** (Practical investigations of real-world cases). |
| **Prerequisites** | Manual analysis of headers, source code, links, and attachments. |
| **New Focus** | Utilizing specialized platforms and tools for reputation checks (IP, URL, Hash). |

---

## 🛡️ Phishing Analysis: Identifying Artifacts (Task 2)

### 📌 1. Lesson Breakdown
When analyzing a suspicious or malicious email, the analyst's primary objective is to collect key artifacts (**Artifacts / IoCs**) that will help determine its legitimacy and intent. These artifacts form the foundation for deeper investigation, such as reputation checks, threat intelligence lookups, and behavioral analysis.

#### 📩 1. Header Artifacts
When starting your analysis by examining the email header, you should thoroughly note down the following points:
- **Sender email address:** Where does the email originate from? (Check the `From:` field and verify if it matches the legitimate domain).
- **Sender IP address:** What is the source IP address and what does its reverse lookup (`reverse lookup` / `Received:` fields) reveal?
- **Email subject line:** Does the subject contain a sense of urgency or a direct call to action?
- **Recipient email address:** Who is the targeted user or group (`To:`, `Cc:`, `Bcc:`)?
- **Reply-To email address:** Where are replies redirected if the user clicks Reply? (Often differs from the `From:` address in phishing).
- **Date and time:** When was the message sent?

#### 📄 2. Body Artifacts
Next, analyze the email body, focusing on the following artifacts:
- **URLs and hyperlinks:** Identify all available links and unpack/expand shortened URLs to reveal their true destination.
- **Attachment name(s):** What files are included in the message, and are their names or extensions suspicious (e.g., `.exe`, `.iso`, `.zip`, `.xlsm`, `.html`)?
- **Attachment hash:** Generate a hash value (MD5, SHA-256) for the attached file to run lookups in threat intelligence databases (such as VirusTotal).

---

### 💡 3. Extra Context (Expert Notes for Notion)

#### 🕵️‍♀️ Golden Rules for Collecting Artifacts in a SOC (SOC Best Practices):

1. **Defang URLs and IPs:**
   Never save or send malicious links or IP addresses in cleartext format within your ticket or notebook, to prevent accidental clicks!
   - **Plain link:** `http://malicious-domain.com/login.php`
   - **Defanged link:** `hxxp[:]//malicious-domain[.]com/login[.]php`
   - **Defanged IP:** `192[.]168[.]1[.]1`

2. **Mismatch between Display Name and Actual Address:**
   Attackers socially engineer users by setting a legitimate display name (e.g., `"PayPal Support" <attacker@badsite.com>`). Always inspect the identity behind the `< >` brackets.

3. **Difference between Envelope Sender (Return-Path) and Header Sender (From):**
   - **From:** What the user sees in their email client (can easily be spoofed).
   - **Return-Path:** The address where the SMTP server routes bounce-backs, which often reveals the attacker's actual infrastructure.

---

### 📊 Quick Overview

| Artifact | Source (`Header` / `Body`) | What are we looking for? |
| :--- | :--- | :--- |
| **Sender Email / IP** | Header (`From:`, `Received:`) | Source address, IP reputation, domain mismatch. |
| **Reply-To Address** | Header (`Reply-To:`) | Do replies go to a different address than the sender? |
| **Subject Line** | Header (`Subject:`) | Psychological pressure ("URGENT", "Invoice Due", "Action Required"). |
| **URLs / Hyperlinks** | Body | Hidden redirects, spoofed domains (Typosquatting), shortened links. |
| **Attachments** | Body | Double extensions (`.pdf.exe`), macros (`.docm`), SHA-256 hashes for VirusTotal. |

---

## 🛡️ Phishing Analysis Tools: Email Header Analysis (Task 3)

### 📌 1. Lesson Breakdown
Some key details can be observed directly within the email client's user interface (for example, in Gmail or Yahoo!). However, details such as the true sender IP address, `Reply-To` fields, and the complete hop-by-hop network routing path are accessible exclusively within the raw email header source code.

To avoid manual line-by-line reading, analysts use automation tools:

#### 🛠️ 1. Header Analysis Tools
- **Google Admin Toolbox — Messageheader:**
  - **Purpose:** A component of Google Admin Toolbox that allows quick parsing of email headers.
  - **How it works:** Copy the entire raw header and paste it into the tool. It automatically visualizes the delivery path, network delays, and authentication results (SPF, DKIM, DMARC).
- **Message Header Analyzer (Microsoft / EasyDMARC):**
  - **Purpose:** A similar web tool (frequently used in Microsoft 365 environments) that breaks down all `Received:` hops, transmission delays, and extracts authentication records into a clean tabular format.

#### 🔍 2. IP & URL Reputation Analysis
When investigating phishing emails, it is extremely important to discover the origin of extracted IP addresses and domains to evaluate their reputation:

| Tool | Search Type | Primary Functionality / Advantage |
| :--- | :--- | :--- |
| **IPinfo** | IP Lookup & Geolocation | Provides fast insight into the IP's geographic location, ASN (Autonomous System Number), and ISP/organization owner. |
| **URLScan.io** | URL Sandbox / Visual Scanning | Safely opens suspicious links in an isolated headless browser, captures screenshots, logs all HTTP requests/redirects, and extracts DOM artifacts. |
| **Cisco Talos Intelligence** | IP & Domain Reputation | Threat Intelligence platform from Cisco providing historical reputation data for IP addresses, domains, and email networks (Good, Neutral, Poor). |

---

### 📝 2. Task 3 Question Answer

- **Question:** According to the Messageheader analysis shown in this task, what is the SPF result for the email?
- 👉 **Answer:** `pass` (or `PASS`)
- *(Explanation: When looking at the example screenshot from the Google Admin Toolbox Messageheader analysis within Task 3 on TryHackMe, under the authentication checks section, the SPF status field indicates `pass`).*

<img width="1712" height="695" alt="image1phishing" src="https://github.com/user-attachments/assets/adfc866f-c3e8-4da2-beef-0cbccd03172a" />

---

### 💡 3. Extra Context

#### 🔐 The Three Pillars of Email Authentication (SPF, DKIM, DMARC):
- **SPF (Sender Policy Framework):** Verifies whether the sender's IP address is authorized to send emails on behalf of that domain (defined in a DNS TXT record).
- **DKIM (DomainKeys Identified Mail):** Provides a digital signature for the message (using asymmetric cryptography) guaranteeing that email content was not altered in transit.
- **DMARC (Domain-based Message Authentication, Reporting, and Conformance):** Combines SPF and DKIM, instructing the receiver on how to handle messages that fail checks (`p=none`, `p=quarantine`, or `p=reject`).

---

### 📊 Quick Overview

| Category | Tool | Application in SOC |
| :--- | :--- | :--- |
| **Header Analysis** | Google Messageheader | IP extraction, SPF/DKIM/DMARC checks, and delivery delays. |
| **IP Intelligence** | IPinfo.io / Cisco Talos | Location analysis, hosting provider, and historical reputation. |
| **URL Sandbox** | URLScan.io | Safe link simulation and redirect chain analysis. |

---

## 🛡️ Phishing Analysis Tools: Email Body Analysis (Task 4)

### 📌 1. Lesson Breakdown
After analyzing the header, focus shifts to the email body, where the true intent of the phishing message is usually revealed. Here, the attacker delivers the malicious payload, which can be:
1. **Hyperlink (URL):** Designed to lead the user to a fake login portal for credential harvesting.
2. **Attachment:** Intended to directly compromise the host system (malware, trojan, macro script).

#### 🔗 1. Body & Link Analysis
- **Manual Link Extraction:** Right-click the link and select *Copy link address* (without clicking directly on it).
- **URL Extractors:** Allow pasting the full raw HTML code of the message for automatic parsing of all embedded links. Reduces the risk of missing hidden or obfuscated URLs.
- **CyberChef:** Data manipulation suite by GCHQ featuring recipes such as `Extract URLs` to rapidly extract all links from raw source code.

#### 📎 2. Safe Handling of Attachments
- **Golden Security Rule:** Attachments must **NEVER** be downloaded or opened on a personal or production machine. Downloading and analysis must strictly take place in an isolated environment (Sandbox or isolated Lab Virtual Machine).
- **Generating Hash Values:** In a Linux environment, use the `sha256sum` command to extract the unique SHA-256 fingerprint of a file:
  ```bash
  sha256sum shady_attachment.pdf
Example Output: 025ba9ce4a2118a9ca7b115c8869ff73bc16bad3732ba359cef1e60ad8f961f9  shady_attachment.pdf
### 🧪 3. Searching Hashes in Threat Intelligence Platforms

Once the hash is obtained, search for it on public platforms (without uploading the actual file, preserving data confidentiality):

| Tool | Hash Analysis Functionality |
| :--- | :--- |
| **Cisco Talos Intelligence** | Allows entering the hash to check its historical reputation (e.g., category: Phishing, Malicious, Spam). |
| **VirusTotal** | Prominent platform comparing the hash (or uploaded file/URL) against over 70 different antivirus engines and security scanners (e.g., CrowdStrike, Kaspersky, Defender). Also provides behavioral reports. |

---

### 📝 2. Task 4 Question Answer

- **Question:** What command can you use in a Linux environment to obtain the SHA256 hash value of an attachment?
- 👉 **Answer:** `sha256sum`
- *(Explanation: In a Linux terminal, the standard utility to calculate the SHA-256 cryptographic hash of any file is `sha256sum`).*

---

### 💡 3. Extra Context

#### 🛑 Critical Warnings for SOC Operations:
- **Searching Hashes vs. Uploading Files to VirusTotal:** Always search the **HASH** first! Uploading an entire file to VirusTotal makes it publicly available to users with Premium access. If the file contains confidential company data (e.g., signed contracts or private personal data within a phishing PDF), it will cause a data leak.
- **HTML Link Obfuscation:** Attackers often manipulate Anchor tags to deceive the user's eyes:
  ```html
  <a href="[http://malicious-phishing-site.com](http://malicious-phishing-site.com)">[https://www.paypal.com/login](https://www.paypal.com/login)</a>
- The user sees `https://www.paypal.com/login` in the email, but clicking it directs them to `malicious-phishing-site.com`. Parsing raw HTML with tools is essential for this reason[cite: 1].

---

### 📊 Quick Overview

| Concept / Tool | Application in Email Body Analysis |
| :--- | :--- |
| **CyberChef** | Automated link extraction (`Extract URLs`), decoding Base64 / URL Encoding. |
| **sha256sum** | Linux command for generating SHA-256 hash from attached files. |
| **VirusTotal** | Reputation scanner for hashes, domains, IPs, and URLs backed by 70+ AV engines. |
| **Sandbox Environment** | Mandatory isolated environment for downloading and inspecting suspicious attachments. |

---

## 🛡️ Phishing Analysis Tools: Malware Sandboxes (Task 5)

### 📌 1. Lesson Breakdown
Defenders do not require advanced reverse engineering skills to understand a malicious payload completely[cite: 1]. Instead, we use online tools and services known as **Malware Sandboxes**[cite: 1].

---

### 📦 What is a Malware Sandbox and why is it used?
- **Definition:** A controlled virtual environment where suspicious files can be executed and analyzed without risking production systems[cite: 1].
- **What does it enable?**
  - Safely observing the behavior of potentially dangerous files[cite: 1].
  - Identifying external URLs that the malware attempts to contact[cite: 1].
  - Uncovering additional payloads downloaded by the malware in the background[cite: 1].
  - Gathering critical Indicators of Compromise (IoCs)[cite: 1].

---

### 🛠️ Popular Sandbox Tools
- **ANY.RUN (Interactive Sandbox):**
  - **Feature:** Real-time interactive sandbox[cite: 1]. Unlike traditional static sandboxes that only return a final report, ANY.RUN allows live interaction with the virtual machine (clicking, running commands), monitoring processes, network activity, and system changes as they occur[cite: 1].
- **Hybrid Analysis:**
  - **Feature:** Free malware sandbox powered by CrowdStrike Falcon Sandbox[cite: 1]. Provides detailed behavioral reports (system changes, network traffic, IoC extraction)[cite: 1].
- **JOESandbox (Joe Security):**
  - **Feature:** Advanced sandbox performing both static and dynamic analysis on suspicious files and URLs[cite: 1]. Generates in-depth reports highlighting behavior, IoCs, and threat classifications[cite: 1].

---

### 💡 3. Extra Context

#### 🔬 Static vs. Dynamic Analysis in a Sandbox:
- **Static Analysis:** Analyzing a file without executing it (e.g., examining metadata, string extractions, headers, PE sections)[cite: 1]. Fast, but advanced malware can obfuscate its content[cite: 1].
- **Dynamic Analysis (Sandbox):** Executing the file inside an isolated virtual machine to observe real-time behavior (Registry entries, child processes, outbound IP connections)[cite: 1]. This is the core function of tools like ANY.RUN[cite: 1].

#### ⚠️ Anti-Analysis / Anti-Sandbox Techniques:
Modern malware can detect virtualized environments (e.g., checking for mouse movements, hypervisor drivers) and remain dormant to evade detection[cite: 1]. This makes interactive sandboxes like ANY.RUN vital for manually simulating user activity to trigger execution[cite: 1].

---

### 📊 Quick Overview

| Sandbox Tool | Key Feature / Advantage |
| :--- | :--- |
| **ANY.RUN** | Live interactive sandbox (allows clicking, user interaction, and real-time process monitoring). |
| **Hybrid Analysis** | Free automated reporting platform for behavioral, network, and IoC analysis. |
| **JOESandbox** | In-depth static and dynamic analysis with enterprise-grade threat classification reports. |
## 🛡️ Phishing Analysis Tools: Using PhishTool (Task 6)

### 📌 1. Lesson Breakdown
PhishTool is a platform designed to streamline and automate phishing email investigations for SOC analysts, threat intelligence teams, and researchers[cite: 1].

#### 🔍 Key Features of PhishTool:
1. **Centralized Artifact View:** Automatically extracts key artifacts (sender addresses, source IP, embedded URLs) upon uploading an email[cite: 1]. Displays rendered HTML, raw HTML, and raw message source code[cite: 1].
2. **Further Analysis:** Upper tabs provide breakdown views of authentication results (SPF, DKIM, DMARC), transmission paths, and all embedded links/attachments[cite: 1].
3. **VirusTotal Integration:** Seamlessly integrates with VirusTotal to present reputation and detection scores directly within the PhishTool interface[cite: 1].
4. **Case Resolution:** Allows formal documentation of findings, categorizing emails as malicious, flagging key indicators, adding analyst notes, and closing tickets[cite: 1].

---

### 📝 2. Task 6 Question Answer
- **Question:** According to the VirusTotal analysis from above, which vendor categorized the URLs as phishing?[cite: 1]
- 👉 **Answer:** `SafeToOpen`[cite: 1]
- *(Explanation: In the VirusTotal breakdown example inside the task, only one security vendor/antivirus scanner flagged the suspicious URL as phishing: SafeToOpen).*[cite: 1]

---

### 💡 3. Extra Context

#### 📊 Significance of VirusTotal Integration in PhishTool:
- **Time Saving:** Eliminates the need to manually copy addresses into separate browser tabs[cite: 1]. PhishTool queries VirusTotal via API automatically[cite: 1].
- **Early Zero-Day Phishing Detection:** In brand-new phishing campaigns, most engines on VirusTotal might show `Clean` or `Unrated`, while specialized vendors (like SafeToOpen) detect malicious patterns early using heuristics[cite: 1].

---

### 📊 Quick Overview

| PhishTool Feature | Description / Function |
| :--- | :--- |
| **Core Purpose** | Automated triage and analysis of reported phishing emails. |
| **Interface Views** | Rendered HTML, raw message source, header tabs, and extracted link lists. |
| **Integration** | Direct inline VirusTotal reputation checks for URLs and attachments. |
| **Task Finding** | The vendor that categorized the link as phishing was **SafeToOpen**. |

---

## 🛡️ Task 7: Case Study 1 — "Your Account Is on Hold"

### 📌 1. Scenario & Role
- **Role:** Tier 1 SOC Analyst[cite: 1].
- **Context:** Analyzing user-reported spam/phishing emails from the desktop file `Phish3Case1.eml`[cite: 1].
- **Objective:** Examine headers and body content, extract Indicators of Compromise (IoCs), and identify malicious tactics[cite: 1].

---

### 🔍 2. Step-by-Step Question Breakdown

#### ❓ Question 1: What reputable brand is this email tailored to impersonate?
- **Analysis:** Opening `Phish3Case1.eml` displays branding, logos, and text claiming an account holds urgency (`"Your Account Is on Hold"`), asking for immediate verification of billing/account details[cite: 1].
- 👉 **Answer:** `Netflix`[cite: 1]

#### ❓ Question 2: Based on the email headers, who is the intended recipient of this message?
- **Analysis:** Inspect the header field `To:` or `Delivered-To:` to find the exact target email address[cite: 1].
- 👉 **Answer:** `redacted@yahoo.com`[cite: 1]

#### ❓ Question 3: In Thunderbird mail use View → Message Source. What is the Received: from IP address?
- **Analysis:** In the raw message source (`Ctrl + U`), locate the lowest/first originating `Received: from` entry showing the sending mail server's IP address within brackets `[...]`[cite: 1].
- 👉 **Answer:** `10.197.37.234`[cite: 1]

<img width="1098" height="807" alt="image2phishing" src="https://github.com/user-attachments/assets/7f181e4a-62cf-4140-b9af-d93b78ee978e" />

#### ❓ Question 4: Check out the Return-Path field in the message source. What would you consider a domain of interest based on this field?
- **Analysis:** The `Return-Path:` field indicates where bounce-backs are sent[cite: 1]. The domain following the `@` symbol in this field exposes the attacker's domain of interest[cite: 1].
- 👉 **Answer:** `etekno.xyz`[cite: 1]

<img width="1131" height="807" alt="image3phishing" src="https://github.com/user-attachments/assets/bf6e353e-a82a-4793-8657-1443f6b4cbb6" />

#### ❓ Question 5: Investigate the UPDATE ACCOUNT NOW button. What is the shortened URL?
- **Analysis:** Inspecting the HTML code (`<a href="...">`) for the "UPDATE ACCOUNT NOW" button reveals a URL shortener used to conceal the landing page[cite: 1].
- 👉 **Answer:** `https://t.co/yuxfZm8KPg?amp=1`[cite: 1]

<img width="1172" height="251" alt="image4phishing" src="https://github.com/user-attachments/assets/74be9fff-4a5e-4e6d-ba63-ccbc1b507ac2" />

---

### 📝 3. Summary Cheat-Sheet

| Question | Source in `Phish3Case1.eml` | Key Field / Indicator |
| :--- | :--- | :--- |
| **Q1: Reputable brand** | Visual Body / Subject | Brand Logo / Netflix |
| **Q2: Intended recipient** | Message Header | `To:` / `Delivered-To:` (`redacted@yahoo.com`) |
| **Q3: Received: from IP** | Message Source (`Ctrl + U`) | Lowest `Received: from` (`10.197.37.234`) |
| **Q4: Domain of interest** | Message Source | `Return-Path:` domain (`etekno.xyz`) |
| **Q5: Shortened URL** | HTML Body / Link | `<a href="...">` (`https://t.co/yuxfZm8KPg?amp=1`) |
## 🛡️ Task 8: Case Study 2 — "Update Payment Details"

### 📌 1. Scenario & Role
- **Role:** Tier 1 SOC Analyst.
- **Context:** Dynamic analysis of a suspicious PDF attachment from a phishing email impersonating a Netflix payment update notice.
- **Analysis Tool:** ANY.RUN Sandbox (interactive analysis of process trees, network connections, and hashes).
- **Objective:** Evaluate the behavior of the malicious PDF file, extracting MD5 hashes, network connections, destination IP addresses, and domains.

---

### 🔍 2. Step-by-Step Question Breakdown

#### ❓ Question 1: How does ANYRUN classify this suspected phishing email?
- **Analysis:** In the upper-right threat verdict bar of ANY.RUN, the platform assigns a threat classification based on observed behavior.
- 👉 **Answer:** `Suspicious activity`

<img width="705" height="326" alt="image5phishing" src="https://github.com/user-attachments/assets/23f54d82-dedd-4ca4-ae8f-4a0e4bffd2da" />


#### ❓ Question 2: What is the name of the malicious PDF attachment?
- **Analysis:** Located within the main analyzed files panel or object list in ANY.RUN.
- 👉 **Answer:** `Payment-updateid.pdf`

<img width="705" height="326" alt="image6phishing" src="https://github.com/user-attachments/assets/1f612955-34bb-4413-95ad-a036e37ec014" />

#### ❓ Question 3: What is the MD5 hash of the PDF file from Q2?
- **Analysis:** Clicking on the main PDF file object inside ANY.RUN opens the **File Details** panel, revealing cryptographic hashes (MD5, SHA-1, SHA-256).
- 👉 **Answer:** `4a2775eae2ebef41901a3f08d3b857c8`

<img width="705" height="326" alt="image7phishing" src="https://github.com/user-attachments/assets/29f80f08-48ff-4a34-b8bc-3564b18fd5d9" />

#### ❓ Question 4: The PDF attachment contains a malicious URL. What URL is shown in the Processes tab on the right?
- **Analysis:** Opening the PDF reveals embedded links. Under the **Processes** tab on the right side of ANY.RUN, browser execution arguments (`iexplore.exe`) reveal the destination URL.
- 👉 **Answer:** `https://lihi1.com/JqdmJ`

<img width="710" height="848" alt="image8phishing" src="https://github.com/user-attachments/assets/e1251ac1-4f6d-4d6b-8876-0639e9fe080e" />

#### ❓ Question 5: Navigate to the Connections (or DNS Requests) tab. Which IP does the domain from Q4 resolve to?
- **Analysis:** Switching to the **Connections** or **DNS Requests** tab in ANY.RUN lists network requests. Locate the domain from Q4 and check its resolved Destination IP.
- 👉 **Answer:** `35.244.149.249`

<img width="1081" height="375" alt="image9phishing" src="https://github.com/user-attachments/assets/b11051e0-59cd-467c-bef6-2643124f8d1d" />

---

### 📝 3. Summary Cheat-Sheet

| Question | Location in ANY.RUN | Extracted Artifact / Value |
| :--- | :--- | :--- |
| **Q1: Classification** | Top Right Verdict Bar | `Suspicious activity` |
| **Q2: PDF File Name** | Main Object / File Info | `Payment-updateid.pdf` |
| **Q3: MD5 Hash** | File Details Panel | `4a2775eae2ebef41901a3f08d3b857c8` |
| **Q4: Malicious URL** | Processes Tab (Right Panel) | `https://lihi1.com/JqdmJ` |
| **Q5: Resolved IP** | Network $\rightarrow$ Connections/DNS Tab | `35.244.149.249` |

---

## 🛡️ Task 9: Case Study 3 — "Excel Executable"

### 📌 1. Scenario & Role
- **Role:** Tier 1 SOC Analyst.
- **Context:** Investigating a malicious Excel attachment inside ANY.RUN sandbox that executes binary files and attempts to exploit known system vulnerabilities.
- **Objective:** Analyze process trees, identify executed binaries, discover CVE vulnerability IDs, destination domains, and associated network threats.

---

### 🔍 2. Step-by-Step Question Breakdown

#### ❓ Question 1: What is the file name of the malicious Excel attachment?
- **Analysis:** Check the initial process object or starting file listed at the top of the ANY.RUN analysis session.
- 👉 **Answer:** `CBJ200620039539.xlsx`

#### ❓ Question 2: What suspicious process is created after the attachment is launched?
- **Analysis:** Launching the Excel file triggers malicious macros/exploits that spawn a secondary process. In the Process Tree on the right panel, observe the child process spawned by Excel.
- 👉 **Answer:** `EQNEDT32.EXE`

<img width="733" height="645" alt="image10phishing" src="https://github.com/user-attachments/assets/d84f3394-2e5b-4b5c-a958-1e2406831359" />

#### ❓ Question 3: Select the process and open its details ("More Info" menu). What is the full path to the malicious binary?
- **Analysis:** Clicking on `EQNEDT32.EXE` and opening the **More Info** menu displays process metadata, including its file path on disk.
- 👉 **Answer:** `C:\Program Files\Common Files\Microsoft Shared\EQUATION\EQNEDT32.EXE`

<img width="1288" height="775" alt="image11phishing" src="https://github.com/user-attachments/assets/acdf90b0-fdf0-4154-aee4-af89f2b2cb48" />

#### ❓ Question 4: What vulnerability (CVE ID) does this malicious file attempt to exploit?
- **Analysis:** In the process details or threat signature alerts inside ANY.RUN, an Office Equation Editor vulnerability exploitation is detected.
- 👉 **Answer:** `CVE-2017-11882`

<img width="1006" height="617" alt="image12phishing" src="https://github.com/user-attachments/assets/a116e96c-f25e-455a-a290-adba1889146b" />

#### ❓ Question 5: Next, the malicious file downloads COVID-themed malware. What domain was it downloaded from (e.g., malware.thm)?
- **Analysis:** In the **HTTP Requests** or **DNS Requests** tab in ANY.RUN, inspect the outbound network requests retrieving the secondary COVID-themed payload.
- 👉 **Answer:** `biz9holdings.com`

<img width="1100" height="301" alt="image13phishing" src="https://github.com/user-attachments/assets/17c44a03-fef8-4317-a55e-9e6d16bf17a9" />

#### ❓ Question 6: What network threat is associated with the download?
- **Analysis:** ANY.RUN categorizes network signatures (using Suricata/Threat Intelligence rules). Locate the network threat label associated with this download stream.
- 👉 **Answer:** *(Check the exact signature/threat label displayed in your ANY.RUN session network tab, e.g., Executable Download or specific malware family name)*

<img width="1128" height="317" alt="image14phishing" src="https://github.com/user-attachments/assets/31a70ded-b288-4eb9-be2a-8adca0c580c0" />

<img width="1053" height="784" alt="image15phishing" src="https://github.com/user-attachments/assets/690a1b12-9507-44ec-bb44-12bf20478bdf" />

### 📝 3. Summary Cheat-Sheet

| Question | Location in ANY.RUN | Focus / Indicator |
| :--- | :--- | :--- |
| **Q1: Excel File Name** | Main Object / Process Tree | Name of the original Excel file. |
| **Q2: Suspicious Process** | Process Tree (Under Excel) | Process spawned after opening the document. |
| **Q3: Full Binary Path** | "More Info" Menu $\rightarrow$ Process Details | Full path to the executed binary file. |
| **Q4: CVE ID** | Exploit / Signatures Tab | Identifier of the exploited vulnerability. |
| **Q5: Download Domain** | HTTP / DNS Requests Tab | Domain from which the COVID malware was downloaded. |
| **Q6: Network Threat** | Suricata Alerts / Network Tab | Classification of the network threat. |

---

### 💡 4. Extra Expert Context (Malware Analysis Practice)

- **Office Macro & Exploit Malwares (Living off the Land / LOLBins):**
  Attackers frequently use office documents (Word, Excel) because users trust them much more than regular `.exe` files. Through macros (VBA) or exploits targeting known CVE vulnerabilities in Office DLL files, they execute system commands (`cmd.exe` / `powershell.exe`) to download a secondary stage malware (Stager / Payload Drop).
- **The Importance of "More Info" in ANY.RUN:**
  The *More Info* menu is a critical tool for SOC analysts because it provides insight into command-line arguments, child-process hash values, and the full path of created files, which is essential for writing EDR detection rules.

---

## 🛡️ Task 10: Conclusion & Summary

### 📌 1. Room Conclusion & Summary
This final task wraps up the complete practical workshop on phishing analysis, summarizing the key skills acquired throughout the room:
- **Structured Approach:** Developing a systematic method for gathering information when investigating suspicious emails.
- **Tools & Techniques Used:**
  - Analyzing raw email headers (`Message Source` / `Ctrl + U`).
  - Checking the reputation of IP addresses and URL links.
  - Dynamic analysis of attachments and malware through interactive sandbox platforms like ANY.RUN.
- **IoC Extraction (Indicators of Compromise):** Identifying malicious domains, hashes (MD5), processes, and network threats that assist in creating new detection rules within SIEM and EDR systems.

---

### 🚀 2. Recommendations for Further Learning
As part of the SOC Level 1 Path, this module continues with the following practical rooms and challenges highly recommended for advancing your cyber career:
1. **Phishing Prevention**
2. **The Greenholt Phish** (Practical challenge case)
3. **Snapped Phish-ing Line** (Advanced phishing challenge)

---

### 📝 SOC Analyst Cheat-Sheet
A short checklist formula for any future phishing incident:

1. **Header Analysis:** Check `From`, `Return-Path`, `Received` paths, and SPF/DKIM/DMARC statuses.
2. **Content / Link Analysis:** Review the email body, buttons, and hidden URL links without clicking directly (using sandboxes or safe previews).
3. **Attachment Sandbox Analysis:** Safely execute attachments (PDFs, Office documents with macros) in ANY.RUN to uncover spawned processes, CVE exploits, and MD5 hashes.
4. **IoC Action:** Block the corresponding IP addresses, domains, and hashes at network perimeters and endpoints.
