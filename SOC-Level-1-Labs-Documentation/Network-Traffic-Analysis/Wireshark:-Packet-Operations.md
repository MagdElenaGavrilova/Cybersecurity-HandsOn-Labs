# 🦈 Wireshark: Packet Operations

Learn the fundamentals of packet analysis with Wireshark and how to find the needle in the haystack!

---

## Task 1: Introduction

### 1. 📋 Room Overview

This room is the second part of TryHackMe's Wireshark trilogy. While the previous room ("Wireshark: The Basics") taught the basics of the interface, navigation, and basic filters, the focus here is on advanced operational techniques for finding the "needle in the haystack" through deep packet analysis.

### 🎯 2. Learning Objectives

* Inspection and investigation of network traffic captures.
* Review of network statistics, including summaries and protocol details.
* Discovery and application of packet filtering principles.
* Application of specific protocol filters.
* Use of advanced filtering.

### ⚙️ 3. Environment & Prerequisites

* **Prerequisites:** Knowledge of Networking Fundamentals and completion of the previous room (Wireshark: The Basics).
* **Working File:** You need to start the lab machine and open the `Exercise.pcapng` file located on the desktop to follow the exercises and instructions.
* **⚠️ Security Warning:** Direct interaction with any domains or IP addresses listed in this room is strictly forbidden—they are exclusively reference data for analytical purposes.

---

### 🔥 EXTRA FROM ME: Transitioning from Basic to Advanced Operations in the SOC

As a SOC Analyst, the difference between basic and advanced packet analysis lies in the ability to recognize hidden threats:

* **From static to dynamic:** Basic filters help you find the protocol (e.g., HTTP or DNS), but the advanced operations in this room will teach you how to dissect fields in detail to uncover advanced attacker techniques (such as data tunneling via DNS or hidden exfiltration channels).
* **Why statistics matter:** Before opening thousands of packets, using Wireshark's built-in statistical tools (like Protocol Hierarchy or Conversations) gives you a "bird's-eye view" of the entire network, allowing you to immediately spot abnormal traffic volume toward an unknown external destination.

---

## Task 2: Statistics | Summary

### 1. 📋 The Role of Statistics in Analysis

The Statistics menu in Wireshark provides a "big picture" of the analyzed PCAP file. Instead of jumping straight to individual packets, this menu helps the analyst build an initial hypothesis about the incident by reviewing:

* Traffic volume and duration,
* Available protocols and services,
* Unique endpoints and their mutual conversations,
* Specific details for protocols like DNS, HTTP/2, or DHCP.

* **Dynamic Host Configuration Protocol (DHCP)** is a network management protocol used on Internet Protocol (IP) networks for automatically assigning IP addresses and other communication parameters to devices connected to the network using a client–server architecture.
* **Domain Name System (DNS)** is the protocol responsible for resolving hostnames, such as tryhackme.com, to their respective IP addresses.
* **Hypertext Transfer Protocol (HTTP)** is the protocol that specifies how a web browser and a web server communicate. Your web browser requests content from the TryHackMe web server using the HTTP protocol as you go through this room.

---

### 🔍 2. Key Functions from the Statistics Menu

#### 🌐 Resolved Addresses

* **Function:** Displays a list of all IP addresses and their corresponding hostnames that Wireshark successfully identified.
* **How it works:** Hostname information is extracted directly from DNS responses stored within the PCAP file itself.
* **Application:** Allows the analyst to quickly identify which resources/domains were accessed during the capture.
* **Menu:** `Statistics` --> `Resolved Addresses`

#### 🌳 Protocol Hierarchy

* **Function:** Displays all protocols present in the traffic in a tree view, classified by packet count and percentage share.
* **Application:** Helps spot anomalies (for example, if normal corporate network traffic shows an unusually high percentage of UDP or DNS traffic, which may indicate DNS tunneling or DDoS).
* **Menu:** `Statistics` --> `Protocol Hierarchy`

#### 💬 Conversations

* **Function:** Displays bidirectional traffic between two specific endpoints (conversers).
* **Formats:** Allows viewing across 5 main tabs: Ethernet, IPv4, IPv6, TCP, and UDP.
* **Application:** Ideal for isolating communication between a potential attacker and a compromised host.
* **Menu:** `Statistics` --> `Conversations`

#### 🖥️ Endpoints

