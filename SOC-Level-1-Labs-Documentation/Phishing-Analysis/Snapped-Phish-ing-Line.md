# Snapped Phish-ing Line Walkthrough

Apply learned skills to probe malicious emails and URLs, exposing a vast phishing campaign.

## 📋 Scenario Overview
As part of the IT team at SwiftSpend Financial, you are facing a serious incident. Multiple employees have received suspicious phishing emails, and some of them have already submitted their credentials, losing control over their accounts. Your task is to analyze the emails, investigate the redirection links, download the phishing kit, uncover the stolen data, and find the secret flag.

---

## 🔍 Questions and Step-by-Step Walkthrough

### 📌 1. Which individual received the email regarding a Quote for Services Rendered?
- **How to find it:**
  1. Start the machine and open the `phish-emails` folder on the desktop.
  2. Review the files or open them with a text editor / Thunderbird to find the email whose `Subject:` contains "Quote for Services Rendered".
  3. Look at the `To:` field or the recipient's name in that email.
- **🎯 Answer:** William McClean

<img width="800" height="578" alt="q1 phish " src="https://github.com/user-attachments/assets/f469f816-7cb1-488f-8fb3-5069f60cf30f" />

---

### 📌 2. What email address was used by the adversary to send the phishing emails?
- **How to find it:**
  - Analyze the headers. Look at the `From:` field to see the spoofed address used by the attacker to send the messages.
- **🎯 Answer:** Accounts.Payable@groupmarketingonline.icu

<img width="800" height="578" alt="q2 " src="https://github.com/user-attachments/assets/114a01d4-aca6-478f-8b9a-5d9a7f2ff826" />

---

### 📌 3. Investigate the attachment in the email addressed to Zoe Duncan. What is the root domain of the redirection URL found within the file?
- **How to find it:**
  1. Open the attachment from Zoe Duncan

<img width="800" height="453" alt="q 3   1 " src="https://github.com/user-attachments/assets/0e3db9aa-bf86-42c8-91a5-1cfb4e767c55" />

<img width="800" height="558" alt="q 4 1 " src="https://github.com/user-attachments/assets/b302db4a-9759-4de7-8741-6f0dd4eaaf2c" />

- **🎯 Answer:** You open CyberChef in the web browser and open the previously downloaded file.
  - `kennaroads.buzz`

<img width="800" height="454" alt="q 4 2" src="https://github.com/user-attachments/assets/d63194f8-8138-492a-ad33-d0c411e9d940" />

<img width="800" height="549" alt="q 4 3" src="https://github.com/user-attachments/assets/b4787cbb-5802-40d4-9d54-a9cdb1faf9e8" />

---
  
  ### 📌 4. Open the attachment in your VM web browser. Which company is the login page impersonating?
  - **🎯 Answer:** Microsoft. The word itself in the path (`office365`) reveals the company and service that the fake page is trying to impersonate in order to steal user credentials.
  - Microsoft

<img width="800" height="456" alt="q 4 " src="https://github.com/user-attachments/assets/ff2ddfab-a148-4bea-84b2-ab3484ded47a" />

---

