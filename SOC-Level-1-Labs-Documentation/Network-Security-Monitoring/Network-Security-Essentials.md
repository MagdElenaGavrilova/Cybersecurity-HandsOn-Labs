# Network Security Essentials: Monitoring and Protecting Against Adversaries

Learn about key aspects of network security essentials and how to monitor and protect against adversaries.

---

## (A Network - Overview)

### 🌐 Network Architecture: Building Blocks and Security Risks
Every company, whether small or a large corporation, relies on the same basic structure to communicate. For a SOC analyst, every device is both a potential source of logs and a potential target.

#### 🖥️ 1. User Workstations (Endpoints)
These are the computers and laptops employees use every day (Windows, macOS, Linux).
- **Why are they a target?** They are the weakest link in the chain. People easily fall victim to social engineering (phishing emails, fake links, downloading infected files).
- **What is the hacker looking for here?** Initial access. Once an employee's computer is infected (for example, in finance or HR), the attacker has an open door to start moving laterally across the network.
- **SOC focus:** Monitoring computer processes (via EDR) and network connections to spot weird commands in PowerShell or Command Prompt.

#### 🗄️ 2. File & Database Servers
The place where the company's entire "treasure" is stored—contracts, customer databases, financial reports, patents.
- **Why are they a target?** This is where the money is.
- **What does the hacker look for here?**
  - **Data Exfiltration:** Copying data to sell on the black market or blackmail the company.
  - **Ransomware:** Encrypting (locking) all files to demand a ransom in cryptocurrencies for their decryption.
- **SOC focus:** Tracking who accesses these folders at odd times of the day or whether massive amounts of files are being downloaded all at once.

#### 🌐 3. Application Servers
Servers hosting websites (Web), corporate emails (Email), or VPN gateways for remote employees.
- **Why are they a target?** Because these servers must be publicly accessible on the internet for clients or employees to use.
- **What is the hacker looking for here?** Software vulnerabilities like SQL Injection, outdated software, or poor configurations to execute remote code (RCE - Remote Code Execution).
- **SOC focus:** Monitoring application logs, brute-force login attempts on VPN or mail, and strange external IP addresses.

#### 🔑 4. Active Directory (AD) / Authentication Servers
The "brain" for managing user profiles. It controls who has access to which devices and files in the company.
- **Why are they a target?** This is the ultimate goal of every hacker once they enter the internal network.
- **What is the hacker looking for here?** Privilege Escalation. The goal is to become a Domain Administrator. Once the hacker obtains those administrator credentials, they have full, unrestricted control over every single computer in the company.
- **SOC focus:** Tracking failed login attempts (password spraying), creation of new admin profiles out of nowhere, or logins at unusual times.

#### 🛣️ 5. Routers & Switches (Network Infrastructure)
- **Routers:** Connect the internal local area network (LAN) to the outside world (the internet) and route traffic.
- **Switches:** Connect all devices in the same room/building so they can communicate with each other.
- **What does a hacker do if they compromise them?** They can intercept all network traffic (Man-in-the-Middle) or redirect traffic to rogue servers.

#### 🛡️ 6. Firewalls
The gatekeeper. It controls traffic between the secure internal network and the insecure internet based on defined rules.
- **Why is it important in the SOC?** This is our first security shield. Firewall log files are the first place we see port scans (like the ones analyzed in Tasks 4 and 5) or blocked intrusion attempts.

---

## (Network Visibility)

There is a golden rule in cybersecurity:
> *"You can't defend what you can't see."*

Network visibility explains the essence of detection. Attackers always leave traces (so-called digital crumbs), and our job as analysts is to know where to look for them.

### 👁️ Network Visibility: A SOC Analyst's Eyes
Imagine a building where a burglary took place:
- If you only look at the security cameras on the street and front door, you will know when the thief entered, but you won't know which safe they opened and what documents they took.
- If you only look at the sensors on the safes themselves, you will know someone opened them, but you won't know how that person entered the building.

Therefore, in cybersecurity, we combine the two main sources of logs:

