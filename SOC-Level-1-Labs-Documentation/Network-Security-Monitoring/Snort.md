# Snort 🐷

Learn how to use Snort to detect real-time threats, analyse recorded traffic files and identify anomalies.

## Task 1: Introduction

### 🎯 1. Room Overview and What Is SNORT?
- **Goal of the room:** To learn how to use Snort for real-time threat detection, analysis of recorded traffic files, and identifying anomalies.
- **Definition of SNORT:** Open-source, rule-based Network Intrusion Detection and Prevention System (NIDS/NIPS).
- **Authors and maintenance:** Developed and still maintained by Martin Roesch, open-source contributors, and the Cisco Talos team.

Official description:
"Snort is the foremost Open Source Intrusion Prevention System (IPS) in the world. Snort IPS uses a series of rules that help define malicious network activity and uses those rules to find packets that match against them and generate alerts for users." *(Translated: Snort is the leading Open Source Intrusion Prevention System (IPS) in the world. Snort IPS uses a series of rules that help define malicious network activity and uses those rules to find packets that match against them and generate alerts for users).*

### 🎓 2. Learning Objectives & Prerequisites

#### 🔹 Learning Objectives
- Overview of IDS / IPS.
- Snort Overview.
- Writing IDS rules.
- Detecting Intrusion with Snort.

#### 🔹 Prerequisites
- You are expected to be familiar with basic Linux command line functionalities (such as general system navigation) and networking fundamentals (ports, protocols, and traffic data).
- The room aims to encourage you to start working with Snort for live traffic and captured traffic analysis.
- Before joining this room, it is recommended to check out:
  - Network Fundamentals Module
  - Linux Fundamentals rooms (1, 2, 3)

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why does the official Snort description emphasize the IPS aspect, while in the previous room we talked about IDS?**
  In practice Snort can work both as a passive IDS (only detects, logs, and alerts) and as an IPS (when placed inline in the traffic path and able to actively drop/block packets matching a rule). For a SOC analyst and detection engineer it is crucial to know in which mode the sensor is deployed: if it is only alert/log, the traffic still passed through and it must be checked whether it had an impact.

---

## Task 2: Interactive Material and VM

### 🚀 1. Starting the Virtual Machine (Lab Machine)
- The machine attached to this task is started by clicking `Start Lab Machine` (currently shown with status `Lab machine Status:Off`).
- Once ready, it will be visible in split-screen view. If you don't see a loaded lab machine, click `Show Split View`.
- To successfully complete this room, you need to set up the virtual environment: start the lab machine and make sure you have the needed tools and access for the upcoming challenges.

### 📁 2. Structure of Materials: Task-Exercises
- Once the machine fully starts, on the `Desktop` you will see a folder named `Task-Exercises`.
- Each exercise has a separate folder and files — use them according to the questions.
- Everything you need is located under the `Task-Exercises` folder.
- Two subfolders are available:
  - `Config-Sample`: Sample configuration files and rule files. These files are provided to show what configuration files look like. The installed Snort instance does not use them, so feel free to practice and modify them. The original core Snort files are located under the `/etc/snort` folder.
  - `Exercise-Files`: Has separate folders for each task. Each folder contains pcap, log, and rule files prepared for work/testing.

### 🚦 3. Traffic Generator (`traffic-generator.sh`)
- The machine is offline, but there is a script `traffic-generator.sh` to generate traffic to your Snort interface.
- You will use this script to trigger traffic to the Snort interface.
- Once you start the script, it will ask you to choose the exercise type and then automatically open another terminal to display the output of the chosen action.
- **Note:** Each traffic flow is designed for a specific exercise. Make sure to first start the Snort instance and wait until the end of the script's execution. Do not stop the traffic flow unless you choose the wrong exercise.
- Started with `sudo`:
  `sudo ./traffic-generator.sh`
- Once you choose an action, the menu disappears and a terminal instance opens to display its output.

### ✅ 4. Question in Task 2
- Navigate to the Task-Exercises folder and run the command "./.easy.sh" and write the output
  1. **How you do this on the machine:** Go to the `Task-Exercises` folder on the `Desktop` (for example with `cd ~/Desktop/Task-Exercises`).
  2. Run the hidden script `./.easy.sh` (for example `./.easy.sh` or `sudo ./.easy.sh` if it requires privileges).
  3. Read and write/insert the output returned by the script into the answer.

