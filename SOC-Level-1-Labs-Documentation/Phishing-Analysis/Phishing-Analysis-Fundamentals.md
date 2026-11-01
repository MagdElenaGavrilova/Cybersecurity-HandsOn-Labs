# 📨 Phishing Analysis Fundamentals

Learn all the components that make up an email.

---

## 📌 Phishing Analysis Fundamentals - Introduction (Task 1)

Phishing and Spam are the most common forms of Social Engineering faced by modern organizations. It takes just one careless employee clicking a malicious link or opening a weaponized attachment to grant an attacker unauthorized access to the internal network.

### 🎯 Core Objectives of this Module:
1. **Email Delivery Fundamentals:** How emails travel from point A to point B.
2. **Header Analysis:** Uncovering the true identity and origin of an email.
3. **Email Body Investigation:** Analyzing links and hidden elements.
4. **Phishing Types:** Understanding the various tactics utilized by threat actors.

---

## 🗺️ Anatomy of an Email Address (Task 2)

Every email address is divided into two primary components by the `@` symbol (first introduced by Ray Tomlinson in the 1970s on ARPANET):

1. **Username:** The segment before the `@`. It identifies the specific mailbox of the user *(Analogy: The name of the person you are sending a letter to)*.
2. **Domain Name:** The segment after the `@`. It specifies the server responsible for receiving the messages *(Analogy: The street address and building number where the person resides)*.

> ⚠️ **SOC Warning (Typosquatting):** Always scrutinize the domain name in detail! Attackers frequently alter one or two characters to deceive users (e.g., `tryhatme.com` instead of `tryhackme.com`).

### Question Walkthrough:
- **Prompt:** Identify the domain used in the following email address: `hatsalesman@tryhatme.com`
- **Answer:** `tryhatme.com`

---

## 📬 Email Protocols and Journey (Task 3)

For an email to successfully travel from point A to point B, three core protocols collaborate behind the scenes:

| Protocol | Function | Key Characteristic |
| :--- | :--- | :--- |
| **SMTP** | Sends emails | Transfers the email from the client to the server and between mail servers. |
| **POP3** | Downloads emails | Downloads messages to a single device and deletes them from the server. |
| **IMAP** | Syncs emails | Retains messages on the server, enabling synchronization across multiple devices (phone, laptop, etc.). |

### 🗺️ The Journey of an Email:
1. The user clicks **Send** ➡️ The email travels to the mail server via **SMTP**.
2. The sending server queries **DNS** (looking up **MX records**) to identify the recipient's mail server.
3. The email is transferred over the Internet to the recipient's mail server.
4. The recipient opens their mail client, and the email is synced via **IMAP** (or downloaded via **POP3**).

When you send an email, several protocols work together behind the scenes to deliver your message from sender to recipient, and each protocol has a specific role:
- **Simple Mail Transfer Protocol (SMTP):** Sends emails
- **Post Office Protocol (POP3):** Downloads emails to a device
- **Internet Message Access Protocol (IMAP):** Syncs emails across devices

### 🔑 Verified Task 3 Answers:
1. **Question 1:** Which protocol is responsible for sending an email from a client to a mail server?
- **Answer:** `SMTP` *(Simple Mail Transfer Protocol - whenever sending data, it routes through SMTP).*
2. **Question 2:** Which service is used to look up the recipient domain’s mail server?
- **Answer:** `DNS` *(Domain Name System - the server queries DNS for MX records to determine destination routing).*
3. **Question 3:** Bob wants to access his email from multiple devices, including his phone and laptop. Which protocol should he use?
- **Answer:** `IMAP` *(Because IMAP synchronizes and stores emails on the server, unlike POP3 which downloads to a single device and removes them from the server).*

---

## 🔍 Header Analysis & Raw Source Code (Task 4)

### E-Mail Headers
Every email consists of two parts: **Header** (metadata, routing details, server logs) and **Body** (content, plain text, HTML). Headers are the core focus for detection engineering because they contain technical details hidden from standard users.

