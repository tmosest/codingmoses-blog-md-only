---
title: "Day 19 HomeLab – Upgrading the Bezalel 3D Printer"
description: "A detailed walk‑through of firmware updates, hardware fixes, and aesthetic customisations on the Bezalel 3‑D printer. Includes Marlin boot‑screen tweaks, axis troubleshooting, and community‑friendly open‑source contributions."
date: 2025-09-13
categories: ["3D Printing", "HomeLab"]
author: moses
tags: ["3d-printer", "firmware", "Marlin", "hardware-upgrade", "troubleshooting", "open-source"]
hideToc: true
---

## Introduction

My Bezalel 3‑D printer had become *unoperational* after a corrupted firmware image stopped the hot‑end from heating and the axes from homing. Rather than replace the unit, I decided to **upgrade both the firmware and the hardware** so that the printer can serve as a reliable testbed for future projects and a showcase for the community.

---

## 1. Confirming Firmware Compatibility

Before diving into mechanical work, I needed to verify that the issue was indeed firmware‑related and not a hardware failure.

### 1.1 What Went Wrong?

- The SD‑card slot was still functional, but the printer wouldn’t boot from the latest image.  
- The X‑axis motor was not running after a reboot, suggesting a possible board short.

### 1.2 How I Tested

1. **Inserted a fresh SD card** with the latest Marlin firmware (v2.0.10‑RC2).  
2. Powered on the printer – it *did* boot, but the homing routine failed.  
3. Removed the X‑axis motor from the board and manually stepped each axis to isolate the issue.  
4. Found that the X‑axis stepper driver was not receiving current, confirming a **board fault**.

> **Takeaway:** The firmware was fine; the hardware needed a quick repair.

---

## 2. Firmware Update & Configuration Release

### 2.1 Updating Marlin

- **Download** the latest Marlin release from the official GitHub repository.  
- **Compile** with the **PlatformIO** extension in VS Code.  
- **Flash** the firmware onto the board using the *USB/Serial* connection.

> **Tip:** Keep a backup of the old configuration (`Configuration.h` and `Configuration_adv.h`) in case you need to revert.

### 2.2 Making the Config Public

I created a **pull request** to the Marlin repository that includes my custom `Configuration.h` tuned for the Bezalel. This helps others who own the same model and keeps the community ecosystem robust.

### 2.3 Adding a Custom Boot‑Screen

I wanted a custom image for the printer’s boot screen, replacing the generic “Iron Man” with an **Academy of Bezalel** logo.

| Step | Description |
|------|-------------|
| 1 | Convert a PNG to a 128x64 bitmap using the <https://marlinfw.org/tools/u8glib/converter.html> tool. |
| 2 | Add the resulting array to `lcd/ultralcd.cpp`. |
| 3 | Re‑compile and flash. |

> **Reference:** [How to Make a Custom Boot‑Screen for Your Marlin 3‑D Printer](https://www.instructables.com/How-to-Make-a-Custom-Boot-Screen-for-Your-Marlin-3/)

---

## 3. Hardware Upgrades

### 3.1 Fixing the X‑Axis Motor

After confirming the firmware was working, I noticed the **X‑axis motor was unplugged**. I re‑connected the stepper driver, ensured the `X_CURRENT` value in `Configuration_adv.h` was set to 800 mA, and tested homing.

### 3.2 Fan Replacement & Cable Management

- Removed the old extruder fans.  
- Sourced new 24 V 40 mm fans (matching the Ender’s power supply).  
- Printed a new 40 mm fan adapter for the X‑axis.  
- Noticed the **belt routing conflict**: the new adapter sits above the belt, which was fine on the Ender but obstructs my design where the belt is on the side.  
- Solution: re‑route the belt to a side mount and use a custom bracket to keep the fan clear.

### 3.3 LED Lighting

Installed a strip of 12 V RGB LEDs for ambient lighting. Powered them via a 12 V regulator and wired a simple switch to the power supply.

---

## 4. Testing & Validation

### 4.1 Voltage Measurement

Using a breadboard setup, I verified that the **controller board’s 12 V rail** was delivering a stable voltage. The multimeter readings matched the expected values, confirming no underlying power issue.

### 4.2 Motor Calibration

Performed a full motor calibration cycle:

- Set `X_HOME_POS` to 0, `Y_HOME_POS` to 0, `Z_HOME_POS` to 0.  
- Adjusted `X_MAX_POS`, `Y_MAX_POS`, `Z_MAX_POS` to the bed dimensions.  
- Ran `Auto Bed Leveling` (BLTouch) to ensure a level build plate.

---

## 5. Community Contribution Ideas

- **Reddit Bot:** I’m considering building a bot that automatically posts troubleshooting tips for Bezalel owners.  
- **Open‑Source Firmware Fork:** Adding support for the Bezalel’s unique thermistor values in Marlin.  
- **Print Guides:** Drafting a PDF guide on “From Firmware to Finish: Upgrading Your Bezalel”.

---

## 6. Conclusion

The Bezalel is back online with a **modern firmware**, **custom boot‑screen**, and a **fully upgraded fan & lighting system**. By fixing the X‑axis issue, cleaning up the wiring, and publishing my configuration, I’ve not only restored functionality but also contributed to the 3D printing community.  

Feel free to fork my PR, adapt the firmware for your own printer, or let me know if you run into similar issues. Happy printing!

---

> **Keywords**: 3D printer firmware update, Marlin boot screen, Bezalel printer fix, hardware upgrade 3D printer, open-source printer configuration, Redesign 3D printer fan bracket.