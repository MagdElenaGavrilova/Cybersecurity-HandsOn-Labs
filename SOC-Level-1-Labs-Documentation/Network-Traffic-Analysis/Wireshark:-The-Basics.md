# Wireshark: The Basics 🦈
Learn the basics of Wireshark and how to analyse protocols and PCAPs.

## Task 1: Introduction

📋 **1. What is Wireshark and why is it used?**
- **Definition:** Wireshark is an open-source, cross-platform network packet analyzer.
  1. **Main functions:** Sniffing: Capturing and recording live network traffic in real time.
  2. **PCAP Inspection:** In-depth analysis and inspection of existing recorded packet files (Packet Captures with extensions like `.pcap` or `.pcapng`).
- **Why it matters:** It is considered one of the best tools for network diagnostics, malware traffic analysis, and protocol dissection.

Wireshark is an open-source, cross-platform network packet analyser tool capable of sniffing and investigating live traffic and inspecting packet captures (PCAP). It is commonly used as one of the best packet analysis tools. In this room, we will look at the basics of Wireshark and use it to perform fundamental packet analysis.

🎯 **2. Learning Objectives**
- Navigation and configuration of the workspace in Wireshark.
- Packet inspection and discovery of information across different layers of the TCP/IP model (from the physical/data link layer all the way to the application layer).
- Applying Display Filters to isolate specific traffic.

📂 **3. Working Files in This Room**
Two separate files are provided on the lab machine for you to work with:
1. `http1.pcapng` — A file used exclusively for practicing, following steps, and simulating actions shown in the room's instructions.
2. `Exercise.pcapng` — The main file that must be used to find the answers to the exercise questions.

❓ **4. Task 1 Answers**
- Which file is used to simulate the screenshots?
  - 👉 `http1.pcapng`
- Which file is used to answer the questions?
  - 👉 `Exercise.pcapng`

🔥 **EXTRA FROM ME: SOC Analyst Perspective & Best Practices**
As a SOC Analyst, analyzing PCAP files is a daily task when determining whether an alert from a security system (SIEM/IDS) is a false positive or a real attack.

🛡️ **Key tips when working with Wireshark:**
1. **Understanding the TCP/IP everyday workflow:** Always keep the network layering model in mind. When analyzing traffic, move logically:
   - Ethernet layer (Who is communicating physically/MAC),
   - IP layer (Source and destination IP addresses),
   - Transport layer (TCP/UDP ports and handshakes),
   - Application layer (HTTP, DNS, TLS content).
2. **Proper use of filters:** Avoid looking at all packets manually. Learn immediately to use filters (e.g., `http`, `dns`, `ip.addr == x.x.x.x`) to reduce noise and focus only on suspicious traffic.

---

## Task 2: Tool Overview

📋 **1. Basic Uses of Wireshark (Use Cases)**
Wireshark is one of the most powerful network traffic analysis tools, and its main applications are:
- **Network Troubleshooting:** Identifying breakdown points in network traffic, congestion, and network errors.
- **Security Anomaly Detection:** Discovering rogue hosts, unusual port usage, and malicious or suspicious traffic.
- **Protocol Details Investigation:** Analyzing responses, status codes (like HTTP 200, 404, 500), and payload data.

⚠️ **Important Note:** Wireshark is not an IDS (Intrusion Detection System). It does not automatically block or detect attacks, but rather enables in-depth packet inspection. Additionally, it is a read-only tool—it does not modify packets, only reads them. The success of the analysis depends entirely on the analyst's knowledge and skills!

🖥️ **2. Graphical User Interface (GUI) and Data Structuring**
The Wireshark interface is divided into several main sections that assist in rapid analysis:
1. **Toolbar:** Menus and shortcuts for filtering, sorting, summarizing, exporting, and merging packets.
2. **Display Filter Bar:** The main section where queries and filters are entered to search traffic.
3. **Recent Files:** A list of recently opened files with quick double-click access.
4. **Capture Filter & Interfaces:** Places to select a network interface (e.g., `lo`, `eth0`, `ens33`) through which the network is listened to.
5. **Status Bar:** Displays tool status, active profile, and numerical packet information.

