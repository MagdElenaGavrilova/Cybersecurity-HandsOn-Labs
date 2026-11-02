# Phishing Emails in Action

Learn the different indicators of phishing attempts by examining actual phishing emails.

---

## 🎣 Phishing Emails in Action - Introduction (Task 1)

This room focuses on the practical analysis of real phishing samples. The main goal is to hone the skills required to recognize the subtle details that distinguish a legitimate notification from a data theft attempt.

### 🎯 Core Objectives of this Lab:
1. **Social Engineering Recognition:** Analysis of psychological tactics (pressure, urgency, fear).
2. **Link Manipulation Detection:** How hackers mask malicious URLs.
3. **Tracking Pixels:** Understanding the methods attackers use to monitor whether an email has been opened.
4. **Attachment Deconstruction:** Analyzing malicious files without executing them.

---

## 🛒 Fake Order Analysis (Task 2)

Attackers frequently use fake purchase receipts to induce panic in the victim. The primary focus in this scenario is the hidden link behind the "Cancel the order" button.

Analyzing a classic phishing trick (Cancel Your Order): a fake receipt for gift card purchases via PayPal.

The hacker's goal with such an email is to make you panic that someone spent your money, prompting you to hastily click the "Cancel the order" button. When clicked, instead of canceling, you are actually directed to a fake site where your credentials are stolen.

For this question, you need to look into the body (content) of the email on your virtual machine screen and see who is listed as the Merchant where the money was supposedly spent.

### 🛡️ SOC Tool: How to Uncover Shortened Links (URL Shorteners)?
Hackers use URL shortening services (such as bit.ly, tinyurl, and similar) to hide the true destination of a malicious site.

- **How to investigate safely?**
  - Never click directly on the link. Instead, copy it and paste it into specialized tools like WhereGoes (`wheregoes.com`).
- **What does WhereGoes do?**
  - This tool traces the path of the link through all redirects and shows us the final webpage where the hacker intends to take us, without our computer actually opening that dangerous site.

*(Track Your Package)*

---

## 📦 Fake Shipment & Tracking Pixels Analysis (Task 3)

This scenario demonstrates how attackers use fake shipping numbers (Shipping Notifications) to force a quick user reaction.

### 🗺️ Key Technical Detection Concepts:
1. **Link Masking:** The text visible to the user displays a legitimate tracking code, but in the background, the HTML code (`<a href="...">`) does not lead to the post office, but rather to the malicious domain `devret[.]xyz`.
2. **Tracking Pixels:** Hidden, invisible images with a 1x1 pixel size. When the email client loads the image from the attacker's server, it confirms to the hacker that the email was successfully opened and that the victim is active.

> 🛡️ **SOC Note:** Modern security systems and email providers (such as Yahoo, Thunderbird) block the automatic loading of remote images by default precisely to prevent this espionage.

`devret.xyz` is actually a malicious domain (a fake website) that the hacker specifically purchased and registered for this attack.

Let's break this down to see what is hidden behind that name:
1. **Why does it have the `.xyz` extension?**
   - Instead of classic `.com`, `.net`, or `.org` extensions, hackers extremely often use extensions like `.xyz`, `.top`, `.club`, or `.biz`.
   - The reason is simple: These domains are incredibly cheap (sometimes costing less than 1 euro for an entire year). A hacker buys 50 such cheap domains at once, uses them for phishing attacks, and when security companies discover and block them, they simply throw them away and use the next one.
2. **What happens if a user clicks on that link?**
   - If an employee falls for the package email and clicks the link leading to `devret.xyz`, they will not see a package at all. Instead, that link most likely takes them to one of two things:
     - **Credential Harvesting:** A site will open that looks identical to Yahoo, Microsoft 365, or DHL, stating: "To view the package status, log in with your email and password." Once entered, the password goes straight to the hacker.
     - **Malware Delivery:** As soon as the site opens, a dangerous file automatically starts downloading in the background (e.g., `Tracking_Document.exe` masked as a PDF). If the user opens that file, their computer gets infected with malware.
3. **How do we treat this as SOC analysts?**
   - When we find this domain `devret.xyz` in the source code, it acts as an **IOC (Indicator of Compromise)**, meaning proof of an attack.
   - Our next steps in the SOC center would be:
     1. Copy the domain.
     2. Submit it to VirusTotal or UrlScan.io to check its reputation and whether it is already flagged as phishing.
     3. Immediately add it to the corporate firewall's blocklist so no other employee can accidentally open that site.

Therefore, `devret.xyz` is essentially the hacker's "trap"—a fake and dangerous site hidden behind nice text about package tracking!

*(Download Document Here)*

---

## 📑 Credential Harvesting via Layered Brands (Task 4)

This attack demonstrates how hackers exploit the reputations of well-known professional services (OneDrive, Adobe, Microsoft) to build a false sense of trust with the victim.

### 🔑 Key Term: Credential Harvesting
- **Definition:** An attack whose sole purpose is the mass collection (harvesting) of usernames and passwords through the deployment of fake login portals.
- **How to recognize it in SOC:** No matter how real the Microsoft or Adobe logo looks on the screen, always check the URL in the browser. If the site asks for an Outlook login, but the URL shows a strange domain, it is a surefire sign of Credential Harvesting.

> ⚠️ **Modern Challenge:** With Artificial Intelligence (AI), attackers today generate flawless text without typos or grammatical errors, making these fake portals even harder to spot with the naked eye.

### 🔑 Correct Answer for Task 4:
- **Question:** The attacker deployed a fake portal to capture and exfiltrate user credentials. What is this type of attack called?
- **Answer:** `credential harvesting`

