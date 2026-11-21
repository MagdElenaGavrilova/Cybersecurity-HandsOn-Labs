Learn how to use NetworkMiner to analyse recorded traffic files and practice network forensics activities.

## Task 1: Room Introduction

### 🎯 1. Room Overview
- **Title:** NetworkMiner (Premium room), approximate duration 60 min.
- **Essence of the room:** You learn how to use NetworkMiner for analyzing recorded traffic files and practice network forensics activities.

### 📓 2. Learning Objectives & Prerequisites
- **Learning Objectives:**
  - Discover what NetworkMiner is.
  - Learn the basics of NetworkMiner.
  - Explore the different information tabs and extract data efficiently.
- **Prerequisites:**
  - Completed room Network Fundamentals.

### 🔑 3. Key Definitions and Tool Description

#### 🔹 Tool Description
- NetworkMiner is an open-source Network Forensic Analysis Tool (NFAT), developed and maintained by Netresec.
- Runs on Windows, Linux, macOS, and FreeBSD.
- Can be used in two ways:
  1. As a passive sniffer that fingerprints hosts, sessions, and open ports without sending any traffic.
  2. As a PCAP parser that reconstructs/reassembles files and certificates from recorded traffic for offline analysis.
- Packet capture (PCAP) is a networking practice involving the interception of data packets travelling over a network. Once the packets are captured, they can be stored by IT teams for further analysis. The inspection of these packets allows IT teams to identify issues and solve network problems affecting daily operations.
- The room covers the basics of network analysis and guides you through investigating recorded traffic with NetworkMiner.

### 🖥️ 4. Environment and Setup

#### 🔹 Lab Machine & Environment
- Throughout the entire room, the same virtual machine (VM) is used for the walkthrough and exercises — an Ubuntu 20.04 VM with two versions of NetworkMiner installed.
- The tasks have instructions on when a specific version should be used.
- The machine is started by clicking Start Lab Machine; it opens in split-view (or by clicking the blue Show Split View at the top of the room).

#### 🔹 How To Start NetworkMiner
- Go to the Desktop of the virtual machine, open the NetworkMiner folder (select the appropriate version), and double-click NetworkMiner.exe to start it.
  - **Important notes for working with PCAP files:**
    - Loading larger PCAP files can take up to one minute — patience is required.
    - Due to how NetworkMiner visualizes data, sometimes some packets/frames are not displayed. If you notice you are missing a frame (for example when answering a question), you need to reload the PCAP file.

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective
- **When in a real investigation do you reach for NetworkMiner, and when for Wireshark?**
  NetworkMiner is great when you want to quickly see who communicated with whom, which hosts and ports exist, which files or certificates were transferred, because by default it organizes data around hosts and artifacts rather than just as a linear list of packets.
  Wireshark is indispensable when you need to dive deep into a specific packet, protocol exchange, headers, timings, or filtering at the packet/stream level. In practice, they usually complement each other.
- **Why are "passivity" and reloading PCAP important in forensics?**
  The passive approach means you do not introduce additional traffic into the network while observing live.
  And during offline PCAP analysis, if some view or visualization does not look complete or you miss a detail for a question/investigation, reloading the PCAP or checking in parallel in Wireshark helps you not draw a wrong conclusion.
- **Note on NetworkMiner.exe on Linux/Ubuntu:**
  Although the room runs on an Ubuntu 20.04 VM, `NetworkMiner.exe` is started (via Mono / the installed environment on the VM according to the instructions in the folder), so always pay attention to which of the two installed versions the specific task requires.

---

## Task 2: NetworkMiner in Forensics

### 🎯 1. Essence of Network Forensics and the Role of NetworkMiner
- The ultimate goal of network forensics is to provide enough information to detect malicious activity, security breaches, and network anomalies based on network traffic.
- NetworkMiner is a great tool for that purpose because it gives quick and useful hints on where to start, more specifically:
  - Context of captured hosts: such as their IP and MAC addresses, hostnames, or operating system (OS) information. Operating System (OS) is a layer between the hardware and the applications. From the application's perspective, the OS provides an interface to access the different hardware components, such as CPU, RAM, and disk storage. Examples of OS are Android, FreeBSD, Linux, macOS, and Windows.
  - List of potential indicators of attack or anomalies: such as traffic spikes or port scans.
  - Tools or toolkits used to execute potential attacks: such as Nmap.

