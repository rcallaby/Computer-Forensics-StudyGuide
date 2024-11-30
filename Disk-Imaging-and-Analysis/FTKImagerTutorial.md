# FTK Imager Tutorial

---

### **Step 1: Install and Set Up FTK Imager**
1. **Download FTK Imager**:
   - Go to the official website of **AccessData** and download the latest version of FTK Imager.
   - Ensure you download it from a trusted source to maintain the integrity of the tool.

2. **Install FTK Imager**:
   - Run the installer and follow the prompts. Alternatively, you can use the portable version.
   - Once installed, launch the application.

---

### **Step 2: Understand the Interface**
- Familiarize yourself with the key areas:
  - **File Menu**: Options to create disk images, view files, export evidence, etc.
  - **Evidence Tree**: Displays file system structures.
  - **Hex View / Text View**: Allows you to view file contents in hexadecimal or text formats.
  - **Bookmarks**: For tagging significant evidence.

---

### **Step 3: Acquire a Forensic Image**
To create a forensic image of a disk or partition:
1. **Open the Create Disk Image Wizard**:
   - Click on **File** > **Create Disk Image**.

2. **Select the Source Type**:
   - Choose the appropriate source (Physical Drive, Logical Drive, Image File, or Contents of a Folder).
   - For example, select "Physical Drive" to acquire an entire disk.

3. **Select the Source Drive**:
   - Select the disk you wish to image. Carefully identify the target based on its size, label, or identifier.

4. **Choose the Image Destination**:
   - Specify the format: 
     - **E01 (Expert Witness Format)**: Allows compression and metadata.
     - **RAW (dd format)**: A bit-by-bit copy of the source.
     - Other formats like SMART or AFF, depending on your needs.
   - Choose the destination folder to save the image file.

5. **Add Case Information** (Optional):
   - Add case-specific metadata like investigator name, case number, evidence number, etc.

6. **Set Imaging Options**:
   - Enable **Verify Image After Creation**: Ensures the hash of the image matches the source.
   - Choose **Compression Level** for E01 files (if applicable).

7. **Start Imaging**:
   - Click **Start** and monitor the imaging process.
   - FTK Imager will generate a hash value (MD5/SHA1) for the source and the image to ensure integrity.

---

### **Step 4: Verify the Image**
- FTK Imager calculates the hash values during acquisition.
- Compare the source and image hash values to ensure they match.
- Save the hash report for documentation purposes.

---

### **Step 5: Load and Analyze Evidence**
1. **Add an Evidence Item**:
   - Click on **File** > **Add Evidence Item**.
   - Choose the type of evidence (e.g., Image File or Physical Drive).
   - Load the previously created image file or connect a drive for live analysis.

2. **Explore the File System**:
   - Navigate through the evidence tree to examine directories, files, and metadata.
   - Look for significant artifacts like:
     - Deleted files
     - User documents
     - Log files
     - Browser history

3. **File Analysis**:
   - Use the **File Preview Pane** to view files.
   - Use the **Hex/Text View** for deeper insights, such as searching for specific patterns or recovering partial files.

---

### **Step 6: Recover Deleted Files**
1. Navigate to the unallocated space or slack space.
2. Identify potential remnants of deleted files.
3. Right-click on the file and choose **Export Files** to recover them.

---

### **Step 7: Bookmark and Document Findings**
1. Use **Bookmarks**:
   - Highlight important evidence like files, strings, or artifacts.
   - Add comments to explain why a file or section is significant.

2. Export the Bookmarks:
   - Generate a report including all bookmarked items and their descriptions.

---

### **Step 8: Export Evidence**
1. **Export Files**:
   - Select the files or folders you need, right-click, and choose **Export Files**.
   - Save them to a secure location for further analysis.

2. **Generate Reports**:
   - FTK Imager allows you to create detailed reports that include metadata, hash values, and analysis notes.

---

### **Step 9: Perform Advanced Analysis (Optional)**
- Use FTK Imager alongside other tools like:
  - **Autopsy**: For deeper analysis and timeline creation.
  - **Volatility**: For memory forensics.
  - **EnCase** or **X-Ways**: For more extensive investigations.

---

### **Step 10: Maintain Evidence Integrity**
1. **Chain of Custody**:
   - Document every action performed, including imaging, analysis, and export processes.
   - Store hash values in your case notes.

2. **Store Evidence Securely**:
   - Keep both the original disk/image and working copies in a secure location.

---

### **Best Practices for FTK Imager Use**
- Always **work on a copy** of the image to avoid altering original evidence.
- Use write blockers when accessing physical drives.
- Record every step for reproducibility and legal admissibility.
