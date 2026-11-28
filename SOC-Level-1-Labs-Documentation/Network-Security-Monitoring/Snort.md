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
 c 
 
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

## Task 7: Operation Mode 3: IDS/IPS

### 📌 Introduction to Task 7: Snort in IDS/IPS Mode
In IDS/IPS mode, Snort does not only sniff or log packets — it analyzes traffic based on defined rules and configuration, generates alerts (such as an `alert` file or on screen), and in IPS mode can actively drop/block packets.

In this section the focus is on operational modes and parameters, while more detailed paths/variables are summarized in `TASK-10`, configuration checking builds on `TASK-3/4`, and rule writing is covered in `TASK-9 & 10`. When working in IDS/IPS mode, logging and sniffing are semi-passive, but additional options like `-i`, `-v`, `-d`, `-e`, `-X`, `-l`, and `-K ASCII` can be activated as needed.

### ⚙️ Parameters for IDS/IPS Mode and Their Meaning

| Parameter | Description and Practical Use |
| :--- | :--- |
| **`-c`** | Defines the configuration file (for example `-c /etc/snort/snort.conf`) or directly a rule file (for example `-c /etc/snort/rules/local.rules`). |
| **`-T`** | Tests the configuration file to check for errors in settings (combined with `-c`). |
| **`-N`** | Disables logging to files, while other selected functions (such as `-v` or `-X` on console) remain active. |
| **`-D`** | Starts Snort in the background (background / daemon mode), suitable for automation and scripts when the configuration is stable. |
| **`-A`** | Selects the alert mode (`console`, `cmg`, `full`, `fast`, `none`). If you use `-A` without specifying a mode, the default is `full`. |

### 🚨 Alert Modes with Parameter `-A`
Different `-A` options affect whether alerts are seen on the console and what format they have in logs:
- **`console`:** Displays alerts in `fast` style directly on the screen in the console (`-A console`).
- **`cmg`:** Displays basic header details together with payload in hex and text format on the console (`-A cmg`).
- **`fast`:** Displays alert message, timestamp, and source/destination IP addresses with ports — no console output, the difference is seen in the log/alert file (`-A fast`).
- **`full`:** Displays maximally detailed information about alerts — no console output, details are seen in the alert file (`-A full`).
- **`none`:** Turns off alerts and does not create an `alert` file, but can still log traffic in binary dump format (`-A none`).

### 🧪 Examples of Using Parameters and Processes

#### 1. Testing configuration (`-c` and `-T`)
```bash
sudo snort -c /etc/snort/snort.conf -T
```
Checks the configuration for possible errors before starting active monitoring.

#### 2. Without logging (`-N`) and background / daemon mode (`-D`)

* **Without writing logs:**
`sudo snort -c /etc/snort/snort.conf -N`
No logs will be created in the log folder, but if you add `-v` or `-X`, you will still see output in the terminal.
* **In the background (daemon):**
`sudo snort -c /etc/snort/snort.conf -D`
Snort detaches as a daemon process (for example `2898`) and gives no console output — with `-l` (and possibly `-v` / `-X`) logs go to the log directory.
* **Checking if the process is running:**
`ps -ef | grep snort`
* **Stopping the process by PID:**
`sudo kill -9 <PID>`



#### 3. Starting only with a rule file (without `snort.conf`)

`sudo snort -c /etc/snort/rules/local.rules -A console`
Useful for quickly testing your own rule (for example `alert icmp any any <> any any (msg: "ICMP Packet Found"; sid: 100001; rev:1;)`), but with lower performance compared to a full configuration.

#### 4. IPS mode and dropping packets (`-Q --daq afpacket`)

For active IPS mode that blocks/drops packets, at least two network interfaces are needed (for example `eth0:eth1`) and `-Q --daq afpacket` is used (along with `-q` for quieter output if needed):
`sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A console`
In the output the `[Drop]` tag is then seen when a rule hits the traffic.

### 🎯 Practical Exercise for `TASK-7 Exercise` and Questions

Answer the questions below
Investigate the traffic with the default configuration file.
`sudo snort -c /etc/snort/snort.conf -A full -l .`
Execute the traffic generator script and choose "TASK-7 Exercise". Wait until the traffic stops, then stop the Snort instance. Now analyse the output summary and answer the question.
`sudo ./traffic-generator.sh`

What is the number of the detected HTTP GET methods?

* **Answer:** `2`

