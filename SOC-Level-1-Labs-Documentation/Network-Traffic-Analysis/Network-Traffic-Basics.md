# Network Analysis Fundamentals

Learn what network analysis is, why it is essential, how to collect network traffic and which tools are available.

---

## Task 1: Introduction

### 🔍 What is Network Traffic Analysis (NTA)?
- **Definition:** The process of capturing, inspecting, and analyzing data as it flows across a network.
- **Main Goal:** To achieve complete visibility and understand precisely what communication is happening inside and outside the network.
- **Important Note:** NTA is not synonymous with Wireshark. Wireshark is merely a single packet analysis tool, whereas NTA is much broader—representing a combination of:
  1. Log correlation.
  2. Deep Packet Inspection (DPI).
  3. Network flow statistics with specifically defined objectives.

### 🛡️ Why is this Skill Crucial for SOC L1 Analysts?
- Knowing network traffic analysis is an essential skill not only for SOC L1 analysts but also for many other roles within blue teams and red teams.
- As an L1 analyst, you must be capable of navigating through the "sea" of network information and recognizing:
  - What constitutes normal network behavior (baseline).
  - What deviates from the baseline and indicates a potential security incident or attack.

### 🎯 Learning Objectives:
- Define what Network Traffic Analysis (NTA) is.
- Understand what can be observed within network traffic.
- Learn how network traffic is monitored.
- Familiarize ourselves with typical network traffic sources and flows.

### 📚 Prerequisites:
- To successfully master this room, it is recommended to complete the following learning paths beforehand:
  - Pre Security
  - Networking Essentials (Network basics: TCP/IP, IP addresses, ports, packets, etc.)

> **Command and Control (C2):** The operator-side infrastructure and implant code that let an attacker control compromised hosts over a network after initial access.

---

## Task 2: Purpose of Network Traffic Analysis

### 🕵️‍♂️ Practical Scenario: DNS Tunneling and Beaconing
To understand why we need network traffic analysis, the room presents a real-world simulated scenario from the daily work of a SOC analyst:
1. **Alert Signal:** We receive an alert stating that host `WIN-016` with IP address `192.168.1.16` is sending an unusual volume of DNS queries.
2. **Firewall Log Analysis:** We observe multiple DNS queries directed toward the same Top-Level Domain (TLD), but each time with different subdomain content (e.g., `aj39skdm.malicious-tld.com`, `cmd01.malicious-tld.com` with a `QTYPE=TXT` record type).

#### What can we extract from ordinary DNS logs?
- **Query and Query Type:** What is being requested and what the record type is (e.g., an `A` record for an IP or a `TXT` record for text).
- **Subdomain and TLD:** We can check domains via tools like AbuseIPDB or VirusTotal to see if they are flagged as malicious.
- **Host IP (Source IP):** We identify the infected system within the network.
- **Destination IP:** Checking the server where the requests are heading.
- **Timestamp:** Building a timeline of suspicious activities.

> **Log Limitation:** Standard firewall or DNS server logs only provide these basic metadata fields. They do not contain the content of the communication itself. Therefore, for deeper analysis, we require full packet inspection (NTA).

### 📦 Content Inspection (Packet Capture & C2 Commands)
**Why is this important?** Attackers can utilize `TXT` records to conceal and transmit Command and Control (C2) commands to a compromised system.

Inside the DNS response packet (via packet capture), the response fields reveal a `TXT` record containing encrypted/encoded data:

TXT: "SSBsb3ZlIHlvdXIgY3VyaW91c2l0eQ=="
(This is Base64 encoded text that, when decoded, reveals a message or a C2 command.)

- Question: What is the name of the technique used to smuggle C2 commands via DNS?   
- Answer: DNS Tunneling (or using DNS for tunneling and data/command transfer).   

📊 General Purpose of Network Traffic Analysis (NTA)
Generally speaking, NTA is used for:
- Monitoring network performance.   
- Checking for abnormalities (sudden spikes in traffic, slow network, etc.).   
- Inspecting the content of suspicious internal and external communications (e.g., exfiltration via DNS, downloading a malicious ZIP file over HTTP, lateral movement).   

From a SOC team perspective, NTA assists in:
1. Detecting suspicious or malicious activity.   
2. Reconstructing attacks during Incident Response.   
3. Verifying and validating received alerts.   

💡 Additional Scenarios Depicted in the Lesson:
- Scenario 1 (HTTP/Malware): An endpoint started deviating from normal behavior around 16:00 UTC. Through network traffic analysis, a suspicious HTTP request was found, and a malicious ZIP file was successfully extracted.   
- Scenario 2 (DNS Tunneling / Data Exfiltration): An alert showed a device sending excessive DNS queries compared to the network baseline. Inspection revealed data was being extracted out of the network using DNS tunneling.   