### 📂 2. Supported Data Types
In network forensics, three main types of data are investigated:
- Live Traffic
- Traffic Captures
- Log Files
- NetworkMiner can process and handle both packet captures (PCAP) and live traffic, which is why in this room the focus is on live and captured traffic.

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective
- **Why are "quick hints" so valuable when opening a new PCAP?**
  When you have a capture with thousands of packets in front of you, immediately seeing which hosts exist (IP, MAC, hostname, possible OS), whether there is port scanning or anomalies, and whether a signature/trace from tools like Nmap appears saves you time and helps you build a hypothesis on where to look next.
- **How do the three data types connect in a real investigation?**
  Traffic Captures (PCAP) give you raw network truth about what passed over the wire/interface, Live Traffic helps during ongoing monitoring, and Log Files (from servers, firewalls, web applications, SIEM) give context on what happened inside systems before, during, and after the traffic.

---

## Task 3: What is NetworkMiner?

### 🎯 1. NetworkMiner in a Nutshell — Capabilities and Description
The text lists the following key capabilities of NetworkMiner:
- **Traffic sniffing:** Can intercept traffic, "sniff" it, and collect and log packets passing through the network.
- **Parsing PCAP files:** Can parse pcap files and display packet content in detail.
- **Protocol analysis:** Can identify protocols used from the parsed pcap file.
- **OS fingerprinting:** Can identify the operating system (OS) used by reading the pcap file; this feature relies heavily on Satori and p0f.
- **File Extraction:** Can extract images, HTML files, and emails from the parsed pcap file.
- **Credential grabbing:** Can extract credentials from the parsed pcap file.
- **Clear text keyword parsing:** Can extract keywords and strings in cleartext from the parsed pcap file.

**Note on version:** In this room the NetworkMiner free edition is used, while the Professional edition has many more features (and the differences between the free and professional versions are listed in the corresponding comparison/link).

### ⚙️ 2. Operating Modes
There are two main operating modes:
- **Sniffer Mode:**
  - Although it has a sniffing feature, it is not intended to be used as a sniffer.
  - The sniffing feature is available only on Windows, while the remaining features are available on Windows and Linux OS.
  - According to experience, the sniffing feature is not as reliable as other features, so it is recommended not to use it as a primary sniffer.
  - Even the official description of the tool says it is a Network Forensics Analysis Tool that can be used as a sniffer, but it is not a dedicated sniffer like Wireshark and tcpdump.
- **Packet Parsing/Processing:**
  - NetworkMiner can parse traffic captures to get a quick overview and information about the capture being investigated.
  - This mode is mainly recommended to collect "low hanging fruit" (the most easily and quickly accessible traces/artifacts) before diving into deeper investigation.

### ⚖️ 3. Pros and Cons
As mentioned in the previous task, NetworkMiner is mainly used to get an overview of the network. Before starting to investigate traffic data, it is important to weigh the pros and cons:

#### ✅ Pros
- OS fingerprinting
- Easy file extraction
- Credential grabbing
- Clear text keyword parsing
- Overall overview

#### ❌ Cons
- Not useful in active sniffing
- Not useful for large pcap investigation
- Limited filtering
- Not built for manual traffic investigation

### 🆚 4. Differences Between Wireshark and NetworkMiner
- Although NetworkMiner and Wireshark have similar basic capabilities, they differ in purpose. Although the main features are identical, some capabilities are much stronger for certain use cases.
- **Best practice:** Capture traffic for offline analysis, do a quick overview of the pcap with NetworkMiner, and then investigate deeply with Wireshark.

### 📊 Comparison Table