<img width="1111" height="827" alt="Get method 2" src="https://github.com/user-attachments/assets/b02e1f63-07cf-4fd6-a6f2-df7380c9bb95" />

---

1. **Enter the `TASK-7` folder and start Snort with `-l .`:**
Navigate to `~/Desktop/Task-Exercises/Exercise-Files/TASK-7` and start Snort using that folder for log output:
`cd ~/Desktop/Task-Exercises/Exercise-Files/TASK-7`
`sudo snort -c /etc/snort/snort.conf -A full -l .`
*Verification:* The prompt is in `.../Exercise-Files/TASK-7` and Snort prints `Commencing packet processing`.
2. **Run `traffic-generator.sh` and choose `TASK-7 Exercise`:**
In another terminal run the script from `~/Desktop/Task-Exercises` and choose `TASK-7 Exercise`:
`cd ~/Desktop/Task-Exercises`
`sudo ./traffic-generator.sh`
Once the generator finishes, return to the terminal with Snort and press `Ctrl + C`.
*Verification:* Snort prints `Snort exiting` and below it prints the final summary / statistics from the processed traffic.
3. **Check the summary on screen or the contents of `snort.log.*`:**
Look at the summary output on screen after Snort finishes (or check the created `snort.log.*` file in `TASK-7` with `sudo snort -r snort.log.* -X | grep -c "GET"`).
*Verification:* In the summary or in the output from `snort.log.*` the specific count of HTTP GET requests (different from `0`) is seen.

---

### 🔥 Extra: Pentester, SOC, and DFIR Perspective

* **Pentester perspective:** Knowing the difference between `console`/`cmg` (immediately visible payload/alert in terminal) and `fast`/`full`/`none` (writing to files without console output) helps when testing whether traffic triggers alerts and how the corresponding packets look.
* **SOC perspective:** In a real environment, `full` gives the most context for alert triage, while `none` or poorly tuned rules can mean missed visibility or too many false positives — that is why `-T` and checking the configuration before production are mandatory.
* **DFIR perspective:** Combining the alert file and binary packet log (`-l`) allows the analyst to first quickly see the hit rules and timestamps, and then return to the raw packets for a full payload check.

---

## Task 8: Operation Mode 4: PCAP Investigation

### 📌 Introduction to Task 8: Investigating PCAP Files with Snort

Packet capture (PCAP) is a networking practice involving the interception of data packets travelling over a network. Once the packets are captured, they can be stored by IT teams for further analysis. The inspection of these packets allows IT teams to identify issues and solve network problems affecting daily operations.

In PCAP mode, Snort reads already recorded packet files (`.pcap`) instead of listening live on a network interface. If you run only `snort -r icmp-test.pcap` without configuration and additional options, you will get only a basic overview and packet statistics. The real power of this mode is when you combine it with configuration/rules (`-c`) and output parameters to quickly uncover known threat patterns and prepare to write your own rules.

### ⚙️ Parameters for PCAP Mode and Their Role

| Parameter | Description and Practical Use |
| --- | --- |
| **`-r` / `--pcap-single=**` | Reads a single `.pcap` file (for example `-r icmp-test.pcap`). |
| **`--pcap-list=""`** | Reads multiple `.pcap` files listed in quotes and separated by a space (for example `--pcap-list="icmp-test.pcap http2.pcap"`). |
| **`--pcap-show`** | Displays the name of the current `.pcap` file on the console while Snort processes the packets — very useful when processing multiple files at once so you know which file the alerts come from. |

### 🧪 Examples from Text: Single PCAP vs. Multiple PCAP Files

#### 1. Single PCAP file with configuration (`-r`)

`sudo snort -c /etc/snort/snort.conf -q -r icmp-test.pcap -A console -n 10`
Here `-c` loads the configuration, `-q` reduces the standard banner/quiet part of the output, `-r` reads `icmp-test.pcap`, `-A console` prints alerts directly to the console, and `-n 10` stops after the first 10 packets. If rules hit traffic (like `ICMP Packet found`), you immediately see the alerts on screen.

#### 2. Multiple PCAP files without `--pcap-show` (`--pcap-list`)

`sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console -n 10`
When processing multiple `.pcap` files without `--pcap-show`, all alerts line up in the same stream and it is harder to immediately separate which file triggered which alert.

#### 3. Multiple PCAP files with `--pcap-show`

`sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console --pcap-show`
With `--pcap-show`, Snort prints `Reading network traffic from "..." with snaplen = ...` before each file, making it easy to distinguish results per file.

