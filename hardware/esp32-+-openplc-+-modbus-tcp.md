# ESP32 + OpenPLC + Modbus TCP

## 🚀 ESP32 + OpenPLC + Modbus TCP: From Setup to Successful Connection

This guide walks through everything I did to get an **ESP32 board running as a Modbus TCP slave** that can communicate with **OpenPLC Runtime**. I’m keeping it as a personal log/blog, so if I or anyone else ever gets stuck, this document can serve as a quick reference.

***

### 1. Installing Required Software

#### PlatformIO (ESP32 Development)

PlatformIO makes flashing firmware on the ESP32 much easier.

```bash
pip install platformio
```

Or use the **PlatformIO IDE extension** in VS Code.

#### OpenPLC Runtime & Editor

* Download the **OpenPLC Runtime** for Linux: [https://www.openplcproject.com/runtime](https://www.openplcproject.com/runtime)
* The Editor (for writing PLC logic) can be run as a web app or standalone IDE.

#### Python (Modbus Client Test)

We’ll use Python’s `pymodbus` library to test the ESP32 Modbus server.

```bash
pip install pymodbus
```

***

### 2. Flashing ESP32 with Modbus TCP Slave Code

Create a new PlatformIO project:

```bash
pio project init --board esp32dev
```

Inside `src/main.cpp`, flash this code (adapt WiFi credentials):

```cpp
#include <WiFi.h>
#include <ArduinoModbus.h>

const char* ssid = "ELECTRICAL WING";
const char* password = "1stfloor123";
#define LED_PIN 2

WiFiServer wifiServer(1502); // Modbus TCP port (use 502 if running as root)
ModbusTCPServer modbusTCPServer;

void setup() {
  Serial.begin(115200);
  pinMode(LED_PIN, OUTPUT);

  // Connect WiFi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("Connected.");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  wifiServer.begin();
  modbusTCPServer.begin();

  // Add a holding register & coil
  modbusTCPServer.configureHoldingRegisters(0, 1);
  modbusTCPServer.configureCoils(0, 1);
  modbusTCPServer.holdingRegisterWrite(0, 25);
  modbusTCPServer.coilWrite(0, 0);

  Serial.println("Modbus TCP server started on port 1502");
}

void loop() {
  WiFiClient client = wifiServer.available();
  if (client) {
    modbusTCPServer.accept(client);
    modbusTCPServer.poll();

    // Simulate sensor: increment HR[0] every 2s
    static unsigned long last = 0;
    if (millis() - last > 2000) {
      last = millis();
      uint16_t temp = modbusTCPServer.holdingRegisterRead(0);
      temp = (temp >= 40) ? 25 : temp + 1;
      modbusTCPServer.holdingRegisterWrite(0, temp);
      Serial.printf("HR[0]=%u\n", temp);
    }

    // Control LED with Coil[0]
    digitalWrite(LED_PIN, modbusTCPServer.coilRead(0) ? HIGH : LOW);
  }
}
```

Upload the firmware:

```bash
pio run --target upload
```

If successful, ESP32 should print:

```
Connected.
IP address: 192.168.0.xxx
Modbus TCP server started on port 1502
HR[0]=25
...
```

***

### 3. Testing ESP32 Modbus TCP with Python

Create `modbus_test.py`:

```python
from pymodbus.client import ModbusTcpClient

ip = "192.168.0.117"  # ESP32 IP
tcp_port = 1502       # Same port as firmware

client = ModbusTcpClient(ip, port=tcp_port)
if not client.connect():
    print("❌ Failed to connect to ESP32")
    exit(1)

print("✅ Connected to ESP32 Modbus server")

# Read HR[0]
rr = client.read_holding_registers(0, 1, unit=1)
if not rr.isError():
    print("HR[0] =", rr.registers[0])
else:
    print("Error reading register", rr)

# Write Coil[0] (toggle LED)
client.write_coil(0, 1, unit=1)  # turn LED ON
client.write_coil(0, 0, unit=1)  # turn LED OFF

client.close()
```

Run it:

```bash
python3 modbus_test.py
```

Expected output:

```
✅ Connected to ESP32 Modbus server
HR[0] = 25
```

***

### 4. Connecting ESP32 to OpenPLC

1. Open the OpenPLC Web UI → _Slave Devices_ → Add New Device.
2. Select **Modbus TCP/IP Slave**.
3. Enter ESP32’s IP (`192.168.0.117`) and port (`1502`).
4. Map registers:
   * **Holding Registers (%QW100):** Start 0, Size 1 → maps HR\[0]
   * **Coils (%QX100.0):** Start 0, Size 1 → maps Coil\[0] (LED)

Run your PLC program, and OpenPLC will talk to ESP32 directly.

***

### ✅ Final Result

* ESP32 serves as a **Modbus TCP slave** (HR\[0] increments, Coil\[0] controls LED).
* Python client can read/write registers and coils.
* OpenPLC Runtime connects and uses ESP32 as an I/O device.

👉 With this setup, ESP32 becomes a fully usable field device in your industrial automation project.

***

#### 📝 Notes & Troubleshooting

* If OpenPLC shows _Connection Refused_, check:
  * ESP32 is on the same network.
  * Correct IP and port are used.
  * Port `502` requires root privileges; `1502` is safer.
* Use `pymodbus` to test before integrating with OpenPLC.

***

That’s it 🎉 — ESP32 is now integrated with OpenPLC over Modbus TCP!
