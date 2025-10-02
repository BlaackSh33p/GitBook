# Kali NetHunter Installation

## Kali NetHunter Installation & Troubleshooting Log

**Purpose:** A comprehensive, copy-pasteable log/blog you can reference later or share with others. It covers the different NetHunter variants, exact steps for installing on supported Android phones, hardware requirements for full features (Wi‑Fi injection, USB HID/BadUSB, OTG), troubleshooting, and a buying checklist. Save this file to your notes or print it — keep it near your toolkit.

***

### Quick Summary

* **NetHunter usually does&#x20;**_**not**_**&#x20;replace Android.** Most phone installs run _on top of Android_ (a Kali chroot/container) with a NetHunter app and optionally a custom kernel.
* **Full hardware features (Wi‑Fi injection, USB HID/BadUSB, advanced OTG)** require a **custom kernel** specifically built for your phone model/codename.
* **Variants:** Full (kernel-based), Lite (rooted but limited), Rootless (non-root, most limited). Choose based on whether you can unlock bootloader & flash kernels.

***

### Table of contents

1. Variants explained (Full, Lite, Rootless)
2. Hardware & software requirements (what really matters)
3. Buying checklist (how to pick a phone)
4. Exact, generalized install flow (with commands & notes)
   * Preparation
   * Full (kernel-based) install flow
   * Magisk-based / Lite install flow
   * Rootless install flow
5. Post-install tests & verification
6. Common problems and fixes
7. Advanced: building your own kernel (brief notes)
8. Safety, ethics & backups
9. Useful commands reference (adb/fastboot/twrp/magisk)
10. Recommended devices (good starting list)
11. Appendix: quick checklist printout

***

### 1) Variants explained

**Full (Kernel-based NetHunter)**

* What it is: Android remains the host OS, but you flash a device-specific NetHunter kernel (or a full NetHunter installer that includes a kernel). This enables kernel-level features like Wi‑Fi monitor/injection, USB HID gadgets (BadUSB), and more.
* Pros: Best hardware support for pentest features. Most powerful.
* Cons: Requires unlocking bootloader, flashing, and often voids warranty.

**Lite (Rooted with NetHunter components)**

* What it is: Device is rooted (Magisk or equivalent), you install NetHunter components (chroot, app) and sometimes a Magisk module. Kernel may be unchanged or lightly modified.
* Pros: Easier than full if a NetHunter kernel doesn't exist. Keeps many tools available.
* Cons: Some kernel-level features may be unavailable.

**Rootless (No root)**

* What it is: NetHunter tools run without root via user-space techniques. Easiest to install but most limited.
* Pros: No unlocking, no voiding warranty, safe to try.
* Cons: Very limited hardware features.

***

### 2) Hardware & software requirements (what affects Wi‑Fi injection, HID, OTG)

* **Device codename in official NetHunter list:** If present, you’re far more likely to get full support.
* **Unlocked bootloader:** Needed for most Full installs.
* **Kernel source availability / community kernels:** If maintainers or community released kernels, you get injection/HID support more easily.
* **Wi‑Fi chipset:** Built-in phone chipsets rarely support injection. Expect to use _external USB Wi‑Fi adapters_ for reliable monitor/injection (USB OTG required).
* **USB OTG support & power:** Device must support USB Host mode and supply enough power (or use powered USB hub) for adapters.
* **Magisk support:** Useful for root management and some Magisk-module installs.
* **Storage & RAM:** 64GB storage and 4GB+ RAM are fine for typical use; more is better for heavy tool use.

***

### 3) Buying checklist (basic & advanced checks)

* ✅ **Model codename listed on NetHunter device/kernel list** (primary requirement for Full features).
* ✅ **Unlocked bootloader possible** (check vendor/carrier policy).
* ✅ **Active dev community (XDA / GitHub)** for your model.
* ✅ **USB OTG supported** and confirmed by specs or user reports.
* ✅ **Kernel sources released** by vendor or community (or prebuilt NetHunter kernel available).
* ✅ **External Wi‑Fi adapter compatibility** (e.g., Alfa AWUS series often used for injection) if needed.

