 # The Greenholt Phish
# Use the knowledge attained to analyze a malicious email.

## Task 1 Analysis

📋 **Scenario Overview**
A sales executive at Greenholt PLC received a suspicious email from a supposedly known client. The email message contains several classic security red flags: a generic greeting, an unexpected request for a money transfer, and an unwanted attachment. The email was escalated to the SOC (Security Operations Center) team for in-depth forensic analysis.

---

🔍 **Questions and Step-by-Step Walkthrough**

📌 **1. What is the Transfer Reference Number listed in the email's Subject line?**
- **How to find it:** Check the subject line of the email headers.
- 🎯 **Answer:** `09674321`
- 
- <img width="800" height="317" alt="q1 greenholt" src="https://github.com/user-attachments/assets/c9b5ae1e-78c9-4081-9e6b-bc1bfb6a7e2b" />

📌 **2. What is the display name of the sender?**
- **How to find it:** Check the `From` header field in the email message source.
- 🎯 **Answer:** `Mr. James Jackson`

<img width="800" height="317" alt="q2 greenholt " src="https://github.com/user-attachments/assets/87b80215-3ffa-4029-bec7-14fb686623d0" />

📌 **3. Continue investigating the email headers. What is the sender's email address?**
- **How to find it:** Extract the email address enclosed in angle brackets within the `From` header.
- 🎯 **Answer:** `info@mutawamarine.com`

<img width="800" height="317" alt="q3 greenholt" src="https://github.com/user-attachments/assets/51d5615f-9747-4fdd-b820-280fe260d967" />

📌 **4. What email address will receive a reply to this email?**
- **How to find it:** Look at the headers for the specific `Reply-To:` field. Attackers often set a different reply address to collect responses from victims instead of the legitimate domain owner.
- 🎯 **Answer:** `info.mutawamarine@mail.com`

<img width="800" height="317" alt="q4 greenholt" src="https://github.com/user-attachments/assets/17a29c84-e47d-48f9-bcda-abc888004d86" />

📌 **5. Begin analyzing the message source. What is the originating IP address of this email?**
- **How to find it:** Analyze the `Received:` headers from bottom to top (the first `Received` line from the bottom shows the original sending source). Find the IP address of the first server that received the email.

<img width="800" height="577" alt="phishi 1  1" src="https://github.com/user-attachments/assets/d090c502-58ad-431e-b56a-9dff37dc5c6e" />

  - *Note:* Scroll down when the message source window opens after clicking "View Source" to find the originating IP address.
- 🎯 **Answer:** `192.119.71.157`

<img width="800" height="646" alt="chall phishing 1 " src="https://github.com/user-attachments/assets/2f5d396d-7b25-4058-9a66-05fa3000d193" />

📌 **6. Investigate the IP from the previous question with WHOIS search. Who is the owner (organization) of that IP address?**
- **How to find it:** Open `www.whois.com` in a web browser and perform a WHOIS lookup for that IP address, or run the terminal command:
  
  whois 192.119.71.157

Alternatively, check via network tools/scripts and look for the `OrgName:` or `Organization:` field.
* **🎯 Answer:** HostPapa

<img width="800" height="557" alt="q5 greenholt" src="https://github.com/user-attachments/assets/68618aa8-3061-4f26-850d-5f5efb1d3bd7" />

---

### 📌 7. Run an SPF record check on the Return-Path domain identified in the email headers. What is the full SPF record for this domain?
* **How to find it:** Find the domain from the `Return-Path:` field. Then, in the terminal, perform a DNS lookup for the TXT records of that domain using `dig` or `nslookup`:
  ```bash
  dig TXT mutawamarine.com
Or using:
`nslookup -type=txt mutawamarine.com`

- Tip: SPF records are always tied to the base domain (or the subdomain from which mail is sent), not the full email address.
- 🎯 Answer: v=spf1 include:spf.protection.outlook.com -all

<img width="800" height="409" alt="q8 greenholt" src="https://github.com/user-attachments/assets/fa31da3a-e8c4-4f4a-bfcb-863f83cd4d02" />

📌 8. Perform a DMARC lookup for the Return-Path domain found in the email headers. What is the complete DMARC record for this domain?
- How to find it: DMARC records are hosted under the _dmarc.<domain.com> subdomain. Perform a DNS lookup for TXT records on that subdomain:
  ```bash
  dig TXT _dmarc.mutawamarine.com