### ⌨️ SOC Analyst Shortcut (Thunderbird):
- Use the shortcut **`Ctrl + U`** (or **View ➡️ Message Source**) to open the raw, unrendered source code of the email (**Raw Message Source**).

### 🏷️ Critical Header Fields:
- **`Subject:`** The full subject line of the email *(attackers often use urgency triggers like "Urgent" or "Action Required")*.
- **`X-Originating-IP:`** Reveals the actual IP address of the client system from which the attacker initiated the email before it traversed intermediary relay servers.

---

## 📄 Email Body & Attachment Analysis (Task 5)

### E-Mail Body
Emails utilize the **MIME** standard to transmit media, formatting, and documents within a text-based protocol format. When inspecting raw email source code, an attachment is identified by three specific headers:

- **`Content-Type:`** Defines the file format (e.g., `application/pdf` for PDF or `image/png` for images).
- **`Content-Disposition:`** Contains the `attachment` flag and reveals the original file name (`filename="document.pdf"`).
- **`Content-Transfer-Encoding: base64`** ➡️ Indicates that the payload underneath is the file binary encoded as plain text (**Base64**).

🛡️ **SOC Tradecraft:** If security controls (such as secure email gateways or AV) miss a malicious email, a SOC analyst can manually extract this Base64 string, paste it into **CyberChef** using the **`From Base64`** recipe, and safely reconstruct the original file for isolated sandbox analysis.

### 🍳 Real-World Resolution (CyberChef Walkthrough):
To perform this procedure live:
1. Copy the large Base64 encoded block located underneath the line `Content-Transfer-Encoding: base64`.
2. Paste the string into the **Input** panel in CyberChef.
3. Drag the **`From Base64`** recipe from the operations menu into the **Recipe** pane.
4. Click the **Save file to file / Download** button in the **Output** panel to save it as a PDF file. Opening this reconstructed PDF reveals the flag.

---

## 🎣 Phishing Classifications & Analysis (Task 6)

Email-based attacks are categorized based on targeting precision and execution methodology:

- **Spam:** Mass, unsolicited bulk emails sent to millions of addresses simultaneously (typically promotional, but classified as Malspam when distributing malware).
- **Phishing:** Broad attacks spoofing reputable brands (e.g., PayPal, Netflix) to harvest user credentials.
- **Spear Phishing:** Highly targeted attacks focused on a specific individual or enterprise. The attacker performs pre-attack reconnaissance to personalize the campaign.
- **Whaling:** A specialized subset of Spear Phishing exclusively targeting C-level executives (CEO, CFO) to execute high-value wire fraud or exfiltrate state secrets.
- **Smishing / Vishing:** Phishing conducted via SMS text messages (Smishing) or live voice calls (Vishing).

### 🩺 SOC Culture: Defanging Artifacts
When documenting IP addresses, URLs, or malicious indicators in incident reports, always defang them using CyberChef to prevent accidental execution or clicking:

- `http://paypal-secure.com` ➡️ `hxxp[://]paypal-secure[.]com`
- `192.168.1.150` ➡️ `192[.]168[.]1[.]150`

---

## 🏢 Business Email Compromise (BEC)

Business Email Compromise (BEC) is a sophisticated attack vector where a threat actor gains unauthorized access to a legitimate corporate email account (typically via prior phishing, credential stuffing, or session hijacking).

### 🎯 How BEC Operates in Practice:
The attacker does not use a fake or spoofed domain; instead, they communicate directly from an authentic corporate mailbox. They quietly monitor active email threads and intercept financial transactions. At the critical moment of invoice processing, they insert themselves into the dialogue:

> *"We have updated our banking details. Please process all future payments to this new account number."*

Because the email originates from a trusted internal or partner address, employees frequently comply without verification, resulting in significant financial loss.

---

## 🔤 Key Concepts & Terminology Summary

`Phishing` | `Spam` | `SMTP` | `POP3` | `IMAP` | `Typosquatting` | `Header Analysis` | `X-Originating-IP` | `Base64 Decoding` | `CyberChef` | `Spear Phishing` | `Whaling` | `BEC` | `Defanging`
