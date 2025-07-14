# Splunk Tutorial Outline

## 1. Introduction to Splunk

## **What is Splunk?**

### **Overview of Splunk as a Data Analysis and Monitoring Tool**

**Splunk** is a proprietary software platform used for **searching, monitoring, and analyzing machine-generated data** via a web-style interface. It is most commonly used to collect, index, and analyze log data in real time from various sources like applications, systems, and devices.

* It enables organizations to gain operational intelligence from machine data.
* It can handle **structured, semi-structured, and unstructured data**.
* Originally developed for log analysis, Splunk has grown into a comprehensive platform for **data analytics, security information and event management (SIEM), and observability**.

**Official source**:

* [Splunk Docs - What is Splunk?](https://docs.splunk.com/Documentation/Splunk/latest/Search/Aboutthismanual)

---

### **Use Cases**

Splunk is widely adopted across multiple domains:

#### **1. IT Operations**

* Helps monitor system performance, detect outages, and diagnose root causes of problems.
* Provides real-time dashboards for **network, server, and application performance**.

#### **2. Security (SIEM)**

* Splunk Enterprise Security is used for threat detection, compliance, and incident response.
* Enables **correlation searches, alerts, and dashboards** for security teams.

#### **3. Business Analytics**

* Enables insights into **customer behavior, marketing performance**, and transaction patterns by analyzing logs and event data from web applications.

#### **4. DevOps/Observability**

* Used with Splunk Observability Suite to **monitor infrastructure, APM (Application Performance Monitoring)**, and troubleshoot issues in CI/CD pipelines.

**Sources**:

* [Splunk ITSI Use Cases](https://www.splunk.com/en_us/solutions/it.html)
* [Splunk Security Use Cases](https://www.splunk.com/en_us/solutions/industries/security.html)
* [Splunk Business Analytics Overview](https://www.splunk.com/en_us/solutions/business-analytics.html)

---

## **Key Features**

### **1. Data Ingestion, Indexing, and Searching**

* **Data Ingestion**: Splunk ingests data from various sources including logs, syslogs, metrics, APIs, and third-party services.
* **Indexing**: Once data is ingested, it is parsed and indexed to allow efficient search.
* **Searching**: Splunk uses **SPL (Search Processing Language)** to search and analyze the indexed data.

Example SPL query:

```spl
index=web_logs status=500 | stats count by source
```

🔗 [Splunk Docs - Search Language](https://docs.splunk.com/Documentation/Splunk/latest/Search/AboutSPL)

---

### **2. Dashboards and Visualizations**

* Users can build **real-time dashboards** with panels showing charts, graphs, tables, etc.
* Visualizations help track **KPIs, anomalies, and trends**.
* Includes support for **custom visualizations using JavaScript or D3.js**.

🔗 [Splunk Docs - Dashboards and Visualizations](https://docs.splunk.com/Documentation/Splunk/latest/Viz/Aboutvisualizations)

---

### **3. Alerts and Reporting**

* **Alerts** can be triggered based on search results, thresholds, or anomalies.
* Alerts can be configured to trigger actions like sending an email, running a script, or creating a ticket in ServiceNow.
* **Reports** are scheduled searches that can be emailed, exported, or saved for auditing.

- [Splunk Docs - Alerts](https://docs.splunk.com/Documentation/Splunk/latest/Alert/Aboutalerts)
- [Splunk Docs - Reports](https://docs.splunk.com/Documentation/Splunk/latest/Search/Aboutreports)

---

## **Splunk Architecture**

### **Components**

Splunk has a **distributed architecture**, especially in enterprise environments. The key components are:

#### **1. Universal Forwarder (UF)**

* A lightweight agent installed on source machines.
* Forwards raw data to the Indexer without parsing it.
* Minimal resource usage.

- [Splunk UF Documentation](https://docs.splunk.com/Documentation/Forwarder/latest/Forwarder/Abouttheuniversalforwarder)

#### **2. Indexer**

* Responsible for **receiving, parsing, indexing, and storing** data.
* Handles search requests and returns relevant results to the Search Head.
* Can be clustered for high availability and scalability.

- [Splunk Docs - Indexing](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/Aboutindexing)

#### **3. Search Head**

* Provides the **user interface for searching, analyzing, and visualizing data**.
* Processes search requests and distributes them to one or more indexers.
* Can be part of a **Search Head Cluster** for horizontal scalability.

- [Search Head Docs](https://docs.splunk.com/Documentation/Splunk/latest/DistSearch/Aboutdistributedsearch)

---

### **Data Flow in Splunk**

The general flow of data is as follows:

1. **Data Source**: Application logs, OS logs, network traffic, etc.
2. **Universal Forwarder**: Collects data and forwards it.
3. **Indexer**: Parses, indexes, and stores data.
4. **Search Head**: Executes searches and generates dashboards/reports for users.

In larger deployments, the architecture may also include:

* **Heavy Forwarders**: Can parse and filter data before sending to indexers.
* **Deployment Server**: Manages configurations for forwarders.
* **License Master** and **Cluster Master**: Manage licensing and clustered environments.

- [Splunk Architecture Overview](https://docs.splunk.com/Documentation/Splunk/latest/Deploy/AboutSplunkEnterprisedeployments)


## 2. Installing Splunk
---

### **System Requirements**

Before installing Splunk Enterprise or Splunk Free, it’s essential to ensure your system meets the official requirements for optimal performance.

#### **Supported Operating Systems**

Splunk supports a range of platforms:

* **Linux (Preferred for production deployments)**

  * RHEL/CentOS, Debian, Ubuntu, SUSE, Amazon Linux, etc.
* **Windows**

  * Windows 10, 11, Server 2016/2019/2022 (64-bit only)
* **macOS**

  * Only for **development or testing**. Not recommended for production.

- [Splunk Platform Requirements](https://docs.splunk.com/Documentation/Splunk/latest/Installation/Systemrequirements)

---

#### **Hardware Requirements**

These vary based on the size and scale of deployment (small test vs. production), but the **general recommendations** are:

| Component | Minimum (Small/Test) | Recommended (Production)  |
| --------- | -------------------- | ------------------------- |
| **CPU**   | 2 cores              | 12 cores+                 |
| **RAM**   | 4 GB                 | 12–16 GB+                 |
| **Disk**  | 20 GB                | SSDs preferred, 800 IOPS+ |

Splunk requires **fast disk I/O** since it's data-intensive. SSDs are highly recommended for indexers.

---

#### **Software Requirements**

* **Python**: Splunk ships with its own Python runtime.
* **Web Browser**: Chrome, Firefox, Safari, or Edge (JavaScript enabled).
* **Firewall/SELinux**: May need configuration to allow port `8000` (Splunk Web) and others.

---

### **Installation Steps**

Splunk is available in two major editions:

* **Splunk Enterprise**: Full-featured, supports clustering, premium apps.
* **Splunk Free**: Same core engine as Enterprise, but limited to one user and lacks advanced features.

- [Download Splunk Enterprise or Free](https://www.splunk.com/en_us/download/splunk-enterprise.html)

---

#### **Installation on Linux**

1. **Download Splunk**

   ```bash
   wget -O splunk-latest.rpm 'https://download.splunk.com/products/splunk/releases/latest/linux/splunk-x.x.x.rpm'
   ```

2. **Install**

   ```bash
   sudo rpm -i splunk-x.x.x.rpm   # For RHEL/CentOS
   sudo dpkg -i splunk-x.x.x.deb  # For Debian/Ubuntu
   ```

3. **Start Splunk**

   ```bash
   sudo /opt/splunk/bin/splunk start --accept-license
   ```

- [Linux Install Guide](https://docs.splunk.com/Documentation/Splunk/latest/Installation/InstallonLinux)

---

#### **Installation on Windows**

1. **Download** the `.msi` installer from Splunk's website.
2. **Run Installer**: Double-click the `.msi` file and follow the wizard.
3. **Set admin credentials** during setup.
4. After installation, services start automatically.

- [Windows Install Guide](https://docs.splunk.com/Documentation/Splunk/latest/Installation/InstallonWindows)

---

#### **Installation on macOS (for testing)**

1. Download `.dmg` file.
2. Drag Splunk into `/Applications`.
3. Run Splunk from Terminal:

   ```bash
   sudo /Applications/Splunk/bin/splunk start --accept-license
   ```

- [macOS Install Guide](https://docs.splunk.com/Documentation/Splunk/latest/Installation/InstallonMacOS)

---

### **Post-Installation Setup**

Once installed, you'll need to start Splunk services and access its web interface for further configuration.

---

#### **Starting Splunk Services**

On **Linux/macOS**:

```bash
/opt/splunk/bin/splunk start
```

To enable auto-start on boot:

```bash
/opt/splunk/bin/splunk enable boot-start
```

On **Windows**, services start via the Windows Service Control Manager by default, but can also be managed with PowerShell:

```powershell
Start-Service Splunkd
```

- [Splunk Service Management](https://docs.splunk.com/Documentation/Splunk/latest/Admin/StartSplunk)

---

#### **Accessing the Splunk Web Interface**

1. Open a browser and go to:

   ```
   http://<your-hostname>:8000
   ```

   * Default port is `8000` unless changed.
   * If running locally: `http://localhost:8000`

2. **Login Credentials**:

   * First-time setup prompts for admin user creation.
   * After setup, use the credentials you created.

3. Once logged in, you can:

   * Add data sources
   * Create dashboards and alerts
   * Run searches with SPL

- [Splunk Web Overview](https://docs.splunk.com/Documentation/Splunk/latest/Search/AboutSplunkWeb)



## 3. Getting Started with Splunk

## **Navigating the Splunk Interface, Adding Data, and Understanding the Data Pipeline**

---

## **Navigating the Splunk Interface**

### **Overview of the Splunk Web UI**

After you install and start Splunk, you access the **Splunk Web Interface** using a browser:

```
http://<hostname>:8000
```

The Web UI provides an intuitive, browser-based way to:

* Search and analyze machine data.
* Create and manage dashboards, alerts, and reports.
* Configure Splunk apps and system settings.

🔗 [Official UI Overview](https://docs.splunk.com/Documentation/Splunk/latest/Search/AboutSplunkWeb)

---

### **Key Sections in the Web Interface**

Splunk Web has a consistent layout with navigation menus and contextual options:

#### 1. **Search & Reporting**

* The **main interface** where you run SPL (Search Processing Language) queries.
* Offers time-range selectors, field pickers, and visualizations.
* Allows saving results as alerts, reports, or dashboards.

🔗 [Search and Reporting App](https://docs.splunk.com/Documentation/Splunk/latest/Search/UsetheSearchApp)

#### 2. **Settings**

Accessible via the gear icon or top navigation bar. Used for:

* **Data Inputs**: Manage sources like files, TCP/UDP streams, scripts, etc.
* **Indexes**: Create or manage storage buckets for your data.
* **User Roles**: Add/manage users, assign permissions.
* **System Settings**: Authentication, server roles, distributed settings.

🔗 [Splunk Settings Guide](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Aboutconfigurationfiles)

#### 3. **Apps**

* **Apps** are modular extensions for different use cases (e.g., Enterprise Security, Splunk DB Connect).
* You can install apps from Splunkbase.
* Each app can have its own dashboards, inputs, and settings.

- [Splunk Apps](https://splunkbase.splunk.com)

---

## **Adding Data to Splunk**

### **Data Sources Supported by Splunk**

Splunk supports a wide range of input types:

| Data Source Type      | Example                                           |
| --------------------- | ------------------------------------------------- |
| **Files/Directories** | Log files, CSVs, JSON files                       |
| **Network Inputs**    | Syslog, TCP/UDP streams                           |
| **Scripts/Commands**  | Output from cron jobs or APIs                     |
| **REST API**          | Push JSON/XML data via HTTP Event Collector (HEC) |
| **Cloud Services**    | AWS, Azure, GCP (via add-ons)                     |

- [Add Data Inputs](https://docs.splunk.com/Documentation/Splunk/latest/Data/Aboutgettingdatain)

---

### **Steps to Add Data (GUI-based)**

1. Go to **Settings > Add Data**.
2. Choose a data source:

   * **Upload** (for one-time files)
   * **Monitor** (for real-time log folders or ports)
   * **Forward** (for data from Universal Forwarders)
3. Select or create an **input type** and define:

   * Source Type (e.g., `syslog`, `apache:access`)
   * Index (e.g., `main`, `web_logs`)
   * Host identifier
4. Review and submit the configuration.

- [Splunk Web - Add Data Wizard](https://docs.splunk.com/Documentation/Splunk/latest/Data/UsetheAddDatapage)

---

### **Verifying Data Ingestion**

After adding data:

* Go to **Search & Reporting**
* Use a basic query:

  ```spl
  index=* | head 10
  ```
* You should see ingested events displayed in the search UI.
* Use filters like `source`, `sourcetype`, and `host` to validate specifics.

- [Search Your Indexed Data](https://docs.splunk.com/Documentation/Splunk/latest/Search/Quickstartsearch)

---

## **Understanding Splunk's Data Pipeline**

Splunk processes data through a **multi-stage pipeline**, which is essential to understand how raw logs turn into searchable events.

---

### **1. Data Input Phase**

* Splunk **collects raw data** from files, ports, scripts, etc.
* Forwarders (especially **Universal Forwarders**) often deliver this data to Indexers.

---

### **2. Parsing Phase**

* Splunk **breaks the data into events** based on timestamps and line breaks.
* **Line merging**, **timestamp extraction**, and **event boundaries** are determined.
* This occurs primarily on **Heavy Forwarders or Indexers**.

---

### **3. Indexing Phase**

* Parsed events are transformed into **indexed segments**.
* Data is stored in compressed files across directories called **buckets** (hot, warm, cold, frozen).
* Metadata like index name, source, sourcetype, and timestamp is added.

---

### **4. Searching Phase**

* When users run queries in the **Search Head**, the request is distributed to the appropriate **Indexers**.
* Results are returned and can be formatted using **commands**, **statistics**, and **visualizations**.

---

### **Visual Summary of Splunk Data Flow**

```
        Data Source
             ↓
      [Forwarder or UI Add Data]
             ↓
        Parsing (event breaks, timestamps)
             ↓
        Indexing (storage, metadata)
             ↓
        Search & Reporting (SPL queries, dashboards)
```

- [Splunk Data Pipeline Docs](https://docs.splunk.com/Documentation/Splunk/latest/Indexer/Howindexingworks)


## 4. Searching in Splunk
### 1 . Search Processing Language (SPL) Basics

| Concept                | Key points                                                                                                                                                                                                                                                                                                                      | Why it matters in forensics                                                                                                                                                                                  |                                                                                               |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------- |
| **Syntax & structure** | • A search is a chain of commands separated by the pipe (\`                                                                                                                                                                                                                                                                     | \`) character. <br>• First token after each pipe is the command; the rest are arguments/options. <br>• SPL is case‑insensitive for commands, but field names are case‑sensitive. ([Splunk Documentation][1]) | Lets investigators build repeatable “recipes” that transform raw log events into evidence.    |
| **`search` command**   | • Default operator; returns events that match Boolean expressions, wild‑cards, CIDR, fields, and time modifiers. <br>• Because `search` runs *first* (unless you explicitly filter earlier with `index=` or `source=`), place the most‑selective terms as early as possible to reduce event volume. ([Splunk Documentation][2]) | Early narrowing lowers disk I/O and speeds triage when timelines are tight.                                                                                                                                  |                                                                                               |
| **`stats` command**    | • Produces aggregate statistics (`count`, `sum`, `avg`, `min`, `max`, etc.) grouped by any field(s). <br>• Syntax \`…                                                                                                                                                                                                           | stats count by src\_ip`, or multi‑stats: `stats dc(user) AS unique\_users, sum(bytes)\` ([Splunk Documentation][3])                                                                                          | Quickly turns millions of firewall or proxy hits into high‑level counts for incident scoping. |
| **`table` command**    | • Reshapes results into a columnar table with only the specified fields: \`…                                                                                                                                                                                                                                                    | table \_time user dest\_ip\`. ([Splunk Documentation][2])                                                                                                                                                    | Produces human‑readable artefacts that can be exported directly into investigative notes.     |
| **`eval` command**     | • Creates or transforms fields on the fly with arithmetic, string, Boolean, or conditional logic: \`…                                                                                                                                                                                                                           | eval suspect\_score=if(bytes>1000000,1,0)\` ([Splunk Documentation][4])                                                                                                                                      | Lets analysts attach context (e.g., “is\_admin\_login”) without altering raw evidence.        |

---

### 2 . Using Search Commands – practical patterns

1. **Filtering & transforming**

   ```spl
   index=web_logs status=404  
   | eval uri_path=lower(uri)  
   | where NOT uri_path LIKE "%.png"  
   | table _time client_ip uri_path
   ```

   *Sequence:* narrow by index, filter on `status`, normalise case with `eval`, drop noise with `where`, present clean table. Each stage prunes data, so later commands operate on fewer events, improving speed. ([Splunk Documentation][2], [Splunk Documentation][4])

2. **Aggregating for pivot views**

   ```spl
   index=endpoint sourcetype=sysmon EventCode=1  
   | stats earliest(_time) AS first_seen latest(_time) AS last_seen by Image, user
   ```

   Useful to see when a binary first executed and which user touched it – a common timeline question in malware‑forensics. ([Splunk Documentation][3])

3. **Visualising quickly**

   ```spl
   index=dhcp  
   | timechart span=1h count by action
   ```

   `timechart` is a macro for `stats` + `_time` bucketing and delivers ready‑to‑graph series in Splunk Web’s UI. ([Splunk Documentation][2])

---

### 3 . Search Best Practices

| Technique                          | Explanation                                                                                                                                                                                                                                      | Source                                                                                                                                                                                        |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Narrow the time window first**   | Use the *time‑range picker* or explicit modifiers such as `index=fw earliest=-2h latest=now` so Splunk only reads buckets that matter. ([Splunk Documentation][5])                                                                               |                                                                                                                                                                                               |
| **Specify index/sourcetype early** | `index=proxy sourcetype=squid` before other terms lets the indexers skip unrelated buckets, improving throughput. ([Splunk Documentation][2])                                                                                                    |                                                                                                                                                                                               |
| **Leverage search modes**          | In Splunk Web, “Fast” mode returns only essential fields and suppresses event lists, trading detail for speed—ideal for iterative pivoting. “Verbose” shows every field/value; reserve it for final evidence review. ([Splunk Documentation][6]) |                                                                                                                                                                                               |
| **Keep pipes ordered by cost**     | Low‑cost filters (`where`, `search`, `dedup`) should precede high‑cost commands (`stats`, `sort`). This minimises the dataset each expensive stage must process. ([Splunk Documentation][5])                                                     |                                                                                                                                                                                               |
| **Reuse work via acceleration**    | For recurring investigations (e.g., daily malware beaconing checks), enable *report acceleration* or build *summary indexes* so later queries read rolled‑up results instead of raw logs. ([Splunk Documentation][6])                            |                                                                                                                                                                                               |
| **Document searches**              | Add \`                                                                                                                                                                                                                                           | comment "Ticket #123 – Initial triage of suspicious VPN logins"\` inside searches; comments are ignored at runtime but preserved in history for chain‑of‑custody. ([Splunk Documentation][1]) |

---

#### Quick forensic workflow example

```spl
index=win_security EventCode=4625  /* failed logons */
| dedup 5 user, src_ip              /* collapse brute‑force noise */
| eval utc_time=strftime(_time,"%Y-%m-%dT%H:%M:%SZ")
| stats count AS failures, values(src_ip) AS hosts by user
| where failures > 10
| sort -failures
```

This single SPL stanza answers *“Which accounts had >10 failed logons, from which hosts, and when?”*—a common intrusion‑detection task—while following the optimisation principles above.

---

**Further reference**

* **SPL Search Reference manual** – exhaustive syntax and examples for every command. ([Splunk Documentation][7])
* **Quick tips for optimisation** – concise checklist to keep searches performant. ([Splunk Documentation][5])
* **Anatomy of a search** – deeper dive into parsing order, fields, and quoting rules. ([Splunk Documentation][1])

These official Splunk documents are updated for Splunk Enterprise 9.4 (2025‑06‑14 build) and are considered the canonical, citable source set for forensic workflows.

[1]: https://docs.splunk.com/Documentation/Splunk/9.4.2/Search/Aboutsearchlanguagesyntax?utm_source=chatgpt.com "Anatomy of a search - Splunk Documentation"
[2]: https://docs.splunk.com/Documentation/Splunk/9.4.2/Search/Aboutthesearchlanguage?utm_source=chatgpt.com "About the search language - Splunk Documentation"
[3]: https://docs.splunk.com/Documentation/SCS/current/SearchReference/StatsCommandOverview?utm_source=chatgpt.com "stats command: Overview, syntax, and usage - Splunk Documentation"
[4]: https://docs.splunk.com/Documentation/Splunk/9.3.2/SearchReference/Eval?utm_source=chatgpt.com "eval | Splunk Docs"
[5]: https://docs.splunk.com/Documentation/Splunk/9.4.2/Search/Quicktipsforoptimization?utm_source=chatgpt.com "Quick tips for optimization - Splunk Documentation"
[6]: https://docs.splunk.com/Documentation/Splunk/9.4.2/Search/Quicktipsforoptimization "Quick tips for optimization - Splunk Documentation"
[7]: https://docs.splunk.com/Documentation/Splunk/9.4.2/SearchReference/WhatsInThisManual?utm_source=chatgpt.com "Welcome to the Search Reference - Splunk Documentation"


## 5. Creating Dashboards and Visualizations

## **1. Introduction to Dashboards**

### Purpose and Benefits of Dashboards

| Purpose                    | Description                                                                                                         | Forensic Use Case                                                                    |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Data Summarization**     | Dashboards provide a visual interface to summarize large volumes of log/event data into easily digestible insights. | Track failed logins, lateral movement, or unusual file access across multiple hosts. |
| **Investigation Support**  | Analysts can correlate events across time, host, and user dimensions to identify suspicious patterns.               | View real-time attack timelines, compromised accounts, or malware communication.     |
| **Operational Visibility** | Dashboards monitor security status and forensics KPIs over time.                                                    | E.g., track host-based intrusion detection logs across the enterprise.               |
| **Automation and Sharing** | Dashboards are shareable and update automatically.                                                                  | Present investigation progress to legal/compliance teams without exporting data.     |

> **Official Reference**:
> [Splunk Dashboards and Visualizations Manual – About Dashboards](https://docs.splunk.com/Documentation/Splunk/latest/Viz/Aboutdashboardpanels)

---

### Types of Visualizations in Splunk

Splunk supports many visualization types through Simple XML and Dashboard Studio:

| Type                      | Description                                        | Forensics Usage                                         |
| ------------------------- | -------------------------------------------------- | ------------------------------------------------------- |
| **Single Value**          | Displays a single stat like `count`, `avg`, `sum`. | Show number of compromised users or malware detections. |
| **Tables**                | Display event or field data in rows/columns.       | Summarize endpoint alerts, file hashes, etc.            |
| **Line and Area Charts**  | Show changes over time.                            | Timeline of login attempts, beaconing patterns.         |
| **Bar and Column Charts** | Compare counts across categories.                  | Most targeted hosts, top event codes.                   |
| **Pie Charts**            | Visualize proportions.                             | % of attacks by protocol or country.                    |
| **Choropleth Maps**       | Geographic heatmaps.                               | Geolocation of attacker IPs.                            |
| **Treemaps**              | Hierarchical data layout.                          | Nested grouping of threat categories.                   |

> [Splunk Visualization Reference](https://docs.splunk.com/Documentation/Splunk/latest/Viz/PanelreferenceforSimplifiedXML)

---

## **2. Building a Dashboard**

### Adding Panels and Visualizations

| Task                     | Description                                                                                         | Source                                                                            |                          |
| ------------------------ | --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- | ------------------------ |
| **Create New Dashboard** | Go to *Search & Reporting* app → Dashboards → *Create New Dashboard* (Classic or Dashboard Studio). | [Splunk Docs](https://docs.splunk.com/Documentation/Splunk/latest/Viz/Dashboards) |                          |
| **Add a Panel**          | You can add a saved search or inline SPL query as a panel. Supports charts, tables, maps.           | Example: \`index=fw                                                               | stats count by src\_ip\` |
| **Panel Options**        | Title, description, drilldown behavior, refresh interval, and visualization type.                   |                                                                                   |                          |

> You can also convert an existing search into a dashboard panel by clicking **Save As > Dashboard Panel** from the Search view.

### Customizing Layout and Appearance

| Option             | Classic Dashboard            | Dashboard Studio                           |
| ------------------ | ---------------------------- | ------------------------------------------ |
| **Layout**         | Grid-based (rows and panels) | Absolute positioning or canvas-like layout |
| **Themes**         | Light or dark themes         | Multiple visual themes and custom colors   |
| **Custom CSS/JS**  | Limited support via web app  | Rich support in Studio                     |
| **Icons & Shapes** | Not supported                | Available in Studio                        |

> [Building Dashboards in Studio](https://docs.splunk.com/Documentation/Splunk/latest/Viz/Buildandeditdashboardswithdashboardstudio)

---

## **3. Advanced Dashboard Features**

### Using Tokens and Inputs

Tokens make dashboards dynamic and reusable by letting users interact with the data via inputs:

| Input Type                     | Example                                           | Use Case                                 |
| ------------------------------ | ------------------------------------------------- | ---------------------------------------- |
| **Dropdown**                   | Select `hostname`, `user`, or `index`             | Filter data by selected host or user     |
| **Text Box**                   | Enter a string (e.g., a filename)                 | Manually search a specific IOC           |
| **Time Picker**                | Select time range                                 | Limit forensic scope to relevant windows |
| **Radio Buttons / Checkboxes** | Choose between views (e.g., overview vs detailed) | Interactive triage dashboards            |

Tokens (like `$host$`, `$user$`) can be embedded in SPL queries, panel titles, or URLs.

> [Dashboard Tokens Documentation](https://docs.splunk.com/Documentation/Splunk/latest/Viz/tokens)

---

### Dynamic Drilldowns and Interactivity

| Feature                              | Description                                                                     | Forensic Benefit                                                |
| ------------------------------------ | ------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| **Drilldowns**                       | Click on a chart/table to pass values to other panels/pages.                    | Click a suspicious IP and open full logs or threat intel panel. |
| **Link to another dashboard/report** | Panels can redirect to other dashboards with parameters (tokens).               | Link summary to case-specific timelines.                        |
| **Custom JavaScript (Advanced)**     | In Classic dashboards only, you can write custom JS to do complex UI behaviors. | Show/hide panels based on security alerts.                      |

**Example Drilldown Scenario:**

* A pie chart shows top 5 attack sources.
* Clicking on one source sets a token (`$src_ip$`) and opens a new panel showing all logs from that IP.

> [Enable Drilldowns](https://docs.splunk.com/Documentation/Splunk/latest/Viz/DrilldownIntro)

---

## Summary

| Section                                                | Key Takeaway                                                                                |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------- |
| **Dashboards are critical in forensic investigations** | They support real-time insights, pattern recognition, and help in briefing decision-makers. |
| **Panel creation and layout is modular**               | You can build custom views using SPL + tokens, especially in Dashboard Studio.              |
| **Advanced features like tokens and drilldowns**       | Allow you to build interactive, investigative workflows that reduce analysis time.          |

---

## Recommended Official Resources

* [Splunk Dashboards and Visualizations Manual](https://docs.splunk.com/Documentation/Splunk/latest/Viz/Aboutvisualizations)
* [Splunk Dashboard Studio Overview](https://docs.splunk.com/Documentation/Splunk/latest/Viz/DashboardStudio)
* [Dashboard Examples App (on Splunkbase)](https://splunkbase.splunk.com/app/1603/)


## 6. Alerts and Reporting

## **1. Creating Alerts in Splunk**

### Defining Alert Conditions

An **alert** in Splunk is a saved search that monitors for specific conditions and triggers an action when those conditions are met. In digital forensics, alerts often flag indicators of compromise (IOCs) or suspicious behavior.

| Step                       | Details                                                                                                                                                                                | Forensic Use Case                                                 |                                                      |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------- |
| **Write SPL search**       | Create a search query that detects suspicious patterns.<br>Example:<br>\`index=wineventlog EventCode=4625                                                                              | stats count by user\`                                             | Detect multiple failed login attempts (brute force). |
| **Set Trigger Conditions** | Alerts can trigger when:<br>• search returns results<br>• a specific number is exceeded<br>• a field matches a pattern<br>• a statistical threshold is crossed                         | “Trigger if failed login count > 10 for same user within 5 mins.” |                                                      |
| **Choose Trigger Type**    | • **Per-Result** – triggers once for *each* event (e.g., every file delete).<br>• **Once** – triggers for overall condition match.<br>• **Rolling Window** – tracks changes over time. | Helps distinguish between single malicious acts and trends.       |                                                      |
| **Set Time Window**        | Choose a **schedule** and **time range** for search execution (e.g., every 5 min, looking back 10 min).                                                                                | Balances timeliness with data availability.                       |                                                      |

> [Splunk Docs – About Alerts](https://docs.splunk.com/Documentation/Splunk/latest/Alert/Aboutalerts)

---

### Configuring Alert Actions

Splunk supports **built-in and custom alert actions** when a condition is triggered:

| Action Type                      | Description                                                                                          | Forensic Application                                    |
| -------------------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| **Email**                        | Sends alert details to analysts or IR teams. Includes subject, message, search results, and links.   | “Email security@ with list of detected malware hashes.” |
| **Webhook / Script**             | Run custom scripts or send JSON to SOAR, SIEM, or ticketing system (e.g., ServiceNow, Cortex XSOAR). | Automatically escalate high-severity alerts.            |
| **Webhook to Slack or Teams**    | Send alert info to collaboration platforms using Splunk Alert Webhook app or 3rd-party integrations. | Immediate triage in response channels.                  |
| **Add to Triggered Alerts list** | Makes alert show up in the *Alerts* tab in Splunk Web for review.                                    | Great for manual investigation workflows.               |
| **Run a Script**                 | Executes a `.sh`/`.py`/`.bat` script hosted on the Splunk server.                                    | Trigger external forensic tools or write to case logs.  |

> [Configure Alert Actions – Splunk Docs](https://docs.splunk.com/Documentation/Splunk/latest/Alert/Configuringscheduledalerts)

---

## **2. Generating Reports in Splunk**

### Saving Search Results as Reports

A **report** in Splunk is a saved search that returns a static or periodically refreshed view of your data.

| Task                          | Description                                                                               | Forensic Use                                                           |
| ----------------------------- | ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Save a search as a report** | From the search UI, click **Save As > Report**. Assign a title, description, permissions. | Save weekly malware detection summaries or file access logs.           |
| **Format output**             | Choose table, chart, or visualization.<br>Can include formatting for CSV/HTML/PDF output. | Create easily readable records for chain-of-custody or external audit. |
| **Report Permissions**        | Set who can view/run/edit (private, app, global).                                         | Secure confidential investigations from general Splunk users.          |

> [About Reports – Splunk Docs](https://docs.splunk.com/Documentation/Splunk/latest/Report/Aboutreports)

---

### Scheduling and Exporting Reports

| Feature                 | Description                                                                       | Forensic Relevance                                         |
| ----------------------- | --------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| **Scheduled Reports**   | Run searches at specific intervals (e.g., daily, hourly, monthly).                | Daily listing of USB insertions, privileged access events. |
| **Export Formats**      | • CSV<br>• PDF<br>• HTML<br>• JSON<br>Export via UI or automated action.          | Easily attach findings to case notes or legal documents.   |
| **Email Report Output** | Configure scheduled reports to be emailed as attachments.                         | Share routine scans with IR, legal, or compliance teams.   |
| **Summary Indexing**    | Store report results in a summary index for faster future access or dashboarding. | Good for recurring investigations over months of data.     |

> [Schedule Reports – Splunk Docs](https://docs.splunk.com/Documentation/Splunk/latest/Report/Schedulereports)

---

## Example: Creating a Suspicious Login Alert

```spl
index=wineventlog EventCode=4625 OR EventCode=4624
| stats count(eval(EventCode=4625)) AS failed, count(eval(EventCode=4624)) AS success by user
| where failed > 5 AND success >= 1
```

**Purpose**: Detect brute-force attack followed by successful login
**Alert Settings**:

* Type: Scheduled Alert
* Frequency: Every 10 minutes
* Trigger: When search results > 0
* Action: Send email to SOC team and log to file for case tracking

---

## Summary Table

| Feature        | Alerts                                | Reports                                  |
| -------------- | ------------------------------------- | ---------------------------------------- |
| **Purpose**    | Trigger actions based on events       | Present or archive search results        |
| **Real-Time**  | Yes (real-time or scheduled)          | No (scheduled only)                      |
| **Actions**    | Email, script, webhook, log entry     | Email, export, dashboard                 |
| **Use Case**   | Intrusion detection, malware behavior | Weekly summaries, audit logs, compliance |
| **Visibility** | Alert tab or incident workflow        | Report tab or dashboard integration      |

---

## Official Resources

* [Splunk Alerting Manual](https://docs.splunk.com/Documentation/Splunk/latest/Alert/Whatsinthismanual)
* [Splunk Reporting Manual](https://docs.splunk.com/Documentation/Splunk/latest/Report/Whatsinthismanual)
* [Search Examples for Alerts](https://docs.splunk.com/Documentation/Splunk/latest/SearchTutorial/Usesearchalerts)

## 7. Splunk Apps and Add-ons


## **Splunk Apps and Add-ons: Overview, Installation, and Management**

## **What Are Splunk Apps?**

### **Overview of Pre-built Apps and Add-ons**

In Splunk, **Apps** are modular packages that **extend the functionality** of the Splunk platform. They provide domain-specific dashboards, data inputs, configurations, and knowledge objects.

* **Apps** may include:

  * Dashboards
  * Saved searches and reports
  * Event types and field extractions
  * Visualizations and workflow actions

* **Add-ons (TAs - Technology Add-ons)** are a **special subset** of apps:

  * Do not include user interfaces or dashboards.
  * Focus on **data normalization, field extraction**, and **modular inputs**.
  * Enable Splunk to parse data from specific technologies or platforms (e.g., Cisco ASA, AWS, Windows).

- Apps and Add-ons are developed by:

* Splunk Inc.
* Certified third-party vendors
* Community contributors

- [Splunk: What are Apps and Add-ons?](https://docs.splunk.com/Documentation/AddOns/released/Overview/AboutSplunkAdd-ons)

---

### **Examples of Commonly Used Apps**

#### **1. Splunk App for Enterprise Security (ES)**

* A **premium SIEM solution** used for threat detection, security posture assessment, and incident response.
* Includes correlation searches, risk-based alerting, threat intelligence integration.

- [Splunk Enterprise Security](https://www.splunk.com/en_us/software/enterprise-security.html)

---

#### **2. Splunk IT Service Intelligence (ITSI)**

* A **premium monitoring and analytics tool** for IT operations.
* Provides health scores, KPI thresholds, service trees, and predictive analytics.

- [Splunk ITSI](https://www.splunk.com/en_us/software/it-service-intelligence.html)

---

#### **3. Splunk App for Windows Infrastructure**

* Includes dashboards and field extractions for monitoring Active Directory, DNS, DHCP, etc.

- [Windows Infrastructure App on Splunkbase](https://splunkbase.splunk.com/app/1680/)

---

#### **4. Splunk Add-on for Microsoft Cloud Services**

* Enables ingestion of **Azure AD logs, Office 365 logs**, etc.

- [Microsoft Cloud Services Add-on](https://splunkbase.splunk.com/app/3110/)

---

## **Installing and Managing Apps**

### **Finding Apps on Splunkbase**

* **Splunkbase** is the official repository for Splunk apps and add-ons.
* You can search for solutions by category (security, networking, cloud, etc.) or by technology/vendor (e.g., AWS, Cisco, Palo Alto).

- [Visit Splunkbase](https://splunkbase.splunk.com)

Each app listing includes:

* Description and use cases
* Compatibility with Splunk versions
* Installation instructions
* Documentation and support links

---

### **Installation Methods**

#### **Method 1: Install from Splunk Web (UI-Based)**

1. Log into **Splunk Web**.
2. Go to **Apps > Find More Apps**.
3. Search for the app, click **Install**.
4. Enter your Splunk.com credentials if prompted.
5. Restart Splunk if required.

- [UI Install Instructions](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Installanapp)

---

#### **Method 2: Install from File (Manual Upload)**

1. Download the `.tar.gz` app package from Splunkbase.
2. In Splunk Web:

   * Go to **Apps > Manage Apps > Install App from File**.
   * Upload the package and install.
3. Restart Splunk if prompted.

---

#### **Method 3: Install via CLI (For Linux Admins)**

1. Place the downloaded `.tar.gz` in `$SPLUNK_HOME/etc/apps/`.
2. Extract it:

   ```bash
   tar -xvzf appname.tar.gz -C $SPLUNK_HOME/etc/apps/
   ```
3. Restart Splunk:

   ```bash
   $SPLUNK_HOME/bin/splunk restart
   ```

- [CLI Install Guide](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Managingapps)

---

### **Configuring Apps After Installation**

* After installation, go to the **app home page** from the Splunk Web interface.

* Some apps may require:

  * **Data input configuration** (e.g., enabling APIs, setting tokens)
  * **Credential management** (e.g., API keys for cloud apps)
  * **Modular input setup**
  * Selecting indexes and source types

* Always **read the app’s documentation** on Splunkbase to ensure proper setup.


## 8. Splunk Administration

## **User Management**

### **Creating and Managing User Roles**

Splunk uses **Role-Based Access Control (RBAC)** to manage permissions. Each user is assigned to one or more **roles**, and each role defines:

* What indexes the user can search
* What capabilities (permissions) they have (e.g., `schedule_search`, `edit_user`)
* What apps they can access

Splunk includes **built-in roles** like:

* `admin`: Full privileges across the system.
* `power`: Can schedule searches and create alerts/reports.
* `user`: Limited access to search and dashboards.
* `can_delete`: Allows deleting indexed data.

You can create **custom roles** via:

* Splunk Web:
  `Settings > Roles > New Role`
* CLI or `authorize.conf` configuration file.

- [Roles and Capabilities](https://docs.splunk.com/Documentation/Splunk/latest/Security/Aboutusersandroles)

---

### **Assigning Permissions**

You can control access to:

* **Indexes**: Limit users to specific data domains (e.g., only access `security_logs`)
* **Apps**: Grant access only to designated apps
* **Knowledge Objects**: Permissions for saved searches, reports, dashboards

To assign users and roles:

* `Settings > Users > New User`
* Assign a role and password
* Choose default app and time zone

- [Add and Manage Users](https://docs.splunk.com/Documentation/Splunk/latest/Security/Addandeditusers)

---

## **Managing Indexes**

### **Creating and Maintaining Indexes**

An **index** in Splunk is a logical repository where events are stored after being ingested and parsed.

#### **Types of Indexes**:

* **Default Index (`main`)** – Used if no index is specified.
* **Custom Indexes** – Created to separate data by source, function, or sensitivity.

To create an index:

* Go to `Settings > Indexes > New Index`
* Define:

  * Index name
  * Home path (where data is stored)
  * Cold path (for older data)
  * Thawed path (for restored data)

Alternatively, use CLI:

```bash
$SPLUNK_HOME/bin/splunk add index <index_name>
```

- [Managing Indexes](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Aboutindexes)

---

### **Understanding Index Retention Policies**

Indexes store data in **buckets**: hot → warm → cold → frozen. Each stage has a role in managing data lifecycle.

Key retention parameters:

* `maxTotalDataSizeMB`: Limits total disk space per index.
* `frozenTimePeriodInSecs`: Defines how long data is retained before deletion or archival.
* `coldToFrozenScript`: Optionally export or archive data before deletion.

Frozen data is deleted **unless** you configure thawing/restoration policies.

- [Index Retention Settings](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Setretentionforindexes)

---

## **Monitoring and Troubleshooting**

### **Using the Monitoring Console (MC)**

The **Monitoring Console**, formerly called the **DMC (Distributed Management Console)**, provides a dashboard-based view of Splunk’s internal health and performance.

Access it via:

```
Settings > Monitoring Console
```

Key views include:

* **Indexing performance**
* **Search performance**
* **Forwarder monitoring**
* **License usage**
* **Resource usage** (CPU, memory, disk)

Modes:

* **Standalone Mode** – For single Splunk instances.
* **Distributed Mode** – For multi-instance deployments.

- [Monitoring Console Overview](https://docs.splunk.com/Documentation/Splunk/latest/DMC/AbouttheMonitoringConsole)

---

### **Troubleshooting Common Issues**

Here are some of the most common Splunk issues and resolutions:

| Issue                              | Troubleshooting Tips                                                                    |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| **Data not appearing in searches** | Verify data input, sourcetype, and index assignment. Use `index=*` for testing.         |
| **Disk space usage too high**      | Check retention settings and index sizes (`Settings > Indexes`).                        |
| **Search is slow**                 | Optimize SPL queries with filters early in the pipeline. Avoid `index=*` in production. |
| **License warnings/errors**        | Use Monitoring Console > License Usage to identify violations.                          |
| **Forwarders not sending data**    | Verify forwarder connection (`splunk list forward-server`) and network ports.           |

Splunk logs (for deeper analysis):

* `$SPLUNK_HOME/var/log/splunk/`

  * `splunkd.log`: Core system logs
  * `metrics.log`: System resource stats
  * `web_service.log`: Web UI logs

- [Troubleshooting Guide](https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/TroubleshootSplunkperformance)


## 9. Advanced Topics
    - **Using Splunk Forwarders**
      - Types of forwarders: Universal and Heavy.
      - Configuring forwarders to send data to indexers.
    - **Data Enrichment**
      - Using lookups and field extractions.
      - Enriching data with external sources.
    - **Performance Tuning**
      - Optimizing Splunk configurations.
      - Managing large-scale deployments.

## 10. Conclusion
    - **Recap of Key Concepts**
      - Summary of what was covered in the tutorial.
    - **Next Steps**
      - Resources for further learning (Splunk documentation, community forums, etc.).
      - Certifications and training programs.

## 11. Appendix
    - **Glossary of Terms**
      - Definitions of key Splunk terms.
    - **References**
      - Links to official Splunk documentation and resources.
    - **Sample Data**
      - Example datasets for practice.
