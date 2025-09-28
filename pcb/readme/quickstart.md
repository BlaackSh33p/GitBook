---
icon: bolt
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Avoiding Common Mistakes

## 📘 **The Practical Guide to Avoiding Common Mistakes in Prototype PCB Design**

Whether you're designing your first board or your fiftieth, prototyping can either be a smooth and satisfying process — or a frustrating one full of avoidable pitfalls. After working on countless prototypes both professionally and personally, I’ve compiled this guide to help you avoid the most common design and testing mistakes.

***

### ✅ **Quick Philosophy**

**Prototype PCBs are meant to help you solve problems, not just look production-ready.**

Don’t optimize for enclosure fit or aesthetics too early. Optimize for **debuggability**, **testability**, and **iteration speed**.

***

### 🔢 **15 Common Mistakes in Prototype PCB Design**

***

#### **1. Relying Too Much on Breadboards ("Stale Breadboards")**

* Breadboards are great for **early concept validation**, but not for long-term prototyping.
* Loose connections develop over time, leading to **untraceable bugs**.
* Use breadboards to test **individual subsystems**, not whole projects.
* Transition to PCB **as early as possible** (within 1–2 weeks of validation).

***

#### **2. Making DIY "Hardboards" (Perfboards, CNC, Etched)**

* DIY boards often result in:
  * Messy wiring
  * Hidden shorts
  * Difficult-to-debug connections
* Most modern chips are **too small** or **not breadboard friendly** (e.g., QFN, BGA).
* Use professional PCB services — boards are **cheap** and **arrive within days**.

***

#### **3. Designing for Production Too Early**

* Expect the **first PCB to fail** or need major changes.
* Design it with **debugging in mind**, not enclosure fit.
* Use **bigger boards** (e.g., 100mm x 100mm) to give yourself room to test.
* Focus on function: **test all features** (power, sensors, UART/I²C, etc.)

***

#### **4. No Test Points**

* Always add test points for:
  * Power rails (3.3V, 5V, GND)
  * Key communication lines (I²C, UART, SPI)
  * Reset lines, enable lines
* Use test loops or test pads to easily clip logic probes or scope leads.
* Saves boards from being scrapped due to **one small mistake**.

***

#### **5. No Diagnostic LEDs**

* Add LEDs for:
  * 3.3V, 5V rails
  * Battery charging status
  * MCU "alive" or heartbeat
* Simple indicators can **save hours** of debugging.
* Use a transistor + GPIO for status LEDs if needed.

***

#### **6. Overcrowding Components**

* Don’t squeeze everything in.
* Use:
  * **Larger passives** (e.g., 0805)
  * **Extra spacing** around ICs and connectors
* Make room for rework and probing.
* Shrink later, **not during prototyping**.

***

#### **7. Poor Silkscreen Usage**

* Every component should have:
  * Clear label (e.g., “100k”, “MCP23017”)
  * Pin 1 markers for ICs and polarized parts
  * Orientation indicators for diodes and LEDs
* Use **≥ 2mm text** wherever possible.
* Silkscreen should be **visible even when populated**.

***

#### **8. No Isolation Jumpers**

* Add **0Ω resistors or cuttable traces** (jumper links) between:
  * Power sections and ICs
  * Communication buses
* Lets you isolate circuits without desoldering components.
* Essential for **methodical bring-up**.

***

#### **9. Not Breaking Out Unused GPIOs**

* Break out **unused MCU pins** to:
  * Test pads
  * Small headers
* Gives you flexibility to:
  * Fix design mistakes
  * Add last-minute features or modules

***

#### **10. UART Mix-Ups (TX/RX Confusion)**

* Double check UART pin direction:
  * TX → RX
  * RX ← TX
* Consider swappable jumper pads:
  * Use 0Ω resistors to allow cross/straight selection
* Can save an entire revision from being scrapped.

***

#### **11. Locked I²C Addresses**

* Many I²C devices have **fixed or semi-fixed addresses**.
* Place **two resistors** on address pins:
  * One to VCC
  * One to GND
  * Populate only one during assembly
* Avoid address conflicts without needing a new board.

***

#### **12. Not Separating Power & Logic Sections**

* Break large boards into:
  * **Main (MCU/logic)**
  * **Power (LDOs, chargers, buck/boost, etc.)**
* Use mouse bites or perforated PCBs for easy separation.
* Allows you to **re-spin the power board independently**.

***

#### **13. Unlabeled SMT Resistors**

* Some manufacturers **no longer mark resistor values**.
* For prototyping:
  * Buy **labeled SMD resistors** (e.g., from Yageo or Panasonic)
  * Makes rework/testing easier and more reliable

***

#### **14. Using the Wrong Footprints**

* Always verify footprint vs. datasheet dimensions:
  * Pin pitch
  * Pad size
  * Total component size
* Never trust your EDA software’s built-in libraries blindly.
* Cross-check with **datasheet mechanical drawings**.

***

#### **15. Not Checking Part Availability Before Layout**

* Before you start layout:
  * Make sure parts are **actually available**
  * Check both **stock** and **lead time**
* Consider pre-ordering key parts (especially PMICs, MCUs, connectors)
* Prevent “ghost BOM” where your board is ready but parts aren’t.

***

### 🔧 Tools & Tips

#### Recommended EDA Tool:

* **KiCad 7+** (KiCad 8 soon)
  * Free, open source
  * Import Altium files
  * Robust, professional, mature

#### PCB Fabricators to Use:

* JLCPCB
* PCBWay
  * \~$2–5 for 100mm x 100mm 2-layer boards
  * Fast turnaround (3–7 days)

***

### 🧠 Bonus Habits for Better Prototyping

* **Log your tests and observations** in a project journal.
* Keep **spare PCBs** and build partial circuits on each.
* Use **colored wires, labeled bags**, and **organized bins**.
* Maintain a checklist for **every PCB revision**:
  * Power check
  * Diode/cap orientation
  * Test pad locations
  * Expected voltages

***

### 📌 Final Thought

> **A prototype PCB is not a final product — it's your lab notebook in physical form. Make it easy to read, edit, and learn from.**

If you follow these guidelines, you’ll save time, parts, and headaches — and enjoy the prototyping process a lot more.
