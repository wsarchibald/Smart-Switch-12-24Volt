# ESP32 Smart Caravan Switch Panel 🚐💡

A robust, **12V/24V DC** smart lighting controller engineered for off-grid reliability. [cite_start]Built on **ESPHome**, this system provides commercial-grade dimming and autonomous logic that functions perfectly with or without a network connection[cite: 366, 495, 497].

---

## 🌟 Key Features

* **Autonomous Offline Operation**: Core logic runs locally on-chip. [cite_start]Physical buttons and dimming work instantly even if Wi-Fi or Home Assistant is offline[cite: 112, 114, 120].
* [cite_start]**Precision PWM Dimming**: High-frequency (**1000Hz–1220Hz**) flicker-free dimming with "soft-start" transitions and a **10% safety floor** to prevent accidental shut-off[cite: 38, 507, 570].
* **Hybrid Connectivity**: Includes a "Caravan-Fallback" Hotspot. [cite_start]If your main router fails, you can connect directly to the switch via your phone to maintain control[cite: 452, 678].
* [cite_start]**Visual Dashboard**: An onboard **SK6812 RGB LED** provides real-time feedback[cite: 58, 185]:
    * [cite_start]**Red Strobe**: Wi-Fi Connection Lost[cite: 188, 508].
    * [cite_start]**Climate Colors**: Temperature-based ambient lighting[cite: 511, 751].
    * [cite_start]**Nightlight**: 5% warm-white output for easy navigation at night[cite: 234, 468].
* [cite_start]**Smart Battery Monitor**: Integrated voltage divider for real-time health checks (Green/Orange/Red status) via the triple-click shortcut[cite: 230, 232, 621].

---

## 🎛️ User Interface Guide

[cite_start]The dual-button interface uses multi-click timing logic to maximize functionality[cite: 615, 636]:

| Action | Result |
| :--- | :--- |
| **1 Click** | [cite_start]Toggle Light On/Off [cite: 46, 616] |
| **Hold** | [cite_start]Smooth Dimming (Release and hold again to reverse direction) [cite: 563, 617] |
| **2 Clicks** | [cite_start]**Night Mode**: Douses all lights + enables Status LED Nightlight [cite: 236, 578] |
| **3 Clicks** | [cite_start]**Battery Check**: LED shows health for 5s (Green >12.6V, Red <12.0V) [cite: 621, 622] |

---

## 🛠️ Hardware Specifications & "Quirks"

[cite_start]This board is designed for the rigorous electrical environment of a mobile unit, utilizing an **AP63203 synchronous buck converter** (up to 30V input) rather than inefficient linear regulators[cite: 14, 298, 730].

### ⚠️ Critical Stability Fixes
[cite_start]If you are replicating this PCB, the following implementations are **mandatory** to prevent boot-loops during Wi-Fi inrush[cite: 258, 260]:

1.  **Hardware POR Delay**: Solder a **10µF capacitor** between the **EN pin** and **GND**. [cite_start]This holds the chip in reset for ~40-100ms until the power rail stabilizes[cite: 224, 266, 291].
2.  [cite_start]**Software Brownout Override**: The YAML must include a C++ lambda to disable the internal detector[cite: 208, 263]:
    ```yaml
    on_boot:
      priority: 800
      then:
        - lambda: 'WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);'
    ```

### 📌 Pin Mapping
| Component | GPIO | Function |
| :--- | :--- | :--- |
| **Battery ADC** | 34 | [cite_start]11:1 Voltage Divider [cite: 66, 503] |
| **Status LED** | 18 | [cite_start]SK6812 (NeoPixel) Data [cite: 58, 454] |
| **Output 1** | 25 | [cite_start]MOSFET PWM (Dining/Bathroom) [cite: 53, 59] |
| **Output 2** | 26 | [cite_start]MOSFET PWM (Kitchen/Bedroom) [cite: 53, 60] |
| **Buttons** | 21, 5 | [cite_start]Momentary Inputs [cite: 169, 192] |
| **Button LEDs** | 32, 33 | [cite_start]Physical button backlighting [cite: 56, 170] |