***

### 4) Exact, generalized install flow (commands & notes)

> These are generalized commands. Replace `DEVICE_CODENAME`, `boot.img`, `nethunter.zip`, and file names with the actual files for your device.

#### Preparation (do this first)

1. **Backup everything.** Unlocking bootloader often factory-resets device.
2. **Install ADB & Fastboot** on your PC (Linux/macOS/Windows WSL). Example (Ubuntu):

```bash
sudo apt update
sudo apt install adb fastboot
```

3. **Enable Developer Options & USB debugging** on phone:
   * Settings → About phone → Tap Build number 7 times
   * Settings → System → Developer options → Enable USB debugging
4. **Check device recognized by ADB**

```bash
adb devices
# accept prompt on phone if shown
```

5. **Reboot to bootloader / fastboot**

```bash
adb reboot bootloader
# OR
adb reboot-bootloader
```

6. **Check fastboot device**

```bash
fastboot devices
```

7. **Unlock bootloader** (WARNING: wipes device)

* For many devices (e.g., Pixel):

```bash
fastboot flashing unlock
# or older devices: fastboot oem unlock
```

Follow on-screen prompts on phone to confirm. **This erases user data.**

#### Full (kernel-based) NetHunter install — generalized steps

> Use this if there is an official NetHunter image or kernel for your codename.

1. **Find the official NetHunter image/installer for your codename** (download the `nethunter-<device>.zip` or `kernel-<device>.img`).
2. **Boot to custom recovery (TWRP)** — if not installed, flash TWRP from fastboot:

```bash
fastboot flash recovery twrp-<device>.img
fastboot boot twrp-<device>.img   # You can boot to it without flashing if you prefer
```

3. **In TWRP:**
   * (Optional) Backup current ROM (TWRP `Backup`) — save boot, system, vendor.
   * Wipe data/cache if the installer requires it (read device-specific instructions).
4.  **Flash NetHunter installer zip** (if provided):

    * `Install` → choose `nethunter-<device>.zip` → Swipe to flash.

    OR flash a prebuilt kernel (`boot.img`) using fastboot:

```bash
fastboot flash boot boot.img
fastboot reboot
```

5. **Reboot to Android**, install Magisk if required (some installers expect Magisk root), then install NetHunter app.
6. **Use NetHunter app Chroot Manager** to install or restore the Kali chroot (or use bundled chroot image). Follow the app prompts.
7. **Post-install:** Test kernel-level features (see next section).

#### Magisk-based / Lite install — generalized steps

> Good when a full kernel installer does not exist, but you have root via Magisk.

1. **Install Magisk (if not installed)**:
   * Patch boot image with Magisk and flash, or use the device-specific Magisk install steps.
2. **Install NetHunter Magisk module** (if provided for your device) or follow the NetHunter docs to install components.
3. **Install NetHunter app and chroot** via the app.
4. **Test features** — likely limited for HID/injection unless kernel already supports them.

#### Rootless install (no root)

1. **Install NetHunter rootless app** (or use the NetHunter Store / package for rootless).
2. **Install the Kali chroot using user-space methods** described in official docs (usually more limited).
3. **Note:** This is good for quick trials and learning tools but not for advanced hardware-level testing.

***

### 5) Post-install tests & verification (What to run and what 'pass' looks like)

**Basic health checks**

* `uname -a` — confirm kernel version and any NetHunter kernel tags.
* NetHunter app → Check installed chroot version.

**Wi‑Fi monitor / injection tests**

* If using built-in Wi‑Fi: run `iw list` and look for `monitor` mode support.
* For external USB adapter: `lsusb` and `iwconfig` / `ip link`.
* Test injection (on a network you own or have permission to test!) with `aireplay-ng --test` or `aireplay-ng --test wlan0`.

**USB HID (BadUSB) test**

* Use `hid-gadget-test` or appropriate scripts in NetHunter to emulate keyboard input. Confirm the device can send keystrokes to a target host.

**USB OTG**

* Plug in a USB Ethernet or Wi‑Fi adapter and check `dmesg`, `ip a` / `ifconfig` to see if it enumerates.