### 🛠️ Steps for the Exercise in `Task-Exercises/Exercise-Files/TASK-8`

1. **Navigate to the `TASK-8` directory and check the files:**
Open a terminal and go to the folder with the exercises for Task 8:
`cd ~/Desktop/Task-Exercises/Exercise-Files/TASK-8`
`ls -la`
*Verification:* In the list `mx-1.pcap`, `mx-2.pcap`, and `mx-3.pcap` are seen.
2. **Run the commands with `snort.conf` and `snortv2.conf` over the PCAP files:**
Run the given commands for `mx-1.pcap`, `mx-2.pcap`, and the combination `mx-2.pcap mx-3.pcap`:
`sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-1.pcap`
`sudo snort -c /etc/snort/snortv2.conf -A full -l . -r mx-1.pcap`
`sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-2.pcap`
`sudo snort -c /etc/snort/snort.conf -A full -l . --pcap-list="mx-2.pcap mx-3.pcap"`
*Verification:* Each command finishes with output/summary in the console and writes logs/alerts into the current folder (`-l .`).
3. **Read the values from the summary on screen for each question:**
For each question from Task 8, look at the summary printed by Snort at the end of the corresponding command:
* **Number of generated alerts** (in the `Alerts` / `Action Stats` section of the summary):
`sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-1.pcap`
* **Answer:** `170`

<img width="1043" height="758" alt="alert 170 t-8" src="https://github.com/user-attachments/assets/e420124e-38eb-4167-bd3c-1ea9d65d529c" />

---

* **Number of TCP Segments Queued** (in the `Stream5` / TCP preprocessor section of the summary):
With the same command from the previous question, just scroll down.
* **Answer:** `18`

<img width="1001" height="758" alt="18 segments quaded" src="https://github.com/user-attachments/assets/b5f33847-5a74-4047-b08b-d24642b6abb9" />

---

* **Number of extracted HTTP response headers** (in the `HttpInspect` / HTTP preprocessor section of the summary):
With the same command from the first question, just scroll down.
* **Answer:** `3`

<img width="1064" height="749" alt="http response " src="https://github.com/user-attachments/assets/1708130d-6f83-4872-a4bd-c04ee0eca888" />

---

* Investigate the mx-1.pcap file with the second configuration file.
`sudo snort -c /etc/snort/snortv2.conf -A full -l . -r mx-1.pcap`
What is the number of the generated alerts?
* **Answer:** `68`

<img width="1065" height="758" alt="2 alerts 68 answ" src="https://github.com/user-attachments/assets/55b7f71d-7531-4e10-9107-e1ef3424b7e5" />

---

* Investigate the mx-2.pcap file with the default configuration file.
`sudo snort -c /etc/snort/snort.conf -A full -l . -r mx-2.pcap`
What is the number of the generated alerts?
* **Answer:** `340`

<img width="1127" height="773" alt="3 alert 370 answ" src="https://github.com/user-attachments/assets/01edc929-a49d-4393-a6d2-63190df404d1" />

---

* Keep reading the output. What is the number of the detected TCP packets?
* **Answer:** `82`

<img width="1001" height="720" alt="TCp packet 82" src="https://github.com/user-attachments/assets/de318f7f-81a5-4abe-971d-2a1540c50289" />

---

* Investigate the mx-2.pcap and mx-3.pcap files with the default configuration file.
`sudo snort -c /etc/snort/snort.conf -A full -l . --pcap-list="mx-2.pcap mx-3.pcap"`
What is the number of the generated alerts?
* **Answer:** `1020`

<img width="1020" height="747" alt="alerts 1020 last q " src="https://github.com/user-attachments/assets/b5d249f8-d314-4304-b7b5-bbbc48c3a09e" />

---

### 🔥 Extra: Pentester, SOC, and DFIR Perspective

* **Pentester perspective:** Testing recorded `.pcap` files with different configuration files (`snort.conf` versus `snortv2.conf`) shows how changing rules or preprocessors directly changes how many and what kind of alerts are raised for the exact same traffic.
* **SOC perspective:** When analyzing suspicious traffic, Snort's summary (number of TCP packets, queued segments, extracted HTTP headers, and number of alerts) helps quickly estimate what type of communication is in the capture and whether different rule profiles yield more or fewer detections.
* **DFIR perspective:** Working with `.pcap` files enables repeatable and safe forensic analysis of the capture without altering the original evidence material, while `--pcap-show` helps clearly document which file in the series contributed to a given finding.