**Answer:** `Too Easy!`

<img width="1184" height="691" alt="too easy!" src="https://github.com/user-attachments/assets/946dac81-33f3-4d09-a223-000b738f9d7c" />

---

### 🔥 EXTRA FROM ME: SOC & Lab Workflow Perspective
1. **Why is it great to have a separate `Config-Sample` and originals in `/etc/snort`?**
   In practice when learning or testing new rules and options, it is great to have a "sandbox" for experimenting so you don't break the main configuration while exploring how the engine reacts. And if you make a mistake in the sample files, you always have a clean reference.
2. **Why is it important that Snort is started first before `traffic-generator.sh`?**
   Because `traffic-generator.sh` sends traffic to your interface in real time — if Snort is not yet listening on that interface, packets will pass through without being monitored and alerted at that moment. So the order is: start Snort → run `traffic-generator.sh` → wait for it to finish.

---

## Task 3: Introduction to IDS/IPS

### 🔍 1. Overview of IDS and IPS
- You can configure your network infrastructure to use both IDS and IPS, but before you start using either of them, it is important to learn the differences.

#### 🔹 Intrusion Detection System (IDS)
- An IDS is a passive monitoring solution for detecting possible malicious activities/patterns, abnormal incidents, and policy violations.
- It generates alerts for every suspicious event.
- There are two main types of IDS systems:
  - **Network Intrusion Detection System (NIDS):** Monitors traffic flow from different areas of the network. The goal is to inspect traffic across the entire subnet. If a signature is identified, an alert is created.
  - **Host-based Intrusion Detection System (HIDS):** Monitors traffic flow from a single endpoint device. Its goal is to inspect traffic on that device. If a signature is identified, an alert is created.

#### 🔹 Intrusion Prevention System (IPS)
Intrusion Prevention System (IPS) is a device or application that detects and stops intrusions attempts proactively. They are usually deployed in front of the protected asset and block any potential threat from reaching their target.
- An IPS is an active protecting solution for preventing possible malicious activities/patterns, abnormal incidents, and policy violations.
- It is responsible for stopping/preventing/terminating a suspicious event as soon as it is detected.
- There are four main types of IPS systems:
  - **Network Intrusion Prevention System (NIPS):** Monitors traffic flow from different areas of the network. The goal is to protect traffic across the entire subnet. If a signature is identified, the connection is terminated.
  - **Behaviour-based Intrusion Prevention System (Network Behaviour Analysis — NBA):** Behavior-based systems monitor traffic flow from different areas of the network in order to protect traffic across the entire subnet. If an anomaly is identified, the connection is terminated.
    - A Network Behaviour Analysis System works similarly to NIPS. The difference between NIPS and behavior-based systems is that behavior-based systems require a training period, also known as "baselining", to learn normal traffic and distinguish malicious traffic and threats.
    - This model gives more effective results against new and unknown threats.
    - The system trains to know "normal" in order to detect "abnormal". The training period is crucial to avoid false positives. If there is a security breach during the training period, the results will be very problematic. Another critical point is ensuring the system is well trained to recognize benign activities.
  - **Wireless Intrusion Prevention System (WIPS):** Monitors traffic flow from a wireless network. Its goal is to protect wireless traffic and stop possible attacks launched from there. If a signature is identified, the connection is terminated.
  - **Host-based Intrusion Prevention System (HIPS):** Actively protects traffic flow from a single endpoint device. The goal is to inspect traffic on a specific device. If a signature is identified, the connection is terminated.
    - The working mechanism of HIPS is similar to that of HIDS. The difference is that while HIDS creates alerts for threats, HIPS stops them by terminating the connection.

### ⚙️ 2. Detection/Prevention Techniques
There are three main detection and prevention techniques used in IDS and IPS solutions:
- **Signature-Based:** Relies on rules that identify specific patterns of known malicious behavior. This model helps detect known threats.
- **Behaviour-Based:** Identifies new threats with new patterns passing through signatures. The model compares known/normal behaviors with unknown/abnormal behaviors, helping detect previously unknown or new threats.
- **Policy-Based:** Compares detected activities against system configuration and security policies. This model helps detect policy violations.

