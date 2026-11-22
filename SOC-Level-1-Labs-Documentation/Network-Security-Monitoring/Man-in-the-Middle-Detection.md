# Man-in-the-Middle Detection 🕵️‍♂️

Learn what MITM attack is, and how to identify the footprints of this attack in the network traffic.

## Task 1: Introduction

### 🎯 1. Room Overview and Definition of MITM

* **Path / Category:** SOC Level 1 → Network Security Monitoring → Man-in-the-Middle Detection (Premium room, estimated time: 60 min).
* **Goal of the room:** We learn what a MITM (Man-in-the-Middle) attack represents and how to identify the footprints of this attack in network traffic.
* **What is a MITM attack:** Man-in-the-middle (MITM) attacks represent one of the most insidious threats in network security.
* **How attackers operate:** Attackers position themselves between legitimate communication endpoints to intercept, modify, or redirect traffic.
* **Blue Team perspective:** Detecting these attacks from a blue team perspective requires a multi-layered approach that combines:
* network monitoring,
* certificate validation, and
* behavioral analysis.


* **Focus in the room:** We learn the fundamentals of Man-in-the-Middle (MITM) attacks and how to recognize the signs in network traffic.

### 📓 2. Learning Objectives & Prerequisites

#### 🔹 Learning Objectives

The room focuses on the following learning objectives:

* Understanding common MITM attack vectors and techniques.
* Learning how to identify indicators of compromise (IoCs) related to MITM attacks.
* Mastering network monitoring tools to detect suspicious traffic patterns.
* Practicing incident response procedures for MITM scenarios.

#### 🔹 Prerequisites

This room expects users to have previously explored or completed the following rooms:

* Wireshark: The Basics (written as "Wirehsark: The Basics" in the text)
* Network Security Essentials
* Networks Discovery Detection

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective

* **Why is MITM such an "insidious" attack for defenders?**
Because from the perspective of the end user or application, the connection looks like it is functioning normally — only the traffic passes first through the attacker's device/host. If there is no good Layer 2/3 verification (e.g., ARP/DHCP/DNS anomalies) or validation of TLS/SSH certificates and keys, the attacker can read or modify data before it reaches the real destination.
* **How does MITM look from both sides (Pentester vs. SOC / Blue Team)?**
From the perspective of an attacker / pentester: the main goal is to take over a communication path (for example via ARP spoofing/poisoning on a local network, LLMNR/NBT-NS poisoning, forged DNS responses, or rogue access points) to obtain credentials, session tokens, or perform a protocol downgrade (for example from HTTPS to HTTP or a weak cipher).
From the perspective of SOC / Blue Team: we look for inconsistencies — for example one MAC address suddenly binding to multiple IP addresses or changing for a known gateway, unusual ARP/DNS/DHCP responses, certificate warnings, or sudden changes in routing/sessions — and then correlate them with the location and role of the hosts in the network for proper incident response.

---

## Task 2: Lab Connection

### 📖 1. Incident Scenario (Scenario Acme Corp)

* A routine network monitoring alert at Acme Corp uncovered unusual traffic patterns indicating a possible Man-in-the-Middle (MITM) attack within the corporate local area network (LAN).
* Over several days, an attacker silently intercepted communications, redirected connections, and captured user credentials.
* In this room you take on the role of a SOC analyst investigating this incident.
* Using the provided network traffic capture (packet capture) and logs, you will uncover evidence of three connected (chained) MITM techniques:
* **ARP Spoofing** (network interception),
* **DNS Spoofing** (redirection),
* **SSL Stripping** (credential capture).



### ⚙️ 2. Lab Access and Setup

* Before continuing further, start the lab machine by clicking the `Start Lab Machine` button (the text above states that the machine starts in split view and takes about two minutes to load; if it is not visible, the blue `Show Split View` button at the top of the task is used, and the machine details are at the top of the page).
* In this snippet the status is shown as `Lab machine Status:On` with the option `See connection methods`.