| Feature | NetworkMiner | Wireshark |
| :--- | :--- | :--- |
| **Purpose** | Quick overview, traffic mapping, and data extraction | In-Depth analysis |
| **GUI** | ✅ | ✅ |
| **Sniffing** | ✅ | ✅ |
| **Handling PCAPS** | ✅ | ✅ |
| **OS Fingerprinting** | ✅ | ❌ |
| **Parameter/Keyword Discovery** | ✅ | Manual |
| **Credential Discovery** | ✅ | ✅ |
| **File Extraction** | ✅ | ✅ |
| **Filtering Options** | Limited | ✅ |
| **Packet Decoding** | Limited | ✅ |
| **Protocol Analysis** | ❌ | ✅ |
| **Payload Analysis** | ❌ | ✅ |
| **Statistical Analysis** | ❌ | ✅ |
| **Cross-Platform Support** | ✅ | ✅ |
| **Host Categorisation** | ✅ | ❌ |
| **Ease of Management** | ✅ | ✅ |

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective
- **What exactly does "low hanging fruit" mean in practical network forensics?**
  Those are the first, most obvious, and quickest-to-reach artifacts from the capture: for example which hosts exist, which files or images were transferred, whether cleartext credentials or obvious keywords appear. With them you immediately get an initial picture of whether there is something suspicious and where you need to dig deeper.
- **Why is the workflow "NetworkMiner first -> Wireshark after" so effective?**
  NetworkMiner gives you a map of hosts and extracted artifacts without having to immediately scroll through hundreds or thousands of packets. Once you notice a specific IP address, session, file, or anomaly, you go to Wireshark for precise filters, decoding, payload analysis, and protocols.
- **Be careful when working with large PCAPs:**
  Because one of NetworkMiner's cons is that it is not best suited for large pcap files and has limited filtering, with larger captures it can be slower or less clear for fine details — so choosing the right tool for the right step is a key skill.

---

## Task 4: Tool Overview 1

### 🎯 1. Tool Overview — Interface and Menus

#### 🔹 Landing Page
- The initial screen of NetworkMiner that loads immediately after opening the application.

#### 🔹 File Menu
- Allows loading a PCAP file or receiving PCAP over IP (Pcap over IP).
- Drag-and-drop can also be used to drag PCAP files.
- Because NetworkMiner here is used for initial investigation, quick artifact retrieval ("low hanging fruit"), and traffic overview, Pcap over IP is skipped in this room.

#### 🔹 Tools Menu
- Helps clear the dashboard and remove collected data.

#### 🔹 Help Menu
- Provides information about news/updates and the current version of the tool.

#### 🔹 Case Panel
- Displays a list of investigated PCAP files.
- Allows reloading/refreshing, viewing metadata details, and removing loaded files.
- **Viewing metadata:** Open the PCAP file, go to the Case Panel on the right side, right-click on the file name, and select "Show Metadata". Using full-screen mode is recommended.

### 📊 2. Information Tabs in NetworkMiner (Tabs)

#### 🔹 Hosts
- Displays all identified hosts in the PCAP file.
  - **Displayed information:**
    - IP address
    - MAC address
    - OS type
    - Open ports
    - Sent/Received packets
    - Incoming/Outgoing sessions
    - Host details
- OS fingerprinting uses Satori and p0f, while the MAC address database uses the `mac-ages` repository.
- Hosts can be sorted via the sort menu, and the color of hosts can be changed. Right-click allows easy copying of values.

#### 🔹 Sessions
- Displays detected sessions in the PCAP file.
  - **Displayed information:** Frame number, Client and server address, Source and destination port, Protocol, Start time.
- Allows searching keywords inside frames via the filter bar.
- **4 filter input types:** `"ExactPhrase"`, `"AllWords"`, `"AnyWord"`, `"RegExe"`.

#### 🔹 DNS
- Displays DNS requests with details: Frame number, Timestamp, Client and server, Source and destination port, IP TTL, DNS time, Transaction ID and type, DNS query and answer.
- Has a search bar.

#### 🔹 Credentials
- Displays extracted credentials and password hashes from investigated PCAP files.
- Hashcat and John the Ripper can be used for decryption.
  - **Types of supported credentials/hashes:**
    - Kerberos hashes
    - NTLM hashes
    - RDP cookies
    - HTTP cookies
    - HTTP requests
    - IMAP
    - FTP
    - SMTP
    - MS SQL
- Right-click allows easy copying of username and password values.

### 📝 3. Questions and Exact Answers for Task 4

#### 📁 Part 1: File `~/Desktop/Exercise Files/mx-3.pcap`
1. **What is the total number of frames?**
   - **Answer:** `460`
   - **How to find it:** In the Case Panel (right), right-click on `mx-3.pcap` and select `Show Metadata`, or in the file details / expanding panels where the total number of frames is listed.