### 📌 3. Summary on IDS vs. IPS and Role of Snort

#### 🔹 Summary
- An IDS can identify threats, but requires user assistance to stop them.
- An IPS can identify and block threats with less user assistance at the moment of detection.

#### 🔹 Snort Official Description, Capabilities, and Modes
The rest of Snort's official description:
"Snort can be deployed in-line to stop these packets, as well. Snort has three primary uses: As a packet sniffer like tcpdump, as a packet logger, which is useful for network traffic debugging or can be used as a full-blown network intrusion prevention system. Snort can be downloaded and configured for personal and business use alike."

- **SNORT** is an open-source, rule-based Network Intrusion Detection and Prevention System (NIDS/NIPS), developed and maintained by Martin Roesch, open-source contributors, and the Cisco Talos team.
- **Capabilities of Snort:**
  - Live traffic analysis.
  - Attack and probe detection.
  - Packet logging.
  - Protocol analysis.
  - Real-time alerting.
  - Modules & plugins.
  - Pre-processors.
  - Cross-platform support! (Linux and Windows).
- **Snort Modes:**
  - **Sniffer Mode:** Reading and displaying IP packets in the console application.
  - **Packet Logger Mode:** Logging all IP packets (incoming and outgoing) visiting the network.
  - **NIDS (Network Intrusion Detection System) and NIPS (Network Intrusion Prevention System) Modes:** Logging/dropping packets considered malicious according to user-defined rules.

### ✅ 4. Questions and Exact Answers for Task 3
- **Which IDS or IPS type can help you stop the threats on a local machine?**
  - **Exact Answer:** `HIPS`
  - **Explanation:** A type that stops threats (so IPS) on a local machine / single endpoint device is requested — which is HIPS (Host-based Intrusion Prevention System).
- **Which IDS or IPS type can help you detect threats on a local network?**
  - **Exact Answer:** `NIDS`
  - **Explanation:** A type that detects threats (so IDS) on a local network / entire subnet is requested — which is NIDS (Network Intrusion Detection System).
- **Which IDS or IPS type can help you detect the threats on a local machine?**
  - **Exact Answer:** `HIDS`
  - **Explanation:** A type that detects threats (IDS) on a local machine / single endpoint device is requested — which is HIDS (Host-based Intrusion Detection System).
- **Which IDS or IPS type can help you stop the threats on a local network?**
  - **Exact Answer:** `NIPS`
  - **Explanation:** A type that stops threats (IPS) on a local network / entire subnet is requested — which is NIPS (Network Intrusion Prevention System).
- **Which described solution works by detecting anomalies in the network?**
  - **Exact Answer:** `Network Behaviour Analysis` = `NBA`
  - **Explanation:** Network Behaviour Analysis (NBA) monitors network traffic from different areas of the network across an entire subnet and terminates the connection when it identifies an anomaly.
- **According to the official description of the snort, what kind of NIPS is it?**
  - **Exact Answer:** `Full-blown` (in the official description: "for can be used as a full-blown network intrusion prevention system."). Pay attention that the first letter is a capital letter! `Full-blown`
- **NBA training period is also known as ...**
  - **Exact Answer:** `baselining`

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why is a "poisoned baseline" (a breach during the training period) dangerous in NBA / anomaly-based systems?**
  If an attacker is already inside the network while the model learns "normal behavior", the system may accept malicious or abnormal traffic as the baseline and later not treat it as an anomaly. Therefore, in real practice baselining is done in a controlled/clean state and with regular review by analysts.
- **Why are pre-processors and modules/plugins in Snort so important before rule matching itself?**
  Attackers often try to evade simple signature matching (for example via packet fragmentation, normalization of HTTP/TCP streams, etc.). Pre-processors and modules prepare and normalize traffic so rules can recognize actual activity more accurately.

---

## Task 4: First Interaction with Snort

### 🔍 1. Checking Version and Validating Configuration

#### 🔹 Checking installed version (`snort -V`)
- First we check whether Snort is installed and view the version of the instance:
  `snort -V`
- In the example from the text, the Snort banner is shown with version `2.9.7.0 GRE (Build XXXXXX)`, the authors (Martin Roesch & The Snort Team), copyrights (Cisco, Sourcefire), as well as the libraries used (`libpcap`, `PCRE`, `ZLIB`).