* **Function:** Displays statistics for individual devices/addresses on the network (unlike Conversations which tracks pairs).
* **Characteristics and Name Resolution:**
* **MAC Resolution:** Based on the first 3 bytes of the MAC address (OUI), Wireshark determines the network card manufacturer (e.g., Micro-St for Micro-Star International). Activated via the Name resolution button in the bottom-left corner.
* **IP & Port Resolution:** Activated via `Edit` --> `Preferences` --> `Name Resolution`.


* **GeoIP Geolocation:** Enables mapping source and destination IP addresses on a geographical map via databases like MaxMind DB (`Edit` --> `Preferences` --> `Name Resolution` --> `MaxMind database directories`). *(Note: Requires an active internet connection and pre-configured database files).*

---

### 🎯 3. Exercise Answers

*(To get the answers, open the `Exercise.pcapng` file on the desktop in Wireshark):*

* **Investigate the resolved addresses. What is the IP address of the hostname starts with "bbc"?**
* **How to find it:** Go to `Statistics` --> `Resolved Addresses`, search for hostnames starting with bbc (e.g., `bbc.map...`), and read the corresponding IP address.
* **Answer:** `199.232.24.81`

<img width="800" height="612" alt="bbc ip " src="https://github.com/user-attachments/assets/ff6c278e-2812-40a1-890f-ca161e436b70" />

---

* **What is the number of IPv4 conversations?**
* **How to find it:** Go to `Statistics` --> `Conversations`, click the IPv4 tab, and look at the total record count displayed on the tab.
* **Answer:** `435`

<img width="800" height="606" alt="ipv4 number" src="https://github.com/user-attachments/assets/33717496-282f-46b8-8c5d-af366d5b7f5b" />

---

* **How many bytes (k) were transferred from the "Micro-St" MAC address?**
* **How to find it:** Go to `Statistics` --> `Endpoints`, open the Ethernet tab, enable Name resolution in the bottom-left corner, find the address with manufacturer Micro-St, and read the value in the transferred bytes column (in kilobytes k).
* **Answer:** `7474`

<img width="1112" height="860" alt="7474 endpoints" src="https://github.com/user-attachments/assets/d8aa419d-206f-415a-afec-ce68e5ef79d4" />

---

* **What is the number of IP addresses linked with "Kansas City"?**
* **How to find it:** In the `Statistics` --> `Endpoints` menu under the IPv4 tab (if GeoIP data is provided), filter or search by location Kansas City and count how many IP addresses are linked to that city.
* **Answer:** `4`

<img width="800" height="613" alt="kansas city" src="https://github.com/user-attachments/assets/d09cbe12-c1a1-47a3-9bbe-659511096c0e" />

--- 

* **Which IP address is linked with "Blicnet" AS Organisation?**
* **How to find it:** In the `Statistics` --> `Endpoints` menu under the IPv4 tab, check the Autonomous System (AS Organisation) details and find the IP address linked to Blicnet.
* **Answer:** `188.246.82.7`

<img width="800" height="616" alt="blicnet 1 img" src="https://github.com/user-attachments/assets/204e850c-b458-4bbc-841e-059993cc7aac" />

<img width="800" height="642" alt="fin blicnet answr" src="https://github.com/user-attachments/assets/9af0a2f6-befb-491c-953f-1f4ae1e44939" />

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on Statistics & Triage

As a SOC Analyst, the Statistics menu is the first step in rapid incident triage:

* **Quickly spotting Data Exfiltration:** When you open `Statistics` --> `Conversations` under the TCP or IPv4 tabs, sort the Bytes column from largest to smallest. If you notice an internal IP address sending gigabytes of data to an unknown external IP address over a short period, it is a strong indicator of suspected data exfiltration.
* **Identifying devices via MAC Vendor (OUI):** The MAC manufacturer recognition option in `Endpoints` --> `Ethernet` helps discover rogue devices on the network, such as someone plugging in an unauthorized device (IoT device, small single-board computer like a Raspberry Pi, or unregistered router) into the internal corporate network.

---

## Task 3: Statistics | Protocol Details

### 1. 📋 Narrower Protocol Statistics

While general statistics give a global overview of the entire PCAP file, sub-menus for specific protocols allow in-depth inspection at the individual protocol level (IPv4, IPv6, DNS, HTTP). These statistics speed up the analysis process when traffic contains specific network protocols of interest to the investigation.

---

### 🔍 2. Specific Options from the Statistics Menu