#### 💻 1. Host-Centric Logs (Inside the room)
These logs are generated directly on the device itself (computer, laptop, or server). They tell us what exactly happened inside the operating system.
- **Key sources:**
  - **Operating System Logs:** Windows Event Logs (most importantly Event ID 4624 for successful logins and Event ID 4625 for failed attempts), Linux Syslog (where commands executed with `sudo` are recorded).
  - **Application Logs:** Web server logs (Apache, Nginx) showing visited web pages or database logs (MySQL).
  - **Security Tools:** Antivirus or EDR (Endpoint Detection and Response) logs warning us if a file contains a virus.
- **What do these logs reveal to us? (Forensic questions)**
  - Which user logged into the computer at 3 AM?
  - Was a weird command launched in PowerShell?
  - Which files or registry keys were deleted or modified?
  - Did someone plug in an infected USB drive?

#### 🌐 2. Network-Centric Logs (On the highway)
These logs are generated by network devices (Firewalls, Routers, Proxies, VPNs) standing in the path of traffic. They tell us what is happening *between* devices.
- **Key sources:**
  - **Firewalls:** Tell us which connections were allowed and which were blocked. This is the first place we see port scanning!
  - **Web Proxies:** Record every attempt by employees to visit a website, crucial for seeing if someone clicked a bad link.
  - **VPN Gateways:** Record where (from which country and IP address) employees log in to work from home.
  - **IDS/IPS (Intrusion Detection/Prevention Systems):** Network detectors analyzing packets in real-time streams and alarming if traffic contains a known hacker attack (e.g., Snort or Suricata).
- **What do these logs reveal to us? (Network questions)**
  - Is someone from the outside trying to scan our ports?
  - Is an infected office computer "talking" to an external hacker server (Command & Control - C2)?
  - Is someone secretly downloading a massive database and sending it out of the company?
  - How did the attacker move laterally from one computer to another?

| Characteristic | Host-Centric Logs 💻 | Network-Centric Logs 🌐 |
| :--- | :--- | :--- |
| **Location** | Generated on the device itself (laptop, server). | Generated on network devices (Firewall, Switch). |
| **Metaphor** | Camera inside the room. | Camera at the building's front door. |
| **Main Advantage** | We see processes, files, users, and code execution. | We see movement, scans, connections, and data transfer. |
| **Typical Log System** | Windows Event Logs, Syslog, EDR. | Firewall logs, Zeek logs, Proxy logs, IDS. |

---

## (Network Perimeter)

### 🛡️ Network Perimeter & Segmentation
The network perimeter is the boundary dividing the company's trusted internal network (Trusted Zone—where employees and databases reside) from the insecure and dangerous external world (Untrusted Zone—the internet).

Imagine it as the main gate and drawbridge of a medieval castle. Everyone wanting to enter or exit must pass right through there and be checked by the guard (the Firewall).

#### 🏗️ Key Components of the Perimeter
The perimeter is not just a single device, but a system of several key components working together:
1. **Firewalls:** The main "guard" at the gate. Inspects all network packets and decides whether to allow or block/drop them based on firewall rules.
   - *SOC significance:* The primary place we see port scans and unauthorized connection attempts.
2. **DMZ (Demilitarized Zone):** Perhaps the most ingenious concept in network security.
   - *What is it?* An isolated, separate "buffer zone" between the internal network and the internet.
   - *Why does it exist?* A company must have a web server or mail server publicly accessible to clients on the internet. If we put these servers on the internal network and someone hacks them, they instantly have access to the entire company.
   - *Solution:* We place them in the DMZ. If a hacker compromises the web server in the DMZ, they are trapped there! The firewall still blocks communication from the DMZ to the internal network (LAN).
3. **Remote Access Gateways / VPNs:** A secure tunnel allowing remote employees to safely connect to the internal network. VPN encrypts all traffic so no one along the path can intercept it.

#### 🔍 Why is the Perimeter a Primary Target for Attackers?
Attackers always start from the outside. They perform reconnaissance and scan our perimeter looking for:
- **Poor configurations:** For example, accidentally exposing RDP (3389) or SSH (22) ports directly to the internet.
- **Outdated software:** Vulnerabilities in VPN devices or web servers that can be leveraged for entry.
- **Weak passwords:** To brute-force VPN or email logins.