### 📂 3. Important Note on Data, Logs, and Splunk

To complete this room we will rely on the network traffic located in the `mitm` folder on the `Desktop`, but there are also other ways to examine and complete the task:

* A complete log file named `mitm_attack.log`, containing the entire chain of the attack, is located in the same folder (`mitm` on the `Desktop`).
* The same logs are ingested into Splunk, which can be accessed at `10.113.183.214` (on the Splunk port, usually `:8000` in a browser), and the logs are ingested into the index `index=network_logs`.

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective on the Chained Attack

* **Why do these three techniques go together in one chain (ARP Spoofing → DNS Spoofing → SSL Stripping)?**
* **Step 1 — ARP Spoofing:** On the local network (LAN), the attacker sends fake ARP messages to impersonate the default gateway to the victim (and/or the victim to the router), so all traffic flows through their host.
* **Step 2 — DNS Spoofing:** Once traffic already passes through the attacker, when the victim requests a certain domain, the attacker can return a fake DNS response that redirects them to an IP address under their control.
* **Step 3 — SSL Stripping:** If the victim initiates or downgrades to unencrypted HTTP instead of HTTPS, the attacker can communicate the session in cleartext with the victim, while using HTTPS themselves to the real server — thereby seeing the username and password upon login.


* **How to use all three evidence sources (`PCAP` + `mitm_attack.log` + `index=network_logs`) during an investigation?**
* Use Splunk (`index=network_logs`) for quick searching by IP addresses, domains, usernames, HTTP methods/statuses, and the timeline of the attack.
* Use `mitm_attack.log` on the `Desktop` when you want to directly check raw text entries, formats, or additional details.
* Use the PCAP file in the `mitm` folder (for example with Wireshark) to see the actual ARP packets, DNS responses, and HTTP/TCP streams.



---

## Task 3: MITM Attacks = An Overview

### 🎯 1. What Is a MITM Attack and How Does It Work?

* **Definition:** A Man-in-the-Middle (MITM) attack is a cyberattack where an attacker secretly intercepts and potentially alters communication between two parties — for example a user and a service — without their knowledge.
* **Attacker's goal:** They may eavesdrop to steal sensitive data such as credentials and credit card information, or inject malicious content. These attacks can target any organization or individual, especially where encryption or authentication is weak.
* **Two main steps of operation:**
* **Interception:** The attacker inserts themselves into the communication flow, often exploiting weaknesses in network protocols or using techniques such as ARP, DNS, or IP spoofing.
* **Manipulation/Decryption:** The attacker attempts to access or alter the communication, decrypting encoded data or injecting harmful content, such as modified webpage responses or fake login forms.



### 🧩 2. Common Types of MITM Attacks

* **Packet sniffing:** Capturing unencrypted data packets exchanged across a network, often on open Wi-Fi.
* **Session hijacking:** Stealing and using session tokens to impersonate users.
* **SSL stripping:** Downgrading HTTPS connections to insecure HTTP in order to steal or alter data being transmitted.
* **DNS spoofing:** Redirecting legitimate web traffic to fraudulent domains by manipulating DNS responses.
* **IP spoofing:** Crafting malicious IP packets that appear to come from trusted systems.
* **Rogue Wi-Fi access point:** Creating fake networks to intercept users' traffic.

### 🌍 3. Real-World Examples

* In 2017, Equifax suffered a major data breach due to a MITM attack, exposing the sensitive data of over 100 million users.
* High-profile attacks have also included code injections by ISPs and state actors intercepting search traffic via SSL spoofing.

### ⛓️ 4. MITM and the Cyber Kill Chain

For effective analysis and response to security events, the analyst must contextualize alerts within a broader model of adversary operations. Isolated indicators gain meaning when mapped to the attacker's Tactics, Techniques, and Procedures (TTPs). A widely accepted model for this is the Cyber Kill Chain framework, which orders the typical phases of an advanced cyber intrusion.