#### 1. 🌐 IPv4 and IPv6 Statistics

* **Function:** Prevents mixing IP protocol versions and allows an isolated view of all events related exclusively to IPv4 or IPv6.
* **Application:** Allows the analyst to quickly identify the most active source and destination addresses, as well as profile network traffic by IP version.
* **Menu:** `Statistics` --> `IPv4 Statistics` (or `IPv6 Statistics`).

#### 🔎 2. DNS Statistics

*Domain Name System (DNS) is the protocol responsible for resolving hostnames, such as tryhackme.com, to their respective IP addresses.*

* **Function:** Breaks down all DNS packets from the PCAP file and displays them in a tree view based on traffic counters and percentages.
* **Sub-details:**
* **Rcode (Response Codes):** Query results (e.g., NOERROR, NXDOMAIN for non-existent domains).
* **Opcode:** Operation type (e.g., standard QUERY).
* **Query Type & Class:** Request types (A records for IPv4, AAAA for IPv6, TXT, MX, etc.).
* **Service Request-Response Time:** Time required to receive a response to a sent DNS query.




* **Menu:** `Statistics` --> `DNS`.

#### 🌐 3. HTTP Statistics

*Hypertext Transfer Protocol (HTTP) is the protocol that specifies how a web browser and a web server communicate. Your web browser requests content from the TryHackMe web server using the HTTP protocol as you go through this room.*

* **Function:** Analyzes everything related to web traffic (HTTP), classifying requests and responses.
* **Sub-details:**
* **Request Codes / Methods:** Distribution of HTTP methods (GET, POST, PUT, DELETE).
* **Response Codes:** Status codes from web servers (200 OK, 301/302 Redirect, 404 Not Found, 500 Server Error).
* **HTTP Requests:** Exact hostnames or URLs to which requests were sent by the client.




* **Menu:** `Statistics` --> `HTTP`.

---

### 🎯 3. Exercise Answers

*(Open the `Exercise.pcapng` file in Wireshark and apply these filters to find the answers by checking the status bar at the bottom):*

1. **What is the most used IPv4 destination address?**
* **How to find it:** Go to `Statistics` --> `IPv4 Statistics` --> `Destinations and Ports`, sort the column by packet count/sessions, and find the most frequently accessed IPv4 destination address.
* **Answer:** `10.100.1.33`

<img width="800" height="536" alt="q 1 ipv4 " src="https://github.com/user-attachments/assets/ce56217a-d104-407f-841f-10d294dcfe67" />

---

2. **What is the max service request-response time of the DNS packets?**
* **How to find it:** Go to `Statistics` --> `DNS`, find the Request-Response Time section, and look at the maximum measured value (Max).
* **Answer:** `0.467897` (seconds)

<img width="800" height="502" alt="req res DNS " src="https://github.com/user-attachments/assets/3a124bce-5750-4c53-bedf-b922b1fdb9fd" />

---

3. **What is the number of HTTP Requests accomplished by "rad[.]msn[.]com"?**
* **How to find it:** Go to `Statistics` --> `HTTP` --> `HTTP Requests`, find the hostname `rad.msn.com` in the list, and read the corresponding number in the requests column (Count).
* **Answer:** `39`

<img width="800" height="501" alt="DNS req " src="https://github.com/user-attachments/assets/433ba4f5-2c95-4041-bf92-94b634d3007f" />

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on DNS and HTTP Statistics

As a SOC Analyst, analyzing these specific protocol statistics is a key skill for uncovering advanced security threats:

* **Analyzing DNS Rcode (NXDOMAIN / DNS Tunneling):** If you notice an unusually high percentage of NXDOMAIN responses or a massive number of requests with TXT records in `Statistics` --> `DNS`, it indicates:
* **DNS Beaconing / C2:** Malware is trying to contact command and control servers via Domain Generation Algorithms (DGA).
* **DNS Exfiltration:** Text-encoded data is being sent out of the network via subdomains in DNS queries.


* **Analyzing HTTP Requests (C2 & Malware Downloads):** In `Statistics` --> `HTTP` --> `HTTP Requests`, a quick overview of all domains allows you to immediately spot accesses to unusual/suspicious domains that are not part of normal business traffic (like `rad.msn.com` in ads, or unknown IP addresses without a valid DNS hostname).

---

## Task 4: Packet Filtering | Principles

### 1. 📋 Comparison: Capture Filters vs. Display Filters