---

## Task 3: What Network Traffic Can We Observe?

🏗️ **1️⃣ The TCP/IP Stack**
To understand what network traffic we can observe, we utilize the TCP/IP model architecture. Each layer appends its own information (headers) via the encapsulation process before forwarding the data down to the subsequent layer.   
- Why we need NTA: Standard logs usually store only portions of these headers, but never the complete details of the packet or its payload.   

🌐 **2️⃣ Application Layer**
- What it contains: Application headers and application payload data.   
  - Example with HTTP (from text): The client sends a GET request for the file `/downloads/suspicious_package.zip`.   
  - The server responds with a 200 OK code and includes the file size (`Content-Length: 10485760` bytes) along with a download header as an attachment.   
  - Log Limitation: Proxies and firewalls will log the header, but they do not display the actual payload of the downloaded ZIP file.   

🎯 **First Question Answer:**
- Question: Look at the HTTP example in the task and answer the following question: What is the size of the ZIP attachment included in the HTTP response? Note down the answer in bytes.   
- Answer: `10485760` (as specified in Content-Length: 10485760 bytes within the HTTP response).   

🔄 **3️⃣ Transport Layer**
- What it contains: Transport headers (typically TCP or UDP) that segment the data.   
- Attack Detection (Session Hijacking): Firewall logs capture source/destination ports and flags. However, detecting attacks like Session Hijacking requires analyzing sequence numbers within the headers. If a sequence number abruptly spikes away from the baseline (like in line 6 of the Wireshark example where another source attempts to inject itself with a massive jump in the number), it indicates tampering.   

🎯 **Second Question Answer:**
- Question: What field in the TCP header can we use to detect session hijacking?   
- Answer: Sequence numbers (or Sequence number).   

🌍 **4️⃣ Internet Layer (IP)**
- What it contains: IP addresses and TTL (Time to Live). If a segment exceeds the MTU (Maximum Transmission Unit), it is fragmented.   
- Fragmentation Attacks: Attackers can utilize fragmentation (e.g., creating tiny fragments or using overlapping byte ranges) to trick Intrusion Detection Systems (IDS) and evade inspection.   

🎯 **Third Question Answer:**
- Question: Which attack do attackers use to try to evade an IDS?   
- Answer: Fragmentation attack (or Packet fragmentation).   

🔗 **5️⃣ Link Layer (MAC Addresses & ARP)**
- What it contains: Physical addresses (MAC addresses).   
- ARP Poisoning / Spoofing: Standard logs are insufficient for detecting ARP poisoning. Through Wireshark packet analysis, one can observe when a rogue host (e.g., with IP 192.168.1.200) responds to every ARP request with its own malicious MAC address (`aa:bb:cc:dd:ee:ff`), thereby redirecting traffic through itself.   

🧠 **What is ARP (Address Resolution Protocol)?**
ARP stands for Address Resolution Protocol. It is a fundamental network protocol used to map a logical IP address to a physical MAC address of a device inside a local area network (LAN).   

⚙️ **Why do we need ARP?**
When computers communicate across a network:
1. Applications and network layers use IP addresses (e.g., 192.168.1.10) because they are logical and easy to manage.   
2. Hardware and network interface cards (NICs) at the physical link layer communicate exclusively via MAC addresses (physical hardware addresses, e.g., 00:11:22:33:44:55).   

When Device A wants to send a packet to Device B on the same network, it knows Device B's IP address, but not its MAC address. This is where ARP steps in.   

🔄 **How ARP Works in Practice (The Process)**
The process unfolds in two steps (as shown in the traffic examples):
1. **ARP Request:**
   - The device broadcasts a message across the entire network:
   - Example from text: "Who has 192.168.1.10? Tell 192.168.1.1".   
2. **ARP Reply:**
   - The device owning that IP address recognizes it and replies directly to the sender:
   - Example from text: "192.168.1.10 is at 00:11:22:33:44:55".   

Once the device receives this reply, it caches it in temporary memory called the ARP Cache to avoid querying repeatedly.   

⚠️ **Why is ARP Vulnerable? (ARP Poisoning / Spoofing)**
The classic ARP protocol lacks any authentication mechanism. This implies that any device on the network can transmit a forged ARP reply without verification.   

As seen in the lesson:
- **ARP Poisoning:** An attacker can impersonate and send a message to the router or other computers claiming: "I am IP address 192.168.1.10 and my MAC address is aa:bb:cc:dd:ee:ff (my fake address)".   
- In this manner, all traffic destined for another device starts routing through the attacker's computer, enabling eavesdropping or session hijacking (Man-in-the-Middle attacks).
Task 4: Network Traffic Sources and Flows

🔌 1️⃣ Network Traffic Sources
Within a corporate network, traffic sources are divided into two main categories: Intermediary and Endpoint.