---

## Task 9: Snort Rule Structure

### 📌 Introduction to Task 9: Architecture and Structure of Snort Rules

Understanding the format of Snort rules is fundamental for every Blue and Purple Teamer. By default, Snort runs in passive mode (IDS). To enable attack prevention (IPS), inline mode is required, but first you must master the syntax and structure of rules.

Every Snort rule consists of two main parts:

1. **Rule Header:** Defines the action, protocol, IP addresses, ports, and traffic direction.
2. **Rule Options:** Optional, yet crucial part in parentheses `(...)` for precisely detecting sophisticated attacks.

Rule syntax:
`action protocol source_ip source_port direction destination_ip destination_port (options)`

<img width="1161" height="745" alt="alert structure rule" src="https://github.com/user-attachments/assets/11ecc8a2-1a5c-4e06-bbab-81f5d5d30df1" />

---

### ⚙️ Analysis of Rule Elements

#### 1. Actions

* **`alert`:** Generates an alert and logs the packet.
* **`log`:** Writes the packet to a log without a specific alert.
* **`drop`:** Blocks and logs the packet (IPS mode).
* **`reject`:** Blocks the packet, logs it, and terminates the session (sends TCP RST or ICMP unreachable).

#### 2. Protocols

Snort 2 supports four basic protocols in the header:

* `IP`, `TCP`, `UDP`, `ICMP`.
*Note:* For detecting application protocols (such as HTTP, FTP, SSH), the corresponding protocol in the header is filtered via port (for example, FTP via TCP port `21`).

### 🌐 Filtering IP Addresses and Ports

#### IP Filtering & Negation Operator (`!`)

* **Single IP:** `alert icmp 192.168.1.56 any <> any any (...)`
* **Range / Subnet:** `alert icmp 192.168.1.0/24 any <> any any (...)`
* **Multiple subnets:** `alert icmp [192.168.1.0/24, 10.1.1.0/24] any <> any any (...)`
* **Negation (exclusion):** `alert icmp !192.168.1.0/24 any <> any any (...)` (all addresses except that subnet).

#### Port Filtering

* **Specific port:** `alert tcp any any <> any 21 (...)`
* **Excluding port:** `alert tcp any any <> any !21 (...)`
* **Range from-to:** `alert tcp any any <> any 1:1024 (...)` (ports from `1` to `1024`).
* **Range from 0:** `alert tcp any any <> any :1024 (...)` (ports from `0` to `1024`).
* **Range above:** `alert tcp any any <> any 1025: (...)` (ports higher than or equal to `1025`).
* **List of ports:** `alert tcp any any <> any [21,23] (...)` (ports `21` and `23`).

#### Direction

* `>`: Traffic from source (left) to destination (right).
* `<>`: Bidirectional traffic.
* *(Operator `<-` does not exist in Snort!)*

### 📐 Rule Options

Rule Options are divided into three basic categories:

#### 1. General Options

* **`msg`:** Text message printed when the rule triggers.
* **`sid` (Snort ID):** Unique identifier for the rule:
* `< 100`: Reserved rules.
* `100 - 999,999`: Built-in rules in Snort.
* `>= 1,000,000`: User-created rules (our rules must have SID greater than or equal to `1,000,000`).


* **`reference`:** Links or CVE ID for additional context (e.g., `reference:cve,CVE-XXXX`).
* **`rev` (Revision):** Rule version. Whenever an analyst modifies/optimizes the rule, they must increment `rev` by `1`.

#### 2. Payload Options

* **`content`:** Looks for a specific string in the payload in ASCII or HEX format.
* ASCII: `content:"GET";`
* HEX: `content:"|47 45 54|";`


* **`nocase`:** Disables case sensitivity (Case-insensitive).
* **`fast_pattern`:** Instructs Snort which content part to use for initial detection to speed up searching when there are multiple `content` options.

#### 3. Non-Payload Options

* **`id`:** Filtering by the IP ID field from the header (e.g., `id:35369;`).
* **`flags`:** Filtering by TCP flags (`S` = SYN, `F` = FIN, `R` = RST, `P` = PSH, `A` = ACK, `U` = URG).
* For example: `flags:S;` for SYN packets or `flags:PA;` for Push-Ack packets.