**Kali tools**

* `apt update && apt upgrade`
* Check Metasploit, nmap, etc. `msfconsole`, `nmap --version`.

***

### 6) Common problems and fixes

**Problem: Device not seen by fastboot/adb**

* Ensure USB debugging is enabled, try different USB cable/port, install OEM drivers (Windows), use `sudo` on Linux.

**Problem: Bootloader unlock option not available**

* Some carriers/devices lock bootloader. Check vendor website or community guides; some require an unlock token.

**Problem: Wi‑Fi adapter not recognized or injection fails**

* Use a known-good adapter (Atheros/Realtek adapters favored for injection). Use a powered OTG hub if adapter needs power.
* Ensure kernel modules for the chipset are present (`lsmod`). If not, injection likely won't work.

**Problem: TWRP won’t flash or device keeps bootlooping**

* Restore TWRP backup (if made) or re-flash stock boot via fastboot. Double-check device-specific patches.

**Problem: NetHunter chroot install fails**

* Use `nethunter chroot` logs (the NetHunter app shows logs). Ensure sufficient storage and network connectivity.

**Problem: Magisk or SafetyNet failure**

* Some installers require Magisk. Reinstall Magisk using the proper boot image and latest Magisk release.

***

### 7) Advanced: building your own kernel (brief notes)

> Only do this if you are comfortable with Linux kernel development and cross-compiling.

* Get kernel source for your device (from vendor/GitHub).
* Set up cross-compile toolchain (aarch64/arm). Example toolchains: `gcc-aarch64-linux-gnu`.
* Apply NetHunter patches or required driver patches.
* Configure `.config` using device defconfig or upstream config.
* Compile, test by flashing boot image.

Building kernels is model-specific and can take a lot of trial-and-error. Keep a development phone or be ready to unbrick.

***

### 8) Safety, ethics & backups

* **Only test on networks and devices you own or have explicit permission to test.** Unauthorized testing is illegal.
* Always **backup your data** before unlocking or flashing.
* Keep a copy of stock firmware and boot images to restore the device if needed.

***

### 9) Useful commands reference

```bash
# ADB
adb devices
adb reboot bootloader
adb reboot recovery

# Fastboot
fastboot devices
fastboot flashing unlock
fastboot flash boot boot.img
fastboot flash recovery twrp.img
fastboot reboot

# TWRP (use TWRP UI to flash zips)
# Magisk
# (Usually install Magisk via patched boot.img or TWRP)

# Linux tools inside Kali chroot
uname -a
lsusb
ip a
iwconfig
airmon-ng start wlan0
aireplay-ng --test wlan0
```

***

### 10) Recommended starter devices (typical community favorites)

* **Google Pixel series (Pixel 3 / 3a / 4 / 4a / 5 / 6)** — developer-friendly, commonly supported.
* **OnePlus 6 / 6T / 7 / 8 series** — good community support and kernels.
* **Poco F1 (beryllium)** — popular with NetHunter Pro builds historically.
* **PinePhone / PinePhone Pro** — if you want a Linux-first phone that can boot Linux from SD (not Android).

> Note: Always confirm the specific _codename_ for the exact device and check NetHunter’s device/kernel list for that codename.

***

### 11) Appendix: Quick checklist (print this)

* [ ] Model codename appears on NetHunter device/kernel list
* [ ] Bootloader unlockable
* [ ] Kernel source / community kernels exist
* [ ] USB OTG supported
* [ ] External Wi‑Fi adapter confirmed compatible
* [ ] Backup created (Nandroid / TWRP / cloud)
* [ ] Magisk/TWRP files downloaded
* [ ] NetHunter installer / chroot downloaded
* [ ] Stock boot image / firmware backed up

***

**If you get stuck:**

1. Reproduce the exact error (copy/paste logs).
2. Check device-specific XDA/NetHunter threads. 3. Restore stock boot and recovery if needed. 4. Ask for help with the exact codename and error logs.

***

_End of log — keep this as your portable NetHunter reference. If you want, I can convert this into a printable PDF, a step-by-step checklist file, or a shorter quick-start cheat sheet._
