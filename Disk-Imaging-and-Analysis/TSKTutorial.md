# Tutorial on Analyzing a Disk Image using TSK 

---

### **Step 1: Install The Sleuth Kit (TSK)**
TSK is a command-line tool, so ensure it is installed on your system.

#### **For Debian/Ubuntu-based systems:**
```bash
sudo apt update
sudo apt install sleuthkit
```

#### **For RHEL/CentOS-based systems:**
```bash
sudo yum install sleuthkit
```

#### **For macOS:**
Using Homebrew:
```bash
brew install sleuthkit
```

#### **For Windows:**
Download from [sleuthkit.org](https://www.sleuthkit.org/) and follow the installation instructions.

---

### **Step 2: Obtain a Disk Image**
For educational purposes, you can download a sample disk image from forensic CTF platforms or repositories.  

#### **Recommended Resources for Disk Images:**
1. **Digital Corpora**:  
   [https://digitalcorpora.org/corpora/disk-images](https://digitalcorpora.org/corpora/disk-images)  
   They provide publicly available disk images for forensic analysis.

2. **Forensic CTF Platforms**:  
   Websites like *Cyber Defenders* or *CTF competitions* often host forensic challenges with downloadable disk images.

3. **Test Images from NIST CFReDS Project**:  
   [https://www.cfreds.nist.gov/](https://www.cfreds.nist.gov/)  

---

### **Step 3: Prepare for Analysis**
1. Download the disk image (e.g., `disk.img`) to a directory.
2. Verify the image integrity using a hash tool:
   ```bash
   sha256sum disk.img
   ```

---

### **Step 4: Inspect the Disk Image Structure**
TSK provides tools to examine the structure of the image.  

#### **Determine the Partition Table:**
```bash
mmls disk.img
```
- This command shows partition offsets, types, and sizes.  
- Note the offset value of the partitions of interest.

#### **Example Output:**
```
DOS Partition Table
Offset    Sector    Start      End         Description
0000000000         0          63          Primary Partition
0000000063         63         15663106    NTFS (0x07)
```
Here, the NTFS partition starts at sector 63.

---

### **Step 5: Extract and Analyze Filesystem Data**
1. **List Filesystem Details:**
   Use `fsstat` to view filesystem metadata:
   ```bash
   fsstat -o 63 disk.img
   ```

2. **List Files:**
   Use `fls` to list files and directories:
   ```bash
   fls -o 63 disk.img
   ```
   - Outputs file names, inode numbers, and other details.

3. **Recover Specific Files:**
   Use `icat` to recover files by their inode number:
   ```bash
   icat -o 63 disk.img 12345 > recovered_file.txt
   ```

4. **Extract Deleted Files:**
   Use `fls` to identify deleted files (`D/d`) and recover them:
   ```bash
   fls -o 63 -r disk.img
   icat -o 63 disk.img <inode> > deleted_file.txt
   ```

---

### **Step 6: Analyze File Content**
#### **Examine Strings in a File:**
```bash
strings recovered_file.txt
```

#### **Analyze Metadata of Files:**
```bash
istat -o 63 disk.img <inode>
```

---

### **Step 7: Advanced Analysis**
#### **Search for Known Hashes:**
Use `hfind` to check against hash databases for known files:
1. Create a hash database:
   ```bash
   md5sum <file> >> hashdb.txt
   ```
2. Search against the database:
   ```bash
   hfind hashdb.txt <md5>
   ```

#### **Analyze Unallocated Space:**
Use `blkls` to carve unallocated space:
```bash
blkls -o 63 disk.img > unallocated.raw
```
You can then analyze `unallocated.raw` with tools like *bulk_extractor* or *photorec*.

---

### **Step 8: Generate a Final Report**
#### **Compile Evidence:**
1. Consolidate findings from commands (e.g., recovered files, filesystem stats).
2. Include hashes, timestamps, and metadata.

#### **Use Automation Tools:**
Consider TSK’s graphical interface, *Autopsy*, for easier reporting.

---

### **Expected End Result**
After completing the analysis, you should have:
1. **File Recovery**: Recovered files, including deleted ones.
2. **Metadata**: Detailed information about files, timestamps, and activity.
3. **Anomalies**: Evidence of tampering, malware, or suspicious activity.
4. **Report**: A structured forensic report detailing your findings.

