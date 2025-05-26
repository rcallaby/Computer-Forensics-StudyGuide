# Snort Tutorial Outline  

## 1. Introduction

### Overview of Snort

#### What is Snort?

**Snort** is an open-source network intrusion detection system (NIDS) and intrusion prevention system (IPS) developed by **Martin Roesch in 1998**, and maintained by **Cisco Systems** since its acquisition of Sourcefire in 2013. Snort is designed to monitor network traffic in real time and analyze it against a set of user-defined rules to detect a variety of network-based attacks and suspicious activities.

Snort operates by performing **packet sniffing and analysis**, identifying patterns of behavior or signatures that match known threats, similar to how antivirus software scans files for malware signatures. It can be configured to operate in three main modes: **sniffer**, **packet logger**, and **network intrusion detection/prevention**.

#### Key Features and Capabilities

* **Protocol analysis and content searching/matching**
  Snort can decode and inspect the payload of packets using pattern matching and protocol analysis to identify anomalies or malicious activity.

* **Flexible rule-based language**
  Snort uses a powerful and customizable ruleset language that allows users to define how packets should be handled, including what to detect and how to respond.

* **Real-time alerting**
  When Snort detects a potential threat, it can generate real-time alerts via syslog, a database, or an alert file.

* **Preprocessor and plugin architecture**
  Snort supports modularity through preprocessors, which allow for advanced traffic inspection (e.g., normalization, protocol decoding) and additional functionality via plugins.

* **Open-source and community-supported**
  Being open-source, Snort is freely available and benefits from a large community of users and developers who contribute rules, documentation, and support.

* **Compatibility with other security tools**
  Snort integrates with tools such as **Barnyard2**, **PulledPork**, and **BASE** for log parsing, rule management, and alert visualization.

---

### Use Cases for Snort

#### Intrusion Detection System (IDS)

In IDS mode, Snort inspects network traffic passively. It analyzes packets against its rule base and logs alerts about potential intrusions without actively interfering with the traffic. This mode is ideal for monitoring and forensic analysis.

Use cases include:

* Detecting known malware signatures in HTTP, DNS, SMTP traffic.
* Identifying port scans, OS fingerprinting attempts, and brute-force login attempts.
* Monitoring insider threats or unauthorized access attempts.

#### Intrusion Prevention System (IPS)

When configured as an IPS, Snort is deployed **inline** (i.e., in the path of network traffic) and can actively drop malicious packets in real time. This transforms Snort from a passive monitor into an active defense tool.

Use cases include:

* Blocking exploit attempts (e.g., buffer overflows, SQL injection).
* Preventing command-and-control communication from compromised hosts.
* Mitigating Distributed Denial-of-Service (DDoS) attacks.

To operate in IPS mode, Snort typically works with a **netfilter/iptables** setup on Linux or uses **NFQUEUE** for packet queuing and decision-making.

#### Packet Logging and Analysis

Snort can also operate purely as a **packet logger**, where it captures and logs packets for later analysis without inspecting them for malicious patterns in real-time.

Use cases include:

* Network diagnostics and troubleshooting.
* Retrospective investigation of traffic patterns during a suspected breach.
* Academic or lab-based research into network protocols and behaviors.