#### 🔹 The Seven Phases of the Cyber Kill Chain

* **Reconnaissance:** The adversary gathers information about the target to identify vulnerabilities.
* **Weaponization:** The adversary pairs an exploit with a malicious payload into a deliverable package.
* **Delivery:** The adversary transmits the prepared package to the target environment.
* **Exploitation:** The adversary's code triggers and uses a software, hardware, or human vulnerability to gain initial access.
* **Installation:** The adversary installs malware or establishes a persistent backdoor on the compromised asset.
* **Command & Control (C2):** The adversary establishes a covert channel for communication and control over the compromised asset.
* **Actions on Objectives:** The adversary executes their final goals, such as data exfiltration, destruction, or lateral movement.

#### 🔹 Placing MITM in the Cyber Kill Chain

MITM is a versatile technique that adversaries primarily use during the **Exploitation** and **Installation** phases:

* **As an Exploitation technique:** A MITM attack exploits the inherent trust and design limitations of key network protocols like ARP and DNS. By manipulating these protocols, the attacker can intercept a communication channel. This act of session interception is a form of exploitation, as it disrupts network integrity and gives the adversary an entry point for eavesdropping or active manipulation.
* **As an Installation vector:** Once the attacker successfully establishes a MITM position, they control the flow of data and can use it as a mechanism to deliver malicious payloads. For example, the attacker can inject a browser exploit, malware dropper, or remote access trojan (RAT) into legitimate, unencrypted downloads. This action corresponds to the Installation phase, where the adversary establishes persistence or deploys additional malicious tools on the victim's system.
* **Key takeaway for SOC:** Detecting a MITM attack is a critical finding. It shows that the adversary is actively involved in the middle phases of the intrusion, which represents a key opportunity for the SOC to intervene and break the attack chain before final objectives are achieved.

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective

* **Why is MITM in the middle phases of the Kill Chain a golden window for Blue Team?**
If you catch MITM while the attacker is only redirecting traffic or trying to do SSL stripping / code injection, you are often still ahead of the main data being stolen or a stronger persistence malware taking root. That is why traffic anomalies in LAN and DNS are not "minor noise" — they are a call for rapid triage.
* **How do pentesters and SOC analysts view encryption and certificates?**
Modern HTTPS with HSTS (HTTP Strict Transport Security), proper certificate validation, and certificate pinning on sensitive applications make classic SSL stripping significantly harder. Therefore, when an attacker tries to downgrade a connection or uses an invalid/unknown certificate, or when fake ARP/DNS responses appear in the network, that immediately becomes a strong signal for alerting and further investigation.

---

## Task 4: Detecting ARP Spoofing

### 🎯 1. What Is the ARP Protocol and What Is ARP Spoofing?

* **What Is the ARP Protocol:** ARP (Address Resolution Protocol) maps IP addresses to MAC addresses on a local network. When a device wants to send data to another IP address, it first asks: `"Who has this IP?"`. The exact device responds with its MAC address.
* **ARP Spoofing:** In ARP spoofing, an attacker sends fake ARP replies to trick devices into associating the attacker's MAC address with a legitimate IP address — most commonly the default gateway. This allows the attacker to intercept, modify, or redirect traffic.

### ⚠️ 2. Why Does ARP Spoofing Work and What Are the Results?

* **Why ARP Spoofing Works:** ARP has no authentication. Any device can send unsolicited `is-at` messages. The attacker exploits this vulnerability by sending fake ARP replies to victims and gateways:
* For example, it is shown that the attacker claims to be the gateway (in the example from the lesson it is stated: `192.16.10.100 is at 02:fe:BB:cd:55:55 attacker claims to be the gateway`).


* **Results:**
* The victim's ARP cache becomes poisoned.
* All traffic destined for the gateway first flows through the attacker (MITM).



### 🔍 3. Indicators of the Attack

When investigating logs or network traffic for a potential MITM attack with ARP spoofing, look for these key indicators:

* **Duplicate MAC-to-IP Mappings:** Multiple MAC addresses claim to use the same IP address. Indicates impersonation.
* **Unsolicited ARP Replies:** A high number of ARP replies without corresponding requests ("gratuitous ARP").
* **Abnormal ARP Traffic Volume:** A high number of ARP packets in short intervals.
* **Unusual Traffic Routing:** Traffic rerouted through the attacker's MAC address.
* **Gateway Redirection Patterns:** Multiple destination MAC addresses for the same gateway IP address.
* **ARP Probe / Reply Loops:** Many ARP requests with patterns `Who has 192.168.1.x? Tell 192.168.1.y`.

### 🗺️ 4. Network Information

The lesson provides the following table with network data:

| Role | IP | MAC | Notes |
| --- | --- | --- | --- |
| **Gateway** | `192.168.10.1` | — (in the filters in the text `02:aa:bb:cc:00:01` appears) | Legit router |
| **Attacker** | — | — (in the filters in the text `02:fe...` is mentioned) | — |
| **Victim** | — | — | — |
| **Domain** | — | — | `corp-login.acme-corp.local` |

### 🦈 5. Network Analysis in Wireshark (`network-traffic.pcap`)

The `network-traffic.pcap` file is located in the `mitm_traffic` folder on the `Desktop`. The steps and filters from the task are:

* **Important note:** Press `CTRL + ALT + 1` to fix the time displayed.
* **Narrowing down ARP traffic:**
`arp`
* **ARP Requests:**
`arp.opcode == 1`
* **ARP Response:**
`arp.opcode == 2`
* **Gratuitous ARP Responses:**
`arp.isgratuitous`
* **ARP traffic related to Gateway (`192.168.10.1` and `02:aa:bb:cc:00:01`):**
`arp && arp.src.proto_ipv4 == 192.168.10.1 && eth.src == 02:aa:bb:cc:00:01`
* **More details on Gateway IP and associated MAC addresses:**
`arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1`
* **Confirmation via Info column for `192.168.10.1 is at`:**
`arp.opcode ==2 && _ws.col.info contains "192.168.10.1 is at"`
* **Examining the suspicious MAC address of the attacker:**
`arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1 && eth.src == 02:fe[REDACTED]`
* **Checking for duplicate IP-to-MAC mappings:**
`arp.duplicate-address-detected || arp.duplicate-address-frame`

### ✅ 6. How to Get the Exact Answers to Questions in Task 4

1. **How many ARP packets from the gateway MAC Address were observed?**
* **How to find it:** Filter with `eth.src == 02:aa:bb:cc:00:01` (or `arp && eth.src == 02:aa:bb:cc:00:01`) in Wireshark and check the number of displayed/matching packets in the bottom bar (`Displayed packets` / results counter).
* **Answer:** `10`

<img width="1166" height="823" alt="arp packet MAC" src="https://github.com/user-attachments/assets/6e2d5c33-2223-45b5-a4a0-a4ce0ef38c11" />

---

2. **What MAC address was used by the attacker to impersonate the gateway?**
* **How to find it:** Apply the filter `arp.opcode ==2 && _ws.col.info contains "192.168.10.1 is at"` and see which MAC address (other than the legitimate `02:aa:bb:cc:00:01`) advertises for `192.168.10.1`.
We can also find it with this filter:
`arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1`
or with this filter:
`arp.duplicate-address-detected || arp.duplicate-address-frame`
* **Answer:** `02:fe:fe:fe:55:55`

<img width="1200" height="859" alt="duplicated ip spoofing" src="https://github.com/user-attachments/assets/30cc27ca-6522-4723-8d48-a6fa0d550db8" />

---

3. **How many Gratuitous ARP replies were observed for `192.168.10.1`?**
* **How to find it:** Filter with `arp.isgratuitous && arp.src.proto_ipv4 == 192.168.10.1` (or check the gratuitous ARP replies related to `192.168.10.1`) and count the matches.
* **Answer:** `2`