#### 🕵️‍♀️ The SOC Analyst's Role in Perimeter Defense
When monitoring the perimeter in the SOC, your daily work involves:
1. **Reviewing Firewall Logs:** Analyzing blocked traffic. If the same external IP repeatedly hits different ports, it is a sure sign of scanning (as done in Tasks 4 and 5).
2. **Brute-Force Detection:** Tracking failed VPN login attempts within a short timeframe.
3. **Beaconing Detection:** Tracking weird outbound traffic from the internal network to the internet. If an office computer sends a small packet to an unknown IP in China or Russia every 5 seconds, it most likely means it is infected and communicating with a hacker C2 (Command & Control) server.

---

## (Network Perimeters: Monitoring and Protecting)

We have raw logs from three different perimeter devices: Firewall, WAF (Web Application Firewall), and VPN Gateway.

### 🛠️ Scenario 1: Port Scan Detection (Firewall Logs)
An attacker tries to probe closed and open ports (doors) on one of our servers. Analyzing the log excerpt:

2025-09-22 08:30:05 BLOCK TCP 203.0.113.10:50001 -> 10.0.0.20:21
2025-09-22 08:30:06 BLOCK TCP 203.0.113.10:50002 -> 10.0.0.20:22
2025-09-22 08:30:08 BLOCK TCP 203.0.113.10:50003 -> 10.0.0.20:23
2025-09-22 08:30:09 BLOCK TCP 203.0.113.10:50004 -> 10.0.0.20:25
2025-09-22 08:30:11 BLOCK TCP 203.0.113.10:50005 -> 10.0.0.20:53

- What do we notice? The exact same external IP (203.0.113.10) sends packets within seconds to different ports (21, 22, 23, 25, 53) on the local machine 10.0.0.20. The firewall immediately blocks them with a BLOCK status.
- Analytic Conclusion: This is a classic vertical port scan.
- 👉 Answer to Question 1: 203.0.113.10

---

🌐 **Scenario 2: Web Server Attacks (WAF Logs)**

While a regular firewall only looks at ports, a WAF (Web Application Firewall) performs "deep inspection" of HTTP requests. It reads the contents of what a user types into the browser.

📋 **Log Example Analysis:**
- `action=ALLOW request="GET /products.php?id=9"` ➡️ Normal request. The user is simply viewing a product with ID 9.
- `action=BLOCK request="GET /search.php?q=<script>alert('XSS')</script>"` ➡️ Attack! Someone is trying to inject malicious JavaScript to perform an XSS (Cross-Site Scripting) attack. The WAF blocks this.
- `action=BLOCK request="GET /../../../../etc/passwd"` ➡️ Attack! Someone is trying to break out of the web directory and read system password files on the Linux server (Directory Traversal).

🔍 **Key Lessons for WAF Analysis:**
- How do we search for incidents in Kibana? We always filter by the value `action: "BLOCK"` or search for specific rules like `rule_id` or `attack_type`.
- Behind all these blocked attacks in this exercise lies the exact same suspicious source IP systematically probing the web application for vulnerabilities.

👉 **THM Answer:** Found in the raw file under the `src_ip` column where `action` equals `BLOCK`. (Typically, this is the IP address `198.51.100.101`; verify it in your own file!)

🔑 **Scenario 3: VPN Gateway Attack (VPN Brute-Force)**

VPNs allow remote employees to access internal company resources, making them a prime target for attackers seeking easy entry.

📋 **Log Example Analysis:**

2025-09-22 10:12:11 FAILED_AUTH TCP 203.0.113.50:31245 -> 10.0.0.1:443 (user 'admin')

2025-09-22 10:12:15 FAILED_AUTH TCP 203.0.113.50:31248 -> 10.0.0.1:443 (user 'admin')

2025-09-22 10:12:21 SUCCESS_AUTH TCP 198.51.100.88:41233 -> 10.0.0.1:443 (user 'b.jones')