* **`dsize`:** Filters payload size (`dsize:100<>300;` or `dsize:>100`).
* **`sameip`:** Checks whether source and destination IP addresses are identical.

### 🛠️ Practical Tasks from Task 9 (Step-by-Step)

For all questions, work in the directory: `~/Desktop/Task-Exercises/Exercise-Files/TASK-9`
The rule file you will work with in this folder is `local.rules`. For editing you can use:
`gedit local.rules`
*(or `nano local.rules` / `mousepad local.rules`)*

#### ❓ Question 1

Use "task9.pcap". Write a rule to filter IP ID "35369" and run it against the given pcap file. What is the request name of the detected packet?

1. **Open `local.rules` and write the rule:**
In `local.rules` delete or comment out (`#`) old rules and add:
`alert ip any any <> any any (msg:"IP ID Found"; id:35369; sid:1000011; rev:1;)`
*Verification:* Save the `local.rules` file.
2. **Run Snort in full alert mode:**
In the terminal run:
`sudo snort -c local.rules -A full -l . -r task9.pcap`
*Verification:* The `alert` file is generated in the current folder.
3. **Read the request name from the alert or with `snort -X`:**
Open the `alert` file with `cat alert` or read the raw packet.
* **Answer:** `TIMESTAMP REQUEST`

<img width="1031" height="719" alt="TIMESTAMP REQUEST" src="https://github.com/user-attachments/assets/612a342f-104d-427a-b545-adef5db33e21" />

---

#### ❓ Question 2

Clear the previous alert file and comment out the old rules. Create a rule to filter packets with Syn flag and run it against the given pcap file. What is the number of detected packets?

1. **Clear the `alert` file and update `local.rules`:**
Delete the previous alert: `rm alert`
In `local.rules` put the rule:
`alert tcp any any <> any any (msg:"SYN Flag"; flags:S; sid:1000002; rev:1;)`
*Verification:* Save the change in `local.rules`.
2. **Run Snort:**
`sudo snort -c local.rules -A full -l . -r task9.pcap`
*Verification:* In the summary at the end or by counting alerts see how many packets are detected.
3. **Count the alerts:**
* **Answer:** `1`

<img width="987" height="702" alt="1 alert S flag " src="https://github.com/user-attachments/assets/600452a5-77a4-42e4-be84-e6b04a89d3d7" />

---

#### ❓ Question 3

Clear the previous alert file and comment out the old rules. Write a rule to filter packets with Push-Ack flags and run it against the given pcap file. What is the number of detected packets?

1. **Clear the alerts and enter the rule for PSH-ACK:**
Delete: `rm -f alert snort.log.*`
In `local.rules` enter:
`alert tcp any any <> any any (msg:"Push-Ack Flags"; flags:PA; sid:1000003; rev:1;)`
*Verification:* The rule uses `flags:PA;`.
2. **Run the detection:**
`sudo snort -c local.rules -A full -l . -r task9.pcap`
*Verification:* Snort processes `task9.pcap`.
3. **Count the detected packets:**
* **Answer:** `216`
We can also check with the command: `grep -c "1:1000003:" alert`

<img width="1079" height="761" alt="216 PA " src="https://github.com/user-attachments/assets/86db6e27-0bc7-4216-ac5e-bd6cf6a69827" />

---

#### ❓ Question 4

Clear the previous alert file and comment out the old rules. Create a rule to filter UDP packets with the same source and destination IP and run it against the given pcap file. What is the number of packets that show the same source and destination address?

1. **Clear the alerts and enter the rule with `sameip` for UDP:**
Delete: `rm -f alert snort.log.*`
In `local.rules` enter:
`alert udp any any <> any any (msg:"Same IP UDP"; sameip; sid:1000004; rev:1;)`
*Verification:* The rule specifically filters `udp` and uses the `sameip;` option.
2. **Run Snort:**
`sudo snort -c local.rules -A full -l . -r task9.pcap`
*Verification:* Read the alert count.
3. **Check the exact number:**
Run: `grep -c "1000004" alert`
* **Answer:** `7`

<img width="1033" height="728" alt="7 alert sameip" src="https://github.com/user-attachments/assets/400cfd00-692e-4966-a0e9-fa9ef8b620ad" />

---

#### ❓ Question 5

Case Example - An analyst modified an existing rule successfully. Which rule option must the analyst change after the implementation?

* **Answer:** `rev` (or `revision`)
* **Explanation:** Whenever a rule is modified or improved, the analyst must manually increment the value of the `rev` option to track the rule's change history.