## 2. Installation  
Here’s a detailed and accurate outline filled out with expert-level information about the **Snort** intrusion detection and prevention system (IDS/IPS), based on verified sources including the official [Snort documentation](https://docs.snort.org/) and trusted community best practices.

---

## **Snort: Forensic and Intrusion Detection Tool**

---

### **System Requirements**

#### **Minimum System Requirements**

The following are the general minimum requirements to run Snort efficiently:

* **CPU**: 1 GHz or faster (multi-core recommended for high traffic)
* **RAM**: 2 GB minimum (4 GB+ recommended)
* **Disk Space**: 200 MB for Snort binaries; additional space required for logs and rules
* **Operating System**: Linux (Ubuntu, CentOS, Debian, etc.) or Windows (Windows 10+, Windows Server 2016+)
* **Network Interface**: A NIC capable of promiscuous mode for packet sniffing
* **Dependencies**:

  * **DAQ** (Data Acquisition Library)
  * **Libpcap** (for packet capture)
  * **PCRE** (Perl Compatible Regular Expressions)
  * **Libdnet** (for packet manipulation)
  * **Zlib** (for compression)
  * **PulledPork** or other rule managers for automatic rule updates

---

### **Installing Snort**

#### **On Linux (Ubuntu 20.04 LTS or later)**

1. **Update System and Install Dependencies**

   ```bash
   sudo apt update && sudo apt upgrade
   sudo apt install -y build-essential libpcap-dev libpcre3-dev libdumbnet-dev bison flex zlib1g-dev liblzma-dev openssl libssl-dev
   ```

2. **Download and Install DAQ Library**

   ```bash
   cd /usr/src
   sudo wget https://www.snort.org/downloads/snort/daq-2.0.7.tar.gz
   sudo tar -xzvf daq-2.0.7.tar.gz
   cd daq-2.0.7
   ./configure && make && sudo make install
   ```

3. **Download and Install Snort**

   ```bash
   cd /usr/src
   sudo wget https://www.snort.org/downloads/snort/snort-2.9.20.tar.gz
   sudo tar -xzvf snort-2.9.20.tar.gz
   cd snort-2.9.20
   ./configure --enable-sourcefire && make && sudo make install
   ```

4. **Create Necessary Directories**

   ```bash
   sudo mkdir -p /etc/snort/rules /var/log/snort /usr/local/lib/snort_dynamicrules
   sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules
   sudo cp etc/* /etc/snort/
   ```

5. **Add Snort to Path**

   ```bash
   sudo ln -s /usr/local/bin/snort /usr/sbin/snort
   ```
---

## **Snort Installation on Arch Linux**

### 🔧 Prerequisites

```bash
sudo pacman -S base-devel libpcap pcre libdnet zlib flex bison
```

### Install DAQ (required for Snort 2.x)

```bash
yay -S daq
```

### Install Snort (from AUR)

```bash
yay -S snort
```

### Create Required Directories

```bash
sudo mkdir -p /etc/snort/rules /var/log/snort /usr/local/lib/snort_dynamicrules
sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules
sudo cp /etc/snort/snort.conf.example /etc/snort/snort.conf
```

### Verify Installation

```bash
snort -V
```

---

## **Snort Installation on Fedora (also applies to RHEL/CentOS)**

### Install Prerequisites

```bash
sudo dnf install -y gcc flex bison zlib-devel libpcap-devel pcre-devel libdnet-devel make
```

### Install DAQ

```bash
cd /usr/src
sudo wget https://www.snort.org/downloads/snort/daq-2.0.7.tar.gz
sudo tar -xvzf daq-2.0.7.tar.gz
cd daq-2.0.7
./configure && make && sudo make install
```

### Install Snort

```bash
cd /usr/src
sudo wget https://www.snort.org/downloads/snort/snort-2.9.20.tar.gz
sudo tar -xvzf snort-2.9.20.tar.gz
cd snort-2.9.20
./configure --enable-sourcefire && make && sudo make install
sudo ldconfig
```

### Create Required Directories

```bash
sudo mkdir -p /etc/snort/rules /var/log/snort /usr/local/lib/snort_dynamicrules
sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules
sudo cp etc/* /etc/snort/
```

### Verify Installation

```bash
snort -V
```

---

## **Snort Installation on macOS (via Homebrew)**

### Install Prerequisites

```bash
brew install libpcap pcre daq
```

### Install Snort

```bash
brew install snort
```

### Create Required Directories

```bash
sudo mkdir -p /etc/snort/rules /var/log/snort /usr/local/lib/snort_dynamicrules
sudo touch /etc/snort/rules/white_list.rules /etc/snort/rules/black_list.rules
sudo cp /usr/local/etc/snort/snort.conf.example /usr/local/etc/snort/snort.conf
```

### Verify Installation

```bash
snort -V
```
#### **On Windows (Snort 2.9.x)**

1. **Download Snort for Windows**

   * Go to: [https://www.snort.org/downloads](https://www.snort.org/downloads)
   * Download the **Snort Installer** for Windows.
   * Also download the **WinPcap** or **Npcap** (in WinPcap-compatible mode).

2. **Install Dependencies**

   * Install **Npcap** with the “WinPcap API-compatible Mode” option enabled.
   * Install **Snort** using the installer (default directory: `C:\Snort`).

3. **Set Environment Variables**

   * Add `C:\Snort\bin` to the system `PATH`.

4. **Download Snort Rules**

   * Register on Snort.org and download the latest Snort rules.
   * Extract the rules and place them in `C:\Snort\rules`.

5. **Edit Configuration File**

   * Modify `C:\Snort\etc\snort.conf`:

     * Set `var RULE_PATH` to `C:\Snort\rules`
     * Update the `include` directives to reflect Windows paths

---

### **Verifying the Installation**

#### **On Linux**

```bash
snort -V
```

* This should output the Snort version and copyright.

#### **Testing Snort with a Simple Rule**

1. Create a test rule:

   ```bash
   echo 'alert icmp any any -> any any (msg:"ICMP Packet"; sid:1000001; rev:1;)' | sudo tee /etc/snort/rules/local.rules
   ```

2. Modify `snort.conf`:

   * Set `include $RULE_PATH/local.rules`

3. Run Snort in packet sniffing mode:

   ```bash
   sudo snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i eth0
   ```

4. **Generate ICMP Traffic** (in another terminal):

   ```bash
   ping -c 1 localhost
   ```

5. You should see an alert similar to:

   ```
   [**] [1:1000001:1] ICMP Packet [**]
   ```

#### **On Windows**

1. **Open Command Prompt (Admin)** and run:

   ```cmd
   snort.exe -V
   ```

2. **Test Run:**

   ```cmd
   snort.exe -i 1 -c "C:\Snort\etc\snort.conf" -A console
   ```

   *(Replace `-i 1` with your actual NIC number from `snort -W`)*

3. **Generate traffic**, e.g., ICMP ping to localhost.

---

### **References**

* Official Snort Downloads: [https://www.snort.org/downloads](https://www.snort.org/downloads)
* Snort Documentation: [https://docs.snort.org/](https://docs.snort.org/)
* Snort GitHub (for source builds): [https://github.com/snort3/snort3](https://github.com/snort3/snort3)
* Snort Rule Management: [https://github.com/shirkdog/pulledpork](https://github.com/shirkdog/pulledpork)

 

## 3. Configuration  

## Understanding Snort Configuration Files

Snort’s configuration is primarily controlled via the `snort.conf` file. This file orchestrates the entire detection pipeline:

* **Defines variables** (like IP ranges for internal/external networks)
* **Loads preprocessors** (like Frag3, Stream5)
* **Specifies rule paths and policy files**
* **Sets up output plugins** for logging or alerting

The configuration file must be **customized** for your network, otherwise Snort will either miss important events or produce excessive false positives.

---

## `snort.conf` Overview

The `snort.conf` file is typically located in `/etc/snort/snort.conf` or `/usr/local/etc/snort/snort.conf`.

**Major Sections:**

| Section                        | Purpose                                                  |
| ------------------------------ | -------------------------------------------------------- |
| **Network Variables**          | Define IP ranges (`HOME_NET`, `EXTERNAL_NET`)            |
| **Decoder Configuration**      | Base layer decoding setup                                |
| **Preprocessor Configuration** | Handles reassembly, normalization, and protocol parsing  |
| **Output Plugins**             | Define alerting/logging methods (e.g., unified2, syslog) |
| **Rule Path and Rules**        | Path to Snort rules and categories                       |
| **Event Thresholding**         | Controls event flooding or duplicates                    |

---

## Setting Up Network Variables

Defined near the top of `snort.conf`:

```bash
ipvar HOME_NET 192.168.1.0/24
ipvar EXTERNAL_NET !$HOME_NET
```

* `HOME_NET`: Your protected/internal network (e.g., LAN)
* `EXTERNAL_NET`: Everything else (often set to `!$HOME_NET` to mean “not home”)

Other useful variables:

```bash
ipvar DNS_SERVERS [192.168.1.53, 192.168.1.54]
ipvar HTTP_SERVERS $HOME_NET
portvar HTTP_PORTS [80, 443, 8080]
```

These variables are referenced in rules to maintain modularity and reusability.

---

## Configuring Preprocessors

Preprocessors inspect and normalize traffic before it reaches the detection engine. They're critical for evasion resistance.

### 1. **Frag3 Preprocessor (IP Fragmentation)**

Reassembles fragmented IP packets to detect fragmentation-based evasion attacks.

```bash
preprocessor frag3_global: max_frags 65536
preprocessor frag3_engine: policy windows detect_anomalies overlap_limit 10 timeout 180
```

* `policy`: Target OS policy (e.g., `windows`, `linux`)
* `detect_anomalies`: Alerts on invalid fragmentation patterns
* `overlap_limit`: Prevents evasion by fragment overlaps

🔍 **Why It Matters**: Attackers may fragment malicious payloads to bypass signature matching. Frag3 puts them back together.

---

### 2. **Stream5 Preprocessor (TCP Reassembly)**

Tracks TCP sessions and reassembles data streams.

```bash
preprocessor stream5_global: track_tcp yes, track_udp yes
preprocessor stream5_tcp: policy linux timeout 180, detect_anomalies, require_3whs 180
preprocessor stream5_udp: timeout 180
```

* `require_3whs`: Require 3-way handshake (minimize false positives)
* `detect_anomalies`: Identifies TCP protocol misuse
* `policy`: OS policy (affects how TCP streams are normalized)

💡 **Note**: Essential for detecting attacks that span multiple TCP segments.

---

### 3. **HTTP Inspect Preprocessor**

Normalizes HTTP traffic, critical for web-based attack detection.

```bash
preprocessor http_inspect: global iis_unicode_map unicode.map 1252
preprocessor http_inspect_server: server default \
    profile apache \
    ports { 80 8080 8000 8888 } \
    flow_depth 300 \
    oversize_dir_length 500
```

* `profile`: Server behavior emulation (`apache`, `iis`)
* `flow_depth`: How much data to inspect (0 = unlimited)
* `oversize_dir_length`: Prevent evasion by long directories
* `unicode.map`: Handles Unicode obfuscation (critical for IIS decoding)

---

## Rule Path and Logging Setup

### Rule Path Configuration

Defined with `var` or `include`:

```bash
var RULE_PATH /etc/snort/rules
include $RULE_PATH/local.rules
include $RULE_PATH/emerging-threats.rules
```

Each `.rules` file contains signature-based detection logic.

To load categories:

```bash
include $RULE_PATH/attack-responses.rules
include $RULE_PATH/web-application.rules
```

### Logging Setup

Snort uses output plugins. Common setups:

**Unified2 (Binary log format, recommended for Barnyard2)**

```bash
output unified2: filename snort.u2, limit 128
```

**Syslog Output:**

```bash
output alert_syslog: LOG_AUTH LOG_ALERT
```

**Fast Alert Format (Simple ASCII, best for manual review)**

```bash
output alert_fast: stdout
```

**Log Directory:**

Specified via CLI:

```bash
snort -c /etc/snort/snort.conf -l /var/log/snort
```

Snort will create log files like:

* `alert`
* `snort.log.<timestamp>`
* Unified2 files (if configured)

---

## Additional Best Practices

* Always match preprocessor `policy` to your **target environment OS** (e.g., `linux`, `windows`, `bsd`)
* Tune `HOME_NET` and `EXTERNAL_NET` accurately to reduce false positives
* Use `threshold.conf` or `rate_filter` in rules to manage alert storms
* Keep `snort.conf` clean by commenting and organizing rule inclusions logically

---

## Verifiable References

* [Snort 2.9.x Configuration Guide (Official)](https://docs.snort.org/)
* [Snort Community Manual](https://www.snort.org/documents)
* [Snort Preprocessor Reference](https://docs.snort.org/faq#Snort-Preprocessors)
* [Barnyard2 + Unified2 Logging](https://github.com/firnsy/barnyard2)
  

## 4. Snort Rules  

## Snort: In-Depth Overview

## Anatomy of a Snort Rule

A **Snort rule** is a set of conditions that define the detection behavior of Snort. It’s made up of two main parts:

### 1. Rule Header

The **rule header** defines:

* The **action** to take when the rule is triggered.
* The **protocol** (e.g., TCP, UDP, ICMP).
* The **source IP address** and **port**.
* The **destination IP address** and **port**.
* **Direction operator** (`->`, `<-`, or `<->`).

**Format:**

```snort
action protocol src_ip src_port direction dst_ip dst_port
```

**Example:**

```snort
alert tcp any any -> 192.168.1.0/24 80
```

This rule alerts when any TCP packet goes to IPs in the 192.168.1.0/24 subnet on port 80.

### 2. Rule Options

Rule options are enclosed in parentheses and define **detection criteria** and **metadata**. Each option ends with a semicolon `;`.

**Categories of rule options:**

* **Payload detection**: `content`, `pcre`
* **Non-payload detection**: `ttl`, `tos`, `flags`
* **Post-detection actions**: `logto`, `session`
* **Metadata**: `msg`, `classtype`, `sid`, `rev`

**Example:**

```snort
alert tcp any any -> 192.168.1.0/24 80 (msg:"HTTP connection attempt"; content:"GET"; sid:1000001; rev:1;)
```

This rule alerts on TCP packets to port 80 in the specified subnet that contain "GET" in the payload, typically indicating an HTTP GET request.

---

## Writing Custom Rules

Snort allows you to define your own rules to detect specific threats relevant to your environment.

### Examples of Basic Rules

**1. Detect PING (ICMP Echo Request):**

```snort
alert icmp any any -> any any (msg:"ICMP Ping Detected"; itype:8; sid:1000002; rev:1;)
```

**2. Detect FTP login attempt:**

```snort
alert tcp any any -> any 21 (msg:"FTP login attempt"; content:"USER"; sid:1000003; rev:1;)
```

### Advanced Rule Options

* **`depth`**: Limits content match to the first N bytes.
* **`offset`**: Skips first N bytes before searching for content.
* **`pcre`**: Uses Perl-compatible regular expressions.
* **`flow`**: Specifies the direction and state of a session (`to_server`, `established`).
* **`http_uri`, `http_header`**: HTTP-specific modifiers for application layer inspection.
* **`byte_test`, `byte_jump`**: Examine specific bytes in the payload—useful for protocol dissection.

**Example with advanced options:**

```snort
alert tcp any any -> any 80 (msg:"Potential Shellshock Attack"; content:"() {"; http_header; pcre:"/User-Agent\:.*\(\)\s+\{/"; sid:1000004; rev:2;)
```

This rule detects a Shellshock exploit attempt in the `User-Agent` HTTP header.

---

## Managing Rule Sets

Snort rules can be grouped and maintained using predefined or custom rule sets.

### Community Rules

The **Snort Community Ruleset** is maintained by Cisco Talos and is available for free. It includes detection for:

* Common vulnerabilities and exposures (CVEs)
* Malware behavior
* Policy violations

**Location:**
You can get them via the [Snort.org downloads page](https://www.snort.org/downloads).

**Default path for rules:**
`/etc/snort/rules/` (Linux) or specified in `snort.conf`.

**Enabling rules:**
In `snort.conf`, uncomment or add include lines:

```bash
include $RULE_PATH/community.rules
```

### Updating Rules with PulledPork

**PulledPork** is a Perl script used to:

* Download rule updates from Snort.org.
* Merge and manage custom rules with downloaded ones.
* Enable/disable rules via SID management.
* Update **sid-msg.map**, **reference.config**, etc.

**Steps to use PulledPork:**

1. **Install PulledPork**:

   ```bash
   sudo apt-get install pulledpork
   ```

2. **Configure pulledpork.conf**:

   * Set rule URL:

     ```conf
     rule_url=https://www.snort.org/rules/snortrules-snapshot.tar.gz|<your_oinkcode>
     ```
   * Set the path to `snort.conf`, rules directory, and backup location.

3. **Run PulledPork**:

   ```bash
   sudo ./pulledpork.pl -c /etc/pulledpork/pulledpork.conf -l
   ```

4. **Restart Snort** to apply new rules.

**Benefits of PulledPork**:

* Automates updates.
* Centralizes rule management.
* Supports local custom rule integration.

---

## Summary

| Component           | Purpose                                                                |
| ------------------- | ---------------------------------------------------------------------- |
| **Rule Header**     | Defines matching conditions and traffic scope (IP, port, protocol).    |
| **Rule Options**    | Defines what to inspect (payload, headers), what message to log, etc.  |
| **Basic Rules**     | Help detect generic protocol or service activity.                      |
| **Advanced Rules**  | Use regex, byte tests, and HTTP inspection for fine-grained detection. |
| **Community Rules** | Maintained by Cisco, updated with new threats, free for use.           |
| **PulledPork**      | Automates downloading and managing Snort rules.                        |


## 5. Running Snort  

## Running Snort in Different Modes

Snort can operate in **three primary modes**, each with different use cases depending on whether you're debugging, capturing traffic, or detecting intrusions.

### 1. **Sniffer Mode**

* **Purpose**: Captures and displays packets in real-time.
* **Use Case**: Ideal for network diagnostics or understanding traffic patterns.
* **Command**:

  ```bash
  snort -v
  ```

  * `-v`: Verbose mode – prints packet headers to the console.
  * `-d`: (Optional) Dumps application layer data.
  * `-e`: (Optional) Shows data link layer headers.
* **Example**:

  ```bash
  snort -vde
  ```

  This will capture packets and display Ethernet headers, IP headers, TCP/UDP headers, and payload data.

---

### 2. **Packet Logger Mode**

* **Purpose**: Logs packets to disk in libpcap format.

* **Use Case**: Ideal for long-term traffic recording or offline analysis with tools like Wireshark.

* **Command**:

  ```bash
  snort -dev -l /var/log/snort
  ```

  * `-l`: Specifies the logging directory.
  * `-dev`: Captures and logs packet headers and payload.

* **Output**:
  Logs are saved in `/var/log/snort/`, often organized by IP addresses and protocol types.

---

### 3. **Network Intrusion Detection System (IDS) Mode**

* **Purpose**: Inspects network traffic and triggers alerts based on defined rules.

* **Use Case**: Real-time detection of malicious or suspicious activities.

* **Command**:

  ```bash
  snort -c /etc/snort/snort.conf -i eth0
  ```

  * `-c`: Specifies the path to the Snort configuration file.
  * `-i`: Defines the network interface to monitor.

* **How it works**:
  Snort reads the rules from `snort.conf`, loads preprocessor modules (e.g., stream5, HTTP inspector), and applies detection rules against live traffic.

* **Alert Output**:
  Alerts are logged to `/var/log/snort/alert`, usually in unified2 or plain-text format.

---

## 🔹 Snort Command-Line Options

Snort supports many command-line options for fine-grained control. Here are the most important ones:

| Option          | Description                                                                  |
| --------------- | ---------------------------------------------------------------------------- |
| `-c`            | Specifies the configuration file path.                                       |
| `-i`            | Sets the network interface (e.g., `eth0`).                                   |
| `-l`            | Sets the log directory for packet or alert logs.                             |
| `-A`            | Specifies alert mode: `fast`, `full`, `unsock`, etc.                         |
| `-K`            | Defines logging output format: `ascii`, `pcap`, `none`, or `unified2`.       |
| `-s`            | Sends alerts to syslog.                                                      |
| `-T`            | Tests the configuration file without starting detection.                     |
| `-X` / `-d`     | Dumps payload in hex / printable format.                                     |
| `-q`            | Quiet mode (suppresses banner and stats).                                    |
| `--daq`         | Specifies the Data Acquisition library (e.g., `pfring`, `dump`, `afpacket`). |
| `-h`            | Specifies the "home network" for rule evaluation.                            |
| `--pcap-single` | Processes a single PCAP file.                                                |

---

## 🔹 Testing Snort with Sample Traffic

Testing Snort in a lab or production environment helps validate its rules and functionality.

### 1. **Using PCAP Files**

**Command**:

  ```bash
  snort -r test.pcap -c /etc/snort/snort.conf -l /tmp/snortlogs
  ```

  * `-r`: Reads traffic from a pcap file instead of live capture.
  * `-c`: Uses standard Snort rule set.

**Purpose**: Great for offline testing, regression testing for rule changes, or verifying detection logic.

### 2. **Using Custom Traffic (Manual or Tools)**

You can simulate traffic using tools like `hping3`, `nmap`, or `scapy`:

**Example** (Ping scan with nmap):

  ```bash
  nmap -sP 192.168.1.0/24
  ```
**Snort Alert Rule Example** (Detect ping requests):

  ```snort
  alert icmp any any -> any any (msg:"ICMP Ping detected"; sid:1000001; rev:1;)
  ```

### 3. **Testing Rule Configuration**

* Use the `-T` flag to verify the configuration:

  ```bash
  snort -T -c /etc/snort/snort.conf
  ```

  * Confirms whether the configuration file is valid and all rules are loaded correctly.

---

## Summary Table

| Mode          | Command Example                               | Purpose                              |
| ------------- | --------------------------------------------- | ------------------------------------ |
| Sniffer       | `snort -vde`                                  | Debug traffic and view packets live. |
| Packet Logger | `snort -dev -l /var/log/snort`                | Store packets for later analysis.    |
| IDS Mode      | `snort -c /etc/snort/snort.conf -i eth0`      | Live detection using rules.          |
| Config Test   | `snort -T -c /etc/snort/snort.conf`           | Check rule config before deployment. |
| PCAP Replay   | `snort -r test.pcap -c /etc/snort/snort.conf` | Simulate past network events.        |

---

## Resources for Verification

* **Official Snort Documentation**: [https://docs.snort.org/](https://docs.snort.org/)
* **Snort GitHub Repository**: [https://github.com/snort3/snort3](https://github.com/snort3/snort3)
* **Cisco Talos Ruleset**: [https://www.snort.org/downloads](https://www.snort.org/downloads)
* **Snort Manual Page**: Run `man snort` on Linux or view [https://man7.org/linux/man-pages/man8/snort.8.html](https://man7.org/linux/man-pages/man8/snort.8.html)


## 6. Analyzing Alerts  
    - Understanding Snort alert formats  
    - Using tools to analyze logs  
      - Barnyard2  
      - Snorby  
      - Splunk integration  

## 7. Performance Tuning  
    - Optimizing Snort for high traffic environments  
    - Preprocessor tuning  
    - Multi-threading and hardware considerations  

## 8. Troubleshooting  
    - Common errors and solutions  
    - Debugging Snort configurations  
    - Checking logs for issues  

## 9. Advanced Topics  
    - Deploying Snort as an IPS  
    - Inline mode configuration  
    - Integration with firewalls and SIEM tools  

## 10. Conclusion  
    - Recap of key points  
    - Additional resources for learning Snort  
    - Best practices for maintaining Snort deployments  