<img width="1203" height="855" alt="2 gratuitous" src="https://github.com/user-attachments/assets/7133b2dd-ec8e-40af-8dc8-f8bc27629b22" />

--- 

4. **How many unique MAC addresses claimed the same IP (`192.168.10.1`)?**
* **How to find it:** Check all distinct `eth.src` / MAC addresses appearing for `192.168.10.1` with `arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1` (usually the legitimate router + attacker = 2, but always check directly in the capture).
* **Answer:** `2`

<img width="1190" height="358" alt="ip unique mac 2" src="https://github.com/user-attachments/assets/30438d97-cc5a-4030-85ff-f9103a3dab63" />

---

5. **How many ARP spoofing packets were observed in total from the attacker?**
* **How to find it:** Filter the ARP packets from the attacker's MAC address:
`arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1 && eth.src == 02:fe:fe:fe:55:55`
* **Answer:** `14`

<img width="1202" height="863" alt="arp spoofing attacker 14" src="https://github.com/user-attachments/assets/9fca5c46-55a5-478c-b607-ecb89a30deaa" />

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective

* **Why is `CTRL + ALT + 1` mentioned in the Wireshark note?**
In some environments and Wireshark/virtual machine settings, shortcuts for changing time format or display can help with faster orientation along the time axis, especially when you want to see how quickly gratuitous ARP packets arrive in a row (bursts / cache poisoning frequency).
* **How are networks defended against ARP Spoofing in reality?**
At the switch infrastructure level, DAI (Dynamic ARP Inspection) is used in combination with DHCP Snooping, and on critical servers/hosts static ARP entries are used where appropriate. From a SOC perspective, when you see duplicate IP-to-MAC and massive `is-at` packets, that is a strong sign that someone is trying to become man-in-the-middle on the local segment right before potential DNS spoofing or session interception.

---

## Task 5: Unmasking DNS Spoofing

### 🎯 1. What Is DNS and How Does DNS Spoofing Work?

* **DNS Protocol Simplified:** Think of DNS like the contacts list in your phone. You don't memorize your friends' phone numbers; you just tap on their names. Similarly, DNS translates human-readable website names (like `[www.google.com](https://www.google.com)`) into computer-readable IP addresses.
* **DNS Spoofing (or DNS Cache Poisoning):** Occurs when an attacker corrupts this system and gives your computer the wrong "phone number" for the website you want to visit.
1. **How it works as a MITM technique (step-by-step):**
* **The Victim:** The victim tries to visit their bank at `my-real-bank.com`.
* **The Attacker:** The attacker, already on the local network (perhaps via ARP spoofing), intercepts the victim's DNS request.
* **The Spoof:** The attacker quickly sends a fake DNS response to the victim. This fake response says `my-real-bank.com` is located at their IP address: `ATTACKER_IP`.
* **The Interception:** The victim's computer trusts this fake response and stores it in its DNS cache. When the victim tries to connect to the bank, they unknowingly connect directly to the attacker's server, which can host a replica of the real banking page.




* **Result:** The attacker is now in the middle and captures everything the victim enters, including username and password.

### 🔍 2. Indicators of the Attack

When investigating logs or network traffic for a potential MITM attack with DNS spoofing, look for these key indicators:

* **Multiple DNS responses for the same query:** A legitimate resolver and a forged responder answer the same query. This is the single most reliable indicator.
* **DNS response from an unexpected source:** A DNS response arrives from an IP address that does not match any configured resolver (such as `8.8.8.8` or your DNS server).
* **Suspiciously short TTL (Time-To-Live) values:** Attackers use very low TTL values (`1 - 30s`) so poisoned entries remain short-lived and reassert control.
* **Unsolicited DNS responses:** A DNS response appears without a corresponding DNS request from the victim.