---

### 🔥 Extra: SOC, Pentester, and DFIR Perspective

* **SOC Perspective:** Writing precise rules with `flags`, `id`, or `dsize` allows quick isolation of known techniques (such as SYN Floods or Land Attacks where source/dest IP are identical `sameip`). Precision prevents flooding with False Positives.
* **Pentester / Red Team Perspective:** Understanding how Snort filters flags (`flags:PA`, `flags:S`) and payload contents helps Red Teamers design Evasion Techniques, such as packet fragmentation or modifying TCP flags in their own exploit scripts.
* **DFIR Perspective:** During deep analysis of saved traffic (pcap), forensic investigators write temporary ad-hoc rules with a unique SID (above `1,000,000`) to extract all packets related to a specific artifact (such as a specific IP ID or characteristic flag).

---

## Task 10: Snort2 Operation Logic: Points to Remember

### 📌 Introduction to Task 10: Internal Logic and Configuration of Snort 2

In Task 10 everything you have gone through so far for Snort 2 comes together — from how packet processing flows internally, to how rules, network ranges, DAQ modules, and outputs are configured in the main configuration file `/etc/snort/snort.conf` and in user `local.rules`.

Because `snort.conf` is a long "all-in-one" configuration file, it is always better to edit the configuration carefully or with appropriate rule-updating tools instead of blindly overwriting the entire file, so as not to cause errors, crashes, or loss of functionality.

### 🧩 Main Components of Snort 2

Every packet entering Snort passes through a clear architectural flow across five main components:

* **Packet Decoder:** Collects traffic and prepares packets for further processing by preprocessors.
* **Pre-processors:** Orders and modifies/normalizes packets (for example reassembling fragments or HTTP normalization) so the Detection Engine can analyze them properly.
* **Detection Engine:** The main component that processes, parses, and analyzes packets applying active rules.
* **Logging and Alerting:** Generates log records and creates alerts when a rule triggers.
* **Outputs and Plugins:** Provides integration with output modules (for example sending alerts to `syslog` or MySQL) and support for additional plugins for rule management and detection.

### 📜 Types of Rules in Snort and Managing Them

There are three main types of rules for Snort:

* **Community Rules:** Free rule set under the GPLv2 license, publicly available without registration.
* **Registered Rules:** Free rule set requiring registration and containing subscriber rules with a 30-day delay.
* **Subscriber Rules (Paid):** Paid rule set with subscription; represents the main set and is updated twice a week (on Tuesday and Thursday).

After installing Snort 2, the needed directories and files are created automatically, but if you use Community or paid Subscriber rules, you must include them appropriately in `snort.conf`. The main two files you most often work with are `snort.conf` (main configuration) and `local.rules` (custom rules).

### ⚙️ Key Sections in `/etc/snort/snort.conf`

To open the main configuration file, you can use:
`sudo gedit /etc/snort/snort.conf`
*(Always make a backup copy before major changes and test the configuration with `sudo snort -c /etc/snort/snort.conf -T`).*

#### Step #1: Set the network variables

This section defines the range of the network you protect and the paths to rules:

| Tag | Role | Example Value |
| --- | --- | --- |
| **`HOME_NET`** | The network/hosts you protect. | `any` or `192.168.1.1/24` |
| **`EXTERNAL_NET`** | The external network relative to the target. | `any` or `!$HOME_NET` |
| **`RULE_PATH`** | Base path to rules. | `/etc/snort/rules` |
| **`SO_RULE_PATH`** | Path to shared object rules (accompanying Registered and Subscriber rules). | `$RULE_PATH/so_rules` |
| **`PREPROC_RULE_PATH`** | Path to preprocessor rules (accompanying Registered and Subscriber rules). | `$RULE_PATH/plugin_rules` |

#### Step #2: Configure the decoder

Here IPS mode and basic decoder parameters are configured. For a single-node IPS installation `afpacket` works best.

| Tag | Role | Example Value |
| --- | --- | --- |
| **`config daq`** | Selecting DAQ module (for example for IPS mode). | `afpacket` |
| **`config daq_mode`** | Activating inline mode. | `inline` |
| **`config logdir`** | Default directory for logs. | `/var/logs/snort` |

Data Acquisition library, for packet I/O. The DAQ replaces direct calls to libpcap functions with an abstraction layer that facilitates operation on a variety of hardware and software interfaces without requiring changes to Snort.