### 🕵️‍♀️ Why do hackers use this complicated chain?
This is a classic question whose logic SOC analysts must understand:
1. **Bypassing Email Filters:** If a hacker places a direct link to their dangerous site in the email, corporate security systems (like Microsoft Defender or Cisco Talos) will immediately recognize the domain and throw the email into Spam. However, if the first link leads to a semi-legitimate or hacked site with a good reputation, the filter will let the email pass into the employee's inbox.
2. **Psychological Layers (False Sense of Security):** When the user clicks and sees a OneDrive logo, followed by an Adobe logo, their brain automatically relaxes because they recognize these brands as "safe."
3. **The Final Trap (The Front System):** When the login form finally appears, it looks like a real Outlook instance, but in the background, there is no password-checking code. Whatever the user types (even total nonsense), the site will display "Incorrect password, try again" or a general error. Why? Because the background script simply takes the characters you enter and sends them via a quick script straight into the hacker's database.

*(Download Document Here)* deals with an extremely dangerous and smart hacker tactic known in reality as **Multi-stage Phishing**.

Here, the attacker does not take you immediately to the dangerous site. Instead, they create a redirection chain: first taking you to a page resembling OneDrive, then when you click Download there, transferring you to a page looking like Adobe Document Cloud, and finally opening a fake login window (Outlook) requesting your password to "unlock" the file.

The purpose of this entire setup is to steal your username and password.

*(Your Account Is on Hold)* is a classic example of a phishing attack targeting regular consumers, spoofing Netflix (written as `Netllx` with two small lowercase "l"s instead of "fli" to deceive you at first glance).

The major difference in this attack is that the hacker did not put a link directly in the email. Instead, they attached a PDF document.
- **Why do that?** Because basic email filters immediately scan links in text, but have a harder time reading links hidden inside attachments. When you open that fake PDF, it contains a large "Update Payment Account" button inside, which takes you to their card-stealing site.

---

## 🎬 Phishing via Fake Netflix and Attachments (Task 5)

This attack uses a combination of social engineering techniques and technical hiding of malicious links.

### 🎭 Key Attacker Tactics:
1. **Display Name Spoofing:** The attacker sets the profile name as `Netllx billing`, but the actual email behind it is `z99@musacombi.online`. This is the first layer of deception.
2. **Attachment Smuggling (Hiding links in PDF):** Security filters more easily detect bad links if they are directly in the email text. Therefore, the hacker puts "clean" text in the email, but attaches a PDF document containing a hidden link to the fake payment page.
3. **Artificial Urgency and Fear:** The message claims the account is suspended to force the user to act in panic without thinking.

*(Your Recent Purchase)*

---

## 🍏 Blank Email and Fake Apple Receipt (Task 6)

This attack uses specific technical manipulations to bypass corporate defense.

### 🕵️‍♀️ New Phishing Components:
1. **Blank Body:** There is no content in the message. This is used to trick email scanner algorithms that analyze text for phishing phrases.
2. **Use of BCC (Blind Carbon Copy):** The victim is placed in a hidden copy. Attackers do this to send the exact same email to thousands of victims simultaneously while hiding the recipient list.
3. **Suspicious Extension (`.dot`):** Instead of a PDF, a `.dot` file (Microsoft Word Template) is attached. When the user opens the document and clicks the image inside, a link is activated that leads them to a sophisticated data-stealing page (Phishing Site).

### 🔑 Correct Answers for Task 6:
1. **Question 1:** What does the acronym BCC stand for?
   - **Answer:** `Blind Carbon Copy`
   - **Explanation:** This means "Hidden copy." The hacker put you in the BCC field so you couldn't see that the exact same email was sent to thousands of other people at once.
2. **Question 2:** What is the file extension of the attachment?
   - **Answer:** `.dot`
   - **Explanation:** This is an extension for a Microsoft Word Template. This is a massive red flag, because real Apple receipts always arrive as clean HTML text in the email or eventually as a regular `.pdf`, never as a Word document that can contain dangerous macro-viruses!
3. This scenario is fascinating because the hacker uses a completely blank email body. There is not a single letter of text in the message body! The attacker intentionally does this to confuse automated spam filters looking for "dangerous words" in the text. The hacker's entire safety relies on the user being overly curious and opening the attached file, which is masked as an Apple Support invoice.

*(Scheduled Shipment)*

---

## 📦 DHL Impersonation and Malware Delivery (Task 7)

This attack is a prime example of malware delivery via office documents (Office Documents Malware Delivery).

### 🚩 Geographic Anomalies (Conflicting Markers):
In this scenario, the attacker made massive amateur mistakes that immediately give them away:
- The email domain is German (`.de`).
- The invoice is addressed to a city in India.
- The content inside the Excel document itself is written in Mandarin (Chinese).
- The conflict of different languages and locations in the exact same email is a huge red flag!

### ☣️ Payload Activation (`regasms.exe`):
When the victim interacts with the document, it attempts to download and launch the program `regasms.exe`. If the attack succeeds, the consequences are:
1. **Persistence:** The hacker creates a script so the virus starts automatically every time the computer boots up.
2. **Data Exfiltration:** Stealing all local files and saved passwords from browsers.
3. **Ransomware:** Locking (encrypting) the entire operating system and demanding a ransom in cryptocurrencies.

### 🔑 Correct Answer for Task 7:
- **Question:** What is the name of the executable that the Excel attachment attempts to run?
- **Answer:** `regasms.exe`

Task 7 (Scheduled Shipment)! This is actually the most serious scenario so far, because it is not about credential harvesting, but about directly infecting the computer with a virus (Malware Delivery).

Here, the attacker masks an Excel document (`.xlsx`) as a DHL invoice. When the user opens the document and clicks the inner link, Excel attempts to automatically download and activate an executable file in the background (a program with a `.exe` extension). If this succeeds, the hacker gains full control over the computer.
