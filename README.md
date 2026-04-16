Caravan ESP32 Smart Switch Panel
This project is a highly customized, robust 12V smart switch controller designed specifically for off-grid caravan and unit lighting
. Built on the ESP32 microcontroller using ESPHome, it features commercial-grade dimming, autonomous offline logic, and multi-functional button interfaces
.
🌟 Key Features
Autonomous Offline Operation: The core logic runs directly on the ESP32 chip. If the caravan is off-grid and loses Wi-Fi or Home Assistant connection, the physical buttons and dimming features continue to function instantly
.
Fallback Network: If the main Wi-Fi drops, the switch broadcasts its own "Caravan-Fallback" Access Point so you can still connect a phone directly to control the lights
.
Commercial-Grade Dimming: Utilizes high-frequency Pulse Width Modulation (PWM) at 1000Hz–1220Hz for flicker-free dimming, with a built-in safety clamp that stops at 10% brightness to prevent the light from accidentally turning off while dimming
.
Status Dashboard LED: An onboard SK6812 RGB pixel acts as a visual dashboard. It displays Wi-Fi connectivity drops (Red strobe), temperature-based climate colors, and functions as a 5% warm-white nightlight
.
🎛️ User Quick Reference Guide
The dual-button interface uses multi-click timing logic to trigger different actions from a single button
:
1 Click: Instantly toggles the specific light On or Off
.
Hold: Smoothly dims or brightens the light. Releasing and holding the button again reverses the dimming direction
.
2 Clicks: Activates Night Mode (turns off all main lights and turns on the status LED nightlight) or triggers a morning radio script
.
3 Clicks: Triggers a Battery Health Check. The status LED will display the battery voltage for 5 seconds
:
🟢 Green: Healthy (> 12.6V)
🟠 Orange: Okay (12.0V - 12.6V)
🔴 Red: Low/Critical (< 12.0V)
🛠️ Hardware Quirks & Required Fixes
If you are replicating this board, pay attention to the following mandatory hardware implementations:
Power Brownout / Boot Loop Fix (CRITICAL): High inrush currents when the ESP32 connects to Wi-Fi alongside the LEDs can cause voltage dips, putting the chip into a "Safe Mode" boot loop
. You must solder a 10uF (or 3.9uF) capacitor between the ESP32's EN (Reset) pin and GND
. This creates a ~40-100 millisecond delay, holding the chip in reset until the main power fully stabilizes
.
Software Brownout Override: The ESPHome configuration includes an injected C++ lambda function (WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0);) during the on_boot sequence (priority 800) to disable the internal brown-out detector
.
Power Supply Choice: Do not use linear regulators (like the AMS1117). This board uses the AP63203WU-7 synchronous step-down converter, which safely and efficiently steps down the fluctuating 12V–14.4V caravan input to 3.3V without generating massive heat
.
📌 Default Pin Mapping
Battery ADC (Voltage Divider 11:1): GPIO 34
Status LED (SK6812 Data): GPIO 18
MOSFET Output 1 (Dining/Bathroom): GPIO 25
MOSFET Output 2 (Kitchen/Bedroom): GPIO 26
Button 1 Input: GPIO 21
Button 2 Input: GPIO 5
Button 1 LED: GPIO 32
Button 2 LED: GPIO 33
