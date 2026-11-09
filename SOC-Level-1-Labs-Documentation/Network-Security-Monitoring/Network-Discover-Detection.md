# Network Discovery Detection

Understand how attackers discover assets in a network, and how to detect that activity.

---

## Network Discovery Detection - Introduction (Task 1)

Network Discovery (Reconnaissance / Scanning) is a phase where an attacker attempts to map the victim's internal network to discover active devices, servers, open ports, and operating systems.

### 🔍 Why is this crucial for the SOC?
- This is one of the most common hacker activities observed during a SOC shift.
- Attackers must perform this to plan **Lateral Movement**.
- Because scanning generates a large number of network packets in a short period, this phase is very "noisy" and relatively easy to detect with proper SIEM/IDS rules.

### 🎯 Objectives of this room:
- Understand the different types of network reconnaissance.
- Learn how scanning tools (such as Nmap) technically function.
- Learn how a SOC analyst can spot and prevent these attempts.

We enter this room in the role of true detectives. As the introduction notes, when the hacker Megatron enters a company's network, he is "blind"—he does not know where the main servers are, where the money is stored, or where the passwords are. Therefore, he must first perform **Network Discovery**.

This scanning and probing is the very first thing you, as a SOC analyst, will spot on your shift. Hackers are at their noisiest here and leave behind many traces!

---

## Task 2: Internal vs. External Scanning

Network discovery is used both by attackers (to find entry points) and defenders (to reduce the **Attack Surface**).

### 🎯 Attacker's Goals:
- Identification of active IP addresses and open ports.
- Operating System (OS) version discovery.
- Service Version discovery to find known vulnerabilities (CVEs) that can be exploited.

### 🛡️ The SOC Analyst's Role:
Since scanning is an everyday occurrence on the internet, SOC teams must filter alerts via:
- **Allowlisting:** Excluding legitimate internal scanners from security rules.
- **Threat Intelligence:** Automatically increasing the Severity of an alert if the scanning originates from a known malicious IP address.

---

### 🧠 SOC Lesson: Good vs. Bad Detection
As a SOC analyst in Frankfurt, you will see scanning in logs every day. However, herein lies the biggest challenge: **Not every scan is an attack!**

- **Good Scanning (Benign):** Your own internal company IT team runs scanners (like Nessus or Qualys) to perform an inventory of computers and ensure everything is secure. Also, search engines like Google constantly scan the internet for indexing.
- **Bad Scanning (Malicious):** The hacker Megatron looking for an open door to enter and cause damage.

#### How do we differentiate in the SOC?
- **Allowlist:** We input our internal scanners' IP addresses into the system to prevent false positives.
- **Threat Intelligence Integration:** We link our SIEM system with threat databases. If we see an external IP starting to scan our ports, and Threat Intel says, *"This IP belongs to a known hacking group,"* we immediately escalate the alert to **Critical**.

---

### 🌐 External vs. Internal Scanning Summary
- **External Scanning:** The attacker is still outside (Reconnaissance phase). No entry into the company has occurred. This is low priority (**Low Severity**) — we block their external IP on the firewall and move on.
- **Internal Scanning:** The attacker has already gained entry to the network (Discovery phase) and is scanning from an internal IP toward other internal IPs (preparing for Lateral Movement). This is a critical alert (**High Severity**) — Incident Response begins immediately!

---

## Task 3: Firewall Log Analysis (External vs. Internal)

When analyzing SIEM logs, a SOC analyst must immediately separate scans by type:

### 🌍 External Scanning
- **Indicators:** Source IP is public/external, destination is internal.
- **Example in exercise:** `203.0.113.25` in `log-session-1.csv`.
- **Severity:** Low (Reconnaissance).

### 🏢 Internal Scanning
- **Indicators:** Both source and destination are internal private IP addresses.
- **Example in exercise:** `log-session-2.csv` (totaling 2276 suspicious attempts).
- **Severity:** High (Discovery). The attacker already has control over one internal host and is attempting to expand through the network.

---