📦 **3. The Three Main Panels During Packet Inspection**
Packet capture (PCAP) is a networking practice involving the interception of data packets travelling over a network. Once the packets are captured, they can be stored by IT teams for further analysis. The inspection of these packets allows IT teams to identify issues and solve network problems affecting daily operations.

When a PCAP file is loaded, data is displayed across three distinct panels:
- **Packet List Pane (Top bar):** Provides a summary of each individual packet (source and destination IP, protocol, basic info). Clicking a packet opens its details in the other panels.
- **Packet Details Pane (Middle bar):** Provides a complete, hierarchical protocol view of the selected packet (across all layers of the OSI model).
- **Packet Bytes Pane (Bottom bar):** Displays packet content in Hex and ASCII formats, and automatically highlights/marks the field clicked in the middle panel.

🎨 **4. Colouring Packets**
Wireshark automatically colors packets according to protocol or specific conditions to help the analyst quickly spot anomalies.
1. **Types of rules:**
   - **Temporary rules:** Apply only to the current session (set via right-click or View -> Conversation Filter).
   - **Permanent rules:** Saved in a settings file (profile) and apply to future sessions as well (managed via View -> Coloring Rules).
- The "Colourise Packet List" option enables or disables these rules.

🔄 **5. Advanced Features: Traffic Sniffing, Merging & File Details**
- **Traffic Sniffing:** The blue "shark" button starts live capture, the red stops it, and the green restarts it.
- **Merge PCAP Files:** Allows merging two PCAP files into one via File --> Merge. (Important: The merged file must be saved before working on it).
- **View File Details:** To identify and prioritize files, details (such as hash, capture time, comments, interfaces) can be viewed via Statistics --> Capture File Properties or by clicking the pcap file icon in the bottom-left corner.

🎯 **6. Exercise Answers**
*(Use the `Exercise.pcapng` file to find these answers in Wireshark):*
1. **Read the "capture file comments". What is the flag?**
   - *How to find it:* Go to Statistics -> Capture File Properties (or click the pcap icon at the bottom left) and read the Comments field.
   - *Answer:* `THM{W4RK_SH4RK}`

<img width="800" height="553" alt="flag 1 " src="https://github.com/user-attachments/assets/1667a476-a5f6-492b-b317-35704faec7e4" />

---

2. **What is the total number of packets?**
   - *How to find it:* In the same Capture File Properties menu or in the status bar when the file is open.
   - *Answer:* `58620`

<img width="800" height="561" alt="q 1" src="https://github.com/user-attachments/assets/9cf60a19-1232-415f-9056-123ce2122931" />

===

3. **What is the SHA256 hash value of the capture file?**
   - *How to find it:* Also found in Capture File Properties under the file hashes section.
   - *Answer:* `f446de335565fb0b0ee5e5a3266703c778b2f3dfad7efeaeccb2da5641a6d6eb`

<img width="800" height="550" alt="hash256" src="https://github.com/user-attachments/assets/0cca0e8a-c603-4d7b-84bd-e4e8e924e03c" />

---

🔥 **EXTRA FROM ME: SOC Analyst Best Practices for PCAP Analysis**
As a SOC Analyst, when receiving a new PCAP file for incident analysis, follow this professional procedure:
- **Always check file properties initially (Capture File Properties):** Checking the SHA256 hash is mandatory to preserve digital integrity (Chain of Custody) and compare it against Threat Intelligence databases if the file originates from a known attack source.
- **Comments often contain key contextual notes** from other analysts or system warnings.
- **Ignore noise through profiles and colors:** In production environments, traffic is massive. Creating custom permanent coloring rules for critical events (such as failed logins, traffic to known C2 servers, or weird TCP flags) saves you hours of manual searching.

---

## Task 3: Packet Dissection