<img width="852" height="560" alt="numb frames q1" src="https://github.com/user-attachments/assets/c6aa7bc6-c2ea-42ae-89d5-a1a5dc48858a" />

---

2. **How many IP addresses use the same MAC address with host 145.253.2.203?**
   - **Answer:** `2`
   - **How to find it:** In the `Hosts` tab, locate host `145.253.2.203` and see its MAC address (or group/sort by MAC address). A total of 2 IP addresses share the same MAC address.

<img width="1342" height="831" alt="q 2 ip addrs " src="https://github.com/user-attachments/assets/c04f8632-d007-4a2c-9701-216e5c7fd840" />

---

3. **How many packets were sent from host 65.208.228.223?**
   - **Answer:** `72`
   - **How to find it:** In the `Hosts` tab, find `65.208.228.223` and hover/stay on the host details where `Sent Packets / Sent frames: 72` is listed.

<img width="1392" height="844" alt="q 3 task 1 packet send" src="https://github.com/user-attachments/assets/d8454a0b-de02-4169-a009-a93c44809fe2" />

---

4. **What is the name of the webserver banner under host 65.208.228.223?**
   - **Answer:** `Apache`
   - **How to find it:** By expanding the details for host `65.208.228.223` in the `Hosts` tab (under open ports / HTTP port 80 / Details), where NetworkMiner automatically extracts and displays the Banner/Server string.

<img width="1383" height="840" alt="q4 task 1 web banner Apache" src="https://github.com/user-attachments/assets/3dd04b8d-9c37-46bf-926f-3471562e61fc" />

---

#### 📁 Part 2: File `~/Desktop/Exercise Files/mx-4.pcap`
1. **What is the extracted username for the 02694W-WIN10 host?**
   - **Answer:** `#B\Administrator`
   - **How to find it:** By loading `mx-4.pcap` and opening the `Credentials` tab. There you find the entry associated with host `02694W-WIN10` where the username is extracted as `#B\Administrator`.

<img width="1373" height="849" alt="task 1 username extraxted credential " src="https://github.com/user-attachments/assets/cddfda01-c3aa-4d80-b95e-56e1dd1cc771" />

---

2. **What is the extracted password for the user logged into the 02694W-WIN10 host? Enter the full NTLM hash.**
   - **Answer:** `$NETNTLMv2$#B$136B077D942D9A63$FBFF3C253926907AAAAD670A9037F2A5$01010000000000000094D71AE38CD60170A8D571127AE49E00000000020004003300420001001E003000310035003600360053002D00570049004E00310036002D004900520004001E0074006800720065006500620065006500730063006F002E0063006F006D0003003E003000310035003600360073002D00770069006E00310036002D00690072002E0074006800720065006500620065006500730063006F002E0063006F006D0005001E0074006800720065006500620065006500730063006F002E0063006F006D00070008000094D71AE38CD601060004000200000008003000300000000000000000000000003000009050B30CECBEBD73F501D6A2B88286851A6E84DDFAE1211D512A6A5A72594D340A001000000000000000000000000000000000000900220063006900660073002F003100370032002E00310036002E00360036002E0033003600000000000000000000000000`
   - **How to find it:** In the `Credentials` tab, for the corresponding row of the `#B\Administrator` user, 2nd in line in the Password/Hash column, right-click -> `Copy Password` (or copy the entire NTLMv2 hash string).

<img width="1389" height="830" alt="ntlm pass" src="https://github.com/user-attachments/assets/ff279e9f-ce9f-4e1b-be5d-2b9794ad6e67" />

---

### 🔥 EXTRA FROM ME: SOC Analyst & Forensics Insights
- **Why do multiple IP addresses share the same MAC address in `mx-3.pcap`?**
  When packets pass through a router/default gateway, for all hosts outside the local network the next-hop MAC address is precisely the router's MAC address. That is why in NetworkMiner you see 2 external IP addresses mapped to the same Layer 2 MAC address.
- **Web server banner analysis:**
  The `Apache` banner immediately tells the analyst/pentester that it is a very old version of Apache and OpenSSL (outdated components with well-known vulnerabilities).
- **How is the extracted NTLMv2 hash used in a real investigation?**
  In the `Credentials` tab, NetworkMiner extracts the entire NTLMv2 specific hash format (which includes username, domain, challenge, and response). This format is directly ready to be entered into Hashcat (mode 5600 for NetNTLMv2) or John the Ripper for offline password cracking.