A. Intermediary Sources
- **What they are:** Devices through which traffic primarily passes. Generally, they do not generate significant traffic volumes themselves.    
- **Examples:** Firewalls, switches, web proxies, IDS, IPS, routers, access points, wireless LAN controllers, and ISP infrastructure.    
- **Where their traffic originates:** These devices generate traffic via routing protocols (EIGRP, OSPF, BGP), management (SNMP, PING), logs (SYSLOG), and support protocols (ARP, STP, DHCP).    

B. Endpoint Sources
- **What they are:** Devices where traffic physically originates and terminates.    
- **Why they matter:** Devices in this category consume the vast majority of network bandwidth.    
- **Examples:** Servers, hosts, IoT devices, printers, lab machines, cloud resources, mobile phones, and tablets.    

↔️ 2️⃣ Network Traffic Flows
Network flows are determined by network services (such as Active Directory, SMB, HTTPS, etc.) and flow in two directions:

A. North-South Traffic
- **Definition:** Traffic entering or exiting the local area network (LAN) that must pass through the firewall toward the WAN/Internet.    
- **Protocols:** Client-server protocols like HTTPS, DNS, SSH, VPN, SMTP, and RDP.    
- **Flow:** Comprises two directions: ingress (incoming) and egress (outgoing). Proper firewall rule and log configuration is vital for visibility here.    

B. East-West Traffic
- **Definition:** Traffic remaining entirely within the corporate LAN (including cloud-extended LANs).    
- **Security Aspect:** Often monitored less, yet critical for spotting attacks. When a network is compromised, attackers exploit internal services to move laterally (lateral movement).    

Core Categories of East-West Services:
1. **Directory, Authentication & Identity Services:** Kerberos/LDAP (Active Directory), RADIUS/TACACS+, internal Certification Authority.    
2. **File shares & print services:** SMB/CIFS (network shares), IPP/LPD (network printing).    
3. **Router, switching, and infrastructure services:** DHCP, ARP broadcast, internal DNS, routing protocols.    
4. **Application Communication:** Database connections (SQL via TCP), Microservices APIs (REST/gRPC).    
5. **Backup & Replication:** File replication between backup servers, MySQL/PostgreSQL replication.    
6. **Monitoring & Management:** SNMP, Syslog, NetFlow/IPFIX, endpoint logs sent to a central log server.    

<img width="785" height="837" alt="img ! NTA" src="https://github.com/user-attachments/assets/33ab0846-7fe9-4bee-bbb7-a3490d7160f6" />

🔬 3️⃣ Practical Flow Examples
- **HTTPS (with Web Proxy and TLS Inspection):** Client sends a request to an NGFW containing a web proxy.    
  - The web proxy acts as a web server to the client while opening a new TCP session with the real web server.    
  - Upon receiving the response, it inspects it for security and forwards it to the client (effectively creating two separate sessions).    
- **External DNS:** Client sends a DNS query to the internal DNS server on port 53.    
  - The internal server checks its cache; if missing, it routes the query through the router and firewall to external DNS servers, returning the response along the same path.    
- **SMB with Kerberos:** When a user attempts to open a network share (e.g., `\\FILESERVER\MARKETING`), authentication via Kerberos occurs first (the client acquires a Ticket Granting Ticket from the Domain Controller, then requests a service ticket).    
  - Once the ticket is approved, an SMB session is established to access shared files.    

🎯 **Task 4 Questions Answers:**
- Which category of devices generates the most traffic in a network? **Answer:** Endpoint (or Endpoint devices).    
- Before an SMB session can be established, which service needs to be contacted first for authentication? **Answer:** Kerberos.    
- What does TLS stand for? **Answer:** Transport Layer Security.    

---

Task 5: How Can We Observe Network Traffic?

🔍 1️⃣ Methods of Information Gathering
To analyze network traffic, we harvest data through three primary methods:    
1. Logs    
2. Full Packet Capture    
3. Network Statistics (Metadata)    

📄 2️⃣ Logs
- **Characteristics:** Logs serve as our primary entry point for discerning network happenings.    
- **No Universal Standard:** Every vendor independently decides how to implement logging and what to record (e.g., Windows Event Logs, Syslog formats, Apache CLF format).    
- **Limitation:** Most vendors do not record full packets, instead capturing only useful fields (like source and destination IP addresses).    
- **Standardized Protocols:** Despite the absence of a universal standard, protocols like Syslog and SNMP enable standardized log transmission to collectors. When logs fall short, full packet capture is required.    

📦 3️⃣ Full Packet Capture
To capture and inspect complete packets with all details, we have two hardware and software approaches:

A. Network Tap
- **What it is:** A physical hardware device placed inline directly between cables.    
- **How it works:** Creates a copy of all network traffic traversing it without impacting performance. Forwards the copied data to an analysis device (IDS or packet capture box) via a dedicated monitoring port.    
- **Key Characteristic:** Operates strictly at the Link Layer (requires no MAC or IP address) by duplicating electrical/optical signals, introducing zero network latency.    

B. Port Mirroring
- **What it is:** A software-based approach for copying packets from one intermediary device port to another port linked to an analytical system.    
- **Vendor Names:** Cisco terms this SPAN (Switched Port Analyzer).
  - Cisco configuration example:

  Switch(config)# monitor session 1 source interface fastEthernet0/1
  Switch(config)# monitor session 1 destination interface fastEthernet0/2
- Virtual Environment: Port mirroring can also be configured on virtual switches (e.g., VMware vSwitch, or AWS VPC Traffic Mirroring in the cloud).
- Disadvantage: Unlike a physical TAP with zero performance degradation, port mirroring can burden device performance when heavy traffic volumes pass through.

⚠️ **Best Practices:**
1. **Placement:** Positioning the TAP or configuring mirroring at the correct location based on targeted traffic.
2. **Storage Duration:** Full packet captures demand massive storage. For instance, a 1 Gbps line over a single day typically requires roughly 10.8 TB of space.
3. **Mirror vs TAP:** Physical TAPs incur zero performance overhead, whereas mirroring can load the router/switch.

🛠️ **Full Packet Capture Analysis Tools:**
- Wireshark (interactive GUI tool)
- TCPdump (command line)
- IPS/IDS systems: Snort, Suricata, Zeek

---

## 📊 4️⃣ Network Statistics (Metadata)

Instead of capturing raw packets, harvesting flow metadata is an exceptional method for spotting anomalies (like C2 traffic, exfiltration, or lateral movement).

- **NetFlow:** A protocol developed by Cisco that collects traffic flow metadata (contains no individual packets, only directional flow information from source to destination IP). Version 9 introduced templates for vendor compatibility.
- **IPFIX (Internet Protocol Flow Information Export):** An IETF successor to NetFlow developed in collaboration with Cisco and other vendors as a vendor-neutral standard. Offers greater flexibility in configuring collection fields.
- **Usage:** They require no new specialized infrastructure; modern NGFWs, IPS, and IDS units already feature built-in flow collection support—activation and collector routing are all that is required.

---

## 🎯 Practical Exercises (Static Site & Flags)

Within this platform task, opening the split-screen static page requires positioning the network TAP appropriately for both scenarios to uncover the flags within the traffic:

1. **Scenario 1 (HTTP Traffic):** Placing the TAP between the client and the network segment to capture HTTP packets and uncover the web flag.
2. **Scenario 2 (DNS Traffic):** Inspecting DNS requests to unearth the second flag.

<img width="800" height="398" alt="Flag 1 http " src="https://github.com/user-attachments/assets/597b7ef3-5ed5-45ba-ae50-75fe0997f5ef" />

<img width="800" height="382" alt="Flag DNS traffic TAP" src="https://github.com/user-attachments/assets/59971be9-eb3a-41bb-9145-ab0f89cdf734" />

🌐 **Placement Guide:**
- **To capture HTTP traffic (North-South / External):** Place the TAP at the `Drop here` slot directly beneath `FW1` (between the Firewall and Router) or at the top near the Internet / WP1 line. All external traffic must traverse the firewall.
- **To capture DNS traffic (Internal / DNS queries):** Place the TAP at the `Drop here` slot directly above `SRV-DNS` (on the left branch near `SW01`) or on the line running from switch `SW01` to the router. This captures local East-West traffic between clients and the DNS server.

---

## Task 6: Conclusion

🏁 **Room Summary and Conclusion**

Successfully completing this room equips us with fundamental concepts critical for every SOC L1 analyst:
- **What NTA is:** We understood that it transcends simple tools like Wireshark, representing an exhaustive process combining log correlation, Deep Packet Inspection (DPI), and network statistics.
- **Why NTA is needed:** We learned how to uncover hidden threats invisible to standard firewall logs, such as DNS Tunneling (smuggling C2 commands via TXT records) and downloading malicious files.
- **TCP/IP Stack and Layer Inspection:** We analyzed observations across layers: application payloads, transport sequence numbers (detecting session hijacking), internet fragmentation (evading IDS), down to link-layer ARP abuses (ARP poisoning).
- **Traffic Sources and Flows:** We distinguished between Intermediary and Endpoint sources, as well as North-South (external) and East-West (lateral/internal) traffic.
- **Traffic Capture Methods:** We mastered data collection via Network Taps (physical link-layer duplication with zero latency), Port Mirroring / SPAN (software duplication), and metadata collection via NetFlow and IPFIX.
