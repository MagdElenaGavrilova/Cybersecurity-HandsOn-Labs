# Data Exfiltration Detection

Learn how to detect data exfiltration attempts in various network channels.

## Task 1: Introduction

### 🎯 1. Room Overview
- **Title:** Data Exfiltration Detection (Premium room).
- **Essence of the room:** We learn how to detect attempts at unauthorized transfer and extraction of sensitive data across various network channels.
- **Definition of Data Exfiltration:** It is the unauthorized transfer of sensitive data from a computer or other device. This represents a primary goal for attackers who have managed to infiltrate the network.
- **Role of the SOC analyst:** Our job is to detect and prevent these attempts before sensitive information leaves the organization ("goes out the door").

### 📓 2. Learning Objectives & Prerequisites

#### 🔹 Learning Objectives
In this room we will cover the following objectives:
- Understanding common methods used for data exfiltration.
- Learning how to detect exfiltration attempts through network traffic analysis.
- Identifying signs of exfiltration on endpoint devices.
- Correlating logs in a SIEM to uncover hidden exfiltration channels.

#### 🔹 Prerequisites
The room expects users to have previously completed or explored the following rooms on TryHackMe:
- Wireshark: The Basics (packet and protocol analysis)
- Network Security Essentials (network security fundamentals)
- Splunk Basics (SIEM platform fundamentals)
- Splunk: Exploring SPL (Search Processing Language in Splunk)

---

### 🔥 EXTRA FROM ME: SOC Analyst & Defense Perspective
- **Where does Exfiltration sit in the MITRE ATT&CK framework?**
  Exfiltration is a corresponding tactic in MITRE ATT&CK (`TA0010: Exfiltration`). Attackers use techniques such as:
  - **Exfiltration Over Alternative Protocol** (e.g., using DNS, ICMP, or steganography instead of classic HTTP/FTP).
  - **Exfiltration Over Web Service** (using trusted Cloud providers like Google Drive, Dropbox, Mega).
  - **Exfiltration Over Asymmetric Encrypted Non-C2 Protocol** (encrypting data with a public key before sending so the SOC team cannot decrypt it).
- **Why is exfiltration so hard to detect?**
  Because attackers often disguise outbound traffic to look like legitimate web traffic (HTTP/HTTPS), DNS lookups, or ICMP ping packets. In addition, using TLS/SSL encryption hides the data itself, so the SOC analyst must look for anomalies in volume, timing, frequency, and destinations instead of reading the content itself in plaintext.
- **How does this connect with what we already learned (SIEM + Wireshark)?**
  Through Wireshark and network captures we learn to see raw anomalies in packets (e.g., huge DNS TXT queries, ICMP packets with added payload).
  Through Splunk / SIEM we learn to look for traffic deviations at the level of an entire organization (e.g., one host suddenly sending gigabytes of traffic to an unknown IP address overnight).

---

## Task 2: Lab Connection

### ⚙️ 1. Setting Up and Starting the Virtual Environment
- Before continuing further, start the lab machine by clicking the `Start Lab Machine` button.
- It takes about 3 minutes for the machine to load properly.
- The virtual machine (VM) will be available on the right side of the split screen. If it is not visible, click the blue `Show Split View` button at the top of the page.

### 📂 2. Where Files and the Splunk Instance Are Located
- All files needed to complete the room are located in the `data_exfil` folder on the `Desktop`.
- Logs for the investigations in the upcoming tasks are already ingested (pre-ingested) into the Splunk instance, accessed from the machine itself at `MACHINE_IP:8000`.

### 🔎 3. Three Approaches for Practical Work
For the practical part in the lab there are three ways to approach:
1. **Network traffic:** Investigating network traffic in the corresponding subfolder inside the `data_exfil` directory on the `Desktop`.
2. **Log analysis:** Performing log analysis on the log file in the same folder.
3. **Viewing logs in Splunk:** Examining the already ingested logs in the Splunk instance, where you must select `All times` and use the index `index=data_exfil`.

---

### 🔥 EXTRA FROM ME: SOC & Splunk Workflow Tips
- **Why are all three approaches useful (PCAP + Log File + Splunk)?**
  In Splunk (`index=data_exfil` with `All times`) you can quickly filter, aggregate, and correlate events via SPL (for example to see which host sends the most data, which domains/IP addresses appear, or where anomalous requests exist).
  In PCAP files (e.g., with Wireshark or `tshark`/NetworkMiner) you can see the actual packet structure, sizes, DNS/HTTP/ICMP fields, or transferred content.
  In log files directly inside `data_exfil` you can check raw entries or do an additional check when you want to see the exact format of the entries.
