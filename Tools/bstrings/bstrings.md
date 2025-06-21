# Course Outline: bstrings Tool  

## 1. Introduction to bstrings  

### Overview of bstrings

**bstring** (short for "better string" or "binary string") is an open-source C library designed to make string handling in C safer and more powerful. Traditional C strings (`char *`) are prone to common bugs such as buffer overflows and undefined behaviors due to poor bounds checking and manual memory management. **bstring** provides a managed string type (`bstring`) with accompanying functions for common string operations, reducing these risks.

**Official project page**: [https://github.com/antirez/bstring](https://github.com/antirez/bstring) — (one of the original popular forks; also used in several other C projects).

### Purpose and Use Cases

**Purpose:**

* To replace standard C string handling (`char *`, `strlen`, `strcpy`, etc.) with safer, more powerful abstractions.
* To manage memory internally (similar to higher-level languages), while providing simple APIs for:

  * Concatenation
  * Substrings
  * Searching
  * Tokenization
  * Formatting
  * Comparison
  * Encoding/decoding
  * Binary-safe operations (strings with embedded null bytes)

**Typical Use Cases:**

* **Embedded systems** — when you want safer string handling but can't use C++ STL or third-party frameworks.
* **Security-sensitive code** — to avoid common vulnerabilities such as buffer overflows.
* **Parsing** or **tokenizing text data**.
* **Dynamic text generation** where fixed-size buffers are too limiting.
* Lightweight C projects needing managed string operations without heavy dependencies.

### Installation and Setup

#### Supported Platforms

* **Operating systems**:

  * Linux
  * macOS
  * Windows (via MinGW or MSVC)
  * BSD variants

* **Compilers**:

  * GCC
  * Clang
  * MSVC

* **Languages**:

  * C (C89 and newer)
  * Usable in C++ projects (extern "C")

#### Downloading and Installing

**Option 1: Clone from GitHub**

```bash
git clone https://github.com/antirez/bstring.git
cd bstring
make
sudo make install
```

This will typically install the `bstrlib.h` and `bstrlib.c` (and optionally other headers).

**Option 2: Manual install**

1. Download from GitHub releases or clone the repo.
2. Copy the `bstrlib.h` and `bstrlib.c` files into your project.
3. Add `bstrlib.c` to your build system (e.g., `gcc main.c bstrlib.c -o myprogram`).

**Option 3: Use via package manager** (only on some Linux distributions — check availability)

```bash
# On Debian/Ubuntu systems (if packaged)
sudo apt-get install libbstrlib-dev
```

**Basic Build Example:**

```bash
gcc -o myapp main.c bstrlib.c
```

Or with CMake:

```cmake
add_executable(myapp main.c bstrlib.c)
```

## 2. Understanding Strings in Binary Files  

### What are Strings in Binary Files?

In the context of binary files, **strings** refer to sequences of human-readable characters (such as text, commands, file paths, URLs, and passwords) embedded within otherwise non-human-readable data (machine code, file headers, etc.).

These character sequences:

* May be stored in **plain text** (null-terminated or length-prefixed), or
* May appear in **encoded** or **compressed** forms.
* Can include **ASCII**, **UTF-8**, **UTF-16**, or even custom encodings.

Binary files like executables (`.exe`), object files, memory dumps, disk images, and proprietary file formats often contain such embedded text — left by developers, libraries, compilers, or user data.

**Common string examples in binaries:**

* Developer comments
* Hardcoded credentials
* Debug output
* File paths
* Domain names or IPs
* Error messages
* Command-line options
* Configuration strings

*Tool example*:
The Unix `strings` tool is often used to extract readable strings from binaries:

```bash
strings mybinaryfile.exe
```

---

### ASCII vs Unicode Strings

**ASCII Strings:**

* Represented using the 7-bit **ASCII** encoding standard.
* Basic character set: 128 characters (printable characters + control codes).
* Often used in legacy software, embedded systems, and simple binaries.

**Unicode Strings:**

* Represent text in **multiple languages and symbol sets**.
* Encodings include:

  * **UTF-8** (variable-length, backward-compatible with ASCII)
  * **UTF-16** (common in Windows binaries, 2 or 4 bytes per character)
  * **UTF-32**
* Used in modern applications to support **internationalization** and **complex character sets**.

**Forensic relevance**:
Modern binaries often use **Unicode** internally, especially on Windows. Using a tool that extracts only ASCII strings may miss critical evidence. Forensic tools like **BinText**, **FLOSS**, and **bulk\_extractor** can extract both ASCII and Unicode strings.

---

### Importance of Extracting Strings in Forensics

Extracting strings from binary files is a **critical technique in digital forensics and reverse engineering** because:

1. **Human-readable clues**:

   * Strings can provide **context** about the file’s purpose or contents without needing full disassembly.
   * Reveal file paths, filenames, author names, build timestamps.

2. **Malware analysis**:

   * Identify **command and control (C2)** servers.
   * Discover **hardcoded credentials** or **encryption keys**.
   * See **obfuscation techniques**.

3. **Memory forensics**:

   * Extract remnants of sensitive data (like **passwords**, **email content**) from memory dumps.

4. **Triage and rapid assessment**:

   * Strings extraction is **fast** and helps prioritize deeper analysis.

5. **Evidence of user actions**:

   * Recover **chat messages**, **URLs**, **filenames** from slack space or disk images.

6. **Detection of steganography or covert channels**:

   * Look for **hidden messages** within seemingly random binaries.

**Tools commonly used:**

* `strings` (Linux, Windows)
* FLOSS (FireEye Labs Obfuscated String Solver)
* BinText
* bulk\_extractor
* Ghidra and IDA Pro (built-in string extraction features)
* Volatility (for memory dumps)

---

**Sources:**

* "Practical Malware Analysis" — Sikorski & Honig
* "The Art of Memory Forensics" — Ligh, Case, Levy, Walters
* "File System Forensic Analysis" — Brian Carrier
* FireEye Labs: FLOSS documentation \[[https://github.com/fireeye/flare-floss](https://github.com/fireeye/flare-floss)]
* SANS DFIR materials
* Official Unix `strings` manpage

## 3. Basic Usage of bstrings  

### Command-line Syntax

**bstrings** (the bstrlib C library) is a **programming library**, not a command-line tool — so it does not have a standalone command-line interface or command-line syntax.

However, you can create your own **C programs** that use bstrings functions to extract, manipulate, and filter string data — and those programs can accept command-line arguments, like this:

```bash
./my_bstring_tool inputfile.bin
```

**In summary**: bstrings is used **via C API functions** inside your own programs — it is not an end-user CLI tool like `strings`.

---

### Extracting Strings from a File

To use **bstrings** for extracting string data from a file, you would typically:

1. **Open the file** in binary mode
2. **Read data** into a buffer
3. **Wrap the data** in a `bstring` object (if you want to use bstring functions)
4. **Search for and extract readable strings**

**Example (simplified):**

```c
#include <stdio.h>
#include "bstrlib.h"

int main(int argc, char *argv[]) {
    if (argc < 2) {
        printf("Usage: %s <file>\n", argv[0]);
        return 1;
    }

    FILE *fp = fopen(argv[1], "rb");
    if (!fp) {
        perror("Error opening file");
        return 1;
    }

    // Read file into bstring
    fseek(fp, 0, SEEK_END);
    long size = ftell(fp);
    rewind(fp);

    unsigned char *buffer = malloc(size);
    fread(buffer, 1, size, fp);
    fclose(fp);

    bstring fileData = blk2bstr(buffer, size);
    free(buffer);

    // Now you can manipulate fileData with bstring functions!
    printf("File size: %d bytes\n", blength(fileData));

    bdestroy(fileData);
    return 0;
}
```

### Filtering ASCII and Unicode Strings

**Filtering strings** with bstrings is done **programmatically** by using:

* **Character tests** (`iscntrl()`, `isprint()`, `isascii()`) in C
* Loops through the `bstring` data
* Building new filtered bstrings based on criteria

**Example: Filtering printable ASCII characters:**

```c
bstring printable = bfromcstr("");

for (int i = 0; i < blength(fileData); i++) {
    unsigned char c = bchar(fileData, i);
    if (isprint(c)) {
        bconchar(printable, c);
    }
}

printf("Extracted printable ASCII: %s\n", bdata(printable));
bdestroy(printable);
```

---

**Handling Unicode**:

* bstrings operates on **byte buffers**, so it does not interpret encodings by default.
* If you know the file is **UTF-16**, you would process the data in **2-byte units** and use an external Unicode library (like ICU or libunistring), or write your own handling to convert to UTF-8 `bstring`s.

---

**Summary**:

* **Command-line syntax** — Not applicable (bstrings is a C API)
* **Extracting strings** — Read file into `bstring` and process as needed
* **Filtering ASCII / Unicode** — Loop over `bstring` data and apply filters programmatically

---

**Sources:**

* [https://github.com/antirez/bstring](https://github.com/antirez/bstring)
* bstrlib.h public documentation
* "Secure Coding in C and C++" — Robert C. Seacord
* Common usage in open-source C projects (Redis, embedded software)


## 4. Advanced Features of bstrings  

### Using Options and Flags

Since **bstrings** is a **programming library**, it does not itself support CLI options or flags like `-n`. However — if you write a C program that uses bstrings, you can **implement your own options and flags** by parsing `argc`/`argv`, for example:

```c
./my_bstrings_tool -n 6 -ascii myfile.bin
```

You would then:

* Parse `-n 6` → set a minimum string length filter
* Parse `-ascii` or `-unicode` → set desired character set filtering

You can use `getopt()` or similar for parsing arguments in C.

---

#### Minimum String Length (`-n`)

To implement **minimum string length**, after loading a file into a `bstring`, you would scan for sequences of printable characters, keeping track of their length:

```c
int minlen = 6;  // Example from -n 6
bstring current = bfromcstr("");

for (int i = 0; i < blength(fileData); i++) {
    unsigned char c = bchar(fileData, i);
    if (isprint(c)) {
        bconchar(current, c);
    } else {
        if (blength(current) >= minlen) {
            printf("%s\n", bdata(current));
        }
        btrunc(current, 0);  // Reset
    }
}

if (blength(current) >= minlen) {
    printf("%s\n", bdata(current));
}
bdestroy(current);
```

---

#### Filtering by Character Set

**ASCII filtering** is straightforward with `isprint()` or `isascii()`:

```c
if (isascii(c) && isprint(c)) { ... }
```

**Unicode filtering** is more advanced:

* bstrings operates on **raw bytes**, so it does not parse UTF-16 or UTF-8 by itself.
* You can:

  * Use external Unicode libraries (ICU, libiconv) + bstrings
  * Or scan manually for UTF-16 sequences (Windows memory dumps, PE files)

---

### Extracting Strings from Memory Dumps

You can absolutely use **bstrings** to extract strings from memory dumps:

**Steps:**

1. Load the entire dump file (`.raw`, `.mem`, `.dmp`) into a `bstring`.
2. Scan for readable ASCII or Unicode sequences as above.
3. Optionally, match against known patterns (URLs, file paths, API calls).

**Example:**

```c
FILE *fp = fopen("memdump.raw", "rb");
fseek(fp, 0, SEEK_END);
long size = ftell(fp);
rewind(fp);

unsigned char *buffer = malloc(size);
fread(buffer, 1, size, fp);
fclose(fp);

bstring memdump = blk2bstr(buffer, size);
free(buffer);

// Now scan memdump as shown earlier
```

**This works very well for:**

* Windows memory captures (Volatility output)
* Linux `/proc/kcore`
* MacOS crash dumps

---

### Recursive Directory Scanning

**bstrings itself does not handle directory traversal** — but in C you can easily combine bstrings with:

* POSIX `opendir()` / `readdir()` for Linux/Unix
* `FindFirstFile` / `FindNextFile` on Windows
* Or cross-platform: `dirent.h` (portable library)

**Approach:**

1. Write recursive directory traversal.
2. For each file:

   * Open and load into a `bstring`
   * Apply your string extraction logic

**Pseudo-code example:**

```c
void scan_dir(const char *path) {
    DIR *d = opendir(path);
    struct dirent *entry;

    while ((entry = readdir(d)) != NULL) {
        if (entry->d_type == DT_DIR) {
            // recurse into subdir if needed
        } else {
            // open file and process
            process_file(entry->d_name);
        }
    }
    closedir(d);
}
```

---

**Summary:**

| Feature                      | How to Implement in bstrings                               |
| ---------------------------- | ---------------------------------------------------------- |
| Using options and flags      | Parse `argc`/`argv` in your program                        |
| Minimum string length (`-n`) | Scan bstring, track sequence length                        |
| Filtering by char set        | `isascii()`, `isprint()`, external libs for Unicode        |
| Extracting from memory dumps | Load full dump → process with bstring functions            |
| Recursive directory scanning | Implement using `dirent.h` + bstring-based file processing |

---

**Sources:**

* [https://github.com/antirez/bstring](https://github.com/antirez/bstring)
* "The C Programming Language" — Kernighan & Ritchie
* Linux manpages for `dirent.h` and `opendir()`
* "Secure Coding in C and C++" — Seacord
* SANS DFIR — memory dump analysis examples
* Volatility Framework documentation (memory forensics context)

## 5. Practical Applications  
    - Analyzing executable files  
    - Extracting metadata from documents  
    - Investigating malicious files  
    - Use in digital forensics investigations  

## 6. Integration with Other Tools  
    - Combining bstrings with grep for filtering  
    - Using bstrings with hex editors  
    - Automating workflows with scripts  

## 7. Case Studies and Examples  
    - Real-world scenarios using bstrings  
    - Step-by-step walkthroughs  

## 8. Best Practices and Tips  
    - Choosing appropriate string lengths  
    - Avoiding false positives  
    - Interpreting results effectively  

## 9. Troubleshooting and Limitations  
    - Common issues and solutions  
    - Limitations of bstrings  

## 10. Conclusion  
    - Recap of key concepts  
    - Additional resources for learning  

## 11. Appendix  
    - Command reference  
    - Links to official documentation  
    - Sample files for practice  