---

## Task 5: Tool Overview 2

### 📊 1. Information Tabs in NetworkMiner (Continuation)

#### 🔹 Files
- Displays extracted files from investigated pcap files.
  - **Displayed information:** Frame number, Filename, Extension, Size, Source and destination address, Source and destination port, Protocol, Timestamp, Reconstructed path, Details.
- Some features (such as OSINT hash lookup and sample submission) are available only in premium mode.
- A search bar is available, and right-click allows easy opening of files and folders and detailed viewing of file properties.

#### 🔹 Images
- Displays extracted images from investigated pcap files.
- Right-click helps easily open images and zoom in & out.
- When you hover the cursor over the image, detailed file information is displayed: source & destination address and file path.

#### 🔹 Parameters
- Displays extracted parameters from investigated pcap files.
  - **Displayed information:** Parameter name, Parameter value, Frame number, Source and destination host, Source and destination port, Timestamp, Details.
- Right-click allows easy copying of parameters and their values.

#### 🔹 Keywords
- Displays extracted keywords from investigated pcap files.
  - **Displayed information:** Frame number, Timestamp, Keyword, Context, Source and destination host, Source and destination port.
  1. **How keywords are filtered:** Add keywords (`Add keywords`).
  2. **Reload case files!**
- **Important note:** You can filter multiple keywords in this section, but you must reload the case files after updating the search keywords. Keyword search investigates all possible data in the processed pcap files.

#### 🔹 Messages
- Displays extracted emails, chat messages, and other messages from investigated pcap files.
  - **Displayed information:** Frame number, Source and destination host, Protocol, Sender (From), Receiver (To), Timestamp, Size.
- After filtering traffic and getting a hit, for the selected message you will discover additional details like attachments and attributes.
- A search bar and right-click menu are available. With the built-in viewer you can review general information, and with the "open file" option you can investigate attachments.

#### 🔹 Anomalies
- Displays detected anomalies in the processed pcap.
- **Important note:** NetworkMiner is not intended as an Intrusion Detection System (IDS), but the developers added a few detections, for example for EternalBlue exploit and spoofing attempts.

### 🔍 2. How Answers to Questions in Task 5 Are Accessed
Here is an exact workflow on how to extract all answers directly in NetworkMiner:

#### 📁 For file `~/Desktop/Exercise Files/mx-7.pcap` (or `mx-7`):
1. **Linux distro associated with frame 63602 (or 63075 if no results for 63602):**
   - Open the `Files` tab (or check reconstructed files / Keywords), search for the corresponding frame (63602 or alternatively 63075), open the reconstructed file (or check its context/content), and find the name of the Linux distribution.
   - **Answer:** `CentOS`
  
<img width="1340" height="841" alt="linux distribution centOS" src="https://github.com/user-attachments/assets/d3550b67-d6b7-4e45-a3e9-4d84d529beae" />

---

2. **What name and surname are mentioned in the file associated with frame 76469?**
   *(Note: If there are no results associated with this frame, check out frame 75942)*
   - In the `Files` tab (or via right-click to open/view the file associated with frame 76469), review the content of the file to find the name and surname.
   - **Answer:** `Ned Flanders`
  
-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269"https://github.com/user-attachments/assets/50bd7e20-e3be-4363-8269-d9a66e82fc2c" />

---

3. **What is the source address of the image "ads.bmp.2E5F0FD9[1].bmp"?**
   - Go to the `Images` tab (or `Files`), find the image `ads.bmp.2E5F0FD9[1].bmp`, and look at the Source column / details on hover.
   - **Answer:** `80.239.178.187`

<img width="1350" height="852" alt="img source IP" src="https://github.com/user-attachments/assets/bd8d25dc-e67f-4b35-98c4-366de2db4e9e" />

---

4. **What is the frame number of the possible TLS anomaly?**
   - Go to the `Anomalies` tab and check the entry associated with TLS anomaly to read the corresponding Frame number.
   - In this case it accepts either one frame number or the other — whichever of those two we can insert in the answer!
   - **Answer:** `36255`

<img width="1345" height="851" alt="anomaly frame numb" src="https://github.com/user-attachments/assets/d8318414-ad39-4e01-81ea-627e38772c56" />

