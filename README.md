---
icon: hand-wave
layout:
  width: default
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# PCB Design Journey

## 📘 PCB Design Journey: A Practical Guide to Prototyping, Testing & Learning

***

### 📌 Why Learn PCB Design?

* Breadboards and modules have limits:
  * Prone to connection errors and instability
  * Not scalable, hard to debug
* Building your own PCBs:
  * Cheaper, more reliable, more compact
  * Required for **products**, **drones**, **custom projects**, and **manufacturing**
* Career reasons:
  * PCB layout is becoming a specialized, in-demand role
  * Great freelance or full-time job opportunities globally

***

### 🧠 Learning Mindset & Strategy

1. **Learn by Doing**
   * Start with real projects, not just theory
   * Apply _progressive overload_: Each project should push your comfort zone
   * Make things you _actually want or need_ (e.g., for a drone, home automation, etc.)
   * Accept mistakes — they teach you more than tutorials
2. **Don't Get Stuck on Tools**
   * Tools don’t matter; concepts do
   * Recommended for beginners:
     * 🟢 **KiCad** (Free, open-source, powerful)
     * ⚙️ Others: Altium, EasyEDA, Eagle, etc. — pick what fits your future goals
   * You can switch tools once concepts are solid
3. **Always Build What You Design**
   * Designing PCBs without manufacturing them is a waste
   * Learn about:
     * Component placement
     * Assembly challenges
     * Manufacturability
   * Services like **JLCPCB**, **PCBWay**, **Aisler**, etc., are affordable even for hobbyists

***

### 🧰 Essential Concepts for PCB Beginners

#### ✅ Early Project Ideas

* Start with:
  * Arduino Shields
  * Simple sensor boards
  * Power regulators
* Progress to:
  * STM32 standalone dev board
  * Basic audio, power, or interface PCBs

#### 🧪 Common Mistakes in PCB Prototyping

1. **Over-relying on breadboards**
2. **Messy perfboards**
3. **Designing for production too early**
4. **No test points**
5. **No power/diagnostic LEDs**
6. **Overcrowding components**
7. **No silkscreen / markings**
8. **No isolation jumpers**
9. **Not breaking out unused GPIOs**
10. **UART mixups (TX/RX reversed)**
11. **Fixed I²C addresses**
12. **Tying power to MCU on same board**
13. **Unlabeled SMD resistors**
14. **Wrong footprints**
15. **Choosing parts that are out of stock**

***

### 🛠️ Practical Tips for Better PCB Prototyping

| Area                  | Best Practice                                                      |
| --------------------- | ------------------------------------------------------------------ |
| **Power**             | Add test LEDs for 3.3V/5V; isolate ICs with 0Ω jumpers             |
| **Debugging**         | Include test points for GND, VCC, I2C, UART, etc.                  |
| **Footprints**        | Always cross-verify with datasheet and footprint dimensions        |
| **GPIOs**             | Break out spares to allow rerouting if needed                      |
| **UART**              | Use jumper-configurable TX/RX pairs or zero-ohm fix                |
| **Silkscreen**        | Add polarity, pin 1 markers, labels — use readable font sizes      |
| **Modularity**        | Separate power from logic using mouse bites or breakouts           |
| **I2C**               | If multiple devices, give address selection with R pull-ups        |
| **Part Availability** | Check stock **before** layout; even common connectors vary         |
| **Manufacturing**     | Design for reworkability — space around ICs, large passives (0805) |

***

### 📚 Resources You Should Bookmark

#### 🔢 Beginner Tutorials

* Dave Jones’ EEVBlog PDF Guide
* KiCad Official Getting Started Guide
* [Phil's Lab](https://www.youtube.com/@ShawsLab)
* Rick Hartley’s “**Grounding & Signal Integrity**” talk (watch this multiple times!)

#### 🎓 Advanced Learning

* Robert Feranec's Courses
* Books:
  * _High-Speed Digital Design: A Handbook of Black Magic_
  * _Fast Circuit Boards: Energy Management_
* IPC Standards:
  * Learn about IPC-2221, IPC-7351, etc.
  * CID/CID+ certification (for jobs, not for learning)

#### 🌐 Online Communities

* [Reddit r/PrintedCircuitBoard](https://www.reddit.com/r/PrintedCircuitBoard/)
  * Ask for design reviews
  * Share and get critique
* Electronics StackExchange
  * For signal integrity, EMI, filtering, layout logic
* [GitHub](https://github.com/)
  * Search “open source PCB” — learn from others’ designs

***

### 🧑‍🎓 How to Structure Your Own Learning Path

> Here’s a suggested progressive track to follow:

| Level            | Project Type                        | Skills Focus                                     |
| ---------------- | ----------------------------------- | ------------------------------------------------ |
| 1️⃣ Beginner     | Arduino shield w/ sensor + LED      | Schematic, footprints, silkscreen, power basics  |
| 2️⃣ Novice       | Standalone MCU board                | Clocking, decoupling, USB, layout for GPIOs      |
| 3️⃣ Intermediate | Multi-board project w/ power board  | Modularity, EMI, testability, BOM handling       |
| 4️⃣ Advanced     | FPGA, high-speed USB/Ethernet board | Stackup, DDR routing, impedance control, SI/PI   |
| 5️⃣ Production   | Enclosure-ready board               | DFM, panelization, assembly prep, EMC compliance |

***

### 🛠️ Tools & Manufacturers

| Category            | Recommended                                             |
| ------------------- | ------------------------------------------------------- |
| PCB Design Software | KiCad (free), Altium (pro)                              |
| PCB Manufacturer    | JLCPCB, PCBWay, Aisler                                  |
| Assembly Services   | JLCPCB, PCBWay                                          |
| Online BOM Tools    | Octopart, LCSC, Mouser, DigiKey                         |
| Debugging Tools     | Logic analyzer, multimeter, oscilloscope, USB-to-serial |

***

### 📌 Final Tips

* Always design with **debugging in mind**
* Review your own designs months later — you’ll see how far you’ve come
* Make it a habit to:
  * Study others' designs
  * Share your own for feedback
  * Reflect on failures
* Set one **real-world** goal (e.g., build a custom flight controller, audio DAC, robot brain)