🔍 **How do we decode this?**
- **SUCCESS_AUTH from normal users:** Legitimate employees logging in normally (e.g., `b.jones`). We ignore this as normal traffic.
- **FAILED_AUTH in rapid intervals:** A massive volume of failed attempts from the same external IP targeting common user accounts like `admin`, `guest`, `root`, `user`.

👉 **Analyst Verdict:** This is a classic Brute-Force attack. The attacker is using a script with a list of common passwords to guess an active profile.

👉 **How do we find the exact answers using terminal commands?**

- To find the total number of failed attempts (`FAILED`):

  grep -c "FAILED" ~/Desktop/Perimeter_logs/task6/vpn_logs.txt

To uncover the suspicious IP performing the attack:

grep "FAILED" ~/Desktop/Perimeter_logs/task6/vpn_logs.txt | awk '{print $5}' | cut -d: -f1 | sort | uniq -c

(This will print the suspicious attacker IP that has hundreds of failed attempts).

SOC Playbook: The Difference Between Scanning, Brute-Forcing, and Beaconing

As an analyst, always look for these mathematical patterns in logs to make a quick diagnosis:
- 🎯 **Repetition from ONE source to MANY destinations / ports = SCANNING**
  - **Attacker's Goal:** Network mapping and reconnaissance.
  - **Example:** The firewall blocks ports 21, 22, 23 from the same IP within 5 seconds.
- 🔨 **Repetition from ONE source to ONE destination / user = BRUTE-FORCING**
  - **Attacker's Goal:** Guessing passwords for unauthorized access.
  - **Example:** Thousands of FAILED_AUTH events on the VPN port (443) in a short period.
- ⏱️ **Traffic in perfect, regular time intervals = MALWARE BEACONING**
  - **Attacker's Goal:** The infected computer "phones home" to the hacker server for new commands.
  - **Example:** Connecting to a weird external IP every exactly 60 seconds (without deviation).

---

## Perimeter Logs: Investigating the Breach! 🕵️‍♀️💥

### 1. External IP that performed the most reconnaissance
- **Context:** The attacker started by scanning with the highest number of blocked attempts on the firewall.
- **Terminal command:**

  cat firewall.log | grep "BLOCK" | cut -d' ' -f5 | cut -d: -f1 | sort | uniq -c | sort -nr

Explanation: This command will print the number of block requests per IP address. You will see one dominant external IP with hundreds of blocked attempts.

Correct Answer: 203.0.113.10

2. Targeted internal host at the start
Context: Looking at blocked attempts from attacker 203.0.113.10:

-**Terminal command:**

cat firewall.log | grep "203.0.113.10" | grep "BLOCK" | head -n 5
Explanation: You will notice that all scans are directed to the same local IP, which according to the network device table is the VPN Gateway.

Correct Answer: 10.0.0.50

3. Targeted username in VPN logs
Context: The attacker shifted to the VPN on target 10.0.0.50 and started brute-forcing usernames.

-**Terminal command:**

cat vpn_auth.log | grep "FAIL" | head -n 10
Explanation: You will notice repetitive failed attempts (FAIL) toward one specific system/service account.

Correct Answer: svc_vpn

4. Assigned local IP address after successful VPN entry
Context: After dozens of failed attempts, the attacker successfully logged in as svc_vpn, and the VPN server assigned a local IP (from the 10.8.0.X range).

-**Terminal command:**

cat vpn_auth.log | grep "svc_vpn" | grep "SUCCESS"
Correct Answer: 10.8.0.23 (This is the "client" now inside our network).

5. Port used for lateral movement via SMB
Context: Once inside with IP 10.8.0.23, the attacker explored the internal network looking for SMB servers. In the Windows world, the SMB protocol uses a standard port.

-**Terminal command:**

cat ids_alerts.log | grep "10.8.0.23" | grep "SMB" | head -n 5
Correct Answer: 445

6. Internal host sending beacons (C2 Beaconing)
Context: After installing malware on an internal machine, it periodically contacted an external C2 server. Let's see which local IP the "C2 Beaconing" alerts come from in the IDS log:

-**Terminal command:**

cat ids_alerts.log | grep "C2 Beaconing" | head -n 5
Correct Answer: 10.0.0.20 (According to the table, this is FINANCE-SRV1).

