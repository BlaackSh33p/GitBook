# ESP32 PlatformIO & WiFi

## Complete ESP32 PlatformIO & WiFi Security Guide

_Based on comprehensive troubleshooting session covering ESP32 development with PlatformIO in Sublime Text_

***

### Table of Contents

1. PlatformIO Installation & Setup
2. ESP32 Project Creation
3. Common Compilation Errors & Solutions
4. Upload Issues & Troubleshooting
5. Serial Communication Problems
6. WiFi Scanner Implementation
7. WiFi Security Monitoring
8. Legal & Ethical Considerations
9. Project Organization Strategies
10. Advanced Troubleshooting

***

### PlatformIO Installation & Setup

#### Prerequisites

* **Sublime Text 4** (latest version recommended)
* **Internet connection** for package downloads
* **USB cable** for ESP32 connectivity

#### Installation Steps

**1. Install Package Control in Sublime Text**

bash

```
# Open Sublime Text and press:
Ctrl+Shift+P (Windows/Linux) or Cmd+Shift+P (Mac)
# Type "Install Package Control" and press Enter
```

**2. Install PlatformIO IDE**

bash

```
# In Sublime Text:
Ctrl+Shift+P → "Package Control: Install Package" → "PlatformIO IDE"
```

**3. Wait for Installation**

* PlatformIO automatically installs toolchains, frameworks, and dependencies
* Initial setup may take 5-15 minutes depending on internet speed
* Progress visible in Sublime Text status bar

#### Verification

* PlatformIO icon appears in left sidebar
* `Tools > PlatformIO` menu available
* `Ctrl+Alt+H` opens PlatformIO Home

***

### ESP32 Project Creation

#### Method 1: PlatformIO Home (Recommended)

bash

```
Ctrl+Alt+H → "New Project"
```

**Configuration:**

* **Name**: Your project name
* **Board**: ESP32 Dev Module
* **Framework**: Arduino
* **Location**: Choose project directory

#### Method 2: Manual Project Structure

text

```
ProjectName/
├── platformio.ini
├── src/
│   └── main.cpp
├── include/
├── lib/
└── test/
```

#### Basic platformio.ini Configuration

ini

```
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
upload_speed = 921600
```

***

### Common Compilation Errors & Solutions

#### Error 1: Multiple Definition Error

text

```
multiple definition of `setup()'
multiple definition of `loop()'
```

**Cause:** Multiple `.cpp` files in `src/` directory containing `setup()` and `loop()`

**Solution:**

* Keep only one main file in `src/` directory
* Delete or move extra files
* Ensure only one file contains `setup()` and `loop()` functions

**Project Structure Fix:**

text

```
src/
├── main.cpp          # ← Only ONE file with setup()/loop()
└── helpers.cpp       # ← Additional helper functions only
```

#### Error 2: Missing platformio.ini

text

```
NotPlatformIOProjectError: Not a PlatformIO project. 
`platformio.ini` file has not been found
```

**Solution:**

bash

```
# Navigate to project directory
cd /path/to/project

# Create platformio.ini manually
cat > platformio.ini << EOF
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
EOF
```

#### Error 3: Undefined Function References

text

```
error: 'setupMonitorMode' was not declared in this scope
```

**Cause:** Using conceptual function names that don't exist in actual libraries

**Solution:** Use only documented ESP32 Arduino core functions

***

### Upload Issues & Troubleshooting

#### Common Upload Error: Chip Stopped Responding

text

```
Writing at 0x00090ef8... (75 %)
Traceback (most recent call last):
StopIteration
A fatal error occurred: The chip stopped responding.
```

#### Solutions Hierarchy (Try in Order):

**1. Manual Bootloader Reset (90% Success Rate)**

bash

```
# ESP32 Upload Sequence:
1. Press and HOLD BOOT button
2. Press and RELEASE EN/RST button
3. Release BOOT button
4. Immediately start upload
```

**2. PlatformIO Configuration Tweaks**

ini

```
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino

; Upload optimization
upload_speed = 115200
upload_port = /dev/ttyUSB0  ; Specify your port

; Reset flags
upload_flags =
    --before=default_reset
    --after=hard_reset

; Flash settings
board_build.f_flash = 80000000L
board_build.flash_mode = dio
```

**3. Hardware Checks**

* **USB Cable**: Use data-capable cable (not charge-only)
* **USB Port**: Connect directly to computer (not through hub)
* **Power Supply**: Ensure stable 5V power
* **Board Detection**: Verify ESP32 is recognized by system

**4. Port Detection**

bash