Two fundamentally different types of filters exist in Wireshark. Although they share a similar purpose (noise reduction), their purpose and syntax differ:

| Characteristic | Capture Filters | Display Filters |
| --- | --- | --- |
| **When applied?** | Before traffic capture starts (live capture). | During or after capture (on already recorded PCAP files). |
| **Are they mutable?** | No. They cannot be changed while capture is running. | Yes. They can be dynamically changed at any time. |
| **Main purpose** | Saving only a specific portion of traffic (saving space/disk). | Investigating and analyzing packets by hiding irrelevant ones. |
| **Protocol support** | Lesser support (uses BPF syntax). | Massive support (over 3000 protocols). |

* **⚠️ Important Rule:** Display Filter syntax cannot be used for Capture Filters and vice versa!
* **Cybersecurity Best Practice:** Capture everything, then analyze and filter using Display Filters. Only experienced professionals use live Capture Filters to avoid losing critical evidence if a filter is set incorrectly.

---

### ⚙️ 2. Capture Filters Syntax (BPF Syntax)

These filters use lower-level syntax based on Scope, Direction, and Protocol:

* **Scope:** `host`, `net`, `port`, `portrange`.
* **Direction:** `src` (source), `dst` (destination), `src or dst`, `src and dst`.
* **Protocol:** `ether`, `wlan`, `ip`, `ip6`, `arp`, `tcp`, `udp`.
* **Example for filtering port 80:** `tcp port 80`
* **Quick reference in Wireshark:** Menu `Capture` --> `Capture Filters`.

---

### 🔍 3. Display Filters Syntax (Operators and Logical Expressions)

This is Wireshark's most powerful feature. Syntax uses dots for protocol decomposition (e.g., `tcp.port == 80`).

#### 📊 Comparison Operators:

Wireshark supports both English abbreviations and C-like operators, as well as decimal and hexadecimal values:

| English | C-like | Description | Example |
| --- | --- | --- | --- |
| `eq` | `==` | Equal | `ip.src == 10.10.10.10` |
| `ne` | `!=` | Not equal | `ip.src != 10.10.10.10` |
| `gt` | `>` | Greater than | `ip.ttl > 250` |
| `lt` | `<` | Less than | `ip.ttl < 10` |
| `ge` | `>=` | Greater than or equal | `ip.ttl >= 0xFA` |
| `le` | `<=` | Less than or equal | `ip.ttl <= 0xA` |

#### 🔀 Logical Expressions:

| English | C-like | Description | Example |
| --- | --- | --- | --- |
| `and` | `&&` | Logical AND | `(ip.src == 10.10.10.10) and (tcp.port == 80)` |
| `or` | ` |  | ` |
| `not` | `!` | Logical NOT | `!(ip.src == 10.10.10.222)` |

* **💡 Consistency Tip:** Avoid using `!= value` (it can yield unexpected results in Wireshark when a packet contains multiple values). Instead, always use the secure format `!(field == value)`.

---

### 🎨 4. Packet Filter Toolbar

The Display Filters input bar features color-coded instant validation:

* 🟢 **Green:** Valid filter.
* 🔴 **Red:** Invalid filter / syntax error.
* 🟡 **Yellow:** Warning. The filter works, but might yield unreliable results (e.g., when using `!=`).

#### Key Characteristics:

1. All filters must be written strictly in lowercase.
2. Built-in Autocomplete while typing.
3. Reference available via the `Analyse` --> `Display Filters` menu.

---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective for Capture vs. Display Filters

As a SOC Analyst, understanding when to apply a Capture Filter versus a Display Filter is critical in high-traffic environments:

* **High-Speed Network Memory Protection:** If you are capturing traffic on a 10 Gbps+ network switch, capturing everything unfiltered will fill all disks and overwhelm RAM within minutes. In such cases, SOC analysts use Capture Filters (e.g., `not port 22` to exclude administrative SSH traffic, or `host 192.168.1.50` to track only a compromised server).
* **Precision with Grouped Filters in Display Filters:** When hunting for unusual behavior, combining logical operators saves time. Example:
`ip.src == 10.10.10.50 and !(tcp.port == 80 or tcp.port == 443)`
This filter shows all connections from IP address `10.10.10.50` that are not standard HTTP/HTTPS web traffic, instantly highlighting unusual ports used for C2 or tunneling!

---

## Task 5: Protocol Filters