#### 🔹 Testing the configuration file (`-T` and `-c`)
- Before starting practical work, we must ensure the configuration file is valid.
- Here `-T` is used for testing the configuration (self-test), and `-c` identifies the configuration file (for example `snort.conf`).
- **Note:** It is possible to use an additional configuration file by pointing to it via `-c`.
- Example for checking configuration:
  `sudo snort -c /etc/snort/snort.conf -T`
- In the output, initialization of Output Plugins, Preprocessors, Plug-ins, Rules Engine (`SF_SNORT_DETECTION_ENGINE`), and Preprocessor Objects (such as `SF_SSH`, `SF_DNS`, `SF_FTPTELNET`, `SF_SSLPP`, `SF_MODBUS`, `SF_DNP3`, etc.) is seen, ending with `Snort successfully validated the configuration!` and `Snort exiting`.

### 🧠 2. Role of the Configuration File and Quiet Mode (`-q`)
- Once we use a configuration file, Snort has much greater power — the configuration file is an all-in-one file for managing Snort.
- In it are identified Rules, plugins, detection engines, default actions, and output settings.
- Multiple configuration files are possible for different purposes and cases, but during a single execution (at runtime) we can use only one.
- **Note:** Snort automatically displays the default banner and initial information about your setup when you start it. You can prevent that by using the `-q` parameter.

### 📋 3. Overview of Parameters from the Task

| Parameter | Description |
| :--- | :--- |
| **`-V` / `--version`** | This parameter provides information about the version of your instance. |
| **`-c`** | Identifies the configuration file. |
| **`-T`** | Snort's self-test parameter allows you to test your setup with this parameter. |
| **`-q`** | Quiet mode prevents Snort from displaying the default banner and initial information about your setup. |

### ✅ 4. Questions and How to Get the Answers in Task 4

1. **Run the Snort instance and check the build number. (Format: `___`)**
   - **How you find it:** Run `snort -V` in a terminal and look in the line where `Version ... (Build ...)` appears. The value in parentheses after `Build` is the build number of your instance.
   - **Answer:** `149`

<img width="1113" height="599" alt="build num 149" src="https://github.com/user-attachments/assets/057fabfa-57c4-4695-80d0-a61b03d638cd" />

---

2. **Test the current instance with `"/etc/snort/snort.conf"` file and check how many rules are loaded with the current build.**
   - **How you find it:** Run `sudo snort -c /etc/snort/snort.conf -T`
   - **Answer:** `4151`

<img width="1124" height="814" alt="rule num " src="https://github.com/user-attachments/assets/970cb733-ffde-4d28-8c40-10d79e76f9b1" />

---

3. **Test the current instance with `"/etc/snort/snortv2.conf"` file and check how many rules are loaded with the current build.**
   - **How you find it:** Run `sudo snort -c /etc/snort/snortv2.conf -T` and check the number of loaded rules in the output.
   - **Answer:** `1`

<img width="1124" height="854" alt="1 snort rule" src="https://github.com/user-attachments/assets/705efcc6-69a1-4388-a707-a6eaed29050a" />

---

### 🔥 EXTRA FROM ME: SOC & Detection Engineering Perspective
- **Why is `snort -T` the first command run every time you change a rule or configuration?**
  One small syntax error (a missing semicolon `;`, wrong character, or invalid option) can cause Snort not to start in production or skip part of the configuration. With `-T` you immediately validate without wasting time and without risking an unclean instance.
- **Why are a large number of preprocessors loaded in `snort.conf` (such as `SF_SSH`, `SF_DNS`, `SF_FTPTELNET`, `SF_SSLPP`, `SF_MODBUS`, `SF_DNP3`)?**
  Each protocol has its own specificities and possible evasion techniques or anomalies (for example OT/ICS protocols like MODBUS and DNP3, or network protocols like DNS and SSH). Preprocessors help Snort properly understand and reconstruct those streams before applying rules.

---

## Task 5: Operation Mode 1: Sniffer Mode

### 🎯 1. Overview of Sniffer Mode and Parameters
- Similar to `tcpdump`, Snort has multiple flags that allow viewing different data about the packet it captures.
- Sniffer mode parameters are explained in the following table:

| Parameter | Description |
| :--- | :--- |
| **`-v`** | Verbose. Displays TCP/IP output in the console. |
| **`-d`** | Displays packet data (payload). |
| **`-e`** | Displays link-layer (TCP/IP/UDP/ICMP) headers. |
| **`-X`** | Displays full packet details in HEX. |
| **`-i`** | This parameter helps define a specific network interface to listen or sniff on. If you have multiple interfaces, you can choose a specific interface for sniffing. |

### 🧪 2. Practical Analysis of Parameters
Snort needs active traffic on your interface to work. To generate traffic, you use the `traffic-generator.sh` script in the `Task-Exercises` folder.

#### 🔹 1. Sniffing with parameter `-i`
- Starting the Snort instance in verbose mode (`-v`) on a specific interface (`-i eth0`):
  `sudo snort -v -i eth0`
- If you have only one interface, Snort uses it by default. The example above demonstrates sniffing on the interface named `eth0`. When you simulate parameter `-v`, you will notice that it automatically uses and displays the `eth0` interface.

#### 🔹 2. Sniffing with parameter `-v`
- Starting in verbose mode:
  `sudo snort -v`
- In another terminal `traffic-generator.sh` is run with `sudo` and ICMP/HTTP traffic is started.
- Snort starts displaying packets in verbosity mode (for example DNS queries `192.168.175.129:34316 -> 192.168.175.2:53` with `TTL`, `TOS`, `ID`, `IpLen`, `DgmLen`, `Len`).
- As seen in the output, verbosity mode provides output information similar to `tcpdump`. Once we stop sniffing with `CTRL+C`, Snort stops and summarizes the sniffed packets.

#### 🔹 3. Sniffing with parameter `-d`
- Starting in packet data payload printing mode:
  `sudo snort -d`
- After running traffic via `traffic-generator.sh`, Snort displays headers plus the packet payload itself.
- For example, for a DNS query the textual/hexadecimal payload is also seen: `... 06 67 6F 6F 67 6C 65 03 63 6F 6D ...` (`google.com`).
- This mode covers verbose mode and provides additional data.

#### 🔹 4. Sniffing with combination `-de`
- Starting in payload mode (`-d`) and link-layer header capture (`-e`):
  `sudo snort -d -e`
- Besides IP/UDP information and the payload, this mode also displays MAC addresses and frame type (for example `00:0C:29:A5:B7:A2 -> 00:50:56:E1:9B:9D type:0x800 len:0x46`).

#### 🔹 5. Sniffing with parameter `-X`
- Starting in full packet dump mode:
  `sudo snort -X`
- Displays the entire packet in HEX and ASCII view from the very start of the offset (`0x0000: ...`), including all network layers, HTTP requests (for example `M-SEARCH * HTTP/1.1`, `HOST: 239.255.255.250:1900`, `USER-AGENT: Chromium...`) or ICMP Echo Reply.

### 📌 3. Ways of Combining Parameters
Parameters can be used in both combined and separate forms:
- `snort -v`
- `snort -vd`
- `snort -de`
- `snort -v -d -e`
- `snort -X`

Make sure to understand and practice each parameter with different types of traffic to discover your favorite combination.

---

### 🔥 EXTRA FROM ME: SOC & DFIR Perspective on Sniffer Flags
- **When do we use `-v`, when `-d`, and when `-X` in reality?**
  - `-v` (Verbose): Fastest check to see "is traffic arriving at all" (IP/ports) without overloading the terminal.
  - `-d` (Payload): When you want to quickly see what is in the application layer (for example HTTP queries, DNS names, or usernames in unencrypted protocols).
  - `-e` (Link-layer): Ideal when investigating Layer 2 incidents (for example ARP Spoofing, MAC address conflicts, or VLAN tags).
  - `-X` (Full Hex Dump): Most powerful when looking for a specific byte offset, hidden bytes, malformed traffic headers, or strange encodings where plain ASCII doesn't show all bytes.
- **Why does Sniffer mode NOT use Snort rules for alerting?**
  Remember: In Sniffer mode Snort works exclusively as a passive traffic viewer (similar to `tcpdump`). Here it has not loaded rules (`snort.conf` / `snort.lua`), so it does not generate IDS alerts, only prints what passes through the interface.

  ---

## Task 6: Operation Mode 2: Packet Logger Mode

