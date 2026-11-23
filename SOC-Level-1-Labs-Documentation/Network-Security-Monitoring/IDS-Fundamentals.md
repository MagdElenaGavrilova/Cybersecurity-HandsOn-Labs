# IDS Fundamentals 🐷

Learn the fundamentals of IDS, along with the experience of working with Snort.

## Task 1: What Is an IDS

Intrusion Detection System (IDS) is a system that detects unauthorised network and system intrusions. Examples include detecting unauthorised devices connected to the local network and unauthorised users accessing a system or modifying a file.

### 🎯 1. Room Overview and Essence of IDS
- **Goal of the room:** Learning the fundamentals of an IDS (Intrusion Detection System), along with practical experience working with Snort.
  - **Why do we need an IDS alongside a Firewall?**
    Firewalls are usually placed at the network boundary to protect incoming and outgoing traffic.
    A firewall checks traffic when a connection is to be established and denies it if it violates the firewall's rules.
    However, security is needed that will detect activity on a connection that has already passed through the firewall and is already taking place.
    If an attacker successfully bypasses a firewall via a connection that looks legitimate and then performs malicious activities inside the network, there must be something that detects that in time — and that is an Intrusion Detection System (IDS).

### 🏢 2. Building Security Analogy and Role of an IDS
- **Firewall = Entrance guard (gatekeeper):** Checks people entering and leaving. There is always a chance a bad actor sneaks in and starts malicious activities.
- **IDS = Security cameras throughout the building (surveillance cameras):** Sits to the side, monitors network traffic based on signature-based and anomaly-based detection, and detects any unusual traffic going out or into the network.
- **Generating alerts without taking action:** On every detection, an alert is generated for security administrators. An IDS does not act directly on those detections; it only notifies security administrators about the malicious activity.
- This room will give you solid knowledge of IDS solutions, and in the upcoming tasks we will explore the most popular open-source IDS solution (Snort).

### 📓 3. Learning Objectives & Prerequisites

#### 🔹 Learning Objectives
- Types of IDS and their detection capabilities.
- Working of Snort IDS.
- Default and custom rules in Snort IDS.
- Making a custom rule in Snort IDS.

#### 🔹 Prerequisites
- Firewall Fundamentals
- Network Concepts

### ✅ 4. Question and Exact Answer for Task 1
- **Question:**
  Can an intrusion detection system (IDS) prevent the threat after it detects it? Yea/Nay
- **Exact Answer:** `Nay`
- **Explanation:** As explicitly stated in the lesson, an IDS does not act on detections and does not block/prevent the threat — it only generates an alert and notifies security administrators. (Real-time prevention/blocking is a characteristic of an IPS — Intrusion Prevention System, whereas an IDS is a passive/detective sensor).

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why is SPAN / port mirroring often used for IDS sensors in SOC practice?**
  Because an IDS only observes and analyzes traffic without blocking it, it usually receives a copy of the traffic (for example via a TAP or switch SPAN port). That way, even if the IDS sensor has high load or restarts, it does not interrupt normal network traffic in production.
- **Signature-based vs. Anomaly-based detection (a brief compass going forward):**
  Signature-based looks for already known patterns, payloads, headers, or signatures of known attacks — very accurate for known threats, but it does not see unknown (zero-day) techniques until there is a rule for them.
  Anomaly-based compares current traffic against an established baseline — useful for unusual behaviors, but requires good calibration so it doesn't create too many false positives.

---

## Task 2: Types of IDS

### 🎯 1. Main Categorization of IDS
- An IDS can be categorized in different ways depending on certain factors.
- The main categorization of an IDS depends on its **Deployment Modes** and **Detection Modes**.

### 🏗️ 2. Deployment Modes
An IDS can be deployed in the following ways:
- **Host Intrusion Detection System (HIDS):**
  Host-based IDS solutions are installed individually on the hosts themselves and are responsible for detecting only potential security threats related to that specific host.
  - They provide detailed visibility into host activities.
  - However, HIDS can be difficult to manage in large networks because they consume more resources (resource-intensive) and require management on each host individually.
- **Network Intrusion Detection System (NIDS):**
  Network-based IDS solutions are key for detecting potentially malicious activity across the entire network, regardless of individual specific hosts.
  - They monitor network traffic across all involved hosts to detect suspicious activity.
  - They provide a centralized view of all detections within the entire network.

