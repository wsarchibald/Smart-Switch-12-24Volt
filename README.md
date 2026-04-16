# ESP32 Smart Caravan Switch Panel 🚐💡

A robust, **12V/24V DC** smart lighting controller engineered for off-grid reliability. Built on **ESPHome**, this system provides commercial-grade dimming and autonomous logic that functions perfectly with or without a network connection.

---

## 🌟 Key Features

* **Autonomous Offline Operation**: Core logic runs locally on-chip. Physical buttons and dimming work instantly even if Wi-Fi or Home Assistant is offline.
* **Precision PWM Dimming**: High-frequency (**1000Hz–1220Hz**) flicker-free dimming with "soft-start" transitions and a **10% safety floor** to prevent accidental shut-off.
* **Hybrid Connectivity**: Includes a "Caravan-Fallback" Hotspot. If your main router fails, you can connect directly to the switch via your phone to maintain control.
* **Visual Dashboard**: An onboard **SK6812 RGB LED** provides real-time feedback:
    * **Red Strobe**: Wi-Fi Connection Lost.
    * **Climate Colors**: Temperature-based ambient lighting.
    * **Nightlight**: 5% warm-white output for easy navigation at night.
* **Smart Battery Monitor**: Integrated voltage divider for real-time health checks (Green/Orange/Red status) via the triple-click shortcut.

---

## 🎛️ User Interface Guide

The dual-button interface uses multi-click timing logic to maximize functionality:

| Action | Result |
| :--- | :--- |
| **1 Click** | Toggle Light On/Off |
| **Hold** | Smooth Dimming (Release and hold again to reverse direction) |
| **2 Clicks** | **Night Mode**: Douses all lights + enables Status LED Nightlight |
| **3 Clicks** | **Battery Check**: LED shows health for 5s (Green >12.6V, Red <12.0V) |

---

## 🛠️ Hardware Specifications & "Quirks"

This board is designed for the rigorous electrical environment of a mobile unit, utilizing an **AP63203 synchronous buck converter** (up to 30V input) rather than inefficient linear regulators.

### ⚠️ Critical Stability Fixes
If you are replicating this PCB, the following implementations are **mandatory** to prevent boot-loops during Wi-Fi inrush:

1.  **Hardware POR Delay**: Solder a **10µF capacitor** between the **EN pin** and **GND**. This holds the chip in reset for ~40-100ms until the power rail stabilizes.
2.  **Software Brownout Override**: The YAML must include a C++ lambda to disable the internal detector:
    ```yaml
    on_boot:
      priority: 800
      then:
        - lambda: 'WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);'
    ```

### 📌 Pin Mapping
| Component | GPIO | Function |
| :--- | :--- | :--- |
| **Battery ADC** | 34 | 11:1 Voltage Divider |
| **Status LED** | 18 | SK6812 (NeoPixel) Data |
| **Output 1** | 25 | MOSFET PWM (Dining/Bathroom) |
| **Output 2** | 26 | MOSFET PWM (Kitchen/Bedroom) |
| **Buttons** | 21, 5 | Momentary Inputs |
| **Button LEDs** | 32, 33 | Physical button backlighting |