In Packet Logger Mode, Snort not only monitors packets on the network interface, but also writes them to logs for later analysis. By default, without an additional format, logs are saved as a single binary file in `tcpdump` format in `/var/log/snort`, unless with a parameter you specify another directory or format.

### ⚙️ Parameters for Packet Logger and Reading Logs

| Parameter | Description and Practical Use |
| :--- | :--- |
| **`-l`** | Activates Logger mode and sets target directory for logs and alerts (default `/var/log/snort`). With `-l .` logs are written in the current directory. Default format is binary (`tcpdump`). |
| **`-K ASCII`** | Writes packets in readable ASCII format, organized into folders by IP addresses and files by protocols/ports (for example `ICMP_ECHO`, `UDP:...`). |
| **`-r`** | Reading option reads an already recorded log file (for example `snort.log.1640048004`) and supports filtering with BPF (Berkeley Packet Filters) and options like `-X` for payload display. |
| **`-n`** | Limits how many packets are processed or read (for example `-n 10` stops after the first 10 packets). |

### 🔐 Logfile Ownership and Permissions
Because sniffing network traffic on an interface requires administrative (`root`) privileges, running Snort with `sudo` means the generated log files and directories will be owned by `root`.

To be able to read and analyze them, you have two main approaches:
- **Privilege escalation:** Running reading/analysis commands with `sudo` or as `root`.
- **Changing ownership (`chown`):** Transferring ownership to your user with `sudo chown username file` or recursively for an entire directory with `sudo chown username -R directory`.

### 🧪 Default Format (`-l`) vs. ASCII Format (`-K ASCII`)

#### 1. Binary / `tcpdump` format (`sudo snort -dev -l .`)
- Creates one comprehensive binary file (for example `snort.log.1638459842` or in the exercise `snort.log.1640048004`).
- Not directly readable with a plain text editor, instead read and analyzed with Snort (`-r`), `tcpdump`, or Wireshark.
- This format can be read with `snort -r` and filtered with BPF expressions.

#### 2. ASCII format (`sudo snort -dev -K ASCII -l .`)
- Writes packets in readable text (ASCII), divided into subdirectories and files according to IP addresses and traffic type (for example in a folder with an IP address files like `ICMP_ECHO` or `UDP:36648-53` appear).
- Easily opened with a text editor without needing a separate packet analyzer, but Snort (`-r`) is not intended to read such ASCII logs the way it reads binary `tcpdump` logs.

### 📖 Reading and Filtering Logs with `-r`, `-n`, and BPF
With the `-r` parameter you can view Snort's binary logs and combine them with Berkeley Packet Filters (BPF) and `-n`:
- `sudo snort -r snort.log.1640048004 -n 10` reads only the first 10 packets from the log.
- `Snort -dvr logname.log -n 10` (or with `sudo`) displays data (`-d`), verbose level (`-v`), reads from log (`-r`), and stops after 10 packets (`-n 10`).
- `sudo snort -r logname.log -X` displays both hexadecimal and ASCII packet content.
- `sudo snort -r logname.log icmp` / `tcp` / `'udp and port 53'` filters only a specific protocol or port using BPF.
- `sudo tcpdump -r snort.log.1638459842 -ntc 10` alternative view of the same binary log via `tcpdump` without DNS resolution (`-n`), without timestamp (`-t`), and with a limit of 10 packets (`-c 10`).

### 🎯 Practical Exercise in `Task-Exercises/Exercise-Files/TASK-6`
In this folder the full flow is practiced:
1. Start Snort in ASCII logger mode (`sudo snort -dev -K ASCII -l .`), run `sudo ./traffic-generator.sh`, and choose the `TASK-6 Exercise` option.
2. Once traffic generation finishes, stop Snort and inspect the created folders (for example `145.254.160.237`) to see the source port that communicated with port `53` (DNS).
3. Then with `snort -r snort.log.1640048004` (and appropriate options like `-n 10` or `-X`) check the packet details in the `snort.log.1640048004` file:
   - IP ID of the 10th packet (read from the IP header in the output).
   - Referer of the 4th packet (HTTP header `Referer:` which can be seen when displaying the packet content/payload).
   - Ack number of the 8th packet (read from the TCP header in the output).
   - Number of packets with TCP port `80` (can be checked by filtering by `tcp port 80` and viewing the counter/packets in the output).