### 1. 📋 What are Protocol Filters?

Wireshark supports over 3000 protocols and allows packet investigation down to individual protocol fields. This task covers specific protocol filters classified by OSI model layers:

1. **IP Filters** (Network Layer / Layer 3): Filtering by IP addresses, versions, TTL (Time to Live), checksums, and more.
2. **TCP & UDP Filters** (Transport Layer / Layer 4): Filtering by ports, sequence numbers, ACK numbers, flags, and length.
3. **Application Level Filters** (Application Layer / Layer 5): Filtering by specific application data for protocols like HTTP and DNS.

---

### 🔍 2. Overview of Basic Protocol Filters

#### 🌐 IP Filters

* `ip` — Displays all IPv4 packets.
* `ip.addr == 10.10.10.111` — Displays all packets containing this IP address (regardless of source or destination).
* `ip.addr == 10.10.10.0/24` — Filtering an entire subnet range.
* `ip.src == 10.10.10.111` — Packets originating exclusively from this source IP address.
* `ip.dst == 10.10.10.111` — Packets sent exclusively to this destination IP address.

#### 🔌 TCP & UDP Filters

* `tcp.port == 80` — All TCP packets using port 80 (source or destination).
* `udp.port == 53` — All UDP packets on port 53 (DNS).
* `tcp.srcport == 1234` — TCP packets originating from source port 1234.
* `tcp.dstport == 80` — TCP packets sent to destination port 80.

#### 🌍 HTTP & DNS Filters

* `http` / `dns` — Display all HTTP or DNS packets.
* `http.response.code == 200` — Packets with a successful HTTP status code 200.
* `http.request.method == "GET"` / `"POST"` — Filtering by web request methods.
* `dns.flags.response == 0` — DNS queries only.
* `dns.flags.response == 1` — DNS responses only.
* `dns.qry.type == 1` — DNS "A" records (IPv4 address queries).

---

### 🛠️ 3. Display Filter Expressions (Helper Tool)

It is impossible to memorize all fields for over 3000 protocols. For this purpose, Wireshark features a built-in assistant:

* **How to open:** `Analyse` --> `Display Filter Expression`.
* **Function:** Provides an interactive view of all supported protocol fields, data types (strings, integers), and possible values, facilitating manual filter construction.

---

### 🎯 4. Exercise Answers

*(Open the `Exercise.pcapng` file in Wireshark and apply these filters to find the answers by checking the status bar at the bottom):*

1. **What is the number of IP packets?**
* **Filter:** `ip`
* **Answer:** `81420`

<img width="800" height="500" alt="ip number" src="https://github.com/user-attachments/assets/49294f15-28c2-4747-b84b-2f430f344d2c" />

---

2. **What is the number of packets with a "TTL value less than 10"?**
* **Filter:** `ip.ttl < 10`
* **Answer:** `66`

<img width="800" height="501" alt="ttl packet " src="https://github.com/user-attachments/assets/6c8218d7-670d-45f1-ab23-3b5fb686f1f9" />

---

3. **What is the number of packets which uses "TCP port 4444"?**
* **Filter:** `tcp.port == 4444`
* **Answer:** `632`

<img width="800" height="501" alt="tcp port 4444 " src="https://github.com/user-attachments/assets/87624f9b-4c0a-487c-9646-9385ed1c919b" />

===

4. **What is the number of "HTTP GET" requests sent to port "80"?**
* **Filter:** `http.request.method == "GET" && tcp.port == 80`
* **Answer:** `3`

<img width="800" height="494" alt="get req port 80" src="https://github.com/user-attachments/assets/96140868-6be5-4e29-bbed-c49480a2ea2a" />

---

5. **What is the number of type A DNS Queries?**
* **Filter:** `dns.qry.type == 1 && dns.flags.response == 1`
* **Answer:** `51`

<img width="800" height="498" alt="dns A response " src="https://github.com/user-attachments/assets/0024a023-54c7-4735-80e9-f253859fd73b" />

---

#### Essential difference between these two filters:

1. **With `dns.qry.type == 1` alone:** This filter shows all DNS packets related to A records (IPv4 queries), whether it is a query sent by the client or a response returned by the DNS server. Thus, results include packets where the client asks "What is the IP address for google.com?" and packets where the server answers "The IP address is 142.250.190.46".
2. **By adding `&& dns.flags.response == 1`:** With this additional filter, you tell Wireshark: "Show me only those DNS packets where the response flag is set to 1" (meaning exclusively responses from the DNS server). This hides initial queries and shows only received results/responses.

