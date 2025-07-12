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
    - **Search Processing Language (SPL) Basics**
      - Syntax and structure of SPL.
      - Common commands: `search`, `stats`, `table`, `eval`.
    - **Using Search Commands**
      - Filtering and transforming data.
      - Aggregating and visualizing results.
    - **Search Best Practices**
      - Optimizing search performance.
      - Using time range pickers and search modes.

## 5. Creating Dashboards and Visualizations
    - **Introduction to Dashboards**
      - Purpose and benefits of dashboards.
      - Types of visualizations available in Splunk.
    - **Building a Dashboard**
      - Adding panels and visualizations.
      - Customizing layout and appearance.
    - **Advanced Dashboard Features**
      - Using tokens and inputs.
      - Dynamic drilldowns and interactivity.

## 6. Alerts and Reporting
    - **Creating Alerts**
      - Defining alert conditions.
      - Configuring alert actions (email, scripts, etc.).
    - **Generating Reports**
      - Saving search results as reports.
      - Scheduling and exporting reports.

## 7. Splunk Apps and Add-ons
    - **What are Splunk Apps?**
      - Overview of pre-built apps and add-ons.
      - Examples: Splunk App for Enterprise Security, Splunk ITSI.
    - **Installing and Managing Apps**
      - Finding apps on Splunkbase.
      - Installing and configuring apps.

## 8. Splunk Administration
    - **User Management**
      - Creating and managing user roles.
      - Assigning permissions.
    - **Managing Indexes**
      - Creating and maintaining indexes.
      - Understanding index retention policies.
    - **Monitoring and Troubleshooting**
      - Using the Monitoring Console.
      - Troubleshooting common issues.

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