7. External IP associated with the C2 server
Context: Now we want to see which external IP these beacons were sent to on port 4444.

-**Terminal command:**

cat ids_alerts.log | grep "C2 Beaconing" | head -n 5
Explanation: Look at the destination IP address in these alerts behind port 4444.

Correct Answer: 203.0.113.100

8. Host performing data exfiltration (theft) attempts
Context: The attacker began sending massive amounts of data via HTTP POST Large Upload to their external server. Let's find the source IP of this alert:

-**Terminal command:**

cat ids_alerts.log | grep "Potential Data Exfiltration" | head -n 5
Correct Answer: 10.0.0.20

## 🧠 Linux Command Toolkit Explained

In Linux, we chain multiple small utilities together to get the exact result using the vertical bar `|` (called a Pipe). It takes the output of the first command and passes it to the next.

Here are the main "commands" we used:

### 1. `cat` (Short for Concatenate)
- **Meaning:** "Open the file and print its entire contents to the screen."
- **Example:** `cat firewall.log` ➡️ This simply opens the `firewall.log` document so we can work with it.

### 2. `grep` (Search utility)
- **Meaning:** "Find only what I am looking for." This is like pressing `Ctrl + F` in Word or Excel.
- **Example:** `grep "BLOCK"` ➡️ Tells the computer: "From the entire file, show me only those lines where BLOCK is written, ignore everything else."

### 3. `head -n 5` (Head / Beginning)
- **Meaning:** "Show me only the first few lines."
- **Why we use it:** If a log file has 100,000 lines, the terminal will flood with text. With `head -n 5` we tell it: "Show me only the first 5 lines so I can see what they look like." (`head` means head, and `n 5` means number of lines = 5).

---

✂️ **Let's explain the trickier ones: `cut`, `sort`, and `uniq`**
We use these to "cut" the text and extract clean data (for example, just the IP addresses).

### 4. `cut` (Column Cutting)
Imagine that a line in a log is divided by spaces (like columns in Excel): 
`2025-08-26 12:12:47 BLOCK TCP 203.0.113.10:64292`

- **Column 1:** `2025-08-26` (Date)
- **Column 2:** `12:12:47` (Time)
- **Column 3:** `BLOCK` (Action)
- **Column 4:** `TCP` (Protocol)
- **Column 5:** `203.0.113.10:64292` (Suspicious IP and port)

When we type: `cut -d' ' -f5`:
- `-d' '` means: "Treat spaces as the boundary between columns."
- `-f5` (Field 5) means: "Cut and keep only the 5th column (where the IP address is), and throw everything else in the trash."

If we have an IP with a port like `203.0.113.10:64292`, and we only want the IP address, we perform another cut: `cut -d: -f1`:
- `-d:` means: "Now treat colons (`:`) as the boundary."
- `-f1` means: "Take only what is before the colon (i.e., the IP address `203.0.113.10`)."

### 5. `sort` and `uniq -c` (Grouping and Counting)
- **`sort`:** Arranges IP addresses in alphabetical order (first all starting with 10, then 192, then 203...). This is mandatory for the next command to work.
- **`uniq -c` (Unique + Count):** Merges identical IP addresses that are right below each other and tells how many times they repeat.
  - For example, instead of printing `203.0.113.10` 46 times, it will write: `46 203.0.113.10`. This is like a Pivot Table in Excel!
- **`sort -nr` (Sort Numerically Reverse):** Sorts numbers from highest to lowest. Thus, right at the top, you will immediately see the attacker who has the most block requests.

---

### 💡 Example of a whole command "translated" into English:

cat firewall.log | grep "BLOCK" | cut -d' ' -f5 | cut -d: -f1 | sort | uniq -c | sort -nr

-**Translation:**

"Computer, open the firewall.log file (cat), extract from it only the lines where BLOCK is written (grep). From those lines, keep only the fifth column where the IP addresses are (cut -f5), delete the ports behind the colons (cut -f1). Then sort them in order (sort), count how many times each IP address appears in the log (uniq -c), and finally sort them from largest to smallest number (sort -nr) so I can see who scanned me the most."