📋 **1. What is Packet Dissection?**
- **Definition:** Packet dissection (also known as protocol dissection) is the process of investigating and breaking down packet details by decoding available protocols and fields.
- **Functionality:** Wireshark supports a huge list of protocols for dissection and also allows writing custom dissection scripts.
- **Connection to the OSI model:** This section builds upon how Wireshark utilizes the layers of the OSI model to break packets down into smaller parts for easier analysis. (Prior understanding of how the OSI model works is assumed).

🔍 **2. Inspecting Packet Details (Details Panel)**
When you click a packet in the top panel (Packet List Pane), its details open in the middle panel (Packet Details Panel). By double-clicking, details can also be opened in a separate window.
Depending on the packet, Wireshark displays 5 to 7 layers directly corresponding to network layers:
1. **The Frame (Layer 1 - Physical Layer):** Displays details specific to the physical layer of the frame/packet.
2. **Source [MAC] (Layer 2 - Data Link Layer):** Displays source and destination MAC addresses.
3. **Source [IP] (Layer 3 - Network Layer):** Displays source and destination IPv4/IPv6 addresses.
4. **Protocol (Layer 4 - Transport Layer):** Displays transport protocol details (UDP or TCP) as well as source and destination ports.
5. **Protocol Errors:** Continuation of Layer 4 showing specific TCP segments that required reassembly.
6. **Application Protocol (Layer 5 - Application Layer):** Displays details of the used application protocol (like HTTP, FTP, SMB, etc.).
7. **Application Data:** Continuation of Layer 5 showing raw application-specific data content.

💡 *Note:* Every time you click an item in the middle panel, Wireshark automatically highlights the corresponding portion in the Packet Bytes Pane in Hex and ASCII format.

🎯 **3. Exercise Answers**
*(Open the `Exercise.pcapng` file in Wireshark and locate packet number 38 to get these answers):*
1. **View packet number 38. Which markup language is used under the HTTP protocol?**
   - *How to find it:* Expand packet 38 down to the HTTP protocol details.
   - *Answer:* `eXtensible Markup Language`

<img width="800" height="539" alt="markup language" src="https://github.com/user-attachments/assets/205f67ce-f57d-4d2c-ab39-2c46e47d0a29" />

===

2. **What is the arrival date of the packet? (Answer format: Month/Day/Year)**
   - *How to find it:* Within the first layer (Frame) in packet details, look for the Arrival Time field. Convert the date into MM/DD/YYYY format.
   - *Answer:* `05/13/2004`

<img width="800" height="567" alt="time date packet" src="https://github.com/user-attachments/assets/589b783d-b8a5-41a5-b5ff-88379a8744ea" />

===

3. **What is the TTL value?**
   - *How to find it:* Open the third layer (Internet Protocol Version 4 / Layer 3) and look for the Time to Live (TTL) field.
   - *Answer:* `47`

<img width="800" height="550" alt="TTL " src="https://github.com/user-attachments/assets/0850d60c-94c4-446a-901e-51c346b61df0" />

---

4. **What is the TCP payload size?**
   - *How to find it:* Open the fourth layer (Transmission Control Protocol) and look for the TCP Payload / Len field.
   - *Answer:* `427`
  
<img width="800" height="554" alt="TCP size" src="https://github.com/user-attachments/assets/4cf7858c-bed7-445e-a346-2239704fee3b" />

---

5. **What is the e-tag value? (For example: 82ecb-6321-9e904585)**
   - *How to find it:* In the fifth layer (Hypertext Transfer Protocol / HTTP Response), expand Response Headers and look for the ETag field.
   - *Answer:* `9a01a-4696-7e354b00`

<img width="800" height="544" alt="eTag " src="https://github.com/user-attachments/assets/37cbd371-6f5c-46ef-9c7f-beb21265c493" />

---