```
# Linux
ls /dev/ttyUSB* /dev/ttyACM*

# Windows
# Check Device Manager → Ports (COM & LPT)

# Mac
ls /dev/cu.* /dev/tty.*
```

**5. Permission Fixes (Linux)**

bash

```
# Add user to dialout group
sudo usermod -a -G dialout $USER
sudo usermod -a -G tty $USER

# Install udev rules
curl -fsSL https://raw.githubusercontent.com/platformio/platformio-core/master/scripts/99-platformio-udev.rules | sudo tee /etc/udev/rules.d/99-platformio-udev.rules
sudo udevadm control --reload-rules
sudo udevadm trigger

# Log out and back in for group changes
```

***

### Serial Communication Problems

#### Symptom: Garbage Characters in Serial Monitor

text

```
�f1B�9TPs�.�!Չ�l�**�m�!�y9�b���6g
```

#### Causes & Solutions:

**1. Baud Rate Mismatch**

**Problem:** Serial Monitor baud rate ≠ code baud rate

**Solution:** Match baud rates in code and PlatformIO configuration

**Code:**

cpp

```
void setup() {
  Serial.begin(115200);  // Must match platformio.ini
  delay(2000);  // Wait for serial initialization
}
```

**platformio.ini:**

ini

```
monitor_speed = 115200
```

**2. Reliable Baud Rates (Try in Order)**

1. **9600** - Most reliable
2. **115200** - Standard for ESP32
3. **74880** - ESP32 bootloader baud rate

**3. Serial Monitor Best Practices**

bash

```
# PlatformIO Serial Monitor Commands:
Ctrl+Alt+S          # Open/Close Serial Monitor
# Check baud rate in status bar
# Close monitor before uploading
```

**4. Reset Sequence**

bash

```
1. Close Serial Monitor
2. Press ESP32 EN/RST button
3. Open Serial Monitor
4. Observe clean output
```

***

### WiFi Scanner Implementation

#### Basic WiFi Scanner Code

cpp

```
#include <Arduino.h>
#include "WiFi.h"

void setup() {
  Serial.begin(115200);
  
  // Set WiFi to station mode
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
  delay(100);
  
  Serial.println("ESP32 WiFi Scanner Started");
}

void loop() {
  int numberOfNetworks = WiFi.scanNetworks();
  
  if (numberOfNetworks == 0) {
    Serial.println("No networks found");
  } else {
    Serial.print("Found ");
    Serial.print(numberOfNetworks);
    Serial.println(" networks:");
    
    for (int i = 0; i < numberOfNetworks; i++) {
      Serial.print(i + 1);
      Serial.print(": ");
      Serial.print(WiFi.SSID(i));
      Serial.print(" (");
      Serial.print(WiFi.RSSI(i));
      Serial.print(" dBm) ");
      
      // Encryption type
      switch (WiFi.encryptionType(i)) {
        case WIFI_AUTH_OPEN: Serial.print("[Open]"); break;
        case WIFI_AUTH_WEP: Serial.print("[WEP]"); break;
        case WIFI_AUTH_WPA_PSK: Serial.print("[WPA/PSK]"); break;
        case WIFI_AUTH_WPA2_PSK: Serial.print("[WPA2/PSK]"); break;
        case WIFI_AUTH_WPA_WPA2_PSK: Serial.print("[WPA/WPA2/PSK]"); break;
        default: Serial.print("[Unknown]"); break;
      }
      
      Serial.println();
    }
  }
  
  Serial.println("-------------------");
  delay(10000);  // Wait 10 seconds
}
```

#### Signal Strength Interpretation (dBm)

| Signal Strength | Quality   | Typical Usage      |
| --------------- | --------- | ------------------ |
| -30 dBm         | Excellent | Next to router     |
| -50 dBm         | Very Good | Same room          |
| -60 dBm         | Good      | Adjacent room      |
| -67 dBm         | Reliable  | Typical home range |
| -70 dBm         | Fair      | Larger distances   |
| -80 dBm         | Poor      | Edge of coverage   |
| -90 dBm         | Very Poor | Barely usable      |
| -100 dBm        | No signal | Disconnection      |

***

### WiFi Security Monitoring

#### Comprehensive Security Monitor

cpp