---

#### 📁 For file `~/Desktop/Exercise Files/mx-9.pcap` (or `mx-9`):
1. **Use "~/Desktop/Exercise files/mx-9" file to answer the questions below: Which platform sent an email with the subject starting with "You have more..."?**
   - Go to the `Messages` tab, search/filter for `"You have more"`, open the message in the built-in viewer, and check the sender / platform (From / message details). Or in the filtering section choose the `Subject` option.
   - **Answer:** `Facebook`

<img width="1354" height="832" alt="facebook platform" src="https://github.com/user-attachments/assets/8e673477-555e-410c-8774-faa9a07c5ece" />

---

2. **What is the email address of Branson Matheson?**
   - In the `Messages` tab (or `Keywords` by searching for `"Branson"` / `"Matheson"` and then `Reload case files!`), check the messages and From / To fields or message content to find his address.
   - **Answer:** `branson@sandsite.org`

<img width="1349" height="833" alt="mail Branson" src="https://github.com/user-attachments/assets/f4330564-5b87-4e58-8dcf-2f26f0d958ff" />

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective
- **Why is `Reload case files!` mandatory with Keywords?**
  Because during processing NetworkMiner indexes and extracts data according to the current list of keywords; adding a new word without reloading the case does not re-examine already existing data with the new criterion.
- **What is the big advantage of Files, Images, and Messages in forensics?**
  Instead of manually reassembling HTTP/SMTP/TCP streams piece by piece, the tool immediately pulls out transferred objects (images, documents, messages, and attachments) with metadata for source/destination, time, and frame number. That way you easily see whether a user downloaded a suspicious file, received a phishing message with an attachment, or transferred data outside the network.
- **Why is Anomalies only a starting hint, and not a replacement for IDS/NSM?**
  As the lesson itself says, NetworkMiner is not a dedicated IDS; its detections (like EternalBlue or spoofing) are a useful radar for a quick signal, but for serious detection and confirmation you must always rely on multiple layers of visibility and detailed analysis.

---

## Task 6: Version Differences

### 🎯 1. Task Overview
- Newer software versions bring improvements in stability, security patches, and new or optimized functionalities.
- Because there are significant differences between older and newer versions of NetworkMiner, on the virtual machine (VM) the two main versions are installed: `v1.6` and `v2.7`.

### 🔑 2. Main Differences Between Version 1.6 and Version 2.x

#### 🔹 MAC Address Processing
- Versions after Version 2 can perform correlation specific to MAC addresses.
- This option helps identify whether there is a MAC Address Conflict / duplicate MAC addresses.
- This feature is **NOT** available before version 2.

#### 🔹 Packet & Frame Handling
- **Packet details:** Versions up to `v1.6` can handle packets in much deeper detail for deeper analysis of sent/received packets. This capability is not available after version 1.6.
- **Frame handling:** Versions up to `v1.6` can handle frames and display the total number of frames and basic details about them. This capability is not available after version 1.6.

#### 🔹 Parameter & Cleartext Processing
- **Parameters:** Versions after Version 2 process parameters in a much more extensive form (capture more parameters than v1.6).
- **Cleartext data:** Versions up to `v1.6` display all extracted cleartext data in one separate tab (useful for a quick overview, although direct mapping between cleartext data and the packets themselves is not possible). This capability is not available after version 1.6.

### 📝 3. Exact Answers for Questions in Task 6
- **Which version can detect duplicate MAC addresses?**
  - **Answer:** `2.7`
- **Which version can handle frames?**
  - **Answer:** `1.6`
- **Which version can provide more details on packet details?**
  - **Answer:** `1.6`

---

### 🔥 EXTRA FROM ME: DFIR & SOC Operational Summary
- **Why is it critical to know which version you are using in an investigation?**
  If a question or real investigation asks for a specific number of frames or specific packet details, version 2.7 will not display those tabs/data the same way as 1.6. On the other hand, for detecting duplicate MAC addresses (potential ARP spoofing / conflict) or extracting richer parameters from modern applications, version 2.x is much better.
- **When do you use 1.6, and when 2.7?**
  - **v1.6:** When you look for a detailed view at the packet/frame level and want all cleartext strings collected in one tab.
  - **v2.x (v2.7):** For modern analysis where you need improved protection, better parameter extraction, and detection of duplicate MAC addresses.