### 🦈 3. Network Analysis in Wireshark (`network-traffic.pcap`)

Open `network-traffic.pcap` from the `mitm_traffic` folder on the `Desktop`. The steps and filters from the task are:

* **Narrowing down DNS traffic:**
`dns`
*(Contains both DNS requests and corresponding responses).*
* **Filtering legitimate traffic (from `8.8.8.8`):**
`dns.flags.response == 1 && ip.src == 8.8.8.8`
*(Displays valid DNS responses from the legitimate DNS server).*
* **Examining DNS responses:**
`dns.flags.response==1`
*(Looks for responses from IP addresses different from the usual DNS server; one DNS response from an IP different from `8.8.8.8` is noticed).*
* **DNS response from the DNS server (`8.8.8.8`):**
`dns.flags.response == 1 && ip.src == 8.8.8.8`
* **DNS traffic for the target domain (`corp-login.acme-corp.local`):**
`dns && dns.qry.name == "corp-login.acme-corp.local"`
* **DNS response for the target domain originating from the DNS server (`8.8.8.8`):**
`dns.flags.response == 1 && ip.src == 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"`
*(Looks normal, as expected).*
* **DNS responses from servers other than `8.8.8.8` for the target domain:**
`dns.flags.response == 1 && ip.src != 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"`
*(This is what DNS spoofing looks like — it shows that a system in the network is acting as a fake/unauthorized DNS server and sending fake DNS responses).*

### 📋 4. Analysis Summary

The analysis in the previous two tasks shows:

* A successful multi-phase Man-in-the-Middle attack where the attacker first poisoned the ARP mapping for the gateway (`192.168.10.1`),
* Followed by fake DNS responses for `corp-login.acme-corp.local` that redirected the victim to the attacker's IP address.
* **Teaser for the next step:** Understanding why SSL stripping is used and finding evidence of SSL stripping that resulted in stealing the victim's data.

### ✅ 5. How to Get the Exact Answers to Questions in Task 5

1. **How many DNS responses were observed for the domain `corp-login.acme-corp.local`?**
* **How to find it:** Apply the filter `dns.flags.response == 1 && dns.qry.name == "corp-login.acme-corp.local"` in Wireshark and check the number of displayed packets (`Displayed`).
* **Answer:** `211`

<img width="1147" height="858" alt="211 responses" src="https://github.com/user-attachments/assets/7ae68724-89e9-4e12-94bc-1e3adf077af3" />

---

2. **How many DNS requests were observed from the IPs other than `8.8.8.8`?**
* **How to find it:** Pay attention to the wording in the question for DNS queries (where `dns.flags.response == 1`) from IP addresses other than `8.8.8.8` — use `dns.flags.response == 1 && ip.src != 8.8.8.8`.
* **Answer:** `2`

<img width="1152" height="853" alt="2 not 8888 bez query easyway" src="https://github.com/user-attachments/assets/968346a7-d8b7-4a9c-a6dd-fcd1d4575c42" />

---

* We can also reach the answer with this filter:
`dns.flags.response == 1 && ip.src != 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"`

<img width="1150" height="849" alt="2 request not 8 8 8 8 " src="https://github.com/user-attachments/assets/411d272a-5b48-439f-91c5-cf2755ff6681" />

---

3. **What IP did the attacker’s forged DNS response return for the domain?**
* **How to find it:** Apply the filter `dns.flags.response == 1 && ip.src != 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"`, select the fake DNS response, and in the bottom panel under `Domain Name System (response)` → `Answers` look at the `Address` field (the IP address returned for the domain).
* **Answer:** `192.168.10.55`

<img width="1157" height="851" alt="forged IP response" src="https://github.com/user-attachments/assets/745d5f9e-db98-4712-af5a-6344b238372d" />

---

### 🔥 EXTRA FROM ME: SOC & Pentester Perspective