- **Watch out for the time range in Splunk:**
  One of the most common beginner mistakes in Splunk is leaving the time range on `Last 24 hours` or `Last 15 minutes` when the data is from another date — so selecting `All times` is crucial so your search doesn't look empty.

---

## Task 3: Overview, Techniques, and Indicators

### 🎯 1. What Is Data Exfiltration and Why Do Attackers Perform It?
- **Definition:** Data exfiltration is the unauthorized transfer of data from an organization to an external destination under the control of the adversary. It can be intentional (insider threat) or via malware / compromised credentials.
- **Reasons for exfiltration:**
  - **Financial Gain:** Stolen data (credit cards, personal records) is sold on the dark web or used for fraud.
  - **Espionage:** Nation-state actors target intellectual property, trade secrets, or classified data for strategic advantage.
  - **Ransomware & Extortion:** Attackers steal data before encrypting systems and threaten public release if ransom is not paid (dual extortion).
  - **Disruption & Sabotage:** Damaging reputation or operations by leaking internal data.
  - **Persistence & Reconnaissance:** Stolen data helps them better understand the environment for future attacks.

### 🏴‍☠️ 2. Threat Actors and Their Techniques

| Threat Actor / Campaign | Exfiltration Technique | Description |
| :--- | :--- | :--- |
| **APT29 (Cozy Bear)** | HTTPS over legitimate domains | Used encrypted HTTPS channels to exfiltrate data from government networks. |
| **FIN7** | HTTP POST to C2 servers | Embedded stolen data inside HTTP POST requests to evade detection. |
| **Lunar Spider (Zloader)** | Encrypted C2 channels | Maintained a two-month intrusion using encrypted channels and staged exfiltration. |
| **DarkSide Ransomware** | Dual extortion: encryption + exfil | Stole data before encryption, then threatened to publicly release it. |
| **APT10 (Cloud Hopper)** | Cloud-to-cloud transfer | Exfiltrated data from Managed Service Providers (MSPs) using Cloud APIs. |

### 🔄 3. Common Phases in Exfiltration
1. **Discovery / Collection:** The attacker finds where sensitive files are located.
2. **Staging / Compression:** The attacker groups, compresses, encrypts, or encodes files (`ZIP`, `RAR`, `7z`, `tar`, `base64`, steganography).
3. **Exfiltration transport:** Transfer over network, removable media (USB), cloud, or covert channels.
4. **Command & Control (C2) coordination:** Coordinating the transfer and confirmation of receipt by the C2 server.

### 🔍 4. Detection Techniques & Indicators
Detecting exfiltration requires correlation between host and network indicators (anomalous outbound transfers, long/high-entropy DNS queries, suspicious processes, Cloud API activity, USB events).

| Technique Category | Examples | Indicator of Attack (IoA) & Where to Look |
| :--- | :--- | :--- |
| **Network-based** | HTTP/HTTPS uploads (S3, Azure Blob, webmail), FTP/SFTP/SCP, DNS tunnelling, ICMP/covert protocols, custom TCP/UDP. | Proxy/Web Gateway logs (large POSTs, uploads), Firewall/NGFW flows (large volume to one IP/ASN), NetFlow (traffic spikes), DNS logs (long hostnames, TXT queries). |
| **Host-based** | PowerShell (`Invoke-WebRequest`), `rclone`, `awscli`, `curl`/`wget`, creating archives (`zip`/`rar`), USB usage, ADS (Alternate Data Streams). | Sysmon/EDR (Process Create Event 1, Network Connect Event 3, File Create Event 11), Windows Security (Event ID 4663/4656), `auditd`/shell history on Linux, USB events. |
| **Cloud exfiltration** | S3 `PutObject` / multipart upload, Azure Blob uploads, GCP Storage objects Insert, Google Drive/SharePoint external sharing. | CloudTrail / Azure Activity / GCP Audit, cloud storage logs, unusual activity on service accounts or IP addresses. |
| **Covert & encoding** | DNS tunnelling, base64 or chunked encoding, steganography in images/audio, splitting files into many small requests (low-and-slow). | DNS logs, proxy logs with many small POST requests, correlation of occasional uploads with suspicious processes. |
| **Insider & collaboration tools** | Uploads/sharing to external users via Slack, Teams, Dropbox, Google Drive, Box; compromised employees. | Audit logs (sharing/downloading events), Mail server logs. |

