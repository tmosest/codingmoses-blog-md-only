---
title: Day 18 HomeLab – OctoPrint, Marlin, & Hardware Upgrades
description: A detailed log of troubleshooting an Ender 5 Pro with OctoPrint, Marlin firmware, and hardware upgrades, including a Raspberry Pi 5 case design and safety enhancements.
date: 2025-09-12
categories: [3DPrinting, HomeLab, RaspberryPi, HardwareUpgrades]
author: moses
tags: [Ender5Pro, OctoPrint, Marlin, BLTouch, RaspberryPi5, BuckConverter, NF-A4x10, KillSwitch]
hideToc: true
---

## 1. 3‑D Printer Troubleshooting – The Ender 5 Pro Story

Yesterday’s session was a classic reminder: **3 printers can be temperamental**. I was trying to print a custom case for a **Raspberry Pi 5**—the same model had no trouble with dozens of Pi 3 cases—yet the print failed outright.

| What I Did | Result |
|-----------|--------|
| Raised the bed temperature | Still failed |
| Slowed the print speed (to “draft” levels used previously) | Still failed |
| Disabled the bed‑leveler plugin | The issue persisted |

The bed‑leveler plugin was also misbehaving, so I suspect an incorrect configuration or a missing Z‑offset setting.  

### Why the Ender 5 Pro is a Moving Target

The Ender 5 Pro is built on multiple motherboard variants (original, R4, and newer). When I opened the case to inspect the board, I realized I had to **re‑uncover the hardware every time**. That’s why I’m treating this session as a chance to **upgrade** as well—rather than just fixing the print.

---

## 2.  Firmware & Bed‑Leveling Fixes

**BLTouch** was not recognized by the firmware. The most common culprit is an incorrect Z‑offset value:

```ini
# In printer.cfg
[bltouch]
x_offset: 0
y_offset: 0
z_offset: -1.0   ; <-- may need adjustment
```

I toggled the offset from **negative to positive** (and vice‑versa) using the “Fine Tuner” stepper control in OctoPrint. Unfortunately, the visualizer plugin (`bed‑leveling‑visualizer`) still had missing G‑code directives, which threw the entire queue off.

> **Lesson:** Whenever you change a firmware setting, double‑check the associated OctoPrint plugin configuration. A single typo can cause the print to crash.

---

## 3.  Hardware Upgrades – Keeping the Lab Safe & Efficient

### 3.1. Fans & Cooling

While cleaning, I found **NF‑A4x10** and **NF‑A4x20** fans.  
- **20 mm fan** will replace the current extruder fan.  
- **10 mm fan** will go inside the printer’s enclosure for general ventilation.

### 3.2. Buck Converters & Enclosures

I discovered several **buck converter** modules with built‑in voltage meters. I printed a **slightly larger case** to accommodate the meter, but it was still a tight fit.  

> **Tip:** When designing enclosures for electronic modules, always leave a 2‑mm clearance on all sides to avoid accidental shorting.

### 3.3. Safety Enhancements

1. **Kill Switch** – a manual power‑off button is now wired to both the printer and the Raspberry Pi.  
2. **Temperature Sensors** – a PT100 sensor will be added to the Pi’s case to monitor ambient temperature.  
3. **Enclosure** – a clear acrylic enclosure with a hinged lid will protect the printers from dust and accidental touches.  
4. **Fire‑Suppression Robot** – a small 3‑D‑printed robot with a built‑in fire extinguisher is in the works for future safety.

---

## 4.  The Bigger Picture – Why One Reliable Printer Matters

At first, the project felt like a sprint to print a single buck‑converter case. But each upgrade adds **redundancy** and **safety** to the lab:

- A second working printer of the same model ensures continuous operation if one fails.  
- Better cooling and power management reduce the risk of overheating.  
- A kill switch and sensors provide immediate response to faults.

**Result:** The Ender 5 Pro now prints more reliably, and the lab’s overall safety posture has improved dramatically.

---

## 5.  Quick Wins – The “Pipboy” (Hostname) Fix

I had to correct the printer’s hostname for networking:

```bash
sudo nano /etc/hostname
sudo nano /etc/hosts
```

Make sure the hostname matches in both files, then reboot.

---

## 6.  Bonus: Offline Wikipedia with Kiwix

When I’m in the middle of a print and lose internet access, I rely on **Kiwix** to keep Wikipedia offline. Install it with:

```bash
sudo apt update && sudo apt install kiwix kiwix-tools
```

Download the desired ZIM files for offline use, and you’ll have a knowledge base on your wrist—no network required.

---

## 7.  Next Steps

| Task | Status |
|------|--------|
| Re‑flash the entire printer with the latest Marlin 2.1.x | ✅ |
| Finish printing the buck‑converter case | ✅ |
| Add PT100 temperature sensor to the Pi | ✅ |
| Finalise enclosure design | In progress |
| Prototype the fire‑suppression robot | Upcoming |

---

### Final Thoughts

A single printer can feel like a handful, but with the right firmware tuning, hardware upgrades, and safety measures, you can turn a lab into a **robust, reliable, and safe** environment. Stay tuned for the next log—maybe it’ll be about building that robot that puts out fires!

---

**Keywords:** Ender 5 Pro, OctoPrint, Marlin, BLTouch, Raspberry Pi 5 case, NF‑A4x10 fan, NF‑A4x20 fan, buck converter enclosure, kill switch, PT100 sensor, Kiwix, offline Wikipedia, 3D printing safety, HomeLab, hardware upgrade.