### Answers to Questions:

#### Question 1:
Investigate the traffic with the default configuration file with ASCII mode.
`sudo snort -dev -K ASCII -l .`
Execute the traffic generator script and choose "TASK-6 Exercise". Wait until the traffic ends, then stop the Snort instance. Now analyse the output summary and answer the question.
`sudo ./traffic-generator.sh`

🛠️ **How to run the script properly**
- Check what is in the current folder (`TASK-6`):
  `ls -la`
- In `TASK-6` `snort.log.1640048004` is usually located (for exercises with `-r`), while `traffic-generator.sh` sits in the parent folder `/home/ubuntu/Desktop/Task-Exercises`.
- Start Snort in ASCII mode in `TASK-6`:
  `sudo snort -dev -K ASCII -l .`
- Leave this terminal active listening for traffic (or open another terminal/tab).
- Run `traffic-generator.sh` from its actual location (`~/Desktop/Task-Exercises`):
  `cd ~/Desktop/Task-Exercises`
  `sudo ./traffic-generator.sh`
- From the menu choose `TASK-6 Exercise` and wait for traffic generation to finish.
- Stop Snort (`Ctrl + C`) and check the logs in `TASK-6`:
  `cd ~/Desktop/Task-Exercises/Exercise-Files/TASK-6`
  `ls -la`
- There the created folders/logs should appear (including `145.254.160.237`), and to open folders made with `sudo`, switch to root with `sudo -i` and then do `cd`.

💡 **Quick reminder for `snort.log.1640048004`**
When you get to the questions for `snort.log.1640048004` in `~/Desktop/Task-Exercises/Exercise-Files/TASK-6`, you can read the binary log with:
- `sudo snort -r snort.log.1640048004 -n 10` = for the first 10 packets (IP ID of the 10th packet, Ack number of the 8th packet).
- `sudo snort -r snort.log.1640048004 -X` = to see ASCII/hex data like `Referer:` on the 4th packet.
- `sudo snort -r snort.log.1640048004 'tcp port 80'` = for packets on TCP port `80`.

After doing `sudo -i`, you ended up in `/root` (`~` for root is `/root`), while the file `snort.log.1640048004` is in `/home/ubuntu/Desktop/Task-Exercises/Exercise-Files/TASK-6`.

1. **Enter `TASK-6` and check the `145.254.160.237` folder:**
   Since you are already in a root session (`root@ip-10-114-130-9:~#`), go directly to `TASK-6` and open the `145.254.160.237` folder:
   `cd /home/ubuntu/Desktop/Task-Exercises/Exercise-Files/TASK-6`
   `ls -la 145.254.160.237`
   *Verification:* The files and ports/protocols inside `145.254.160.237` are displayed without `Permission denied`.

2. **Read `snort.log.1640048004` from the actual folder:**
   Once you are in `/home/ubuntu/Desktop/Task-Exercises/Exercise-Files/TASK-6`, run the command for the first 10 packets (`sudo` is not even needed when already root):
   `snort -r snort.log.1640048004 -n 10`
   *Verification:* Snort loads `snort.log.1640048004` without `No such file or directory` and prints the first 10 packets.

