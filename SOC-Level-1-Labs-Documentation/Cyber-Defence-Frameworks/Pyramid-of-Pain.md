# 🛡️ Pyramid of Pain (TryHackMe Walkthrough)

**Lab Title:** Pyramid of Pain  
**Category:** SOC Level 1 / Threat Intelligence & Incident Response  
**Platform:** TryHackMe  
**Status:** Completed  

---

## 📌 Executive Summary
The **Pyramid of Pain** is a conceptual model in cybersecurity created by David Bianco that ranks Indicators of Compromise (IoCs) based on how difficult and "painful" it is for an adversary to change them once detected. The model helps Security Operations Center (SOC) analysts, Threat Hunters, and Threat Intelligence teams prioritize detection engineering. The higher an indicator is on the pyramid, the more operational friction and cost it inflicts on the attacker.

---

## 🗺️ Structure of the Pyramid of Pain (Bottom to Top)
The pyramid consists of 6 distinct tiers, moving from the easiest indicators for defenders to detect (and easiest for attackers to change) at the bottom, to the most sophisticated behaviors at the top:
1. **Hash Values** (Lowest level: Trivial for attackers to change)
2. **IP Addresses** (Easy)
3. **Domain Names** (Simple)
4. **Network/Host Artifacts** (Medium / Annoying)
5. **Tools** (Hard / Challenging)
6. **TTPs — Tactics, Techniques, and Procedures** (Apex / Extremely painful for an adversary)

---

## 🔺 Tier 1: Hash Values — Trivial

### 1. Definition & Mechanics
A hash value is a fixed-length digital fingerprint of a file generated through mathematical algorithms (MD5, SHA-1, SHA-256).

* **Pain Level for the Adversary:** Trivial (Minimal).
* **Why?** If the SOC team discovers and blocks a malware sample using its SHA-256 hash, the attacker only needs 1 second to change a single bit or add an insignificant character in the code (e.g., via `echo`). This instantly generates a completely new hash that bypasses traditional signature-based scanners.

> **Note:** Tools like VirusTotal and MetaDefender Cloud allow rapid checking of hashes against global databases to see if a file has been previously flagged as malicious.

### 🕵️‍♂️ Hashing Algorithms Crucial for SOC
* **MD5:** Produces a 32-character hexadecimal string. Insecure due to Hash Collisions (two different files producing the same hash).
* **SHA-1:** Developed by the NSA; produces a 40-character string. Deprecated due to brute-force vulnerabilities.
* **SHA-256 (SHA-2 Family):** The current industry gold standard. Produces a 64-character string used globally in Cyber Threat Intelligence.

### 🥖 The Bread Recipe / Smoothie Analogy
Think of a hashing algorithm as a blender. You throw in ingredients (file data: text, image, or virus), and it blends them into a smoothie. You cannot un-blend the smoothie to get the exact original strawberries back (hashing is a one-way function). However, if you throw in the exact same ingredients down to the milligram, you always get the identical smoothie. Change just one drop, and the taste (hash) changes completely.

