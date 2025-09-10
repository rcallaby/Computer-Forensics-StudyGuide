# Android Debug Bridge

# What is ADB — short, authoritative

ADB (Android Debug Bridge) is the official command-line tool that lets your computer talk to an Android device (or emulator). It’s part of the Android SDK Platform-Tools package and provides a client (your PC), a server (runs on your PC), and a daemon (adbd) that runs on the device. Use it to run shell commands, install/uninstall apps, copy files, capture logs, reboot, do development installs, and more. ([Android Developers][1])

---

# Prerequisites (hardware / what you’ll need)

* A computer (Windows / macOS / Linux) with terminal/PowerShell access.
* A USB cable that supports data (not a charge-only cheap cable).
* An Android device with Developer Options and USB Debugging enabled (steps below).
* The Android SDK Platform-Tools (contains `adb`) installed on your computer. ([Android Developers][2])

---

# Install ADB (Platform-Tools) — platform by platform (exact steps)

**Option A — Official download (recommended if you want the latest official binary)**

1. Go to the Android SDK Platform-Tools page and download the ZIP for your OS. Unpack it into a folder you control (e.g., `C:\platform-tools` on Windows, `~/platform-tools` on macOS/Linux). ([Android Developers][2])
2. From a shell/terminal, `cd` into that folder and test:

```bash
# from the platform-tools folder
./adb version       # macOS/Linux
adb.exe version     # Windows (or just adb version if added to PATH)
```

**Option B — macOS (Homebrew)**
If you use Homebrew:

```bash
brew install --cask android-platform-tools
adb version
```

(That keeps `adb` updated by Homebrew.) ([Homebrew Formulae][3])

**Option C — Debian/Ubuntu (apt)**

```bash
sudo apt update
sudo apt install android-tools-adb android-tools-fastboot
adb version
```

(Distribution package names vary — consult your distro; installing the official Platform-Tools ZIP is still fine.) ([Ask Ubuntu][4], [LinuxBabe][5])

**Make `adb` easy to run:** add the `platform-tools` folder to your PATH or invoke it with `./adb` from that folder.

---

# Prepare your Android device (enable Developer options + USB debugging)

1. Open Settings → About phone → **Build number** and tap it *7 times* until Developer options are enabled.
2. Back to Settings → System → Developer options → enable **USB debugging** (and optionally **Wireless debugging** on Android 11+ if you want wireless pairing).
3. When you first connect the device to a computer and run `adb`, the device will prompt you to “Allow USB debugging?” — tap **Allow** (you can remember the computer if you trust it). ([Android Developers][6], [Google Help][7])

---

# First connection and simple verification

1. Connect the device with USB. On the PC terminal, run:

```bash
adb devices
```

Expected result:

```
List of devices attached
0123456789ABCDEF    device
```

If the device is listed as `unauthorized` — check the device screen and accept the RSA key prompt. If nothing shows up, see troubleshooting later. ([Android Developers][1])

If adb says the server is not running or permissions issues occur, try:

```bash
adb kill-server
adb start-server
adb devices -l
```

---

# The most useful ADB commands (with examples and what they do)

I’ll list the practical commands you’ll use daily. Where useful, I mention platform notes.

> **Note:** Most of the following commands are documented on Google’s adb/logcat pages — I’ll cite the official docs for core behavior. ([Android Developers][1])

### Device / server management

* `adb start-server` — start adb server on your PC (usually automatic).
* `adb kill-server` — stop it.
* `adb devices -l` — list attached devices with extra info. ([Android Developers][1])

### Open a device shell

```bash
adb shell
# now you’re in device shell (like SSH)
```

Run single commands:

```bash
adb shell getprop ro.product.model
adb shell pm list packages | grep 'com.example'
```

`adb shell` gives you a Unix shell on the device (limited by device privileges). ([Android Developers][1])

### Install and uninstall APKs