* **Why is the "race condition" key in classic DNS Spoofing?**
When a victim sends a DNS request, the attacker tries to get their fake packet there faster than the legitimate response from the real DNS server (like `8.8.8.8`). The first validly formatted response that the resolver/client receives and accepts for that Transaction ID and port often wins and gets cached. That is why, when you see two responses for the same request (one from `8.8.8.8`, and another from a local/unexpected host), that is a textbook signal for DNS spoofing.
* **Why do attackers use a low TTL (`1 - 30s`)?**
If an attacker sets a high TTL (for example `86400` seconds = 24 hours), and gets an IP address wrong or changes infrastructure, the victim remains stuck on that entry for a long time. With a low TTL, the attacker can easily refresh or change the redirection target during the attack.

---

## Task 6: Spotting SSL Stripping in Action

### 🎯 1. What Is SSL Stripping and How Does It Work?

* **Definition:** SSL stripping is a Man-in-the-Middle technique in which the attacker intercepts and modifies communication to remove or prevent TLS encryption between the client and server. This allows the client to communicate over HTTP instead of HTTPS.
1. **How it works (step-by-step):**
* The victim initiates an HTTPS request to a certain website.
* The attacker intercepts the request using ARP spoofing or a rogue access point.
* The attacker connects to the website over a secure HTTPS channel, but relays the response to the victim over unencrypted HTTP.
* The victim unknowingly communicates over HTTP, exposing all sensitive data in plaintext.




* **End result:** The attacker maintains the secure HTTPS session with the server, while transmitting plain HTTP to the victim, enabling eavesdropping and credential theft.

### 🔍 2. Indicators of SSL Stripping

When investigating traffic, we look for:

* **Initial Request vs. Response:** The user's initial request may be for HTTPS (port `443`), but subsequent packets immediately switch to unencrypted HTTP (port `80`) for the same domain.
* **Redirects / Link Rewriting:** Redirects (HTTP status codes `301`, `302`) that repeatedly redirect an HTTPS request to an HTTP resource.
* **Certificate Errors:** Although the attacker usually tries to hide this, the initial TLS/SSL Handshake may fail or display a self-signed certificate if the attacker uses a more direct proxying technique.

### 🦈 3. Network Analysis in Wireshark (`network-traffic.pcap`)

We continue the analysis in `network-traffic.pcap` (located in the `mitm_traffic` folder on the `Desktop`).