### 💻 How to solve the practical part on your virtual machine?
Open the terminal on your TryHackMe machine and enter the following commands to analyze log files located in `/home/ubuntu/Downloads/logs`:

cd /home/ubuntu/Downloads/logs

There are three log files inside. Let's check the sources and destinations to see which file contains internal scanning (192.168.x.x toward another local range) and which contains external scanning.

1. Find the file with internal scanning:
By examining the files, you will notice that:
- log-session-1.csv contains an external IP scanning internal resources.
- log-session-2.csv contains exclusively internal private IP addresses scanning other internal addresses.
- Answer: log-session-2.csv

2. Count the lines for the internal attacker:
To count total log entries in that internal log (excluding the header row), use wc -l (word count lines):

wc -l log-session-2.csv

The total count of log entries in that TryHackMe lab file is:
- Answer: 2276

3. Find the external IP performing scanning:
Looking at log-session-1.csv (or running head -n2 log-session-1.csv), the source IP (source.ip) is the second field:
- Answer: 203.0.113.25

Task 4: Horizontal vs. Vertical Scanning
📐 How to easily understand and differentiate them?
↔️ Horizontal Scan:
- Concept: The attacker scans ONE identical port (e.g., port 445 for SMB) across MANY different computers (IP addresses).
- Why? Just like the WannaCry malware spread — searching for any computer in the network with vulnerable port 445 open to infect it.
- Log Pattern: One Source IP ➡️ Same Destination Port ➡️ Different Destination IPs.

↕️ Vertical Scan:
- Concept: The attacker scans MANY DIFFERENT ports (from 1 to 1000) on ONE single computer (IP address).
- Why? Thoroughly mapping that specific critical server to see if SSH (22), web (80, 445), database, etc., are open.
- Log Pattern: One Source IP ➡️ Same Destination IP ➡️ Different Destination Ports.

🎯 Practical Questions & Answers

1. Which IP address range was horizontally scanned?
Analyzing the file, the external attacker scans the same port across many computers in the local network. All those internal destination addresses start with 203.0.113.
- Answer: 203.0.113.0/24

2. Advanced AWK Log Processing for Vertical Scans
When analyzing raw CSV security logs in the SOC, standard tools like cut can easily get confused if a column (like a timestamp) contains a comma inside quotes. Therefore, we use an advanced awk command that intelligently processes columns and calculates unique ports per IP pair:

awk -v FPAT='([^,]+)|(\"[^\"]+\")' '
NR>1{
gsub(/"/,"",$2); gsub(/"/,"",$4); gsub(/"/,"",$5);
key=$2","$4;
ports[key][$5]=1
}
END{
for(k in ports){
n=0;
for(p in ports[k]) n++;
if(n>1){
split(k,a,",");
print "Vertical scan: source=" a[1] " target=" a[2] " ports=" n
}
}
}' log-session-2.csv

Detailed Breakdown of the AWK Script:
1. Smart Column Definition (-v FPAT): Tells awk how to define a field/column. It treats items inside quotes (like "Sep 7, 2025 @ 17:16") as a single column despite internal commas.
2. Skipping Headers (NR>1): Skips the first row containing column names.
3. Cleaning Quotes (gsub): Strips quotes from columns 2, 4, and 5 (source.ip, destination.ip, destination.port).
4. Creating a Key (key=$2","$4): Forms a unique relationship pair between the attacker and victim.
5. Unique Port Tracking (ports[key][$5]=1): Uses an associative array to store unique ports, preventing duplicate counts.
6. END Block Calculation: Counts unique ports for each pair and prints output if n > 1.

Output Analysis:
Vertical scan: source=192.168.230.127 target=192.168.230.145 ports=1001
Vertical scan: source=192.168.230.127 target=192.168.230.1 ports=5

- Interpretation: Machine 192.168.230.127 targeted 192.168.230.145 across 1001 unique ports, representing a classic Vertical Port Scan.
- Answer (Target of Vertical Scan): 192.168.230.145

3. Which ports are scanned for common services on the other IP address?
- Answer: 80, 445, 3389

# Analysis of Specific Ports on a Targeted Host (80, 445, 3389)

When analyzing and noticing that a device in the network is scanned on only a few ports, it usually means the attacker is not performing a brute-force scan of everything, but rather checking only the most common security "entries" (Common Services).

In this exercise, we analyze the device `192.168.230.1` (which is most often the router or main network gateway) to see which ports were targeted on it.

## 📋 Filtering Code in the Terminal:

awk -v FPAT='([^,]+)|(\"[^\"]+\")' 'NR>1{
  d=$4; gsub(/"/,"",d);
  if(d=="192.168.230.1") print NR ":" $0
}' log-session-2.csv | head -n 8

## 🔍 Code Explanation: Part by Part

### 1. Smart mapping and destination definition (`d=$4`)

d=$4; gsub(/"/,"",d);

🔍 Code Explanation: Part by Part
1. Smart mapping and destination definition (d=$4)
d=$4; gsub(/"/,"",d);
- What it does: Takes the value from the 4th column (which in this log is the destination IP, i.e., the victim) and saves it in the variable d.
- With the function gsub(/"/,"",d), we immediately clean the quotes from the IP address to get clean text for comparison (e.g., 192.168.230.1 instead of "192.168.230.1").
2. Specific filter for the victim (if(d=="..."))
if(d=="192.168.230.1") print NR ":" $0
- What it does: This is the "filter"! We tell awk: "Check every line in the log file. If the destination IP (d) is EXACTLY 192.168.230.1, then print the line number (NR) followed by a colon, and then the entire log record ($0)."
- Why do we print the line number (NR)? So that at any moment we know exactly which line in the huge CSV file that event is located on if we need to investigate it further.
3. Limiting results (| head -n 8)
- Since there may be thousands of lines for this IP in the log, adding | head -n 8 at the end of the command tells the terminal to display only the first 8 lines to give us a quick overview without flooding the screen.
📊 Result and Port Decryption
In the printed output, within the Zeek JSON message ("id.resp_p"), the following destination ports appear: 80, 445, 3389, and 0.
🔑 Why are these specific ports considered "Common Services"?
Attackers always target known ports first because they offer the highest chance for an attack:

| Port | Protocol / Service | Description and Why the Attacker Scans It |
| :--- | :--- | :--- |
| **80** | HTTP (Web) | Web traffic. The attacker checks if the router has an open web administration interface using a weak password or vulnerable software. |
| **445** | SMB (Microsoft Directory / File Sharing) | File sharing. This is hackers' favorite port for spreading viruses across the internal network (like WannaCry). |
| **3389** | RDP (Remote Desktop Protocol) | Remote computer control. The attacker checks if they can log in and take full visual control over the server. |

⚠️ **Important SOC Note: What about "Port 0" (ICMP)?**
In the logs, you will notice lines where the destination port is recorded as 0 and the protocol is ICMP.
- ICMP (like the `ping` command) is used to check if a computer is powered on in the network.
- ICMP does not use TCP or UDP ports (like 80 or 443). Therefore, in port detection logs, a value of 0 (or empty) indicates that it is a regular Ping, not a port scan.
- Because the TryHackMe question strictly asks for ports, ICMP with a value of 0 is ignored, and the correct answer contains only the three real TCP ports in ascending order.

🏁 **Final Answer for TryHackMe:**
👉 `80, 445, 3389`

awk -v FPAT='([^,]+)|(\"[^\"]+\")' 'NR>1 { gsub(/"/,"",$5); print $5 }' log-session-2.csv | sort | uniq -c | sort -nr | head -n 10

This command is pure art — much simpler, faster, and provides a crystal-clear result on the screen without flooding you with long JSON lines. Hats off to the initiative to shorten and adapt the command to make it easier to read!

## 📊 Elegant Port Detection with AWK (The Simplest Method)

When we want a quick overview of which ports are most targeted in a log file, there is no need to read the entire message. It is enough to extract the port column, count them, and sort them.

### 🔍 How this command "cleans" the chaos:
This command does the following in three simple steps:
1. `gsub(/"/,"",$5); print $5` ➡️ Takes only the 5th column (destination port), deletes the quotes from it, and prints it on the screen.
2. `sort | uniq -c` ➡️ Sorts the ports and counts how many times each appears (e.g., indicating a port appeared exactly 4 times).
3. `sort -nr` ➡️ Performs a Numeric Reverse sort. This means the ports appearing the highest number of times are pushed to the top of the terminal.

| Port | Service / Protocol | What it represents in the real world? |
| :--- | :--- | :--- |
| **80** | HTTP | Standard web traffic (Web Server). |
| **445** | SMB | Windows file sharing (Microsoft File Sharing). |
| **3389** | RDP | Windows desktop remote control (Remote Desktop). |
| **3306** | MySQL | Popular open-source database. |
| **5432** | PostgreSQL | Advanced open-source relational database. |
| **1521** | Oracle Database | Oracle database (very common in large corporations). |
| **1433** | MSSQL | Microsoft SQL Server (database). |

### 🎯 Conclusion on Detection:
The attacker in this log targeted web servers (80), remote access systems (3389), file sharing (445), and even 4 different types of databases (MySQL, PostgreSQL, Oracle, MSSQL) to discover where important information is stored in the company!

256 0
  8 445
  8 3389
  7 80
  2 999
  2 998
  2 997
  2 996
  2 995
  2 994

  - **Appearing 256 times as "port 0":** As we learned earlier, this is not a real TCP/UDP port. This is ICMP (Ping). The attacker sent a Ping to 256 addresses to see which devices are actually powered on in the network (known as a Ping Sweep). Because the question asks for real service ports, this is ignored.
- **The three most common ports (Common Services) that remain are:**
  - **445** (appears 8 times) ➡️ SMB (Windows file sharing)
  - **3389** (appears 8 times) ➡️ RDP (remote desktop control)
  - **80** (appears 7 times) ➡️ HTTP (web traffic)
- **Sorting in Ascending Order:** The task requires entering the ports from smallest to largest: `80`, then `445`, then `3389`.

👉 **Correct Answer for TryHackMe:** `80, 445, 3389`

# The Mechanics of Scanning! 🚀

This is the part where we dive into specific network protocols and how scanners (like Nmap) "talk" to computers to find out if they are online and which ports are open.

## ⚙️ Mechanics of Scanning (What happens behind the scenes?)

### 1. Ping Sweep (ICMP Sweep)
The attacker sends a regular ICMP Echo Request (Ping) to an entire network. Every computer that is online and not blocking this traffic will return an ICMP Echo Reply. This quickly maps which devices are "live".

### 2. TCP SYN Scans (Half-Open Scan)
Instead of establishing a full TCP connection via the known Three-Way Handshake:
The attacker uses a stealth method:
1. The attacker sends a SYN.
2. The victim returns a SYN-ACK (meaning the port is open).
3. The attacker, instead of finishing the connection with an ACK, immediately sends a RST (Reset) to drop the connection. This way, the port is detected as open, and the victim's application often does not log this at all!

In Zeek logs, this behavior is recorded via special connection state tags (`conn_state`):
- **S0:** The scanner sent a SYN, but received no response (port is closed or there is a Firewall).
- **SH:** The scanner sent a SYN, received a SYN-ACK, and immediately sent a RST to drop the connection (classic TCP SYN Scan).

---

## ⚙️ Task 5: Network Scanning Mechanics

Understanding protocols behind scanning:

### 🌐 Ping Sweep (ICMP Sweep) - Internet Control Message Protocol (ICMP)
- **How it works:** Sending ICMP Echo Request packets to an entire range (Subnet). Devices that are online return an ICMP Echo Reply.
- **Example from exercise:** Source `192.168.230.127` sent 256 ICMP packets in `log-session-2.csv` (Ping Sweep across the entire subnet).

### ⚡ TCP SYN Scan (Stealth / Half-Open Scan) - Transmission Control Protocol (TCP)
Transmission Control Protocol (TCP) is a connection-oriented protocol requiring a TCP three-way-handshake to establish a connection. TCP provides reliable data transfer, flow control and congestion control. Higher-level protocols such as HTTP, POP3, IMAP and SMTP use TCP.
- **How it works:** The attacker sends a SYN packet. If they receive a SYN-ACK, the port is open, but they immediately send a RST (Reset) to drop the connection before it is fully established.
- **Zeek tags:**
  - **S0** = SYN sent, no response (Connection attempt seen, no reply).
  - **SH** = SYN sent, SYN-ACK received, then RST sent to drop.
- **Example from exercise:** The activity of `203.0.113.25` against `192.168.230.145` with state S0 is a classic TCP SYN Scan.

### 🛑 UDP Scan
- **How it works:** An empty UDP packet is sent. If the port is closed, the victim returns ICMP Port Unreachable. Very slow and unreliable scan.
- **Result in exercise:** No UDP activity in logs (`N`).

---

## 🌐 Ping Sweep Detection via Kibana (KQL) and Raw Logs

### TryHackMe Question:
*Which source IP performs a ping sweep attack across a whole subnet?*

### 📋 How do we define a "Ping Sweep" in the SOC?
A Ping Sweep (or ICMP Sweep) represents sending a large number of ICMP Echo Request packets from a single source IP address to many different destination IP addresses within a single subnet. The attacker's goal is to quickly identify all active ("live") devices on the network.

### 🔍 Step 1: Searching in Kibana with KQL
When working in the Kibana Discover interface, instead of viewing all logs at once, we use the following KQL filter to isolate only ICMP packets:

network.protocol : "icmp"

What does this do? Filters all traffic and displays only log entries using the ICMP (Internet Control Message Protocol) protocol.

📊 **Step 2: Grouping and Cardinality Analysis**
After filtering ICMP packets in Kibana, to prove the scan we use the following logic:
1. Add the `source.ip` field as a column in Discover.
2. Add the `destination.ip` field.
3. Observe the number of unique destination IP addresses (referred to in databases as **Cardinality**) that each source IP contacted.

If a single source IP contacts a huge number of unique destination addresses (close to 254 addresses, which is the capacity of a /24 C-class network), it is definitive proof of a Ping Sweep.

🛠️ **Step 3: Confirmation via Raw Logs (Terminal)**
To check the same logic directly in the terminal without Kibana, we use this fast `awk` command on `log-session-2.csv`:

awk -v FPAT='([^,]+)|(\"[^\"]+\")' 'NR>1 {
  gsub(/"/,"",$2); gsub(/"/,"",$5);
  if($5=="0") print $2
}' log-session-2.csv | sort | uniq -c

256 192.168.230.127
Interpretation of Proof: Source 192.168.230.127 sent exactly 256 ICMP packets (on "port 0" which Zeek uses for ICMP traffic). This number matches the size of the /24 subnet (192.168.230.0/24), meaning this host scanned the entire subnet.

🔑 Final Answer for TryHackMe:
👉 192.168.230.127

⚡ Decrypting Zeek conn_state & Detecting TCP SYN Scans
Understanding Zeek Connection States (conn_state) is literally a "cheat sheet" for any analyst who wants to quickly discover what happened on the network without having to open bulky PCAP files in Wireshark.

TryHackMe Question: Using the information provided by this value, identify the type of scan being performed by 203.0.113.25 against 192.168.230.145

📋 What is a TCP SYN Scan (Half-Open Scan)?
During standard legal communication, a full Three-Way Handshake is used: SYN ➡️ SYN-ACK ➡️ ACK.
During a TCP SYN Scan (Half-Open), the attacker does the following:

Sends a SYN packet to a specific port on the victim.

If the port is open, the victim returns a SYN-ACK. The attacker immediately sends a RST (Reset) to drop the connection before it is fully established (hence "half-open").

If the port is closed or protected by a Firewall, the attacker sends a SYN, but receives no response (silence).

🔍 How does Zeek record this in logs? (conn_state)
Zeek uses short letter tags in the conn_state (Connection State) column to tell us the history of each connection.
During the attack from 203.0.113.25 against 192.168.230.145, the dominant state is S0.

| Zeek Tag | Real-World Meaning | What it tells us about the scan |
| :--- | :--- | :--- |
| **S0** | Connection attempt seen, no reply. (SYN sent by the attacker, but no response from the victim). | The port is closed or blocked. When we see thousands of such S0 rows for the same ports toward an IP, it is undeniable proof of a TCP SYN Scan where most ports are closed. |
| **SF** | Normal establishment and termination. (Normal three-way handshake and orderly connection close). | Legal traffic. If the attacker used this state on all ports, it would mean a Full Connect Scan (which is much noisier and easier to detect). |
| **REJ** | Connection attempt rejected. (The victim immediately returned an RST packet to reject the connection). | The port is definitely closed. The victim's active OS explicitly tells the scanner that no active service lives there. |

🛠️ **How to reproduce the proof in the terminal?**
To extract and count all connection states for this specific IP pair, we use the following `awk` command on `log-session-1.csv`:

awk -v FPAT='([^,]+)|(\"[^\"]+\")' 'NR>1 {
  gsub(/"/,"",$2); gsub(/"/,"",$4); gsub(/"/,"",$12);
  if($2=="203.0.113.25" && $4=="192.168.230.145") print $12
}' log-session-2.csv | sort | uniq -c

(Note: Column $12 in this log file represents the zeek.conn.conn_state value).

Expected output in the terminal:
You will get thousands of lines with the S0 state. This shows that the attacker sent thousands of SYN packets with no response, which is a textbook example of a half-open port scan.

🔑 Final Answer for TryHackMe:
👉 TCP SYN scan (or SYN scan)

🚫 Deep Analysis: Detecting UDP Scanning vs. Normal UDP TrafficTryHackMe Question: Is there any UDP scanning attempt in the logs? (Y/N)

👉 Answer: N

1. The Major Trap: Why is the mere presence of UDP not a scan?
Legal UDP traffic flows continuously in any network. For example, this line appears in your log:192.168.230.127 ➡️ 239.255.255.250:3702

What is this in reality? This is multicast traffic used by devices for automatic discovery on the network (like smart TVs and printers, via protocols like SSDP or UPnP).

Why is it NOT a scan? Because this is an isolated, normal network wave, not an organized attack.

🕵️‍♀️ How do we define a real UDP Scan in the SOC?
To declare a UDP scan, we must look for one of these three patterns in the logs:

Vertical UDP Scan: A single local IP sends UDP packets to many different ports on the exact same target machine.

Horizontal UDP Scan: A single IP sends UDP packets to the same port on many different machines in the network.

Indirect Proof (ICMP Unreachable): Since UDP is a connectionless protocol, when a port is closed, the victim's OS returns an ICMP Port Unreachable packet. If we see a wave of such ICMP responses toward a host, it means that host was scanning UDP.

Since none of these patterns exist in our log files, the correct answer is definitely N.

These are the commands I showed you earlier, but now they are optimized with additional logic so you can use them for any other task in the future!

1. Quick check of most active source IPs:
cat log-session-2.csv | cut -d "," -f3 | uniq -c
- Purpose: Quickly shows which IP sent the most traffic.

2. Cleaning and extracting destination IP addresses (without quotes):
awk -v FPAT='([^,]+)|(\"[^\"]+\")' 'NR>1 { gsub(/"/,"",$4); print $4 }' log-session-2.csv | sort | uniq
- Purpose: Extracts all unique destination addresses, fully immune to date format errors.

3. Automatic Vertical Scan detector:
awk -v FPAT='([^,]+)|(\"[^\"]+\")' '
NR>1{
  gsub(/"/,"",$2); gsub(/"/,"",$4); gsub(/"/,"",$5);
  key=$2","$4;
  ports[key][$5]=1
}
END{
  for(k in ports){
    n=0; for(p in ports[k]) n++;
    if(n>1){
      split(k,a,",");
      print "Vertical scan: source=" a[1] " target=" a[2] " ports=" n
    }
  }
}' log-session-2.csv
- Purpose: Analyzes the entire table, counts unique ports per IP pair, and directly prints the victim and the number of scanned ports.

4. Extracting scanned ports on a specific device in a single line (comma-separated):
awk -v FPAT='([^,]+)|(\"[^\"]+\")' '
NR>1{
  d=$4; p=$5; msg=$10;
  gsub(/"/,"",d); gsub(/"/,"",p);
  gsub(/""/,"\"",msg);
  if(d=="192.168.230.1"){
    if(p ~ /^[0-9]+$/) print p;
    if(match(msg,/id\.resp_p":([0-9]+)/,a)) print a[1];
    if(match(msg,/id\.orig_p":([0-9]+)/,b)) print b[1];
  }
}
' log-session-2.csv | grep -oE '^[0-9]+' | sort -n | uniq | paste -sd, -
- Purpose: Looks for machine 192.168.230.1, extracts all ports (even those hidden inside Zeek messages), sorts them, and formats them neatly for TryHackMe (e.g., 80,445,3389).

🎨 **SOC Analyst Cheat Sheet (Kibana & Elastic)**
When transitioning from the terminal to a graphical interface, you use these quick filters in Kibana for the same investigation:
- **For Ping Sweep detection:**
  `network.protocol:"icmp" and icmp.type:8`
  (Type 8 is requested because it designates an ICMP Echo Request, meaning the Ping itself).
- **For port detection on a specific flow:**
  In Dev Tools, use aggregation on the `zeek.conn.conn_state` field to see how many times the `S0` state appears relative to `SF` for a given source and destination.

---

## 🎯 Practical Takeaways and Application in the SOC

Network scanning is always the first step (Reconnaissance) of any cyber attack. An attacker cannot hack something they do not know exists. Therefore, how they scan reveals their plan and intent:
- **ICMP (Ping) Sweeps:** The attacker attempts to quickly map out the network to see which hosts are active.
- **Horizontal Scans:** The attacker searches for a specific vulnerability across the entire network. For example, checking if anyone has a web server running (port 80) to exploit it.
- **Vertical Scans:** The attacker has already chosen a specific target and wants to thoroughly inspect everything running on it to find the easiest way in.

### 📊 Incident Overview in this Exercise

| Scan Type | Attacker (Source) | Target | How We Proved It |
| :--- | :--- | :--- | :--- |
| **Ping Sweep** | `192.168.230.127` | Entire `/24` range | Found 256 ICMP packets sent to the entire subnet in `log-session-2.csv`. |
| **Horizontal Scan** | `203.0.113.25` | `203.0.113.0/24` | A single IP scanned a specific port set across hundreds of different devices in the same network range. |
| **Vertical Scan** | `192.168.230.127` | `192.168.230.145` | Extracted proof that the attacker targeted this local machine across 1001 unique ports in `log-session-2.csv`. |

### 🔍 How SOC Analysts Read These Traces:
When writing automated detection rules in SIEM systems like Elastic, we use these exact mathematical logics:
1. **For SYN Scan detection:** We look for an unusually high ratio of Zeek `S0` states (Connection attempt seen, no reply). If there are thousands of S0 traffic waves from a single IP, it automatically triggers a SYN Scan alarm.
2. **For Horizontal Scan detection:** We calculate how many unique destination IP addresses a single source IP contacted within a 5-minute window.
3. **For Vertical Scan detection:** We calculate how many unique destination ports were targeted between a specific attacker-victim pair.

⚠️ **Important lesson regarding UDP traffic in the SOC:**
> *"The absence of evidence in logs is not absolute evidence that an attack did not occur."*

In security, we must always remain skeptical. If the firewall is configured to silently drop unwanted UDP packets without logging them, our SIEM will see nothing. However, based on the available logs in this exercise, we found no pattern of UDP scanning (there was only legal multicast/discovery traffic), so the correct answer is definitely `N`.