🔥 **EXTRA FROM ME: SOC Analyst Perspective on Packet Dissection**
As a SOC Analyst, the skill of isolating and reading individual layers through dissection is critical when investigating advanced web attacks or phishing campaigns:
- **Why is TTL (Time to Live) important?** TTL value helps in network forensics to determine whether a packet originates directly from a device with a specific OS or passes through routers (initial standard defaults are usually 64 for Linux/Unix, 128 for Windows, etc.). Changes in TTL can indicate network redirections or NAT.
- **Application Headers (HTTP ETag / Headers):** Fields like ETag or Server Headers are used by analysts for fingerprinting web servers and applications. They can help confirm the exact software version the target is using, aiding in vulnerability assessment.

---

## Task 4: Packet Navigation

📋 **1. Packet Navigation & Finding**
When working with large PCAP files containing thousands or millions of packets, manual searching is impossible. Wireshark offers several powerful navigation tools:
- **Packet Numbers:** Wireshark automatically assigns a unique number to each packet for easy return to a specific traffic location.
- **Go to Packet:** The "Go" menu allows jumping quickly to a specific packet number, navigating up/down, or following a conversation.
- **Find Packets (Content Search):**
  - Via Edit --> Find Packet, you can search for specific attack patterns or errors.
  - Supports four input types: Display filter, Hex, String, and Regex.
  - *Very important rule:* You can choose which panel to search in (packet list, packet details, or packet bytes). If you are looking for information located in the middle panel (details) while searching in the top panel (list), Wireshark won't find it even if it exists!

📌 **2. Data Organization and Extraction for Analysts**
- **Mark Packets:** Marking packets (via right-click or Edit menu) helps highlight suspicious events. Marked packets display in black. *(Note: Markings are temporary and lost when closing the file).*
- **Packet Comments:** Adding comments to specific packets. Unlike markings, comments remain saved in the file until explicitly removed.
- **Export Packets:** Allows isolating specific, suspicious packets into a new file via File --> Export Packets, eliminating unnecessary noise during deep analysis.
- **Export Objects (Files):** Wireshark can automatically extract and save files that traveled over the wire. This option is available for protocols like DICOM, HTTP, IMF, SMB, and TFTP.

⏱️ **3. Time Display & Expert Info**
- **Time Display Format:** By default, Wireshark displays seconds from capture start, but analysts frequently use UTC Time (View --> Time Display Format) for synchronization with other logs (SIEM).
- **Expert Info:** Wireshark automatically detects network anomalies and problems via categories and severities:
  - 🔵 **Chat (Blue):** Information about normal workflow.
  - 🖵 **Note (Cyan):** Significant events (e.g., application code errors).
  - 🟡 **Warn (Yellow):** Warnings (unusual errors or problems).
  - 🔴 **Error (Red):** Serious issues (e.g., malformed packets).
  - *How to open:* Via Analyse --> Expert Information or the bottom-left corner icon.

🎯 **4. Exercise Answers**
*(Use the `Exercise.pcapng` file to find these answers in Wireshark):*
1. **Search the "r4w" string in packet details. What is the name of artist 1?**
   - *How to find it:* Go to Edit --> Find Packet, select String, search in Packet details, and input `r4w`. Check the packet where it is found (usually in HTTP content or images) to read the first artist's name.
   - *Answer:* `r4w8173`
  
<img width="800" height="557" alt="r4w artist1 " src="https://github.com/user-attachments/assets/d5e5cb14-7464-452c-9c63-b84bc6db726a" />

---

2. **Go to packet 12 and read the packet comments. What is the answer? Note: use md5sum terminal command to get MD5 hash**
   - *How to find it:* Go to packet 12 (Go -> Packet -> 12), open details, and read the packet comment to find the extracted filename. Then run `md5sum <filename>` in the Kali terminal to get its MD5 hash.
   - *Answer:* `911cd574a42865a956ccde2d04495ebf`
   
<img width="800" height="563" alt="comment 3975 packet " src="https://github.com/user-attachments/assets/e12a2d0a-17b1-46ad-83ee-6d57ae43513b" />

<img width="800" height="539" alt="save export bytes" src="https://github.com/user-attachments/assets/0db571c2-0d55-417b-a8b6-e9de0c391ce7" />

<img width="800" height="534" alt="final md5sum" src="https://github.com/user-attachments/assets/f290abf8-ea6a-4b8e-9cfe-e0764be8f30e" />

