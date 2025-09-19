---
title: Day 20 HomeLab – Upgrading Bezalel & OctoPrint
description: A detailed look at upgrading a Raspberry Pi‑based 3D printer, troubleshooting Wi‑Fi issues, and refining home‑lab networking.
date: 2025-09-14
categories: [HomeLab, 3D Printing, Raspberry Pi]
author: moses
tags: [Raspberry Pi, OctoPrint, Bezalel, HomeLab, Wi‑Fi, Cable Management, 3D Printing]
hideToc: true
---

# Day 20 HomeLab – Upgrading Bezalel & OctoPrint

Yesterday I left off the last 3D‑print that was taking longer than expected.  
Today I finally finished it, but the machine behaved like it was in a **quantum‑build** state: one part printed cleanly, while the center piece collapsed into a tangle of filaments.  The culprit turned out to be a lost Wi‑Fi connection during the print, forcing me to rethink my HomeLab network architecture.

---

## 1.  The Wi‑Fi Black‑Hole

When the Wi‑Fi dropped mid‑print, OctoPrint sent an error that looked like this:

```text
Connection lost – printing interrupted
```

After a quick reboot, the network came back, but the 3‑D‑printer never resumed.  The lesson?  
**Stability matters**.  Even a brief disconnect can wipe an entire print job.

---

## 2.  Re‑designing the HomeLab Network

I spent the rest of the day overhauling the network layout.  The cable management was a nightmare—unsecured cables ran straight across the floor, and many RJ‑45 connectors were in the wrong positions.

Key changes:

| Problem | Fix |
|---------|-----|
| Loose cables | Strain‑relief clips & color‑coded zip ties |
| Incorrect wall‑plate positions | Re‑ordered wall plates to match the new topology |
| Poor Wi‑Fi coverage | Added a mesh‑router to cover the entire lab room |

With a clean setup, the printer now connects reliably to OctoPrint and the Raspberry Pi controlling the feeder motor.

---

## 3.  The Raspberry Pi Blunder

The biggest mistake of the day: **mis‑wired the Pi’s power pin**.

### What happened?

I had a dog barking nearby, and I inadvertently slipped the USB cable into the wrong GPIO pin.  The board now might be irreparably damaged.  

> **Did I break the Pi?**  
> **Short answer:** Possibly, but there are workarounds.

### Why this is a risk

The Raspberry Pi’s **micro‑USB (Pi 3) or USB‑C (Pi 4)** port is protected by a **polyfuse** that limits current to prevent damage.  The **GPIO header** lacks this protection, so any over‑current or mis‑wired power source can fry the board.  

> *“Yes, the Raspberry Pi's microUSB (or USB‑C on Pi 4) port includes a polyfuse for over‑current protection, but the GPIO pins do not. Powering the Pi through the GPIO header bypasses this protection, leaving the board vulnerable to power‑related issues like short circuits or over‑voltage if not handled with care.”*

### Fixing the damage

I upgraded the power source to an **old micro‑USB charging cable** that has its own over‑current protection.  I also replaced the power board with a **dedicated Pi‑specific power supply** (3.3 V/5 V switching supply) that keeps the current within safe limits.

---

## 4.  Printer‑Specific Troubleshooting

### Air‑flow nozzles

Another issue: the **air‑flow nozzles** for the printer’s cooling system weren’t printing correctly.  The solution involved:

1. **Slicing adjustments** – increasing the fan speed and layer height to reduce heat‑shock.
2. **Hardware tweak** – adding a small duct around the hot‑end to direct airflow better.

### Celebrimbor issues

Celebrimbor, the open‑source slicer I’m experimenting with, had intermittent errors.  I found that updating to **v1.2.4** resolved the “file not found” crashes during print preparation.

---

## 5.  A Brief Detour – Daddy Dog Care

While the network was re‑wired, I took a quick break playing **“Daddy Dog Care”**.  It’s a fun reminder that even the best‑planned labs need a little downtime—so I kept a safe distance from the wiring when the dog got a bit too excited.

---

## 6.  Take‑Away Points

| # | Key Insight |
|---|--------------|
| 1 | Keep your Wi‑Fi signal strong; a single drop can ruin a print. |
| 2 | Wire your Raspberry Pi through the protected USB/USB‑C port. |
| 3 | Use a dedicated power supply with over‑current protection. |
| 4 | Clean cable management improves reliability and safety. |
| 5 | Test slicer updates before committing to large jobs. |

---

### Final Thoughts

After a series of adjustments—network re‑design, power supply upgrade, and a couple of slicing tweaks—Bezalel is now **stable** and ready to take on more complex prints.  OctoPrint reports no more interruptions, and the Wi‑Fi connection remains steady throughout.  The home‑lab network is cleaner, more organized, and resilient to future disruptions.

I’m looking forward to tackling the next set of prints, armed with a better‑wired Pi, a reliable network, and a fresh cup of coffee. Happy printing!