### 🔬 3. Detection Modes
- **Signature-Based IDS:**
  Many attacks happen every day and each attack has its own unique pattern called a signature.
  - These signatures are stored by the IDS in its databases so that, if the same attack happens in the future, it can be detected by its signature and reported to security administrators for action.
  - The stronger the IDS's signature database, the more effectively it will detect known threats.
  - However, a signature-based IDS cannot detect zero-day attacks. Zero-day attacks have no prior signatures (patterns) and are not stored in the IDS databases. Therefore, a signature-based IDS can detect only attacks that have occurred previously and whose signatures are stored in the database.
  - In the upcoming tasks we will explore a signature-based IDS called Snort.
- **Anomaly-Based IDS:**
  This type of IDS first learns the normal behavior (baseline) of the network or system and makes detections if there is any deviation from normal behavior.
  - An anomaly-based IDS can also detect zero-day attacks because for detection it does not rely on existing signatures.
  - It detects abnormalities in the network or system by comparing the current state against normal behavior (baseline).
  - However, this type of IDS can generate many false positives (marking harmless/legitimate activities as malicious) because the nature of most legitimate programs overlaps with malicious ones. An anomaly-based IDS would flag them as malicious and consider anything that behaves unusually to be malicious.
  - We can reduce false positives generated by an anomaly-based IDS with fine-tuning (manually defining normal behavior in the IDS).
- **Hybrid IDS:**
  A hybrid IDS combines the detection methods of a signature-based IDS and an anomaly-based IDS to take advantage of each approach.
  - Some known threats may already have signatures in the IDS database — in that case the hybrid IDS uses the signature-based IDS technique.
  - If it encounters a new threat, it can use the anomaly-based IDS detection method.

### ⚖️ 4. Comparison and Choosing an IDS by Factors
- A signature-based IDS can detect threats quickly, while other IDS solutions can have high processing overhead.
- However, it is important to evaluate an IDS based on multiple different factors:
  - A signature-based IDS can be a good option for covering a small threat surface.
  - Anomaly-based IDS and hybrid IDS can help in detecting modern zero-day attacks, which grow daily and can cause huge damage to organizations.

### ✅ 5. Questions and Exact Answers for Task 2
- **Which type of IDS is deployed to detect threats throughout the network?**
  - **Exact Answer:** `Network Intrusion Detection System` (or abbreviated `NIDS`).
  - **Explanation:** NIDS is deployed to monitor all hosts and detect potentially malicious activity across the entire network with a centralized view.
- **Which IDS leverages both signature-based and anomaly-based detection techniques?**
  - **Exact Answer:** `Hybrid IDS`.
  - **Explanation:** Hybrid IDS combines signature-based and anomaly-based detection methods to leverage the advantages of both approaches.

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why do HIDS/EDR and NIDS almost always go together in real SOC environments?**
  NIDS gives you the broad network picture (e.g., lateral movement, unusual protocols, exfiltration, or scans across segments), while HIDS / EDR gives you the deep contextual picture on the host itself (which process, with which command line, and which user did it — something a network sensor alone cannot see, especially if there is encryption).
- **What does fine-tuning mean in practice for a SOC analyst?**
  When a new sensor or anomaly rule starts up, at first it may "scream" about legitimate but rare activities (for example backup windows, administrative scripts, new internal services). By adding to the baseline / allowlist after careful verification (and not blindly disabling!), alert fatigue is reduced without losing real visibility.

---

## Task 3: IDS Example: Snort

### 🎯 1. What Is Snort and How Does It Work with Rules?
- **Snort** is one of the most widely used open-source IDS solutions, developed in 1998.
- It uses signature-based and anomaly-based detection to identify known threats, which are defined in the Snort tool's rule files.
- Included in this tool's package are several pre-installed built-in rule files, which contain various patterns of known attacks.
- Snort's built-in rules can detect a large portion of malicious traffic.
- However, you can configure Snort to detect specific types of network traffic that are not covered by the default rule files.
- You can create custom rules according to your requirements to detect specific traffic.
- You can also disable any built-in detection rules if they do not indicate harmful traffic for your system or network, and define custom rules instead of them.
- In the next task we will explore built-in rules and make custom rules for detecting specific traffic.

### ⚙️ 2. Modes of Snort
There is a difference between Packet Sniffer mode, Packet Logging mode, and NIDS mode:

| Mode | Description | Use Case |
| :--- | :--- | :--- |
| **Packet sniffer mode** | Reads and displays network traffic without performing any analysis on it. This mode is not directly related to IDS capabilities, but can be useful for network monitoring and troubleshooting. In some cases system administrators need to read traffic flow without detection to diagnose specific problems. This mode allows displaying network traffic on the console or even outputting it to a file. | The network team notices network performance issues. To diagnose the problem, they need detailed insights into network traffic and for that purpose can use Snort's packet sniffer mode. |
| **Packet logging mode** | Snort performs detection on network traffic in real time and displays detections as alerts on the console for security administrators to take action. However, in some cases network traffic needs to be logged for later analysis. Snort's packet logging mode allows logging network traffic as a PCAP (standard packet capture format) file, which includes all network traffic and all detections from it. Forensic investigators can use these Snort log files for root cause analysis of past attacks. | The security team needs to initiate a forensic investigation into a network attack and needs traffic logs for root cause analysis. Network traffic logged via Snort's packet logging mode can help them. |
| **Network Intrusion Detection System mode** | Snort's NIDS mode is the primary mode that monitors network traffic in real time and applies its rule files to identify matches with known attack patterns stored as signatures. If there is a match, it generates an alert. This mode provides the core functionality of an IDS solution. | The security team must proactively monitor their network or systems to detect potential threats and for that can use Snort's NIDS mode. |

The most relevant use of Snort as an IDS comes from its NIDS mode, though Snort can be used in any of the above modes depending on need.

### ✅ 3. Questions and Exact Answers for Task 3
- **Which mode of Snort helps us to log the network traffic in a PCAP file?**
  - **Exact Answer:** `Packet logging mode`
  - **Explanation:** Packet logging mode allows writing network traffic into a PCAP file for later forensic analysis.
- **What is the primary mode of Snort called?**
  - **Exact Answer:** `Network Intrusion Detection System mode`

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective on Snort Modes
- **How do Snort's modes connect with practical investigation (like the PCAP analyses we already did)?**
  When Snort runs in Packet logging mode and generates a PCAP file, you can later open that capture in Wireshark or NetworkMiner and inspect the packets, streams, and files in detail. When it runs in NIDS mode, it continuously compares live traffic against rules and alerts when it sees a known pattern.
- **Why do built-in rules in a real network sometimes need fine-tuning / disabling certain rules?**
  Generic built-in rules can generate unnecessary alerts for specific internal services or protocols that are legitimate in your network, so the defender tailors them or writes custom rules targeted at the specific services and risks in the organization.

---

## Task 4: Snort Usage

### ⚙️ 1. Installation, Promiscuous Mode, and Snort Directories
- When installing Snort, you must specify the network interface and range.
- You can start Snort normally, where it captures only packets destined for your host. However, if you want to use Snort to capture and detect intrusions across the entire network, you must enable promiscuous mode on your host's network interface.
- First start the lab machine by clicking `Start Lab Machine` (in split view, and if not visible, the blue `Show Split View` button at the top of the task is used; machine details are at the top of the page, status shown as `Lab machine — Status:On` with option `See connection methods`).
- Snort has built-in rule files, a configuration file, and other files stored in the `/etc/snort` directory.
- In Snort 3 this location is not fixed — it is chosen when installing Snort, so it can differ between systems. When building from source `/usr/local/etc/snort` is often used, while on this machine `/etc/snort` is used. Whichever location is used, the configuration is loaded by passing its path to the `-c` flag.
- A key file for Snort is its configuration file `snort.lua`, where you can specify which rule files to enable, which network range to monitor, and other settings. Rule files are stored in the `rules` folder.
- Listing the files and folders in Snort's main directory:
  `ls /etc/snort`
- Contents shown in the example: `Intro_to_IDS.pcap`, `community-sid-msg.map`, `reference.config`, `snort.conf`, `snort.lua`, `unicode.map`, `classification.config`, `gen-msg.map`, `rules`, `snort.debian.conf`, `threshold.conf`.

