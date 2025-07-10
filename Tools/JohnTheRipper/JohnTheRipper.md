# JohnTheRipper - Outline

## **Module 1: Introduction to John the Ripper**

### 1.1 Overview and History

* Origin and creator: Openwall
* Original purpose: Unix password cracking
* Evolution: Community-enhanced Jumbo version
* Comparison with tools like Hashcat, Hydra

### 1.2 Use Cases

* Pentesting password security
* Digital forensics investigations
* Hash type identification and analysis
* Password auditing and policy enforcement

---

## **Module 2: Installation and Setup**

### 2.1 Installing the Core and Jumbo Versions

* Differences between core and Jumbo
* Installation on:

  * **Linux**: From source and package managers (`apt`, `yum`)
  * **Windows**: Precompiled binaries
  * **macOS**: Homebrew or from source

### 2.2 Verifying Installation

* Checking version: `john --list=build-info`
* Ensuring dependencies (e.g., OpenSSL, zlib)

### 2.3 Configuration Files

* Location of `john.conf` or `john.ini`
* Customizing configurations (rules, character sets)

---

## **Module 3: Core Concepts**

### 3.1 Understanding Hashes

* What is a password hash?
* Common formats: MD5, SHA1, bcrypt, NTLM, etc.

### 3.2 Supported Hash Types

* Listing formats: `john --list=formats`
* Format groups: raw, dynamic, crypt-style, proprietary software

### 3.3 Modes of Operation

* **Single Crack Mode**
* **Wordlist Mode**
* **Incremental Mode**
* **External Mode** (scriptable cracking logic)

---

## **Module 4: Basic Usage and Syntax**

### 4.1 Creating and Preparing Input

* `unshadow` for combining `/etc/passwd` and `/etc/shadow`
* `zip2john`, `rar2john`, `pdf2john`, etc.
* `john --show` to reveal cracked passwords
* Input file formats (username\:hash)

### 4.2 Basic Commands

* Syntax: `john [options] [hash_file]`
* Wordlist-based cracking
* Incremental brute force cracking
* Cracking specific formats using `--format=`

---

## **Module 5: Advanced Techniques**

### 5.1 Rule-Based Cracking

* Rule sets in `john.conf`
* Custom rule creation
* `--rules` and `--rules=SECTION` options

### 5.2 External Cracking Scripts

* Writing Lua or custom scripts
* Using external C-like mini-language

### 5.3 Session Management

* Saving sessions: `--session=name`
* Restoring/resuming: `--restore=name`
* Pausing/resuming long-running tasks

### 5.4 Cracking Performance Optimization

* Using `--fork` (multi-process parallelism)
* Using `--format` to specify optimized formats
* GPU acceleration (via Jumbo and OpenCL builds)

---

## **Module 6: Format-Specific Cracking**

### 6.1 Cracking Windows Hashes

* NTLM hashes from SAM file
* LM hashes
* Tools: `samdump2`, `pwdump`, `hashdump` (Metasploit)

### 6.2 Cracking Archive and Document Hashes

* `zip2john`, `rar2john`, `office2john`, `pdf2john.py`
* Supported formats and limitations

### 6.3 Cracking Web App and CMS Hashes

* WordPress (md5), Joomla, phpBB
* Dumping hashes using forensic tools

---

## **Module 7: Hash Identification**

### 7.1 Using `--list=formats`

### 7.2 Identifying unknown hashes

* Using online tools like [https://hashes.com/en/tools/hash\_identifier](https://hashes.com/en/tools/hash_identifier)
* Comparison with `hashid`, `hash-identifier`, and `hashcat --example-hashes`

---

## **Module 8: Wordlists and Custom Dictionaries**

### 8.1 Using Default Wordlists

* Location of built-in lists
* Using `--wordlist=FILE`

### 8.2 Creating Your Own Wordlists

* `cewl`, `crunch`, and `john --make-charset`
* Sorting and optimizing with `uniq`, `sort`, `john --dupe-suppression`

---

## **Module 9: Scripting and Automation**

### 9.1 Bash and Python Automation

* Looping over multiple hash files
* Automating report generation

### 9.2 Using John with Other Tools

* Integration with Metasploit, Volatility, etc.
* Feeding JtR output into forensic workflows

---

## **Module 10: Ethical and Legal Considerations**

### 10.1 Responsible Usage

* Legal contexts (with vs. without authorization)
* Compliance with organizational security policies
* Certifications and red team rules of engagement

### 10.2 Logging and Auditing

* How to log cracking attempts
* Reporting cracked passwords responsibly

---

## **Module 11: Troubleshooting and Limitations**

### 11.1 Common Errors

* Format mismatch
* Invalid hash file structure
* Missing wordlist or config

### 11.2 Performance Bottlenecks

* CPU vs GPU vs multi-core
* When John the Ripper is not ideal (use Hashcat instead)

---

## **Module 12: Practical Labs and Exercises**

### 12.1 Cracking a Unix Shadow File

### 12.2 Cracking NTLM Hashes from Windows

### 12.3 Cracking Encrypted Zip Files

### 12.4 Using Rule-Based Cracking on Custom Wordlist

### 12.5 Automating Crack and Report Pipeline

---

## **Module 13: Further Reading and Resources**

### 13.1 Official Resources

* [Openwall JtR Official Site](https://www.openwall.com/john/)
* [GitHub Repository](https://github.com/openwall/john)

### 13.2 Community and Forums

* Openwall mailing list
* Hashes.org
* Reddit /r/HowToHack and NetSec

### 13.3 Related Tools

* **Hashcat**
* **Hydra**
* **CrackStation**
* **CeWL**
* **Crunch**

---

## inal Project (Optional Capstone)

Build a complete password auditing pipeline using:

* Multiple hash formats
* Wordlist generation
* Rule-based attacks
* Session control and logging
* Output generation and reporting