1. **Narrowing down SSL/TLS traffic:**
`tls || ssl`
*(Displays all traffic related to SSL/TLS).*
2. **Checking TLS Handshake to the server:**
`tls.handshake.type == 1 && tls.handshake.extensions_server_name == "corp-login.acme-corp.local"`
*(Confirms that the domain name normally uses TLS for communication).*
3. **DNS redirection preceding SSL Stripping:**
`dns.flags.response == 1 && ip.src == 192.168.10.55 && dns.qry.name == "corp-login.acme-corp.local"`
*(Shows two incidents in which the attacker's IP address `192.168.10.55` sent fake DNS responses for the target domain).*
4. **Confirmation that TLS disappears (HTTP communication victim ↔ attacker):**
`http && ip.src == 192.168.10.10 && ip.dst == 192.168.10.55`
*(Clearly shows that the victim `192.168.10.10` connected to the attacker `192.168.10.55` after SSL stripping and performed a login in cleartext).*

### 📋 4. Complete Summary & Attack Timeline

* **ARP Spoofing (cache poisoning):** ARP poisoning begins; the attacker sends unsolicited ARP `is-at` claiming to have the gateway's IP address.
* **DNS Spoofing (forged DNS responses):**
* The victim sends a DNS request for `corp-login.acme-corp.local`.
* The attacker returns a fake DNS response mapping the domain to the attacker's IP address (`192.168.10.55`).


* **SSL Stripping (TLS downgrade / credential capture):**
* The victim initiates a connection to the resolved IP address over HTTP to the attacker (effect of SSL stripping).
* A POST request with credentials in cleartext is observed.



### ✅ 5. How to Get the Exact Answers to Questions in Task 6

1. **How many POST requests were observed for our domain `corp-login.acme-corp.local`?**
* **Filter in Wireshark:**
`http.request.method == "POST" && http.host == "corp-login.acme-corp.local"`
*(or `http && ip.src == 192.168.10.10 && ip.dst == 192.168.10.55` for connections to `192.168.10.55`). Check the number of displayed packets in the bottom bar (`Displayed`).
* **Answer:** `1`

<img width="1186" height="844" alt="post 1 " src="https://github.com/user-attachments/assets/7687d47f-50e7-4152-aa3e-a660b733efbe" />

---

2. **What's the password of the victim found in the plaintext after successful ssl stripping attack?**
* **Method in Wireshark:**
1. Apply the filter `http.request.method == "POST"`.
We can also use the previous filter:
`http && ip.src == 192.168.10.10 && ip.dst == 192.168.10.55`
2. Select the POST packet sent by the victim (`192.168.10.10` to `192.168.10.55`), right-click → `Follow` → `HTTP Stream` (or expand the `HTML Form URL Encoded` panel at the bottom of the packet).
3. Inside the parameter bodies you will see `username` and the `password` field exposed in cleartext.


* **Answer:** `Secret123!`

<img width="1187" height="855" alt="Secret123! pass" src="https://github.com/user-attachments/assets/fae760b2-1fd8-4d74-ae5b-c9d6dba28333" />


---

### 🔥 EXTRA FROM ME: SOC & Mitigation Perspective

* **How does HSTS (HTTP Strict Transport Security) prevent SSL Stripping?**
Without HSTS, the victim's browser accepts downgrading to `http://` if the attacker intercepts the HTTP redirect. When a domain has an HSTS header enabled (`Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`), the browser refuses to establish any unencrypted HTTP connection for that domain and automatically forces HTTPS before a packet is even sent onto the network.
* **SOC Trick for quick triage on SSL Stripping:**
When on a login portal (`corp-login...`) in network logs HTTP POST requests suddenly appear on port `80` (instead of HTTPS on port `443`), preceded by earlier ARP/DNS anomalies, that is irrefutable proof that the entire session is compromised via MITM.

---

## Task 7: Conclusion & Room Wrap-up

### 📌 1. Room Summary and Additional Practice Sources

* In this room we explored some common man-in-the-middle attacks linked in a chain within one scenario and how to identify the footprints in network traffic.
* Similar logs are located in the `mitm_network logs` folder and are already ingested into Splunk for additional practice.
* If you like this room, it is recommended to also check out the **Data Exfiltration Detection** room, which examines and identifies exfiltration attempts (written as "filtration attempts" in the question text) across multiple network channels.

### 🧩 2. Key Concepts Covered in the Room

* **ARP Spoofing detection:** Detected by finding duplicate MAC addresses for different IP addresses in the ARP cache (`arp -a`) or by spotting them in Wireshark.
* **DNS Spoofing:** Uncovered by finding multiple, conflicting DNS responses.
* **SSL Stripping:** Uncovered by finding sensitive data, such as passwords, being sent in plaintext over HTTP to webpages that should be secure.

---

### 🔥 EXTRA FROM ME: SOC & Incident Response Takeaway

* **Why is correlation between ARP, DNS, and HTTP/TLS so powerful during MITM triage?**
If you see only one isolated event (for example only an `arp -a` change or only one DNS response), it can sometimes look ambiguous. When you connect the fake ARP response for the gateway, the conflicting DNS response leading to a local/attacker host, and an unencrypted HTTP POST with credentials to a domain that normally uses TLS, you get a complete incident story with high confidence for escalation and isolation.
* **How to use the exercise with `mitm_network logs` in Splunk?**
Try to find and reconstruct the same chain (ARP → DNS → HTTP/credentials) using SPL searches in Splunk — that way you practice log-based hunting as well, not just packet analysis in Wireshark.