### 📌 5. General IoAs & Triage Signals
- High volume of outbound traffic to external IPs/domains.
- Unknown destination domains.
- Suspicious processes and command lines.
- Numerous file read events followed by an external network connection.
- Multipart/streamed uploads.
- **Correlation:** Proxy/Firewall/NetFlow + DNS + Sysmon/EDR (EventID 1, 3, 11) + Mail server logs.

### 📝 6. Question and Exact Answer for Task 3
- **Question:**
  Exfiltrating the data through HTTP comes under which technique?
- **Exact Answer:** `Network-based`
- **Explanation:** In the table from the lesson, data transfer over HTTP/HTTPS (such as POST uploads, FTP, DNS tunneling, ICMP) is explicitly categorized under Network-based techniques.

---

### 🔥 EXTRA FROM ME: SOC Analyst & Triage Perspective
- **What does real triage by an L1 SOC analyst look like on an exfiltration alert?**
  When you get an alert for "High Outbound Data Volume" or "Suspicious HTTP POST":
  1. **Who is the source?** (Which host and which user are involved?)
  2. **Which process is making the transfer?** (Is it a legitimate browser/application or a command line like `powershell.exe`, `curl.exe`, `rclone.exe`?)
  3. **What is the destination?** (Is it a known provider, private IP, newly registered domain / NRD, or foreign Cloud address?)
  4. **How was the data staged?** (Was there Event ID 11 / creation of `.zip` / `.rar` / `.7z` files in `C:\Windows\Temp\` or `AppData` before the connection?)
- **Why is "Low and Slow" exfiltration a nightmare for traditional SIEM rules?**
  Most simple alerting rules look at a static threshold (for example: "alert if there is over 1GB transfer in 5 minutes"). Attackers know this and split the data into small chunks (for example 2MB every hour or via hundreds of small HTTP POST/DNS queries), so in a SIEM we need trend and anomaly analyses over a longer time period.

---

## Task 4: Detection: Data Exfil through DNS Tunneling

### 🎯 1. Essence of DNS Exfiltration and DNS Tunneling
- **DNS exfiltration** abuses the Domain Name System — a protocol normally allowed across networks — to smuggle bytes encoded inside DNS queries/responses so firewalls and web proxies don't notice.
- Because DNS is usually allowed and often unfiltered or forwarded to public resolvers, it is attractive for covert channels.

#### 🔹 DNS Tunneling — Key Points
- **DNS (Domain Name System)** translates human-readable domain names (for example `example.com`) into IP addresses and provides other record types (`A`, `AAAA`, `TXT`, `MX`, `CNAME`, etc.).
- DNS queries are ubiquitous: almost every host performs DNS lookups.
- DNS is normally allowed through firewalls and gateways, making it an attractive covert channel.
- DNS uses UDP (most commonly) on port 53 for queries and responses; TCP is used for zone transfers or large responses.

#### 🔹 Why Do Attackers Use DNS for Exfiltration?
- **Always-on service:** DNS lookups are routine and often allowed in outbound traffic.
- **High cover:** Queries look like normal requests unless inspected carefully.
- **Flexible payload:** Data can be encoded in subdomain labels or in `TXT` responses.

### 🔍 2. Indicators of Attack (IoA)
When analyzing DNS traffic for possible indicators of data exfiltration, we should look for:
- High number of DNS queries sent to a single external domain, especially with a very high count compared to the normal baseline.
- Long subdomain labels or unusually long full query names (`> 60–100` characters).
- High entropy or Base32 / Base64-like patterns in the query name (many mixed uppercase/lowercase letters, digits, `=` signs for base64).
- Rare record types (`TXT`, `NULL`) or very large `TXT` responses.
- Unusual response behavior: frequent `NXDOMAIN` (if the attacker uses exfil-by-query without a response), or TCP / large UDP fragments for DNS.
- Queries at regular intervals (beaconing behaviour).

### 🦈 3. Detection via Wireshark (`dns_exfil.pcap`)
The `dns_exfil.pcap` file is located in `/data_exfil/dns_exfil/` on the `Desktop`. The lesson provides the following display filters in Wireshark:
- **Filter for all DNS queries/traffic:**
  `dns`
- **Filter for DNS queries without a response:**
  `dns.flags.response == 0`
  *(Here DNS records with large query length are noticed).*
- **Finding long queries (suspicious subdomain lengths):**
  `dns && frame.len > 70`
  *(The result identifies one specific suspicious domain receiving these suspicious DNS queries).*
- **Filtering by the suspicious domain:**
  `dns && dns.qry.name contains <REDACTED>`
- **What we can observe from network traffic:**
  - Multiple internal hosts are compromised.
  - All of those hosts send data in chunks using DNS tunneling techniques.
  - Only one external domain is identified receiving the DNS queries.

### 📊 4. Investigation and Correlation in Splunk (`index=data_exfil`)
Open the Splunk instance and enter the following queries in the search bar (with `All times`):
- **Basic view of DNS logs:**
  `index=data_exfil sourcetype=DNS_logs`
  *(Displays results matching `sourcetype=DNS_logs` — the lesson also mentions a lowercase `sourcetype=dns_logs` variant in the next query).*
- **Statistics of DNS queries by source IP address (`src_ip`):**
  `index="data_exfil" sourcetype="DNS_logs" | stats count by src_ip`
  *(Looks for single hosts generating far more DNS queries than normal).*
- **Statistics of queries sorted by count (from highest to lowest):**
  `index="data_exfil" sourcetype="dns_logs" | stats count by query | sort -count`
  *(Clearly displays unusual DNS queries with large size).*
- **Filtering queries with length greater than 30 characters (`len(query) > 30`):**
  `index="data_exfil" sourcetype="DNS_logs" | where len(query) > 30`
- **Key indicators confirming DNS tunneling exfiltration:**
  - High number of DNS queries without a response.
  - Large DNS query length.

### ✅ 5. How to Get the Exact Answers to Questions in Task 4
Since this overview is made from the task description without an active VM session loaded right now, here is how to extract all three values from `dns_exfil.pcap` and Splunk:

1. **What is the suspicious domain receiving the DNS traffic?**
   - **How to find it:** Filter `dns && frame.len > 70` in Wireshark (or check long queries in Splunk with `index="data_exfil" sourcetype="DNS_logs" | where len(query) > 30`) and look at the common external domain at the end of the subdomain strings receiving these requests.
   - **Answer:** `tunnelcorp.net`

<img width="1287" height="879" alt="tunnelcorp q1 data ex" src="https://github.com/user-attachments/assets/497aed94-559d-4eed-b994-d0b18886f9f0" />

---

or in Splunk:

<img width="1294" height="830" alt="dns splunk q 1 " src="https://github.com/user-attachments/assets/89c1ef91-f237-4a47-832d-ef8cd364d2b4" />

---

2. **How many suspicious traffic/logs related to dns tunneling were observed?**
   - **How to find it:** `index="data_exfil" sourcetype="DNS_logs" | where len(query) > 30` — the number of events/logs in Splunk for that suspicious domain / filter.
   - **Answer:** `315`

<img width="1289" height="847" alt="315num suspic dns " src="https://github.com/user-attachments/assets/617ccf31-ea74-4bd3-b658-4cda7fa699e2" />

---

3. **Which local IP sent the maximum number of suspicious requests?**
   - **How to find it:** Check in Splunk with `index="data_exfil" sourcetype="DNS_logs" | where len(query) > 30` — in `interested fields` on the left side click on `src_ip` to see which local/internal IP address has the highest number of suspicious DNS queries.
   - **Answer:** `192.168.1.103`

<img width="1297" height="896" alt="ip suspic" src="https://github.com/user-attachments/assets/00c4f6c0-f8c8-467a-a0d4-bbacf76da39d" />

---

### 🔥 EXTRA FROM ME: SOC Analyst & Detection Engineering Perspective
- **Why are `dns.flags.response == 0` and long subdomains such a good signal?**
  In DNS tunneling, the attacker often encodes the stolen file into subdomains (for example `<encoded_data>.evil-domain.com`) and sends them to their own authoritative DNS server. Even if the response is empty, `NXDOMAIN`, or a large `TXT` record, the shape of the query itself — strings without words, high entropy, and an unusually high number of queries to the same domain — strongly deviates from normal DNS traffic.
- **Why is Wireshark + Splunk correlation important?**
  In Splunk you quickly see statistics and trends (which `src_ip` makes the most queries, which `query` strings are longest and most frequent across all logs).
  In Wireshark you see the raw packet and context (how long the packet is `frame.len`, whether there is a response or not `dns.flags.response`, which DNS record type was used, and what the whole packet looks like).

---

## Task 5: Detection: Data Exfil through FTP

### 🎯 1. Essence and Use of FTP for Exfiltration
- **FTP (File Transfer Protocol)** is one of the oldest protocols for transferring files between a client and server over a TCP/IP network.
- Attackers use it to extract large amounts of data from the network, sometimes via compromised credentials, misconfigured servers, or ephemeral accounts.
- **Detection is based on:**
  - Packet inspection (for plain FTP).
  - Server logs.
  - SSH session metadata.
  - Network flow, size, and pattern analysis.

#### 🔹 How Do Adversaries Use FTP for Exfiltration?
- Use legitimate FTP servers (public or misconfigured internal servers) to stage/transfer data.
- Use compromised credentials (service accounts, user credentials).
- Use non-standard ports or tunneling to blend with other traffic.

### 🔍 2. Indicators of Attack (IoA)
When investigating FTP traffic, attention focuses on:
- `USER` and `PASS` commands (credentials transmitted in cleartext).
- `STOR` (upload) and `RETR` (download) commands: repeated transfers or transfers of large files.
- Large data connections to unusual external IP addresses, especially outside working hours.
- Opening data channels on ephemeral ports (`PASV`) combined with large payloads.

### 🦈 3. Investigation with Wireshark (`ftp-lab.pcap`)
The `ftp-lab.pcap` file is located in `/data_exfil/ftp_exfil/` on the `Desktop`. The analysis steps and corresponding display filters are:

1. **Isolating FTP control and data traffic:**
   `ftp || ftp-data`
   *(This filter isolates FTP control traffic).*
2. **Searching for credentials (login attempts):**
   `ftp.request.command == "USER" || ftp.request.command == "PASS"`
   *(Allows you to look for suspicious usernames or weak passwords).*
3. **Looking for anomalies in filenames or commands:**
   `ftp contains "STOR"`
   *(Allows you to see all commands for uploading files to the server).*
   - **TCP Stream analysis:** Right-click on a packet → `Follow` → `TCP Stream` opens the entire communication in cleartext.
4. **Filtering by specific file extensions (`.pdf`, `.csv`, `.txt`):**
   `ftp contains "csv"`
   *(The result shows that a suspicious IP address connected as a Guest account transferred sensitive `.csv` files to a suspicious external IP address).*
5. **Identifying traffic with large payload size:**
   `ftp && frame.len > 90`
   *(Reviewing the content in TCP Stream reveals transfer of documents with sensitive information to an external IP address).*

### 📝 4. Solving and Guidance for Questions in Task 5
To get the exact values directly in Wireshark from `ftp-lab.pcap`:

1. **How many connections were observed from the guest account?**
   - **Filter in Wireshark:**
     `ftp.request.command == "USER" && ftp.request.arg == "guest"`
   - **Answer:** `5`

<img width="1275" height="882" alt="5 times guest " src="https://github.com/user-attachments/assets/9214c657-95bc-4b27-8d57-560d601f1358" />

---

2. **Apply the filter; what is the name of the customer-related file exfiltrated from the root account?**
   - **Filter in Wireshark:** First filter the sessions where the user is `root` (`ftp contains "root"` or follow the TCP Stream for `USER root`), then look for the `STOR` command for file transfer. The file containing customer information:
   - **Answer:** `customer_data.xlsx`

<img width="1272" height="864" alt="store customerdata user related" src="https://github.com/user-attachments/assets/64defb4a-3789-4820-9467-42c8136e0129" />

---

3. **Which internal IP was found to be sending the largest payload to an external IP?**
   - **Method in Wireshark:** Go to `Statistics` → `IPv4 Statistics` → `Source and Destination Addresses` (or `Conversations` → `TCP` tab), sort by `Bytes` from highest to lowest, and see which internal IP (`10.x.x.x` or `192.168.x.x`) sent the most bytes to the external IP.
   - Or with filter `ftp && frame.len > 90`.
   - **Answer:** `192.168.1.105`

<img width="1271" height="895" alt="ip ftp large size " src="https://github.com/user-attachments/assets/21a0e069-3844-4cf2-9ed0-ec3a05dd99e1" />

---

4. **What is the flag hidden inside the ftp stream transferring the CSV file to the suspicious IP?**
   - **Method in Wireshark:** Apply the filter `ftp contains "csv"` to find the packets containing the transfer of the `.csv` file. Right-click on the corresponding FTP/FTP-Data packet → `Follow` → `TCP Stream`. Inside the content of the CSV file or in the header/text you will see the flag (format `THM{ftp_exfil_hidden_flag}`).
   - **Answer:** `THM{ftp_exfil_hidden_flag}`

<img width="1260" height="851" alt="flag FTP" src="https://github.com/user-attachments/assets/5765eab9-8e7c-4814-88cf-8f3c9473aec9" />

---

### 🔥 EXTRA FROM ME: SOC & Protocol Architecture Perspective
- **FTP Control Channel vs. FTP Data Channel (Port 21 vs. Port 20/Passive):**
  In FTP there are two separate channels:
  - **Control Channel (Port 21):** Commands go here in cleartext (`USER`, `PASS`, `STOR`, `RETR`, `PASV`).
  - **Data Channel (Port 20 in Active mode, or an ephemeral high port range in Passive mode):** The actual file content goes here.
  - **SOC Trick:** If in Wireshark you filter only with `ftp`, you see only the commands! To see the actual bytes and content of the files being transferred, you must use `ftp-data` or follow the `TCP Stream` of the corresponding connection.
- **Why is FTP easy to detect, yet still used by attackers?**
  Because FTP is not encrypted (unlike SFTP/FTPS), the entire content and credentials are in cleartext, so for a SOC analyst with Wireshark or NetworkMiner it is extremely easy to see the exact content payload. However, attackers use it because many outdated legacy systems still have open FTP servers with generic or forgotten usernames (like `guest` or `anonymous`).

---

## Task 6: Detection: Data Exfil via HTTP

### 🎯 1. Essence of HTTP Exfiltration and Why It Matters
- **Data exfiltration via HTTP** occurs when an attacker transfers sensitive data outside the target network using HTTP as the transport.
- HTTP is often abused because it blends into normal web traffic, can pass through firewalls and proxies, and can be obfuscated (encoding, encryption, tunneling).
- The goal of this detection is to teach SOC analysts how to recognize signs of exfiltration over HTTP in packet captures (Wireshark) and logs (Splunk), with practical queries and investigation steps.

#### 🔹 Why it matters
- HTTP is very common: attackers hide exfiltration in the noise of legitimate web usage.
- Successful detection stops data breaches and helps track attacker activity post-compromise.
- Organizations must detect and respond to protect sensitive data and meet compliance requirements.

### 🏴‍☠️ 2. How Adversaries Use HTTP for Exfiltration
- **POST uploads to external servers:** Large amounts of data are sent to attacker-controlled hosts or cloud storage in request bodies.
- **GET requests with encoded data:** The attacker embeds small chunks in query strings or path segments (useful for low-and-slow exfiltration).
- **Using known services / CDNs:** Exfiltration disguised as uploads to popular services or attacker-controlled subdomains under reputable domains.
- **Custom headers:** Data placed in headers (for example `X-Data: <base64>`) can bypass some string-based DLP (Data Loss Prevention) rules.
- **Chunked transfer / multipart:** Large payloads split across multiple requests to avoid size thresholds.
- **HTTPS/TLS tunneling:** An encrypted channel hides content; detection requires TLS inspection, SNI analysis, or metadata-based detection.
- **Staging via cloud services:** The attacker uploads to Dropbox / GitHub / Gist, then downloads externally.
- **Attacker adaptation:** They use low-and-slow approaches, encryption/encoding, and legitimate services to evade detection.

### 🔍 3. Indicators of Attack (IoA)
Common network indicators:
- Unusually large HTTP POST requests to external / unexpected hosts.
- HTTP requests to low-reputation domains or domains rarely seen in baseline traffic.
- Frequent small requests (beaconing) to the same host, followed by large uploads.
- Chunked or multipart transfers where multiple requests assemble a larger file.

### 📊 4. Log Analysis in Splunk (`sourcetype="http_logs"`)
Open Splunk with time range `All Time` and run the following queries:

1. **View all HTTP logs:**
   `index="data_exfil" sourcetype="http_logs"`
2. **Filter only POST requests:**
   `index="data_exfil" sourcetype="http_logs" method=POST`
3. **Average, maximum, and minimum bytes sent per domain:**
   `index="data_exfil" sourcetype="http_logs" method=POST | stats count avg(bytes_sent) max(bytes_sent) min(bytes_sent) by domain | sort - count`
4. **Isolating POST requests with large payloads (`bytes_sent > 600`):**
   `index="data_exfil" sourcetype="http_logs" method=POST bytes_sent > 600 | table _time src_ip uri domain dst_ip bytes_sent | sort - bytes_sent`
   *(The analysis points to one suspicious entry with a large chunk of data sent to an external source, which we then correlate with the PCAP capture).*

### 🦈 5. Network Analysis with Wireshark (`http_lab.pcap`)
The `http_lab.pcap` file is located in `/data_exfil/http_exfil/`. The steps and filters are:

1. **Filter for HTTP traffic:**
   `http`
   *(Both GET and POST requests are seen).*
2. **Filter for POST requests:**
   `http.request.method == "POST"`
3. **Filter for POST requests with frame size over 500 bytes:**
   `http.request.method == "POST" and frame.len > 500`
4. **Increasing the size threshold to 750 bytes to reduce noise:**
   `http.request.method == "POST" and frame.len > 750`
   *(This yields only one entry — the same one appearing in Splunk. Opening the HTTP Stream shows the content of the secret document being exfiltrated to the external IP address).*

### ✅ 6. How to Get the Exact Answers to Questions in Task 6

1. **Which internal compromised host was used to exfiltrate this sensitive data?**
   - **How to find it:** In Splunk from the table with `bytes_sent > 600` (or in Wireshark at the packet with `http.request.method == "POST" and frame.len > 750`), look at the value for `src_ip` (the source internal IP address of the host sending the POST request with the large `bytes_sent` / `frame.len`).
   - **Answer:** `192.168.1.103`

<img width="1298" height="832" alt="q1 http src IP" src="https://github.com/user-attachments/assets/2f25814c-78c7-4e26-a92b-be009f070e75" />

---

2. **What's the flag hidden inside the exfiltrated data?**
   - **Answer:** `THM{http_raw_3xf1ltr4t10n_succ3ss}`

<img width="1304" height="893" alt="flag http q " src="https://github.com/user-attachments/assets/d78290a6-6ad0-424e-8162-94741a8990ba" />

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective
- **Why is the POST body a classic channel, and where do more advanced attackers hide?**
  When a firewall or proxy looks only at the URL path or query string, the HTTP POST body often isn't inspected in detail without SSL/TLS inspection or deep packet inspection, making it ideal for uploading files or JSON/Base64 blocks.
  More subtle attackers use `GET` with parameters (for example `?data=...`) when they want to bypass systems blocking large POST requests, or use custom headers (like `X-Data: ...`) where few people look during standard overview triage.
- **How to read logs in Splunk without wasting time?**
  The combination of `stats count avg(bytes_sent) max(bytes_sent) by domain` and `where bytes_sent > <threshold> | table _time src_ip uri domain dst_ip bytes_sent | sort - bytes_sent` is a gold standard for hunting exfiltration: first you see the anomaly by destination/domain, and then you pull the exact fields to correlate with packets in Wireshark.

---

## Task 7: Detection: Data Exfiltration via ICMP

### 🎯 1. Essence of ICMP Exfiltration
- **ICMP** is a network-layer protocol used for diagnostics and control (for example `ping`, `TTL exceeded`).
- Because it is often allowed through firewalls and usually inspected less strictly than TCP/UDP, attackers sometimes abuse ICMP for tunneling and data exfiltration.
- Malicious actors encode data in ICMP payloads (echo request/reply, timestamp, info) and send it to a remote listener under their control.

### 🏴‍☠️ 2. How Adversaries Use ICMP for Exfiltration

#### 🔹 Common techniques
- **ICMP echo (type 8) / reply (type 0) tunneling:** Attackers put encoded (base64, hex) chunks of files inside ICMP payloads. The remote server collects and decodes them.
- **Custom ICMP types/codes:** Using unusual ICMP types or codes other than zero to evade signature-based detections.
- **Fragmentation and reassembly:** Large payloads are split across multiple packets.
- **Encryption/obfuscation:** Encrypting or encoding payloads (base64 is common) to make them look like random data.

#### 🔹 Indicators that something may be malicious
- Persistent ICMP sessions to an external host not used for legitimate monitoring.
- Unusually large ICMP payloads or frequent ICMP with a payload larger than typical ping size.
- ICMP payloads containing high-entropy data or patterns consistent with base64/hex.
- Bursts of ICMP followed immediately by no other legitimate application traffic from the same host.

### 🦈 3. Indicators of Attack in Wireshark
When reviewing a pcap in Wireshark, pay attention to:
- **Volume of ICMP packets:** One host sending many ICMP echo requests to an external IP address.
- **Large `frame.len` or `icmp.payload`:** Pings with payloads much larger than typical ones (for example `> 64` bytes payload).
- **Unusual values for ICMP type/code:** For example unusual use of timestamp (`13`/`14`) or custom codes.
- **Regular timing (periodicity):** Evenly spaced ICMP packets carrying payloads of similar size.
- **Fragments with reassembly:** Multiple ICMP fragments from the same src/dst pair.

### 🔬 4. Traffic Analysis (`icmp_lab.pcap`)
For this task, open the `icmp_lab.pcap` file located in `/data_exfil/icmp/exfil` on the `Desktop` (the path `/data_exfil/icmp/exfil` is given in the task text).

1. **Filtering all ICMP traffic:**
   `icmp`
   *(Isolates all ICMP traffic; looking for unusually frequent or large ICMP Echo Requests/Replies).*
2. **Isolating Echo Requests:**
   `icmp.type == 8`
3. **Examining large ICMP packets:**
   `icmp.type == 8 and frame.len > 100`
   *(Flags packets with unusually large payloads. Normal pings are around `~74` bytes total. Anything over `100` is suspicious).*

- **Conclusion from the task:** ICMP is simple, and any anomaly can be easily detected by examining frame size and investigating larger payload size than usual.

### ✅ 5. How to Get the Exact Answer to the Question in Task 7
- **Question:**
  What is the flag found in the exfiltrated data through ICMP?
- **How to find it in Wireshark:**
  1. Open `icmp_lab.pcap` in Wireshark and apply the filter:
     `icmp.type == 8 and frame.len > 100`
  2. Click on the first/relevant large ICMP packet, open the `Internet Control Message Protocol` section in the bottom `Packet Details` panel, and look at the `Data / Payload` field (or select the `Data` section and look at the ASCII display in `Packet Bytes` below).
  3. Check the large ICMP packets in order inside the data/payload section (or in the assembled content from the packets) — you will find the flag in the format `THM{1cmp_3ch0_3xf1ltr4t10n_succ3ss}`.
- **Answer:** `THM{1cmp_3ch0_3xf1ltr4t10n_succ3ss}`

<img width="1165" height="857" alt="flag ICMP" src="https://github.com/user-attachments/assets/4ebf7550-b8cc-44c8-a9cb-d5965736c7d0" />

---

### 🔥 EXTRA FROM ME: SOC & Packet Inspection Perspective
- **Why is a normal ping from Linux around 74 bytes (`frame.len`) when viewed in Wireshark?**
  A standard ping usually sends 56 bytes of ICMP data payload (or 64 bytes total with the 8-byte ICMP header at the ICMP level), and when adding 14 bytes Ethernet header + 20 bytes IPv4 header + 8 bytes ICMP header = 98 bytes (or ~74 bytes for smaller standard packets/settings). Therefore, when you suddenly see `frame.len` over 100 (or hundreds of bytes), you immediately know that someone inserted additional data inside.
- **How do attackers hide data in ICMP in real networks?**
  Besides echo request/reply (type 8 / type 0), in real tests and attacks tools may be encountered that use Base64, Hex, XOR, or custom magic headers at the beginning of the ICMP data field so the receiving side knows which packet is first, which is last, and how to assemble the data.

---

## Task 8: Conclusion

### 📌 1. Room Summary from a Defender's Perspective
- This completes this room.
- The room covered only a portion of the channels used for data exfiltration and focused on network-centric log sources to find traces of data sent outside the organization.
- We examined data exfiltration from a defender's perspective.

### 🧩 2. What We Covered Throughout the Tasks
- **The Fundamentals:** What data exfiltration represents and why it is a critical threat to organizations.
- **Attacker Frameworks:** How data exfiltration fits into established models like the Cyber Kill Chain (Actions on Objectives).
- **Common Techniques:** The different methods attackers use to steal data, including FTP, HTTP, DNS, and ICMP tunneling.

### 🔭 3. What Follows Next
- Adversaries use other channels for data exfiltration as well, and other log sources can be used to find indicators of attack.
- Future rooms will cover additional techniques and sources.

---

### 🔥 EXTRA FROM ME: SOC & Threat Hunting Takeaway
- **Why is Cyber Kill Chain — Actions on Objectives a crucial context for exfiltration?**
  When you see exfiltration (or even preparatory signals for it), the attacker has already passed through previous phases (access, movement/privileges, data collection) and is in the final phase where they accomplish their goal. Timely detection here can prevent actual damage before the data ends up with the attacker.
- **How to keep this knowledge set active in daily practice?**
  Whenever you analyze network traffic or logs, ask yourself three questions:
  - Which protocol is being used? (Is it typical for that host?)
  - What is the volume and length of the packets/requests? (Are there unusual spikes, long subdomains, large POSTs, or large ICMP payloads?)
  - Is there consistency in timing and destination?