* **💡 What is the difference in packet count?** Depends on network traffic content in the PCAP file:
* If you wanted total queries sent by users, `dns.flags.response == 0` is used.
* If you filtered with `&& dns.flags.response == 1`, you viewed only received responses.
* In many cases, query and response counts are similar, but if a query timed out, counts may differ.



---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on Advanced Protocol Filtering

As a SOC Analyst, the skill of precisely combining protocol filters is the difference between successfully resolving an incident in minutes or wasting hours:

* **Detecting illegitimate traffic on standard ports:** Attackers frequently mask their traffic (e.g., Meterpreter reverse shell communication over port 4444 or tunneling). When you apply a filter like `tcp.port == 4444`, you instantly isolate potential remote management software regardless of what protocol the application claims to use.
* **Analyzing DNS A records during Malware C2 communication:** When performing forensics on a workstation infected with a trojan, filtering by `dns.qry.type == 1` allows you to see all domains the victim attempted to resolve into IP addresses. Often, suspicious domains with strange names (DGA) appear directly in this list, leading to rapid identification of malicious infrastructure.

---

## Task 6: Advanced Filtering

### 1. 📋 Advanced Operators and Functions

While basic filters help find a specific protocol or IP address, Wireshark's advanced filters enable searching for specific texts, regex patterns, value sets, and content manipulation (like uppercase/lowercase conversion).
Here are the six key advanced filters covered in this task:

#### 1. 🔍 contains (Content Search Operator)

* **Type:** Comparison Operator
* **Purpose:** Searches for a specific value/text inside packets. Case-sensitive and offers functionality similar to "Find", but focused on a specific field.
* **Example:** Find all HTTP packets whose server field contains the text "Apache".
* **Syntax:** `http.server contains "Apache"`

#### 🔤 2. matches (Regex Pattern Search)

* **Type:** Comparison Operator
* **Purpose:** Searches for a pattern using regular expressions. Case-insensitive.
* **Example:** Find all .php or .html pages in the host field.
* **Syntax:** `http.host matches "\.(php|html)"`

#### 📦 3. in (Set Membership Operator)

* **Type:** Set Membership Operator
* **Purpose:** Searches for a value or field belonging to a specific range or set of values.
* **Example:** Find all packets using ports 80, 443, or 8080.
* **Syntax:** `tcp.port in {80 443 8080}`

#### 🔠 4. upper (Uppercase Function)

* **Type:** Function
* **Purpose:** Converts a text field to uppercase. Very useful when searching case-insensitively.
* **Example:** Find servers containing "APACHE" regardless of casing.
* **Syntax:** `upper(http.server) contains "APACHE"`

#### 🔡 5. lower (Lowercase Function)

* **Type:** Function
* **Purpose:** Converts a text field to lowercase.
* **Example:** Find servers containing "apache".
* **Syntax:** `lower(http.server) contains "apache"`

#### 🔣 6. string (Text Conversion Function)

* **Type:** Function
* **Purpose:** Converts a non-text value (e.g., frame/packet number) into a string, enabling regex searches over numbers.
* **Example:** Find all odd-numbered frames (ending in 1, 3, 5, 7, 9).
* **Syntax:** `string(frame.number) matches "[13579]$"`

---

### 🔖 2. Bookmarks, Filtering Buttons, and Profiles

To avoid typing complex filters manually over and over, Wireshark offers tools for saving and organization:

1. **Bookmarks:** The filter bar has a section for saving favorite or complex filters for quick access with a few clicks.
2. **Filtering Buttons:** Allow creating buttons placed directly under the filter bar, clicking which instantly applies a predefined filter.
3. **Profiles:** Ideal for organization. When working on different investigations (requiring different coloring rules and filter buttons), you can create custom profiles via `Edit` --> `Configuration Profiles` or from the bottom-right corner of the status bar.

---

### 🎯 3. Exercise Answers

*(Open the `Exercise.pcapng` file in Wireshark and apply these advanced filters):*

1. **Find all Microsoft IIS servers. What is the number of packets that did not originate from "port 80"?**
* **Filter hint:** First find IIS servers (`http.server contains "Microsoft-IIS"`), then exclude source port 80 via negation: `http.server contains "Microsoft-IIS" and !(tcp.srcport == 80)`
* **Answer:** `21`