### 📝 2. Snort Rule Format
There is a specific way of writing rules in Snort. A sample rule would detect ICMP packets (commonly used when pinging a host) coming from any IP address and port and reaching the home network (the network range is defined in Snort's configuration file) on any port. Once such traffic is detected, it generates alerts `"Ping Detected"`.

Details of the components included in the rule:
- **Action:** Determines what action to take when the rule triggers. In this case the action is `alert` when traffic matches the rule.
- **Protocol:** Refers to the protocol matching the rule. In this case the `ICMP` protocol is used when pinging a host.
- **Source IP:** Specifies the IP address from which the traffic originates. Because we want to detect traffic from any source IP address, we set this as `any`.
- **Source port:** Specifies the port from which the traffic originates. Because we want to detect traffic from any source port, we set this as `any`.
- **Destination IP:** Specifies the target IP address to which matching traffic arrives and generates an alert. In this case we use `$HOME_NET`. This is a variable whose value is defined as the range of our entire network in Snort's configuration file.
- **Destination port:** Specifies the port to which the traffic will arrive. Because we want to detect traffic arriving at any port, we set this as `any`.
- **Rule metadata:** Every rule has certain metadata defined at the end of the rule in parentheses. Its components are:
  - **Message (`msg`):** Describes the message displayed when the rule triggers. The message should indicate the type of detected activity. In the example `"Ping Detected"` is used.
  - **Signature ID (`sid`):** Every rule has a unique identifier that distinguishes it from others. This identifier is called signature ID (`sid`). In the example `"10001"` is specified.
  - **Rule revision (`rev`):** Sets the revision number of the rule. Every time the rule is modified, the revision number is incremented, which helps track changes to any rule.

### ✍️ 3. Creating and Testing Rules + Working with PCAP

#### 🔹 Creating a rule in `local.rules`
1. Open the `local.rules` file in a text editor:
   `sudo nano /etc/snort/rules/local.rules`
2. Add the rule after existing rules in the file (without deleting the other rules, since they will be needed in the next task):
   `alert icmp any any -> 127.0.0.1 any (msg:"Loopback Ping Detected"; sid:10003; rev:1;)`
3. After editing successfully, press `ctrl+x`, then `y` to save the changes.

#### 🔹 Testing the rule live
1. Start Snort with `sudo` privileges for intrusion detection defined in the rule file:
   `sudo snort -q -l /var/log/snort -i lo -A alert_fast -c /etc/snort/snort.lua`
   *(If the loopback interface is not named `lo`, replace it with the exact interface name).*
2. Ping the loopback address:
   `ping 127.0.0.1`
3. In the sample output, generated alerts for `"Loopback Ping Detected"` from `127.0.0.1 -> 127.0.0.1` with `{ICMP}` are seen.

#### 🔹 Running Snort on PCAP files
When you have historical network traffic recorded in a PCAP file and need to do a forensic investigation for signs of intrusion, use:
`sudo snort -q -l /var/log/snort -r Task.pcap -A alert_fast -c /etc/snort/snort.lua`
*(Replace `Task.pcap` with the path to the PCAP file for analysis).*

### ✅ 4. Questions and Exact Answers for Task 4
- **Where is the main directory of Snort that stores its files?**
  - **Exact Answer:** `/etc/snort`
- **Which field in the Snort rule indicates the revision number of the rule?**
  - **Exact Answer:** `rev`
- **Which protocol is defined in the sample rule created in the task?**
  - **Exact Answer:** `ICMP` (or `icmp`)
- **What is the file name that contains custom rules for Snort?**
  - **Exact Answer:** `local.rules`

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why is the revision number (`rev`) important in production and rule versioning?**
  When the detection engineering team changes thresholds, adds/modifies a string, or fixes a false positive in a rule, incrementing `rev` (e.g., `rev:1;` → `rev:2;`) helps know exactly which version of the rule generated a given alert at a given time.
- **How does `-r <pcap>` work with Snort in everyday practice?**
  You can take a recorded PCAP file (for example from `tcpdump`, Wireshark, or an incident capture) and run it through Snort with your rules without waiting for live traffic — ideal for testing new rules on real or historical samples before running them on a live interface.

#### 1. How do you know what the interface is called (whether `lo` or another name)?
In 99% of Linux machines (including the TryHackMe virtual machine), the loopback interface is named exactly `lo`.
If you want to check all interfaces you have on the machine, open a new terminal and type one of these two commands:
`ip a`
or
`ifconfig`

What you will see on the screen:
The network interfaces will be listed. Usually they include:
- `lo` = Loopback interface (when the machine talks to itself, i.e., IP `127.0.0.1`).
- `eth0` or `ens33` / `ens5` = Main network card for traffic to the external network.

For the task where we ping `127.0.0.1`, Snort must listen on `lo` because `127.0.0.1` traffic passes only through that interface.

#### 2. How do you find the path to the PCAP file and who made it?
You do not need to make a new PCAP file!
In TryHackMe labs (and in real forensic investigations), the PCAP file is already pre-prepared by the author of the room or from system logs. It contains the recorded packets from the attack you are investigating.

How to find out where the PCAP file is located on the machine?
1. Check the current folder you are in:
   In the terminal type:
   `ls`
   If in the list you see the file (for example `Intro_to_IDS.pcap` or `Task.pcap`), then its path is simply its name:
   `sudo snort -q -l /var/log/snort -r Intro_to_IDS.pcap -A alert_fast -c /etc/snort/snort.lua`
2. If the PCAP file is in another folder (for example on the `Desktop`):
   Then in the command you write the full path to it:
   `sudo snort -q -l /var/log/snort -r /home/ubuntu/Desktop/Task.pcap -A alert_fast -c /etc/snort/snort.lua`
3. If you don't know where exactly the PCAP file is saved:
   You can quickly find it with the `find` command:
   `find / -name "*.pcap" 2>/dev/null`
   This will list all `.pcap` files on the machine along with their exact paths.

💡 **Summary for Snort commands:**
- For live traffic on loopback (`127.0.0.1`):
  Use `-i lo` (with that you tell Snort: "Listen on network interface `lo`").
- For a recorded PCAP file:
  Use `-r /path/to/file.pcap` (with that you tell Snort: "Don't listen live, read the packets from this recorded file").

---

## Task 5: Practical Lab

### 🎯 1. Scenario and PCAP File Location
- **Role and scenario:** You are a third-party forensic investigator. A company contacts you to investigate a recent attack on their network and provides you with a PCAP file named `Intro_to_IDS.pcap`, which contains the network traffic recorded during the attack.
- **Task:** Run Snort on this PCAP file and answer the questions given in this task.
- **Location note:** The file `Intro_to_IDS.pcap` is located in the `/etc/snort/` directory. You need to change the current directory to `/etc/snort` and run the PCAP analysis command on this file the same way as in Task 4.

### 🛠️ 2. Step-by-Step Workflow in Terminal
1. **Change directory to `/etc/snort`:**
   `cd /etc/snort`
2. **Verify that the file is there (optional, for confirmation):**
   `ls -la Intro_to_IDS.pcap`
3. **Run Snort on `Intro_to_IDS.pcap`:**
   `sudo snort -q -l /var/log/snort -r Intro_to_IDS.pcap -A alert_fast -c /etc/snort/snort.lua`
4. **Review generated alerts / logs:**
   - Alerts are displayed in the console (via `-A alert_fast`), and logs are also written to `/var/log/snort` (check the log files there with `ls -la /var/log/snort` and read them with `cat` or `less`, especially to see the exact `sid` in the `[gid:sid:rev]` format).

### ✅ 3. Questions and Guidance for Answers in Task 5
1. **What is the IP address of the machine that tried to connect to the subject machine using SSH?**
   - **Answer:** `10.11.90.211`

<img width="1256" height="867" alt="ssh ip " src="https://github.com/user-attachments/assets/57cd748c-1327-4a4a-9908-9fa8d07ae30a" />

---

   - **How to read it from the output:** In the line with the alert for SSH you see the source and destination IP addresses (`SRC -> DST`, with port `22` on the target machine). The address from which the SSH connection attempt to the target machine originates is the requested source IP.
2. **What other rule message besides the SSH message is detected in the PCAP file?**
   - **Format:** `ping detection`

<img width="1250" height="834" alt="ping detection" src="https://github.com/user-attachments/assets/3b0155db-b251-457d-b614-3c11b15ab475" />

---

   - **How to read it from the output:** Besides the message related to SSH, another message from a `ping detection` rule appears in the alerts from `Intro_to_IDS.pcap`.
3. **What is the sid of the rule that detects SSH?**
   - **Format:** `1000002`
   - **How to read it from the output:** In Snort alerts the identifier appears in the format `[gid:sid:rev]` (for example `[1:XXXXXXX:1]`) — the second number in the middle group is the `sid` (Signature ID) of the rule that detected SSH.

<img width="1250" height="834" alt="sid num" src="https://github.com/user-attachments/assets/842c4de5-1033-47b6-8b76-6c783d255711" />

---

### 🔥 EXTRA FROM ME: SOC & Forensic Investigator Perspective
- **Why before running Snort on a new PCAP is it always good practice to check if previous tests (like `Loopback Ping Detected`) are still in `local.rules`?**
  If a rule from practice (for example for `127.0.0.1`) remains in `local.rules`, in some PCAP captures it may not interfere, but in production or larger captures old/test rules can introduce noise or shift triage focus. That is why in real work you always know exactly which rules are active (`snort.lua` + `local.rules`).
- **How does a forensic investigator go a step further after Snort alerts?**
  Once Snort tells you "there was an SSH attempt from that IP address and another alert", the next step in the investigation is to open the same `Intro_to_IDS.pcap` in Wireshark (or `tcpdump -r Intro_to_IDS.pcap -nn`) and see the exact TCP packets, timings, whether the connection stayed at SYN/banner or there were multiple attempts, and what exactly triggered the second alert.