- 🎯 Answer: `v=DMARC1; p=quarantine; fo=1`

<img width="800" height="427" alt="q9 greenholt" src="https://github.com/user-attachments/assets/64369907-54d5-4bda-aeb3-c849a458e52f" />

📌 9. What is the file name of the attachment found in the email?
- How to find it: Inspect the MIME parts or attachment headers within the message source.

- 🎯 Answer: `SWT_#09674321____PDF__.CAB`

<img width="800" height="532" alt="q10 1 greenholt" src="https://github.com/user-attachments/assets/3027c49c-82d8-426d-83d5-701d57fd40aa" />

📌 10. Download the attachment to your virtual environment. Using the sha256sum command, what is the SHA256 hash of the file?
- How to find it:
1. Save the attachment from the email.

<img width="800" height="564" alt="q10 greenholt" src="https://github.com/user-attachments/assets/72cf0cb6-f904-4013-b6bb-5994c46892a7" />

  1. In the terminal where the file is saved, run the following command:

`sha256sum SWT_#09674321____PDF__.CAB`

- 🎯 Answer: 2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f (Copy the exact output from your terminal)

<img width="800" height="471" alt="q11 greenholt" src="https://github.com/user-attachments/assets/7f9e79e7-720b-4e78-ae9d-b96bcb1449ed" />

📌 11. Investigate the file hash from the previous question using VirusTotal. What is the attachment's file size in KB (e.g., 122.31 KB)?
- How to find it: Enter the obtained SHA256 hash into the VirusTotal search engine, or check the properties of the extracted file locally on the system (ls -lh or via a file manager). The exact file size is listed under the "Details" tab on VirusTotal.

- - 🎯 Answer: `400.26 KB`

<img width="800" height="389" alt="q 12 true KB" src="https://github.com/user-attachments/assets/06775cc7-8664-4efc-b3a0-3b6daef45420" />

  - Note: When checking via terminal using ls -lh, the size might show as `401K`, but VirusTotal provides the precise measurement.

<img width="800" height="309" alt="q12 wrong KB" src="https://github.com/user-attachments/assets/0a1aca3d-8da1-426f-84fc-011ce36ac984" />

📌 12. Continue your research on the file. What is the actual file type of the attachment?

- How to find it: Run the following command in Linux to reveal the true nature of the file:

`file SWT_#09674321____PDF__.CAB`

<img width="760" height="237" alt="last q 13" src="https://github.com/user-attachments/assets/11a1d978-f874-4620-ab7e-021d4b3733ef" />

Or check the File Type / Magic section on VirusTotal.

- 🎯 Answer: `RAR`

<img width="800" height="385" alt="last q virustotal" src="https://github.com/user-attachments/assets/ba025633-445e-4f09-951e-c8060298aadd" />

---
🔥 EXTRA FROM ME: SOC Analyst Playbook for this Scenario
As you prepare for real SOC Level 1 tasks, keep these three key things in mind when investigating phishing campaigns involving malware attachments:
1. Typosquatting:
2. Notice that the sender's domain in this task likely contains a hidden deception (e.g., substituting the lowercase letter 'l' with an uppercase 'I', or similar visual lookalikes). Always carefully inspect the characters in the sender's domain (Domain Spoofing / Typosquatting).
3. Double Extensions:
4. Attackers frequently use tricks where a file is named like Invoice.pdf.exe. The Windows operating system hides known extensions by default, so users only see Invoice.pdf and assume they are opening a safe document, while in the background a .exe file executes, installing a Trojan or malware.
5. Why do we check SPF and DMARC?
6. If a DMARC policy is set to p=reject or p=quarantine, and an email arrives from an unauthorized IP address (such as the WHOIS-verified hosting provider in this case), legitimate mail servers will automatically drop or quarantine it before it ever reaches the user. This helps us confirm that the domain has been spoofed.