#### Step #6: Configure output plugins

Manages outputs from IDS/IPS actions (log and alert formats). By default a lot of information goes to the console, so good tuning of this section makes work more efficient.

#### Step #7: Customise your ruleset

Here local and downloaded rule files are included. The `#` sign is a commenting operator — to activate a certain line, you must uncomment it (remove `#` before it):

* Local rules: `include $RULE_PATH/local.rules`
* Additional/downloaded rules: `include $RULE_PATH/<rule_file_name>`

### 🔌 Data Acquisition Modules (DAQ) in Snort

DAQ modules are specialized packet input/output (I/O) libraries that provide flexibility when choosing type and mode of operation:

Data Acquisition library, for packet I/O. The DAQ replaces direct calls to libpcap functions with an abstraction layer that facilitates operation on a variety of hardware and software interfaces without requiring changes to Snort.

* **`PCAP`:** Standard mode (known as Sniffer mode).
* **`Afpacket`:** Inline mode (known as IPS mode; most popular for inline/IPS alongside standard `pcap`).
* **`Ipq`:** Inline mode on Linux via Netfilter (replaces the older `snort_inline` patched approach).
* **`Nfq`:** Inline mode on Linux (Netfilter Queue).
* **`Ipfw`:** Inline mode on OpenBSD and FreeBSD via divert sockets with `pf` and `ipfw` firewalls.
* **`Dump`:** Test mode for inline processing and normalization.

### ✅ Quick Workflow for Checking and Activating Configuration Changes

1. **Check the variables and rules in `snort.conf`:**
Open `/etc/snort/snort.conf` and check that `HOME_NET`, `EXTERNAL_NET`, rule paths, and `include $RULE_PATH/local.rules` are properly set and uncommented where needed.
*Verification:* The lines you want active do not have `#` at the beginning and point to valid paths.
2. **Test the configuration with `-T`:**
Before starting active IDS/IPS monitoring or analysis, check for syntax or configuration errors:
`sudo snort -c /etc/snort/snort.conf -T`
*Verification:* The command finishes with confirmation that the configuration was successfully validated without fatal errors.
3. **Start Snort in the appropriate IDS or IPS mode:**
Run Snort with the chosen mode (for example standard IDS or `afpacket` inline mode) and check logs in `/var/logs/snort` or in the chosen log directory.
*Verification:* The process runs stably and generates expected alerts/logs according to active rules.

---

### 🔥 Extra: SOC, Pentester, and DFIR Perspective

* **SOC perspective:** Properly set `HOME_NET` and `EXTERNAL_NET` are key to knowing what is internal and what is external traffic — an incorrect `HOME_NET` easily leads to missed alerts or false positives. Regular rule updates (especially Subscriber Rules on Tuesday and Thursday) help detection stay relevant for new threats.
* **Pentester perspective:** Knowing preprocessors and normalization helps understand how defensive sensors reassemble fragmented packets or HTTP/TCP streams before comparing them with rules, as well as why some obfuscation techniques are sometimes normalized and sometimes pass through.
* **DFIR perspective:** Alignment of `config logdir`, output plugins, and proper logging allows during incident investigation to easily correlate alerts with exact log files and timestamps, without risking overwriting or misconfiguring the active sensor.

---

## Task 11: Conclusion

### 📌 Room Summary and Main Habits When Working with Snort

With Task 11 the foundation for Snort wraps up: from definition and the four main operating modes (Packet Sniffer, Packet Logger, IDS/IPS, and PCAP Investigation), through rule structure and key options, all the way to internal architecture and configuration in `snort.conf` and `local.rules`.

In the text six practical rules for good engineering practice are highlighted that are worth keeping as a constant reminder in every upcoming exercise and challenge:

* **Basics and practice before advanced options:** Master basic commands, parameters, and rule logic before moving on to complex combinations and more specific options.
* **Build rules step-by-step:** Do not write a complex rule all at once — add options one by one to more easily spot syntax errors or unexpected behavior.
* **Don't reinvent the wheel:** If a good rule or template already exists, use it, adapt it, or upgrade it instead of starting from scratch.
* **Make a backup before changes:** Always make a backup copy of configuration files before modifying settings.
* **Never delete a rule that works well:** If you don't need it at the moment, just comment it out (`#`) to preserve logic and history.
* **Test before production:** Test every new or modified rule (and configuration) in a lab / with `.pcap` or `-T` before it goes into production.
