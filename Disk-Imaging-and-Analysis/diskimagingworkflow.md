# **Step-by-Step Tutorial on Disk Imaging Workflow Using `dd`, `dcfldd`, and Guymager**

## **1. Preparation**

### **1.1 Ensure Proper Tools Are Installed**
- For `dd` and `dcfldd`:
  ```bash
  sudo apt update
  sudo apt install dcfldd -y
  ```
- For Guymager:
  ```bash
  sudo apt update
  sudo apt install guymager -y
  ```

### **1.2 Setup Write-Blocking**
- Use a hardware write-blocker (e.g., Tableau or WiebeTech).
- If unavailable, mount the drive read-only in Linux:
  ```bash
  sudo mount -o ro /dev/sdX /mnt
  ```
  Replace `/dev/sdX` with your target device.

### **1.3 Identify the Source Device**
- Use the `lsblk` command to list all connected drives:
  ```bash
  lsblk
  ```
- Example output:
  ```
  NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
  sda      8:0    0   1TB  0 disk
  sdb      8:16   1   16GB  0 disk
  ```
  Here, `/dev/sdb` is the target drive (verify carefully to avoid mistakes).

---

## **2. Disk Imaging with `dd`**

### **2.1 Create a Disk Image**
- The basic `dd` syntax:
  ```bash
  sudo dd if=/dev/sdX of=/path/to/output/image.img bs=4M conv=noerror,sync
  ```
  **Explanation:**
  - `if=/dev/sdX`: Input file (source disk).
  - `of=/path/to/output/image.img`: Output file (image file location).
  - `bs=4M`: Block size (4MB for better performance).
  - `conv=noerror,sync`: Continue on errors and sync writes.

### **2.2 Verify the Integrity**
- Generate an MD5 or SHA256 hash of the original disk and image:
  ```bash
  md5sum /dev/sdX
  md5sum /path/to/output/image.img
  ```
  Compare the hash values to ensure integrity.

---

## **3. Disk Imaging with `dcfldd`**

`dcfldd` is an enhanced version of `dd` that adds features like hashing.

### **3.1 Create a Disk Image with Hashing**
- Example command:
  ```bash
  sudo dcfldd if=/dev/sdX of=/path/to/output/image.img hash=md5 log=/path/to/logfile.txt
  ```
  **Explanation:**
  - `if=/dev/sdX`: Input file (source disk).
  - `of=/path/to/output/image.img`: Output file (image file location).
  - `hash=md5`: Calculate an MD5 hash during imaging.
  - `log=/path/to/logfile.txt`: Save output log for documentation.

### **3.2 Split Large Images**
- To split an image into smaller files:
  ```bash
  sudo dcfldd if=/dev/sdX of=/path/to/output/image.img split=1G hash=sha256
  ```
  This splits the image into 1GB chunks with SHA256 hashing.

---

## **4. Disk Imaging with Guymager**

Guymager provides a graphical interface for imaging, suitable for users who prefer a GUI.

### **4.1 Launch Guymager**
- Open Guymager with root privileges:
  ```bash
  sudo guymager
  ```

### **4.2 Select the Target Device**
- Identify and select the device to image from the list in Guymager.
- Verify the size and name of the device to ensure you’ve selected the correct one.

### **4.3 Configure Imaging Options**
- Right-click on the device and choose **"Acquire Image"**.
- Fill in the required details:
  - **File name:** Choose the output location and name for the image.
  - **Format:** Raw (DD) or EWF (Expert Witness Format).
  - **Compression:** Optional (default is none for raw images).
  - **Hashing:** Enable MD5 and/or SHA256 for integrity verification.

### **4.4 Start Imaging**
- Click **"Start"** to begin the imaging process.
- Guymager will show real-time progress and provide a detailed log.

### **4.5 Verify the Image**
- After imaging, Guymager automatically generates hash values.
- Verify the hashes against the original disk.

---

## **5. Documentation and Storage**

### **5.1 Documentation**
- Record the following details in your forensic report:
  - Imaging tool used.
  - Command or process details.
  - Source device details (size, serial number, etc.).
  - Hash values of the source and image.

### **5.2 Secure Storage**
- Store the disk image in a secure location with restricted access.
- Back up the image to multiple locations if necessary.

---

## **6. Practical Tips**
- Always use a write-blocker to preserve the integrity of the evidence.
- Double-check device paths (`/dev/sdX`) to avoid overwriting critical data.
- Use larger block sizes (`bs=64M`) for faster imaging, but ensure your system supports it.