---

## Task 7: Exercises

### 📌 1. IMPORTANT Note
- **Loading PCAP files:** Pressing/uploading larger files like `case1.pcap` and `case2.pcap` can take up to 5 minutes, during which NetworkMiner may appear frozen/unresponsive.
- **Best practice for performance:** Close NetworkMiner and reopen it before loading `case1.pcap`. Do the same (restart the application) before loading `case2.pcap`.

### 📁 2. Analysis and Answers for `case1.pcap`

1️⃣ **What is the full OS name of the host 131.151.37.122?**
- **How to find it in NetworkMiner:**
  1. Open the `Hosts` tab.
  2. Find the host with IP address `131.151.37.122`.
  3. Expand the host details and look at the Host Details / OS / OS Fingerprint (Satori/p0f) field.
- **Answer:** `Windows - Windows NT 4`

<img width="1370" height="678" alt="Windos OS NT 4" src="https://github.com/user-attachments/assets/5243f416-e59f-4e53-b9fa-e49b4180fac5" />

---

2️⃣ **Investigate the host communication between 131.151.37.122 and 131.151.32.91. How many bytes were sent by the client (*.32.91) through port 1065?**
- **How to find it in NetworkMiner:**
  1. Open the `Sessions` tab (or find the session under host `131.151.32.91` in the `Hosts` tab -> Outgoing Sessions).
  2. Filter or find the session between `131.151.32.91` (Source port: `1065`) and `131.151.37.122`.
  3. Check the session details/bytes sent by the client (Sent Bytes / Client Bytes).
- **Answer:** `192`

<img width="1387" height="821" alt="192 byts" src="https://github.com/user-attachments/assets/8512ff39-87d7-4565-8687-c6cb25e4d9cd" />

---

3️⃣ **Investigate the communication between 131.151.37.122 and 131.151.32.21. How many bytes were sent back by the server (*.37.122) through port 143?**
- **How to find it in NetworkMiner:**
  1. In the `Sessions` tab, find the IMAP session (port `143`) where the server is `131.151.37.122` and the client is `131.151.32.21`.
  2. Review the session details for bytes sent back by the server (Server Bytes / Sent back bytes).
- **Answer:** `20769`

<img width="1401" height="849" alt="20769 bytes" src="https://github.com/user-attachments/assets/709d58a2-4045-4d8f-a55a-8583a444eaaa" />

---

4️⃣ **What is the sequence number of frame number 9?**
- **How to find it in NetworkMiner / Wireshark:**
  1. To access raw TCP field details like Sequence Number, use version `v1.6` (as we learned in Task 6, v1.6 enables deeper packet/frame handling) or open the frame in Wireshark.
  2. Find Frame 9 and read its TCP Sequence Number.
- **Answer:** `2AD77400`

<img width="1020" height="659" alt="seq number " src="https://github.com/user-attachments/assets/c57dfd7f-84ed-4953-9c62-56eba5a20c2b" />

---

5️⃣ What is the number of the detected "content types"?
1. How to find it in NetworkMiner:
   1. Open the Files tab (or Parameters).
   2. Look at the details of the extracted HTTP files/headers where NetworkMiner summarizes or groups the unique Content-Type types.

- In the image, in the Parameter value column for Content-Type, the following are visible:
  - `text/plain`
  - `multipart/mixed`

Since the list in Parameters is long and the scrollbar is at the top, to count all unique Content-Type values across the entire case1.pcap:
1. Click on the header of the `Parameter value` column to sort the values alphabetically (this is the easiest way to see all different Content-Types side-by-side without counting duplicates like `text/plain` that repeat multiple times).
2. Look at all the distinct values of Content-Type and count how many unique types there are — the answer to the question is `2`.

3. Small SOC tip for quickly counting unique values:
   When you have hundreds of rows with identical values in Parameters or logs (like `text/plain` on frames 22, 23, 24, 26...), always sort the column by value so you immediately see how many distinct types exist instead of getting lost in repetitions.

- Answer: `2`

<img width="1385" height="818" alt="2 par value content " src="https://github.com/user-attachments/assets/7624b10e-480a-45a5-bdba-2fe6d0b968f6" />

---