---

3. **There is a ".txt" file inside the capture file. Find the file and read it; what is the alien's name?**
   - *How to find it:* Go to File -> Export Objects -> HTTP, find the `.txt` file in the list, save it, and open it via terminal (`cat filename.txt`) to read the alien's name.
   - *Answer:* `PACKETMASTER`

<img width="800" height="553" alt="txt file export object 1 slika " src="https://github.com/user-attachments/assets/9e90b503-eb8b-492e-a673-6d2bda48ee50" />

<img width="800" height="548" alt="save txt " src="https://github.com/user-attachments/assets/89f2dae4-8427-4485-b00a-8b0470713802" />

<img width="800" height="555" alt="terminal txt " src="https://github.com/user-attachments/assets/e2221125-d694-4f72-b1fe-370d0bcccf54" />

<img width="800" height="524" alt="packetmaster" src="https://github.com/user-attachments/assets/cd327e06-e533-44cb-9fb6-196ccaef0834" />

---

4. **Look at the expert info section. What is the number of warnings?**
   - *How to find it:* Go to Analyse --> Expert Information and check the warnings column (Warn yellow color) to see the total number of occurrences.
   - *Answer:* `1636`

<img width="800" height="558" alt="num warning" src="https://github.com/user-attachments/assets/9c4f3667-d38f-425a-b303-174f7a60a341" />

---

🔥 **EXTRA FROM ME: SOC Analyst Perspective on Packet Export & Expert Info**
As a SOC Analyst, using these advanced Wireshark tools accelerates Incident Response:
- **Export Objects:** Imagine a scenario where a user downloaded malware via a phishing link. Instead of analyzing hundreds of HTTP packets manually, using File -> Export Objects -> HTTP allows you to directly download the executable file (`.exe`, `.zip`) or script from the traffic and immediately forward it to an isolated Sandbox environment or calculate its SHA256 hash for VirusTotal.
- **The Power of Expert Info:** When dealing with a massive PCAP file, Expert Info serves as your "fast track" to errors. The appearance of red Errors (Malformed packets) often indicates active attempts at DDoS, manipulation of network stacks, or buffer overflow attacks that break protocol structures.

---

## Task 5: Packet Filtering

📋 **1. Packet Filtering**
Wireshark possesses an exceptionally powerful filtering system that helps analysts reduce traffic volume and focus only on what matters. There are two main types of filters:
1. **Capture Filters:** Used to capture only those packets that satisfy the filter before they are even written to the file.
2. **Display Filters:** Used to view (display) already recorded packets in the file.

💡 *Analyst Golden Rule:* "If you can click on it, you can filter and copy it" — you don't always have to manually write complex filters; Wireshark makes filter creation easy via right-click.

🛠️ **2. Filtering and Analysis Methods via Menus**
- **Apply as Filter:** The easiest filtering method. Right-click the desired field and select Apply as Filter to see only packets with that value.
- **Conversation Filter:** Unlike the previous one which filters a single field, this filter displays all related packets in a single network session (focusing on IP addresses and ports) and hides the rest.
- **Colourise Conversation:** Colors related packets in a specific color without hiding the rest of the packets from view. Can be reset via View --> Colourise Conversation --> Reset Colourisation.
- **Prepare as Filter:** Adds the filter to the filter bar but doesn't execute it immediately, waiting for you to add logical operators (like `and` / `or`) or press Enter.
- **Apply as Column:** Adds a new vertical column in the top packet list panel for easier tracking of values across the entire PCAP file.

🔄 **3. Follow Stream**
Wireshark displays everything as individual packets, but the Follow Stream function enables reconstruction of full communication at the application level (such as HTTP, TCP, or UDP protocols).
- **What it looks like:** A separate window opens where server data is marked in blue and client data in red.
- **Important Note:** When you open a stream, Wireshark automatically applies a filter for that stream, changing the packet count displayed. To return to all packets, click the `X` button to the right of the filter bar.