```
#include <Arduino.h>
#include "WiFi.h"

// CONFIGURATION - USE ONLY ON NETWORKS YOU OWN
const String AUTHORIZED_NETWORK_BSSID = "1C:59:9B:29:E0:50"; // Your network MAC
const int SCAN_INTERVAL = 30000; // 30 seconds

struct NetworkInfo {
  String ssid;
  String bssid;
  int rssi;
  int channel;
  wifi_auth_mode_t encryption;
  unsigned long firstSeen;
  unsigned long lastSeen;
};

std::vector<NetworkInfo> networkHistory;

void printSecurityReport(wifi_auth_mode_t encryption, int rssi) {
  Serial.print(" | Security: ");
  
  if (encryption == WIFI_AUTH_OPEN) {
    Serial.print("INSECURE - No encryption");
  } else if (encryption == WIFI_AUTH_WEP) {
    Serial.print("VULNERABLE - Easily cracked");
  } else if (encryption == WIFI_AUTH_WPA_PSK) {
    Serial.print("WEAK - Vulnerable to attacks");
  } else if (encryption == WIFI_AUTH_WPA2_PSK) {
    Serial.print("SECURE - Strong encryption");
  } else if (encryption == WIFI_AUTH_WPA3_PSK) {
    Serial.print("VERY SECURE - Latest standard");
  }
}

void setup() {
  Serial.begin(115200);
  delay(2000);
  
  Serial.println("╔══════════════════════════════════════════════════╗");
  Serial.println("║           ESP32 WiFi Security Monitor           ║");
  Serial.println("║              Educational Use Only               ║");
  Serial.println("╚══════════════════════════════════════════════════╝");
  
  WiFi.mode(WIFI_STA);
  WiFi.disconnect();
}

void loop() {
  int numNetworks = WiFi.scanNetworks();
  
  Serial.println("\n=== WiFi Security Scan ===");
  
  for (int i = 0; i < numNetworks; i++) {
    Serial.print("• ");
    Serial.print(WiFi.SSID(i));
    Serial.print(" | ");
    Serial.print(WiFi.BSSIDstr(i));
    Serial.print(" | ");
    Serial.print(WiFi.RSSI(i));
    Serial.print(" dBm | Ch:");
    Serial.print(WiFi.channel(i));
    
    printSecurityReport(WiFi.encryptionType(i), WiFi.RSSI(i));
    
    if (WiFi.BSSIDstr(i) == AUTHORIZED_NETWORK_BSSID) {
      Serial.print(" | ⭐ AUTHORIZED");
    }
    
    Serial.println();
  }
  
  delay(SCAN_INTERVAL);
}
```

***

### Legal & Ethical Considerations

#### ⚠️ Critical Warning: Legal Boundaries

**What You CAN Do Legally:**

* Scan your own networks
* Monitor networks you own or have explicit permission to test
* Educational research in controlled environments
* Security testing on your own equipment

**What You CANNOT Do Legally:**

* Capture handshakes from unauthorized networks
* Attempt to crack WiFi passwords without permission
* Perform deauthentication attacks on others' networks
* Monitor networks without explicit authorization

#### ESP32 Limitations for Security Testing

**Capabilities:**

* ✅ Network scanning and discovery
* ✅ Signal strength mapping
* ✅ Basic packet monitoring (with limitations)
* ✅ Educational demonstrations

**Limitations:**

* ❌ No native monitor mode in Arduino framework
* ❌ Limited packet processing capability
* ❌ Small memory for capture storage
* ❌ Insufficient computing power for PSK cracking

#### Ethical Usage Framework

cpp

```
/*
 * ETHICAL USAGE CHECKLIST:
 * [ ] Only testing networks I own
 * [ ] Have explicit written permission
 * [ ] Using for educational purposes
 * [ ] Following local laws and regulations
 * [ ] Respecting privacy of others
 */
```

***

### Project Organization Strategies

#### Option 1: Multiple Independent Projects (Recommended)

text

```
PlatformIO_Workspace/
├── blink_led/
│   ├── platformio.ini
│   └── src/main.cpp
├── wifi_scanner/
│   ├── platformio.ini
│   └── src/main.cpp
├── sensor_test/
│   ├── platformio.ini
│   └── src/main.cpp
└── common_libs/
    └── shared_code.cpp
```

#### Option 2: Single Project with Multiple Environments

**platformio.ini:**

ini

```
[env:blink_led]
platform = espressif32
board = esp32dev
framework = arduino
src_filter = +<blink_led>

[env:wifi_scanner]
platform = espressif32
board = esp32dev
framework = arduino
src_filter = +<wifi_scanner>

[env:sensor_test]
platform = espressif32
board = esp32dev
framework = arduino
src_filter = +<sensor_test>
```

**Build specific environment:**

bash

```
Ctrl+Shift+P → "PlatformIO: Build" → Select environment
```

#### Option 3: Code Snippet Switcher

