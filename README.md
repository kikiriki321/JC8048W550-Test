
⚠️ **Note:** Some terms in this code are not translated into English, so please have some translation software handy.
The language is - Croatian.

📘 User Manual: Guition JC8048W550 Test Bench
=============================================

This firmware transforms the **Guition JC8048W550** development board into a standalone hardware diagnostic tool. It verifies the functionality of the ESP32-S3 chip, the 4.3" RGB Panel, the GT911 Touch controller, and all onboard peripherals.

### 🏠 Main Menu

Upon powering up, the device presents a Main Menu with **4 selection buttons**. Tapping any button launches a specific diagnostic test. A red **BACK** button is available on every sub-screen to return to this menu.

* * *

1\. WIFI TEST

Wireless Network Diagnostics
----------------------------

Verifies the WiFi antenna and stack functionality.

*   **Scanning:** The board actively scans for available 2.4GHz networks and lists them on the left side of the screen along with their signal strength (RSSI).
*   **Connectivity:** Attempts to connect to a pre-defined network (Edit `WIFI_SSID` and `WIFI_PASS` in `main.cpp` to configure).
*   **NTP Time:** Upon successful connection, it fetches the current date and time from `pool.ntp.org` and displays it on the right side.

2\. BLUETOOTH TEST

BLE Radar & Range Test
----------------------

Activates the Bluetooth Low Energy (BLE) module to scan the environment.

*   **Radar Scan:** Performs a 5-second active scan for nearby devices.
*   **Results:** Lists discovered BLE devices (Smartphones, Beacons, Smart Watches, etc.).
*   **Details:** Displays the Device Name and RSSI (Signal Strength) for each item, useful for testing antenna range.

3\. SD CARD TEST

Storage & SPI Bus Check
-----------------------

Verifies the physical microSD slot and SPI communication (Pins 10, 11, 12, 13).

*   **Initialization:** Attempts to mount the SD card file system.
*   **Capacity Info:** Displays Total Size (MB) and Used Space (MB).
*   **File Browser:** Lists all files and folders found in the root directory.

⚠️ **Note:** The card must be formatted as **FAT32** and must be **≤ 32GB**. Larger cards (exFAT) are not supported by the standard library.

4\. SCREEN / COLORS

Panel Quality & Dead Pixel Check
--------------------------------

A visual inspection tool for the RGB Panel.

*   **Interactive Mode:** Tapping the screen cycles through full-screen solid colors.
*   **Sequence:**
    1.  🔴 **RED** (Check Red channel / Dead pixels)
    2.  🟢 **GREEN** (Check Green channel)
    3.  🔵 **BLUE** (Check Blue channel)
    4.  ⚪ **WHITE** (Check backlight uniformity)
    5.  ⚫ **BLACK** (Check for light bleeding)
