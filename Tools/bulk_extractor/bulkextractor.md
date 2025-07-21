# Bulkextractor

## Tutorial Outline: bulk\_extractor

### 1. Introduction

* **What is bulk\_extractor?**
  A C/C++ high-performance forensic scanner that processes disk images, files, or directories *without* parsing file systems—extracts features like emails, URLs, credit cards, etc. ([Forensics Wiki][1], [Digital Corpora][2])
* **Key strengths**

  * Stream-based scanning for high speed (parallelized across cores) ([Simson][3])
  * Optimistic decompression handling ZIP, gzip, RAR, Windows Hibernation, BASE64, etc. ([Digital Corpora][4])
  * Thorough feature detection from compressed/unallocated regions ([Forensics Wiki][1])

### 2. Installation

* **Supported platforms**
  Linux, macOS, Windows (binaries and GUI available)&#x20;
* **Linux/macOS**

  * Download tarball
  * `./configure && make && sudo make install`&#x20;
  * Dependencies: libewf2,(libexpat, libgcrypt, etc.) ([Kali Linux][5])
* **Windows**

  * Use installer `.exe`, includes `BEViewer` GUI ([Digital Corpora][2])

### 3. Running bulk\_extractor

* **Command‑line basics**

  ```
  bulk_extractor -o output_dir input_image_or_file
  ```

  Creates directory of feature files—15–25 files ([Digital Corpora][2])
* **BEViewer GUI**

  * Launch via `BEViewer` (Linux/macOS) or Start Menu (Windows)&#x20;
  * Set input and output, click “Start”

### 4. Feature Extraction & Output

* **What is extracted?**
  Email addresses, domains, IPs, URLs, phone numbers, MAC addresses, credit‑card numbers, EXIF data, ZIP metadata, histograms, stopped items, etc. ([Digital Corpora][4], [YouTube][6])
* **Directory contents**

  * `email.txt`, `ip.txt`, `url.txt`, `ccn.txt`, etc.
  * Histogram files `*_histogram.txt`
  * `report.xml` detailing run metadata ([Forensics Wiki][1])

### 5. Post‑Processing & Analysis

* **Histogram interpretation**
  Identify most frequent features (e.g. recurring email domains)&#x20;
* **bulk\_diff.py**
  Compare runs to detect changes (e.g. before/after) ([Simson][7])
* **Integration & triage use**
  Common in OSINT and forensic triage workflows ([Malware Analysis, News and Indicators][8])

### 6. Advanced Configuration

* **Enabling/disabling scanners**
  Flags: `-e`, `-x`, `--enable_exclusive`, `-f`, `-F` ([Kali Linux][5])
* **Stop lists, wordlists, alert lists**
  Control extraction noise vs. relevance ([SlideServe][9])
* **Performance tuning**

  * Multithreading: `-j` threads, `--no_threads` ([Kali Linux][5])
  * Context margins (`-C`), page sizes (`-G`) ([Kali Linux][5])

### 7. Under the Hood

* **Processing model**
  Bulk stream scanning, 16 MiB pages distributed to threads ([Forensics Wiki][1])
* **Architecture**

  * Sbuf objects, scanner modules, feature recorders, histograms ([Digital Corpora][4])
  * Recursive decompression, compressed/hybrid encoding support&#x20;

### 8. Plugin Development

* **Who it’s for**
  Developers writing custom scanner plug-ins ([Digital Corpora][4])
* **Development steps**

  * Setup build environment
  * Implement `PHASE_STARTUP`, `PHASE_SCAN`, `PHASE_SHUTDOWN` handlers ([Digital Corpora][4])
  * Examples: ASCII, XOR scanners included in manual ([Digital Corpora][4])

### 9. Recent Evolution & Community

* **Modern updates**
  Refactored to C++17, improved multithreading, 75% more throughput ([arXiv][10])
* **Usage in academia & practice**
  Used in case studies (e.g. NPS, FBI triage), part of BitCurator, referenced in OSINT tutorials, Stanford webinars ([Simson][7])