**utils/switch\_snippet.py:**

python

```
#!/usr/bin/env python3
import os
import shutil
import sys

def switch_snippet(snippet_name):
    snippet_file = f"src/snippets/{snippet_name}.cpp"
    main_file = "src/main.cpp"
    
    if os.path.exists(snippet_file):
        shutil.copy(snippet_file, main_file)
        print(f"Switched to {snippet_name}")
    else:
        print(f"Snippet {snippet_name} not found")

if __name__ == "__main__":
    if len(sys.argv) > 1:
        switch_snippet(sys.argv[1])
```

***

### Advanced Troubleshooting

#### PlatformIO Common Commands Reference

bash

```
# Build project
Ctrl+Alt+B

# Upload to device
Ctrl+Alt+U

# Clean build files
Ctrl+Alt+C

# Serial monitor
Ctrl+Alt+S

# PlatformIO Home
Ctrl+Alt+H

# Library Manager
Ctrl+Shift+P → "PlatformIO: Library Manager"
```

#### ESP32 Specific Issues

**1. Upload Protocol Errors**

**Symptoms:** Random upload failures, chip not responding

**Solutions:**

* Use slower upload speeds (115200 instead of 921600)
* Ensure stable USB connection
* Use manual reset method consistently

**2. Memory Issues**

**Typical ESP32 Resources:**

* **RAM**: 320KB
* **Flash**: 4MB
* **CPU**: 240MHz Dual-core

**Monitoring in PlatformIO:**

text

```
RAM:   [=         ]   6.4% (used 21112 bytes from 327680 bytes)
Flash: [==        ]  18.3% (used 239789 bytes from 1310720 bytes)
```

**3. Library Dependencies**

**Adding libraries to platformio.ini:**

ini

```
lib_deps = 
    bblanchon/ArduinoJson@^6.19.4
    me-no-dev/AsyncTCP@^1.1.1
```

#### Performance Optimization

**1. Build Speed**

ini

```
; platformio.ini optimizations
build_flags = 
    -Wl,-gc-sections
    -ffunction-sections
    -fdata-sections
```

**2. Runtime Performance**

cpp

```
// Use efficient string operations
String networkList = "";
// Instead of multiple Serial.print() calls

// Optimize WiFi scans
WiFi.scanNetworks(true, true);  // Async, hidden networks
```

#### Debugging Techniques

**1. Serial Debugging**

cpp

```
#ifdef DEBUG
#define DEBUG_PRINT(x) Serial.print(x)
#define DEBUG_PRINTLN(x) Serial.println(x)
#else
#define DEBUG_PRINT(x)
#define DEBUG_PRINTLN(x)
#endif

void setup() {
  Serial.begin(115200);
  DEBUG_PRINTLN("Debug mode enabled");
}
```

**2. Error Handling**

cpp

```
bool initializeWiFi() {
  if (WiFi.status() == WL_NO_SHIELD) {
    Serial.println("WiFi shield not present");
    return false;
  }
  return true;
}
```

***

### Quick Reference Cheat Sheet

#### PlatformIO Project Structure

text

```
project/
├── platformio.ini    # Configuration
├── src/
│   └── main.cpp      # Main code (ONLY ONE setup()/loop())
├── include/          # Header files
├── lib/             # Library files
└── test/            # Test files
```

#### Essential platformio.ini Settings

ini

```
[env:esp32dev]
platform = espressif32
board = esp32dev
framework = arduino
monitor_speed = 115200
upload_speed = 115200
upload_port = /dev/ttyUSB0  ; Your specific port
upload_flags = --before=default_reset --after=hard_reset
```

#### Common ESP32 Pinout

* **GPIO2**: Often connected to onboard LED
* **GPIO4, GPIO5**: Common I2C pins
* **GPIO16, GPIO17**: UART2 pins
* **GPIO34-39**: Input only pins

#### Troubleshooting Flowchart

text

```
Upload Failed?
├── Check USB cable/port
├── Try manual reset (BOOT + EN)
├── Verify platformio.ini settings
├── Check serial port permissions
└── Try different upload speed
```

***

### Conclusion

This comprehensive guide covers the entire ESP32 development workflow with PlatformIO in Sublime Text, from initial setup to advanced WiFi security monitoring. Remember to always prioritize ethical usage and legal compliance when working with wireless networks.

The key success factors are:

1. **Proper project structure** with only one main file
2. **Correct platformio.ini configuration**
3. **Consistent manual reset procedure** for uploading
4. **Matching baud rates** for serial communication
5. **Ethical boundaries** for network testing