### 🔍 How Do We Get Hashes in Reality?
1. **Manual Generation on Local PC (PowerShell):**
   ```powershell
   Get-FileHash .\slika.jpg -Algorithm SHA256
From SOC Alerts (SIEM / EDR Systems): Automated logs record when a user downloads a suspicious file along with its hash.

From Cyber Threat Intelligence (VirusTotal): Pasting the hash into VirusTotal reveals historical analysis and original filenames.

🔑 Practical Answers for Task 3
Question: Analyse the report associated with the hash b8ef959a9176aef07fdca8705254a163b50b49a17217a4ff0107487f59d4a35d here. What is the filename of the sample?

Answer: Sales_Receipt 5606.xls (A classic malicious Excel file containing macros)

🔺 Tier 2: IP Addresses — Easy
1. Definition & Mechanics
An IP address is the network-layer identifier of the attacker's infrastructure (most commonly their Command and Control / C2 servers).

Pain Level for the Adversary: Easy.

Why? While the SOC team can instantly block the IP address on perimeter firewalls, advanced attackers easily bypass this by acquiring new IP addresses or utilizing Fast Flux infrastructure.

🌪️ Fast Flux Infrastructure
A sophisticated DNS technique where a single malicious domain is linked to a massive number of IP addresses that constantly and automatically rotate in short intervals. This complicates detection and renders simple IP blocking ineffective.

🔺 Tier 3: Domain Names — Simple
1. Definition & Mechanics
A domain name is a text address (e.g., evilcorp.com) mapped to the attacker's IP address via the DNS system.

Pain Level for the Adversary: Simple / Moderate.

Why? The attacker must spend time and money registering new domains, giving defenders a chance to track them via WHOIS databases or DNS logs.

🥷 Attacker Techniques with Domains
Punycode Attacks: Utilizing Unicode characters from other languages that visually resemble original letters (e.g., replacing i with ı) to deceive users in spearphishing attacks. Browsers translate these into xn-- formats (e.g., xn--addas-o4a.de).

URL Shorteners: Hiding the true malicious domain behind services like bit.ly or tinyurl.com.

💡 SOC Trick: You can preview the true destination of a shortened URL by adding a + sign at the end of the URL in your browser.

🔑 Practical Answers for Task 4
Question 1: Go to this report on app.any.run and provide the first suspicious domain request you are seeing...

Answer: craftingbox.in

Question 2: What term refers to an address used to access websites?

Answer: Domain Name

Question 3: What type of attack uses Unicode characters in the domain name to imitate a known domain?

Answer: Punycode attack (or Punycode)

Question 4: Provide the redirected website for the shortened URL using a preview: https://tinyurl.com/bw7t8p4u

Answer: https://tryhackme.com/

🔺 Tier 4: Host Artifacts — Annoying
1. Definition & Mechanics
Host artifacts are all the digital footprints, traces, and evidence that an adversary leaves behind on a compromised operating system.

Pain Level for the Adversary: Annoying / Medium.

Why? If the SOC team discovers these specific behavioral patterns (e.g., dropped files, registry modifications), the attacker is forced to go back, rewrite their malware code, and change their entire methodology, wasting valuable time and resources.

📂 Common Examples of Host Artifacts
Dropped Files: Secondary executables downloaded and hidden in folders like C:\Users\AppData\Local\Temp (e.g., G_jugk.exe).

Registry Values: Modifying keys in the Windows Registry to ensure persistence.

Suspicious Processes: A Word document unexpectedly spawning cmd.exe or an anomalous process like regidle.exe.

🔑 Practical Answers for Task 5
Question 1: A process named regidle.exe makes a POST request to an IP address based in the United States (US) on port 8080. What is the IP address?

Answer: 96.126.103.196

Question 2: The actor drops a malicious executable (EXE). What is the name of this executable?

Answer: G_jugk.exe

Question 3: Look at this report by VirusTotal. How many vendors determine this host to be malicious?

Answer: 9

🔺 Tier 5: Network Artifacts — Annoying
1. Definition & Mechanics
Network artifacts are specific traces, text strings, or patterns generated by malicious software while communicating across computer networks (cables or Wi-Fi).

Pain Level for the Adversary: Annoying / Medium.

Why? If the SOC team detects and blocks these specific network signatures, the attacker must interrupt their operation and reconfigure the network code of their malware.

🌐 Key Examples & Tools
User-Agent Strings: Hidden text strings sent in HTTP headers identifying client software. Attackers often use custom or rare User-Agent strings for their C2 malware (e.g., in Emotet), which may contain typos or match outdated browsers.

URI Patterns: Suspicious paths in web requests during HTTP POST requests (e.g., /pcap/upload.php).

Detection Tools: Packet analyzers like Wireshark or TShark, alongside Intrusion Detection Systems (IDS) like Snort.

🔑 Practical Answers for Task 6
Question 1: Which network indicator helped us identify the malware type (Emotet)?

Answer: User-Agent

Question 2: How many POST requests are in the screenshot from the PCAP file?

Answer: 6

🔺 Tier 6: Tools — Challenging
1. Definition & Mechanics
At this level, defense focuses on identifying and blocking the actual software utilities and command programs used by the attacker (e.g., custom stealers, password crackers, C2 backdoors, or specific network scanners).

Pain Level for the Adversary: Challenging / Hard.

Why? The attacker must completely discard the tool they invested time and money into, write or acquire a brand-new tool architecture, or undergo retraining to learn new software. Many attackers give up here and look for easier targets.

🛡️ Advanced SOC Detection Weapons for Tools
YARA Rules: Specific rules based on text or binary patterns helping identify and classify malware families.

Fuzzy Hashing (SSDeep): Measures similarity between files by breaking them into smaller chunks (Context Triggered Piecewise Hashes). Unlike SHA-256, a fuzzy hash can detect if a new file is a sophisticated variation/modification of a known malware family, even if the attacker modified parts of the code.

🔺 Tier 7 (Apex): TTPs — Tough (Maximum Pain)
1. Definition & Mechanics
TTPs stand for Tactics, Techniques, and Procedures. At the absolute peak of the pyramid, defenders no longer block hashes or tools; instead, they recognize and block the attacker's actual mindset, strategy, and behavioral pattern (as cataloged in the MITRE ATT&CK Matrix).

Pain Level for the Adversary: Tough / Extremely Painful.

Why? If the SOC team detects and blocks the attacker's core technique (e.g., how Cobalt Strike communicates across the network), the attacker is forced to abort operations. Changing an entire operational procedure requires massive time, financial resources, and retraining.

📊 Real-World Example (Chimera Group)
The Chinese APT group Chimera utilizes commercial software like Cobalt Strike for C2 communication and data exfiltration. Blocking the behavioral patterns of this tool neutralizes the group regardless of what IP addresses or infrastructure they spin up.

🔑 Key Concepts & Definitions
Pass-the-Hash: An attack where an adversary steals password hashes from memory rather than plaintext passwords. Instead of brute-forcing the password, they pass the hash to authenticate across other systems in the network.

MITRE ATT&CK: A massive, publicly accessible digital encyclopedia (matrix) documenting all known adversary tactics and techniques used in real-world attacks.

C2 Beacons: Automated, periodic heartbeat signals sent by malware back to an attacker's server (e.g., every 10 seconds) to report status and request instructions: "I am alive, waiting for commands."

Data Exfiltration: The final stage where an adversary compresses valuable enterprise data (into .zip or .rar archives) and secretly transfers it out of the organization to their own server for extortion.

🧩 Practical Mapping Summary (TryHackMe Alignment)
TTPs ➡️ The attacker's plans and objectives.

Tools ➡️ The attacker has utilised these to accomplish their objective.

Hash Values ➡️ These signatures can be used to attribute payloads and artefacts to an actor.

Domain Names ➡️ An attacker has purchased this and used it in a typo-squatting campaign.

IP Addresses ➡️ These addresses can be used to identify the infrastructure an attacker is using for their campaign.

Network / Host Artifacts ➡️ These artefacts can present themselves as C2 traffic for example.

🏁 Conclusion: As David Bianco stated, the amount of pain inflicted on an adversary directly correlates with the tier of indicators defenders can identify and block. As a SOC analyst, the ultimate goal is moving past trivial hashes toward detecting behavioral TTPs using frameworks like MITRE ATT&CK.