🔍 **4. Simple Display Filter Queries**
For quick searching, use the following structures:
1. **Filtering by protocol name:** Just enter the name in the bar (e.g., `http`, `arp`, `dns`, `ftp`, `smtp`).
2. **Filtering by port:** `tcp.port == <port_number>` or `udp.port == <port_number>` (e.g., `tcp.port == 80`).
3. **Filtering by IP address:** `ip.addr == <IP_address>` (e.g., `ip.addr == 192.168.1.2`).

🎯 **5. Exercise Answers**
*(Use the `Exercise.pcapng` file in Wireshark to find these answers):*
1. **Go to packet number 4. Right-click on the "Hypertext Transfer Protocol" and apply it as a filter. Now, look at the filter pane. What is the filter query?**
   - *How to find it:* Click packet #4, expand details down to Hypertext Transfer Protocol, right-click, and select Apply as Filter -> Selected. Look at the filter bar above.
   - *Answer:* `http`

<img width="800" height="553" alt="http filter " src="https://github.com/user-attachments/assets/80045265-e683-4e09-b6ec-c338cdb86cee" />

2. **What is the number of displayed packets?**
   - *How to find it:* After applying the above filter (`http`), look at the status bar at the bottom to see the displayed packet count.
   - *Answer:* `1089`

<img width="800" height="555" alt="num http " src="https://github.com/user-attachments/assets/77873fd4-34fa-4557-9562-48004b0e24ee" />

===

3. **Go to packet number 33790, follow the HTTP stream, and look carefully at the responses. Looking at the web server's response, what is the total number of artists?**
   - *How to find it:* Find packet 33790 (or via Go -> Packet -> 33790), right-click -> Follow -> HTTP Stream, and read the JSON response from the server where the number of artists is listed.
   - *Answer:* `3`

<img width="800" height="553" alt="find artist num" src="https://github.com/user-attachments/assets/6566bc53-c162-42d1-93a4-6e20c11f0d31" />

<img width="800" height="537" alt="3 artists answ" src="https://github.com/user-attachments/assets/6387355b-0ce1-4a63-91bd-76cf313795d5" />

---

4. **What is the name of the second artist?**
   - *How to find it:* In the same opened HTTP stream from the previous question, locate the list of artists and read the name of the second artist in sequence.
   - *Answer:* `Blad3`

<img width="800" height="559" alt="name 2 artist" src="https://github.com/user-attachments/assets/13555d11-2e08-4f6b-b6b3-fcd4a1e89ca9" />

---

🔥 **EXTRA FROM ME: SOC Analyst Perspective on Follow Stream and Filters**
As a SOC Analyst, using these tools for filtering and stream following is crucial when analyzing credential transmission or scripts:
- **Discovering Cleartext Credentials:** Many old or misconfigured applications use insecure protocols (like HTTP, Telnet, FTP). When you use Follow HTTP Stream on suspicious traffic, you can immediately see usernames and passwords in cleartext (plaintext), which is a strong indicator of account compromise.
- **Noise Reduction:** When analyzing potential DDoS or scanning, using the Conversation Filter allows you to isolate the entire dialogue between the attacker and victim within a second, ignoring the rest of the company's network traffic.

---

## Task 6: Conclusion

📋 **1. Room Summary**
By completing this room, you have successfully mastered the basics of working with one of the most powerful network tools in cybersecurity: Wireshark. The exercises covered the following key areas:
- **Tool Introduction:** What Wireshark is, how it works, and its role (as a non-invasive packet analyzer, not an IDS).
- **Interface and Navigation:** Becoming familiar with the three main panels (Packet List, Packet Details, Packet Bytes) and workspace organization.
- **Packet Dissection:** Understanding the relationship between packets and OSI model layers (from the physical layer all the way to application data).
- **Navigation, Comments, and Extraction:** Using packet numbers, find filters, adding comments, marking, and extracting objects/files from traffic.
- **Packet Filtering:** Applying display filters, using Apply as Filter, Conversation Filters, and following network streams via Follow TCP/UDP/HTTP Stream.
