# Marlin Bugfix 2.1.x for Ender 3 Max (BTT SKR Mini E3 V3 + CR-Touch + PEI Bed)

This is a customized, fully tested, and optimized **Marlin Bugfix 2.1.x** configuration tailored specifically for the **Creality Ender 3 Max** upgraded with the **BigTreeTech SKR Mini E3 V3** mainboard. 

It is specifically tuned for a lightweight **PEI magnetic bed** (replacing the heavy stock glass) and higher printing speeds.

## 🛠️ Hardware Specification & Physical Offsets
* **Printer:** Creality Ender 3 Max
* **Mainboard:** BigTreeTech SKR Mini E3 V3 (STM32G0B1RE MCU)
* **Print Bed:** Lightweight PEI sheet (Glass bed removed)
* **Probe:** CR-Touch / BL-Touch
  * **X probe offset:** 50 mm
  * **Y probe offset:** -15 mm

## 🔧 Smart Features & Firmware Modifications

### 🚀 Performance & Speeds
* **Max Speed:** Increased limits to support printing speeds up to **120 mm/s** (Requires matching slicer settings, e.g., in Ultimaker Cura).

### 📐 Advanced Bed Leveling (UBL / Bilinear)
* **25-Point Grid:** Increased leveling precision from a 3x3 (9 points) grid to a **5x5 (25 points) grid**.
* **Smart Mesh Storage:** The printer performs the full 25-point calibration only once. It saves the mesh to EEPROM, heats up, primes with two purge lines on the side, and starts printing immediately without re-probing every time.

### ❄️ Optimized Cooling & Fan Control
* **Board Cooling:** Mainboard cooling fan starts immediately when the printer is powered on / program starts.
* **Hotend Cooling:** Auto-fan for the hotend activates when the nozzle reaches **40°C** and automatically shuts off below 40°C to keep the printer quiet when idle.

### 🎛️ UI & Live Adjustments (Babystepping)
* **Quick Access Babystepping:** Hold down the main rotary knob during printing to instantly access live Z-offset tuning (Z-babystepping).
* **Visual Guides:** Enabled visual UI indicators showing exactly which direction the nozzle moves when adjusting the offset.

---

## 💻 Ultimaker Cura Slicer Setup

To make full use of the smart mesh storage and custom features, configure your printer profile in Cura with the following dimensions and custom G-codes:

### 📏 Printer Dimensions
* **X (Width):** 300.0 mm
* **Y (Depth):** 300.0 mm
* **Z (Height):** 340.0 mm
* **G-code Flavor:** Marlin

### 🏁 Start G-code (Počiatočný G-kód)
```gcode
; Ender 3 Max Custom Start G-code
G92 E0 ; Reset Extruder
G28 ; Home all axes
M420 S1 ; Nacitanie ulozenej 25-bodovej siete z pamate
M900 K0.15 ; Nastavenie Linear Advance
M413 S0 ; Vypnutie Power Loss Recovery pre SKR dosku

G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching
G1 X5.0 Y20 Z0.3 F5000.0 ; Move to start position
G1 X5.0 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X5.4 Y200.0 Z0.3 F5000.0 ; Move to side a little
G1 X5.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line
G92 E0 ; Reset Extruder
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching
```

### 🛑 End G-code (Ukončujúci G-kód)
```gcode
G91 ; Relative positioning
G1 E-2 F2700 ; Retract a bit
G1 E-2 Z0.2 F2400 ; Retract and raise Z
G1 X5 Y5 F3000 ; Wipe out
G1 Z10 ; Raise Z more
G20 ; Absolute positioning

G1 X0 Y{machine_depth} ; Present print
M106 S0 ; Turn-off fan
M104 S0 ; Turn-off hotend
M140 S0 ; Turn-off bed

M84 X Y E ; Disable all steppers but Z
```

---

## 💾 How to Install Firmware
1. Format your MicroSD card to **FAT32**.
2. Download the `firmware.bin` file from this repository.
3. Copy `firmware.bin` to the root directory of your MicroSD card.
4. Power off your Ender 3 Max.
5. Insert the MicroSD card and power on the printer. The screen will remain blank for 5-15 seconds while flashing.
6. Once it boots, go to **Configuration -> Store Settings** (or send `M500`) to initialize the EEPROM.