* Install: `adb install path/to/app.apk`
* Update in place (keep data): `adb install -r path/to/app.apk`
* Uninstall: `adb uninstall <package.name>`
  For large apps on Android 11+, there’s **ADB incremental install** (`adb install --incremental`) to speed up very large APK installs. ([Android Developers][1])

### Copy files

* Push from PC → device:

```bash
adb push localfile /sdcard/Download/
```

* Pull from device → PC:

```bash
adb pull /sdcard/Download/log.txt ./log.txt
```

### Read logs (essential)

* Stream device logs:

```bash
adb logcat
# filter by tag or PID:
adb logcat -s MyAppTag:D
```

Use `adb logcat --pid=<pid>` (newer adb supports `--pid`) or pipe through `grep`. For GUI log inspection use Android Studio Logcat window. ([Android Developers][8])

### Capture screenshots & screen recordings

* Screenshot:

```bash
adb shell screencap -p /sdcard/screenshot.png
adb pull /sdcard/screenshot.png
```

* Screen recording:

```bash
adb shell screenrecord /sdcard/demo.mp4
# press Ctrl+C to stop, then:
adb pull /sdcard/demo.mp4
```

Screencap/screenrecord run on the device and then you pull the file. ([Stack Overflow][9], [android-notebook.hanmajid.com][10])

### Reboot or boot to bootloader/recovery

```bash
adb reboot                 # reboot normal
adb reboot recovery        # reboot to recovery
adb reboot bootloader      # reboot to bootloader (fastboot mode)
```

Fastboot is a separate tool used while the device is bootloader/fastboot mode (flashing images, unlocking bootloader). Use fastboot for flashing; use adb for OS/runtime tasks. ([Android Open Source Project][11])

### Port forwarding & networking

* Forward host port → device port:

```bash
adb forward tcp:6100 tcp:7100
```

* Reverse (device → host):

```bash
adb reverse tcp:7100 tcp:6100
```

Useful for debugging servers running on your PC or your app. ([Android Developers][1])

### Wireless ADB: two modern methods

**A — Old method (adb TCP/IP).**