3. **Check the details for the remaining questions:**
4. 
   To see ASCII/HTTP details (like `Referer` on the 4th packet) and packets on TCP port `80`:
   ```bash
   snort -r snort.log.1640048004 -X -n 10
   snort -r snort.log.1640048004 'tcp port 80'
   
*Verification: In the output, packet contents and packets filtered on tcp port 80 are seen.*

Now, you should have the logs in the current directory. Navigate to folder "145.254.160.237". What is the source port used to connect port 53?

**Answer:** `3009`

<img width="1360" height="842" alt="q 1 port 53 snort log packet" src="https://github.com/user-attachments/assets/66faf9f0-cd76-43ef-b1a8-108e4b4b3de1" />

---

#### Question 2:
Use snort.log.1640048004
Read the snort.log file with Snort; what is the IP ID of the 10th packet? snort -r snort.log.1640048004 -n 10

**Answer:** `49313`

<img width="1386" height="861" alt="ID num packet " src="https://github.com/user-attachments/assets/ed9316d3-b251-4e2a-8736-c3cfa1078445" />

---

In Snort in reading mode (snort -r snort.log.1640048004 -n 10), packet headers are often not printed in the standard short format if you don't add options for detailed display of IP/TCP/payload headers (such as -v, -d, -e, or -X), or packets from the very beginning don't have a separate ID field (for example some packets in the log or when the display format doesn't show all fields). Therefore, the simplest way is to start with -vd or -X to see the full output of the first 10 packets and find the 10th block/packet.

- Run snort -r with detailed display (-vd or -X): 

- In the terminal (in the directory where snort.log.1640048004 is located) run: `snort -r snort.log.1640048004 -vd -n 10`

or to also see hexadecimal/ASCII data: `snort -r snort.log.1640048004 -X -n 10`

*Verification: Longer blocks for the packets with headers are printed in the terminal instead of just one line.*

-Count the packets from 1 to 10 and check the 10th block:

-Each packet in the output starts with a timestamp and IP addresses (for example -> or =+=+=+ separator between packets). Count to the 10th packet and in its IP header look for ID: (for example ID:12345).

*Verification: You see the 10th packet in order in the output and check whether ID:... appears in its IP line.*

If the 10th packet has no ID field in the header, check with tcpdump or change the option:

If in Snort's output for the 10th packet ID: does not appear at all, you can check the same file with tcpdump (with detailed header -vv) to see if there is an id field: tcpdump -r snort.log.1640048004 -vv -c 10

Verification: tcpdump's output for the 10th packet shows detailed IP fields (like id ...), or confirms that for that packet the field is not present/displayed.

#### Question 3:
Read the "snort.log.1640048004" file with Snort; what is the referer of the 4th packet?

**Answer:** `http://www.ethereal.com/development.html`

<img width="1369" height="820" alt="referer " src="https://github.com/user-attachments/assets/87f2f671-58e6-4b63-b642-60c2f7f54626" />

---

⚡ Fastest command in terminal
In the directory where your file is located (~/Desktop/Task-Exercises/Exercise-Files/TASK-6), run the following command: `snort -r snort.log.1640048004 -X -n 4`

🔍 How to quickly read the answer in the output

Scroll to the last (4th in order) packet that Snort prints.

On the right side of the textual (ASCII) view, look for the line starting with Referer:.

#### 💡 Alternative with grep (even faster)
If you don't want to search manually in the text, you can filter the output directly with grep: snort -r snort.log.1640048004 -X -n 4 | grep -i "referer"

Verification: The command will print only the line containing Referer: http://..., so you will immediately see the full URL address without looking at the previous 3 packets.

#### Question 4
Read the "snort.log.1640048004" file with Snort; what is the Ack number of the 8th packet?
Ack number of the 8th packet: snort -r snort.log.1640048004 -n 8 (Look in the 8th packet in the line where Ack: 0x... or the decimal Ack number is located).

**Answer:** `0x38AFFFF3`

<img width="1372" height="843" alt="ack num pac 8" src="https://github.com/user-attachments/assets/7c15de6e-b14c-485c-8350-7b8fd21349a2" />

---

#### Question 5
Read the "snort.log.1640048004" file with Snort; what is the number of the "TCP port 80" packets?
Number of packets for "TCP port 80": snort -r snort.log.1640048004 'tcp port 80' | grep -i "Analyzed" (Or at the very end in the summary look at the Total / Analyzed count of filtered packets).

**Answer:** `41`

<img width="1385" height="836" alt="total paket port 80" src="https://github.com/user-attachments/assets/30cc7672-f313-43ea-948c-6abdbd774921" />

---

### 🔥 Extra: Pentester, SOC, and DFIR Perspective

Pentester perspective: When testing in a lab or network, recording traffic in binary format (-l) allows you to later open the same file in Wireshark or filter it with BPF to isolate only the relevant protocols, ports, or HTTP/DNS exchanges without missing a detail.

SOC perspective: The analyst must know when to use structured viewing and filtering (BPF / tcp port 80 / udp and port 53) to quickly find indicators — for example a suspicious Referer, an unusual source port to DNS (port 53), or unexpected HTTP traffic.

DFIR perspective: Preserving the original binary packet log (tcpdump format) is important in forensics because it keeps all raw data from headers and payload, while ASCII logs are useful for quick browsing, but do not replace the full binary capture during deep event reconstruction.