<img width="800" height="544" alt="Microsoft IIS not 80" src="https://github.com/user-attachments/assets/9ee8fc9e-13af-47c5-93a6-269d70bb1645" />

--- 

2. **Find all Microsoft IIS servers. What is the number of packets that have "version 7.5"?**
* **Filter:** `http.server contains "Microsoft-IIS/7.5"` (or `http.server contains "Microsoft-IIS" and http.server contains "7.5"`)
* **Answer:** `71`

<img width="800" height="551" alt="Microsoft IIS version 7 5" src="https://github.com/user-attachments/assets/7e0166d1-e030-4a3a-a198-d04bc2b182f1" />

---

3. **What is the total number of packets that use ports 3333, 4444 or 9999?**
* **Filter:** `tcp.port in {3333 4444 9999}` or `udp.port in {3333 4444 9999}` (or simply `tcp.port in {3333 4444 9999}`)
* **Answer:** `2235`

<img width="800" height="550" alt="tcp 3333 4444 9999" src="https://github.com/user-attachments/assets/13795c70-bf1a-451a-947b-71742fc7f5c7" />

---

4. **What is the number of packets with "even TTL numbers"?**
* **Filter:** `string(ip.ttl) matches "[02468]$"` (Finds all packet TTL strings ending in an even digit).
* **Answer:** `77289`

<img width="800" height="540" alt="even string matches" src="https://github.com/user-attachments/assets/204d1328-31e5-4543-80e1-40cd948c8902" />

---

5. **Change the profile to "Checksum Control". What is the number of "Bad TCP Checksum" packets?**
* **How to do it:** Go to `Edit` --> `Configuration Profiles` and select the "Checksum Control" profile (or click the profile name in the bottom right status bar). Then apply the filter for bad TCP checksum: `tcp.checksum.bad.expert`
* **Answer:** `34185`

<img width="800" height="548" alt="change profil" src="https://github.com/user-attachments/assets/453d6daa-3b83-4cf7-9f52-4e071bf3a291" />

---

6. **Use the existing filtering button to filter the traffic. What is the number of displayed packets?**
* **How to find it:** Find the existing filtering button under the main filter bar (labeled as gif/jpeg with http-200). Click it to apply the complex filter formula automatically, then check the displayed packet count in the bottom-right status bar.
* **Answer:** `261`

<img width="800" height="550" alt="final q filter advenced answer" src="https://github.com/user-attachments/assets/22cf2e7d-ccdf-4ad9-9f19-1189d2ef147c" />


---

### 🔥 EXTRA FROM ME: SOC Analyst Perspective on Advanced Filtering and Profiles

As a SOC Analyst, advanced filters and profiles are tools that make you productive in real incident situations:

* **The power of contains and matches in Threat Hunting:** When analyzing HTTP traffic from an infected workstation, an attacker might obscure code patterns with slight variations in User-Agent or URI paths. Using `http.user_agent matches "(curl|wget|python)"` instantly discards normal browsers (Chrome/Firefox) and shows scripts and tools used by the attacker to download malware.
* **Why profiles save hours of work:** In a real SOC center, each incident type (e.g., Ransomware, Phishing, Data Leak) requires different coloring rules and filters. By creating specialized profiles for each attack type, a single click changes your entire working environment in Wireshark, instantly highlighting suspicious packets in red or yellow according to your standards.

---

## Task 7: Conclusion

### 1. 📋 Room Summary

By successfully completing this room, you progressed from Wireshark's basic features to advanced operational traffic analysis techniques. The exercises mastered the following key areas:

* **Network Statistics:** Exploring global overviews via Resolved Addresses, Protocol Hierarchy, Conversations, and Endpoints, plus specific statistics for IPv4/IPv6, DNS, and HTTP protocols.
* **Filtering Principles:** The difference between Capture Filters (pre-capture) and Display Filters (during/post-capture), mastering BPF and Display Filter syntax.
* **Protocol Filters:** Precise filtering at the network (IP), transport (TCP/UDP), and application layers (HTTP, DNS) using the built-in Display Filter Expressions assistant.
* **Advanced Filtering:** Utilizing advanced operators and functions like `contains`, `matches` (with regex), `in`, `upper`, `lower`, and `string`.
* **Organization:** Creating saved filters (Bookmarks), Filtering Buttons, and isolated working Profiles.