1. Connect device by USB.
2. `adb tcpip 5555` (restarts adbd to listen on TCP 5555).
3. `adb connect <device-ip>:5555`
4. `adb devices` should list the device by IP. (Note: `tcpip` mode can be less secure; it's ephemeral and often reset by reboots.) ([Stack Overflow][12])

**B — Android 11+ Wireless Debugging (paired)** — preferred if available because it’s secure and uses a pairing code / QR code: enable **Wireless debugging** in Developer Options, then pair your workstation using Android Studio’s “Pair devices using Wi-Fi” or the `adb pair <ip:port>` command and the device’s pairing code / QR code. Official docs show pairing steps. ([Android Developers][1])

### `adb backup` / `adb restore` — caution

`adb backup` and `adb restore` have been deprecated/limited across recent Android releases and will not reliably back up modern apps (Android 12+ changed behavior). Use platform backup (Google’s Auto Backup / cloud backup) or app-specific export where available. Don’t rely on `adb backup` for modern full user-data backups. ([OSnews][13], [Stack Overflow][14])

---

# Typical developer workflows (step-by-step examples)

### Example A — Install, run, and view logs

1. `adb devices` — verify the device is connected and authorized. ([Android Developers][1])
2. `adb install -r ./app-debug.apk` — install updated debug APK and keep app data. ([Android Developers][1])
3. `adb logcat --clear` then `adb logcat` (or use Android Studio Logcat) to see logs as you exercise the app. ([Android Developers][8])

### Example B — Capture a crash report (bugreport)

1. Reproduce the problem on device.
2. `adb bugreport ./bugreport.zip` — collects system state and logs for sharing with the team. (Large; takes time.) ([Android Developers][1])

### Example C — Pulling a file you need (e.g., app DB)

1. `adb shell ls -la /sdcard/Download` (find path).
2. `adb pull /sdcard/Download/somefile.db ./` — copies to current PC folder. ([Android Developers][1])

---

# Troubleshooting — common problems & fixes

**Device not listed / `adb devices` shows nothing**

* Make sure USB cable supports data (try another cable/port).
* On device, enable USB Debugging and accept the RSA fingerprint prompt. ([Android Developers][6])

**Windows: “No driver” or device not recognized**

* Install OEM drivers or the Google USB driver (if your device uses it). Follow Google’s OEM USB drivers doc. Then replug and in Device Manager update the driver. ([Android Developers][15])

**Linux: permission denied / “insufficient permissions”**

* Add your user to `plugdev` (or appropriate) group and create udev rules to match vendor IDs (e.g., Google `18d1` etc.), then reload udev rules. Example udev rules collections exist (community maintained). After updating rules: `sudo udevadm control --reload-rules && sudo udevadm trigger`. ([Android Developers][16], [GitHub][17], [Stack Overflow][18])

**`adbd cannot run as root in production builds` when running `adb root`**

* `adb root` only works on development builds (eng/userdebug) or devices whose `adbd` supports root. On stock/production devices it will not enable adbd as root. For normal debugging you don’t need `adb root`. ([Android Enthusiasts Stack Exchange][19], [Android Open Source Project][20])

**`unauthorized` listing**

* Revoke USB debugging authorizations on the device (Developer options → Revoke USB debugging authorizations) and reconnect and accept the prompt. ([Android Developers][6])

---

# Security & best practices (important)

* Only enable USB Debugging when needed. Treat it as a powerful access vector — anyone with a trusted computer and physical access can run commands. Revoke authorizations for unknown machines. ([Android Central][21])
* For wireless debugging, use the secure pairing method introduced in Android 11 rather than leaving `adb tcpip` open on a device. ([Android Developers][1])
* Keep Platform-Tools updated; they’re updated by Google and include security fixes. ([Android Developers][2])

---

# Quick ADB cheat-sheet (copy this)

```text
# Basic
adb devices                    # list devices
adb version                    # show adb version

# Shell & system
adb shell                      # interactive shell
adb shell getprop              # device properties
adb shell pm list packages     # installed packages

# Install/uninstall
adb install app.apk
adb install -r app.apk         # replace/upate, keep data
adb uninstall com.example.app

# Files
adb push localfile /sdcard/
adb pull /sdcard/file.txt ./

# Logs
adb logcat                     # stream logs
adb bugreport bugreport.zip

# Screen capture
adb shell screencap -p /sdcard/screen.png
adb pull /sdcard/screen.png

adb shell screenrecord /sdcard/video.mp4
adb pull /sdcard/video.mp4

# Reboot / bootloader
adb reboot
adb reboot bootloader

# Wireless
adb tcpip 5555
adb connect 192.168.1.100:5555
adb pair 192.168.1.100:37179  # Android11+ pairing, then adb connect

# Server control
adb kill-server
adb start-server
```

(Referenced commands and examples follow the official ADB docs; see the citations earlier.) ([Android Developers][1])

---

# Recommended reading & watching (official + high-quality tutorials)

**Official / primary sources (read first):**

* Android Debug Bridge (adb) — official command reference & overview. ([Android Developers][1])
* SDK Platform-Tools release notes & downloads (keep these up to date). ([Android Developers][2])
* Run apps on a hardware device (enabling device for adb, drivers, platform notes). ([Android Developers][16])

**Wireless / pairing**

* Wireless debugging / pairing instructions (Android 11+ official steps). ([Android Developers][1])

**Troubleshooting & platform-specific guides**

* Windows OEM USB driver instructions (Google’s page + OEM links). ([Android Developers][15])
* Linux `udev` rules & plugdev guidance (community repos + StackOverflow examples). ([GitHub][17], [Stack Overflow][18])

**Community tutorials & videos (to watch / follow along)**

* XDA Developers — “How to install ADB on Windows/macOS/Linux” (clear, practical). ([XDA Developers][22])
* Android Developers YouTube channel (official channel — great for demos and new features). ([YouTube][23])
* Android Police and other reputable Android sites often publish step-by-step ADB wireless guides — good for walkthroughs. ([Android Police][24])

---

# Final notes & pro tips

* Keep the Platform-Tools up to date (Google pushes security/compat updates). `adb version` tells you what you have; re-download platform-tools if older than the release notes. ([Android Developers][2])
* On macOS, Homebrew `--cask android-platform-tools` is an easy way to manage updates. ([Homebrew Formulae][3])
* Don’t rely on `adb backup` for modern devices — Android backup behavior changed and the command is deprecated/limited on newer Android releases; use cloud/Auto Backup or app-specific export. ([OSnews][13])

[1]: https://developer.android.com/tools/adb "Android Debug Bridge (adb) | Android Studio"
[2]: https://developer.android.com/tools/releases/platform-tools "SDK Platform Tools release notes | Android Studio"
[3]: https://formulae.brew.sh/cask/android-platform-tools "android-platform-tools"
[4]: https://askubuntu.com/questions/34702/how-do-i-set-up-android-adb "software installation - How do I set up Android ADB?"
[5]: https://www.linuxbabe.com/ubuntu/how-to-install-adb-fastboot-ubuntu "How to Install ADB & Fastboot on Ubuntu 20.04, 18.04, 21.04"
[6]: https://developer.android.com/studio/debug/dev-options "Configure on-device developer options | Android Studio"
[7]: https://support.google.com/android/community-guide/273205728/how-to-enable-developer-options-on-android-pixels-6-secret-android-tips "How to Enable Developer Options on Android & Pixels (6 ..."
[8]: https://developer.android.com/studio/debug/logcat "View logs with Logcat | Android Studio"
[9]: https://stackoverflow.com/questions/27766712/using-adb-to-capture-the-screen "Using ADB to capture the screen [duplicate]"
[10]: https://android-notebook.hanmajid.com/docs/screen-recordings/taking-screen-recordings/with-adb "Taking Screen Recordings with adb - Android Notebook"
[11]: https://source.android.com/docs/setup/test/running "Flash with Fastboot"
[12]: https://stackoverflow.com/questions/33726622/how-to-debug-in-android-studio-using-adb-over-wifi "How to debug in Android Studio using adb over WiFi"
[13]: https://www.osnews.com/story/130000/google-warns-that-adb-backup-and-restore-may-be-removed-in-a-future-android-release "Google warns that ADB backup and restore may be ..."
[14]: https://stackoverflow.com/questions/74387730/back-up-android-with-adb-backup "Back up android with adb backup"
[15]: https://developer.android.com/studio/run/win-usb "Get the Google USB Driver | Android Studio"
[16]: https://developer.android.com/studio/run/device "Run apps on a hardware device | Android Studio"
[17]: https://raw.githubusercontent.com/m0rf30/android-udev-rules/master/51-android.rules"https://raw.githubusercontent.com/m0rf30/android-u..."
[18]: https://stackoverflow.com/questions/43771918/how-do-i-set-up-udev-rules-for-debugging-a-physical-android-device-with-android "How do I set up udev rules for debugging a physical ..."
[19]: https://android.stackexchange.com/questions/213116/why-does-adb-root-do-nothing "Why does \"adb root\" do nothing? - Android Enthusiasts"
[20]: https://source.android.com/docs/devices/admin/multi-user-testing "Test multiple users"
[21]: https://www.androidcentral.com/phones/tech-talk-what-is-usb-debugging-and-should-you-enable-it "Tech Talk: What is USB Debugging and should you enable it?"
[22]: https://www.xda-developers.com/install-adb-windows-macos-linux/ "How to install ADB on Windows, macOS, and Linux"
[23]: https://www.youtube.com/user/androiddevelopers "Android Developers"
[24]: https://www.androidpolice.com/use-wireless-adb-android-phone "How to use wireless ADB on your Android phone or tablet"