### 📌 5. Let’s check if the attacker left any files exposed on the same website. Navigate to the /data directory. What is the name of the archive file?
- **How to find it:**
  - In the web browser (or via a direct link path on the attacker's server), open the `/data/` path and check the publicly accessible files (directory listing). Look for the archive.
- **🎯 Answer:** Update365.zip

<img width="800" height="477" alt="q 5 " src="https://github.com/user-attachments/assets/ac48279f-b786-42d8-bc89-bdb0dd2f9a3a" />

---

### 📌 6. Download the phishing kit archive to your virtual environment. Using the sha256sum command, what is the SHA256 hash of the file?
- **How to find it:**
  1. Download the archive to your virtual environment (e.g., in `/home/ubuntu/Desktop` or via `wget`).

<img width="800" height="577" alt="q 6 1 true " src="https://github.com/user-attachments/assets/aea5f1be-1db0-4f09-9547-0160c6162539" />

  2. Open the terminal and calculate the hash with the command:
     sha256sum 'Update365.zip'
- **🎯 Answer:** ba3c15267393419eb08c7b2652b8b6b39b406ef300ae8a18fee4d16b19ac9686

<img width="800" height="502" alt="q 6 2 " src="https://github.com/user-attachments/assets/d6282fec-44c5-49b6-8c1a-eb1c9df1cde8" />

---

### 📌 7. Investigate the file hash from the previous question using VirusTotal. Aside from phishing, what other threat category is assigned to the ZIP archive?
- **How to find it:**
  - Enter the obtained SHA256 hash into the VirusTotal search bar and check the threat categorization (Threat Names / Categories). Aside from phishing, antivirus benchmarks assign another classification.
- **🎯 Answer:** Trojan *(or another malware category listed on VirusTotal)*

<img width="800" height="373" alt="q 7 right" src="https://github.com/user-attachments/assets/ae798a0b-9b1e-4c19-8666-5f799f8163b8" />

---

### 📌 8. Review the VirusTotal Details page for the phishing kit. How many files are contained within the archive?
- **How to find it:**
  - On VirusTotal, open the "Details" tab.
- **🎯 Answer:** 49

<img width="800" height="573" alt="q 8" src="https://github.com/user-attachments/assets/96641143-6209-4430-96c3-da2e17ca3e98" />

---

### 📌 9. Let’s see if the attacker has exposed any captured credentials. Navigate to the /data/Update365/ directory and investigate the log file. What is the email address of the user who submitted their credentials more than once?
- **How to find it:**
  1. Via web or direct access to the attacker's server, open the `/data/Update365/` path.
  2. Locate the log file where the stolen data is stored (e.g., `log.txt` file).
  3. Read its contents and see which user submitted credentials multiple times.
- **🎯 Answer:** michael.ascot@swiftspend.finance

<img width="800" height="553" alt="q 9" src="https://github.com/user-attachments/assets/94b63e08-4fa8-4ec1-8b9b-1ac7d7ce6d03" />

---

### 📌 10. Extract the phishing kit archive and locate the submit.php file. What email address is used by the adversary to collect compromised credentials?
- **How to find it:** There are two ways: manually or via terminal.
  1. Extract the archive locally in the terminal:
     unzip Update365.zip
  2. Open the `submit.php` file with a text editor: navigate to the directory where you extracted the zip and type:
     nano submit.php
  3. Locate the line where the script sends an email or forwards the stolen data (look for the email variable, e.g., `mail()`, `$to = ...`).
- **🎯 Answer:** m3npat@yandex.com

<img width="800" height="566" alt="q 10 1 " src="https://github.com/user-attachments/assets/0d4bc21b-5129-4988-b93e-d0de72c7775d" />

- Or manually find it in the `Validation` folder where the zip file was extracted.

<img width="800" height="545" alt="q 10" src="https://github.com/user-attachments/assets/eb178303-b55e-41b9-a67a-db7d160a95c9" />

---

### 📌 11. Return to the phishing URL and locate the flag.txt file. What is the secret value?
- **How to find it:**
  1. Open the path `http://kennaroads.buzz/data/Update365/office365/` on the server in your browser and append `/flag.txt`.
  2. It contains encoded text (Base64).
  
  <img width="800" height="418" alt="q 11" src="https://github.com/user-attachments/assets/22c643a7-a3e8-4365-bb6a-7e320500cd87" />

  4. Use CyberChef to decode the text. Since you will get a reversed flag, add the `Reverse` operation in CyberChef to decode the text and get the secret value (flag).
- **🎯 Answer:** THM{pL4y_w1Th_tH3_URL}

<img width="800" height="284" alt="q11 reverse " src="https://github.com/user-attachments/assets/3b85f728-bd7d-4d61-af49-d0f834fd1c4d" />

<img width="800" height="289" alt="q 11 final flag " src="https://github.com/user-attachments/assets/a762ba73-4629-415a-be06-87eb4aa05113" />

---

## 🔥 EXTRA FROM ME: SOC Analyst Playbook for Phishing Kits

As seen in this exercise, attackers frequently utilize pre-built phishing kits (such as `Update365` here):

1. **Why do attackers leave logs exposed publicly (`/data/`)?**
   Phishing scripts are often poorly configured. Attackers frequently forget to set up protection (`.htaccess` rules or path restrictions), allowing anyone visiting that path to view stolen credentials from other victims. This serves as a critical vulnerability that you, as a SOC analyst, can leverage for quick triage and identification of compromised employees!

2. **The role of `submit.php`:**
   In phishing kit forensics, `submit.php` represents the core execution mechanism. It is the PHP script responsible for extracting the username and password from the login form and either appending them to a local `.txt` file on the server or exfiltrating them via email to the attacker. Locating that destination address in the code helps identify threat actor infrastructure (C2 / adversary infrastructure).
