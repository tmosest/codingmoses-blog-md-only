---
title: Day 24 HomeLab - HomeLab Adventures: ESP32, 3‑D Printing, and More
description:  A deep dive into building an ESP32‑powered RC car, troubleshooting a 3‑D printer, and exploring Raspberry Pi UAV projects. Includes code snippets, hardware hacks, and SEO‑friendly tech insights.
date: 2025-09-18
categories: ["HomeLab", "Embedded Systems", "3D Printing"]
author: moses
tags:  ["ESP32", "MicroPython", "Wi‑Fi Manager", "3D Printing", "Raspberry Pi", "Warp Terminal", "RC Car", "Vertical Farming"]
hideToc: true
---

# 🚗 Hot Wheels RC Spy Race Car POV – ESP32 & Sense HAT

> “Granny shifting when you should have been double‑clutching…” – *Fast & Furious*  

I started a new GitHub repo and picked up where I left off. I had a 12 V‑to‑5 V buck converter that fried during a power surge, so I rebuilt it from scratch. Unlike the Raspberry Pi, this little power adapter didn’t feel *too* dramatic when it failed.

## Power & Control Layout

- **Power Rig**: One breadboard supplies regulated 5 V/3.3 V to the ESP32 and the rest of the circuitry.
- **RC Car Components**: A second breadboard holds the motor driver, ESC, and the sensor array.

## Getting the Software in Gear

We’re using MicroPython on the ESP32, and the following resources were invaluable:

- [MicroPython Wi‑Fi Manager for ESP32/ESP8266](https://randomnerdtutorials.com/micropython-wi-fi-manager-esp32-esp8266/)
- [Xiao ESP32S3 with MicroPython](https://wiki.seeedstudio.com/xiao_esp32s3_with_micropython/)

Once the Wi‑Fi Manager is up, the ESP32 can be configured as an Access Point (AP) or join an existing network. The next step is to flash the firmware and test the remote‑control logic.

> **Next Day Preview** – I’ll recap the last episode of *Dragon Ball Z* and dive deeper into the RC car’s telemetry stack.

---

# 🌱 Vertical Farming – From Wordpress Plugin to Disaster Relief Vessel

Vertical farming is a passion of mine, especially given the droughts and food shortages plaguing the U.S. My previous project was a WordPress plugin that let community members add plants to a shared database, complete with QR codes that linked back to the plant’s page.

### Key Features I Built

1. **Automatic Lighting Control** – A simple relay circuit turned grow lights on/off based on a light sensor.
2. **QR Code Generation** – Each plant entry had a QR code that opened the plant’s detail page.
3. **Robotic Photography (In‑Progress)** – I was developing a small robot to capture images of the plants and QR codes for automatic uploads to WordPress.

### Future Vision

I want to scale this into a **barge‑class** vertical farm that can harvest and desalinate seawater. With modular sensors and AI‑driven nutrient management, such vessels could serve as **disaster relief** platforms in coastal regions.

---

# 🧵 3‑D Printer Troubleshooting – CR‑Touch & Firmware

My Ultimaker‑style printer’s CR‑Touch probe was misaligned. I needed accurate nozzle‑to‑probe offsets to enable reliable bed leveling.

```cpp
// NOZZLE_TO_PROBE_OFFSET { X, Y, Z }
#define NOZZLE_TO_PROBE_OFFSET { -53, -12, 0 }  // old, incorrect
```

After recalibrating with the LCD and console commands (`M851`, `M500`, etc.), I updated the offset to:

```cpp
#define NOZZLE_TO_PROBE_OFFSET { -53, -24, 0 }  // new, accurate
```

### What I Learned

- **Calibration**: Use a caliper to measure the probe’s X/Y distance from the nozzle tip.
- **Firmware Tweaks**: `PROBE_OFFSET_WIZARD` and `M500` store the new settings to disk.
- **Practical Tips**: Check for DC motor binding or an empty bucket before assuming a firmware bug.

---

# 📚 Tinkering with T‑Shirt and Other Tech Tools

## Warp Terminal – A Rust‑Powered Command Line

Warp (built in Rust) offers:

- **Command History & Autocomplete**: Learns from your command patterns.
- **Warp AI**: Integrates an OpenAI model for on‑the‑fly suggestions.
- **Warp Drive**: Cloud‑based collaboration templates for multi‑user workflows.

> *Windows users* find Warp particularly useful for quick Python environment fixes. *macOS* users may hit a few quirks, especially with the AI wizard rewriting `platform.ini` files. Still, it’s worth a try for the time‑savings on command suggestions.

---

# 🚁 Raspberry Pi UAV – OS Flashing & UAV Frameworks

The next milestone is flashing a flight‑control OS onto a Raspberry Pi and hooking it to a UAV.

| Resource | Focus |
|----------|-------|
| [ArduPilot on Raspberry Pi via MAVLink](https://ardupilot.org/dev/docs/raspberry-pi-via-mavlink.html) | Full‑stack PX4/ArduPilot control |
| [DroneKit‑Python Quickstart](https://dronekit-python.readthedocs.io/en/latest/guide/quick_start.html) | Python API for mission scripting |
| [RPanion Server](https://www.docs.rpanion.com/software/rpanion-server) | Lightweight Raspberry Pi web server for real‑time telemetry |

I’ll test each of these in succession, starting with RPanion for rapid prototyping and later migrating to a full ArduPilot stack.

---

# 🧑‍💻 Warp Terminal on macOS – AI Features & Platform.ini Issues

> I’ve used Warp on Windows to smooth out my Python setup, but macOS can be unforgiving. The AI “sandbox” sometimes rewrites `platform.ini` files (adding duplicate entries or deleting keys). Still, the auto‑completion and pattern recognition dramatically reduce command‑line fatigue.

> **Recommendation**: Use Warp on Windows for quick setup; macOS users should manually edit `platform.ini` after Warp AI runs.

---

# 🐶 Wall E Trash Bot – A Practical Take on a Sci‑Fi Concept

While walking the dog, I noticed a lot of litter in the yard. This sparked the idea for a **practical Wall E‑inspired trash‑collecting robot**.

### Core Components

- **Camera Module** (e.g., Raspberry Pi Camera V2) – for object detection.
- **DC Motor Wheels** – 2–4 motors depending on the desired speed and torque.
- **Dog Poop Scooper Bucket** – A lightweight, low‑profile container.
- **Control Board** – ESP32 or Raspberry Pi Zero W for wireless operation.

> The design will prioritize *safety* and *efficiency* over pure novelty.

---

# 🔧 Sonic Screwdriver – Flashlight Battery Repair

I disassembled an old flashlight because the battery seemed dead. Turns out it was a *locked* cell, not a drained one. Re‑assembling required:

1. **Extracting the battery** – Using a small screwdriver set.
2. **Re‑installing the battery** – Ensuring polarity and secure contacts.
3. **Testing** – Checking the LED’s forward voltage (~3 V) before final re‑assembly.

---

# 🏠 Garage Golden Dome & Palantir‑Inspired Data Hub

I’m on the brink of building my own *Palantir Gotham*‑style data center in a garage‑style startup. The vision is to harness GPU servers for:

- **Advanced AI workloads** (e.g., image classification, natural language processing)
- **Real‑time telemetry dashboards**
- **Secure data collaboration** (think *Palantir Gotham* but on a budget)

> **Funding Challenge**: I’m looking for high‑performance GPUs—perhaps through a partnership or by leveraging existing cloud credits.

---

# 📚 ChatGPT Proof‑Reader & Writing Assistant

I’ve experimented with ChatGPT as a content‑enhancement tool. It’s great for:

- **Grammar & Style Checking**  
- **Technical Writing** – generating code documentation and summaries  
- **SEO Optimization** – suggesting keyword‑rich phrasing  

> *Tip*: Start prompts with “Improve this article for a tech audience” and iterate on the output until it meets your style guidelines.

---

# 🗑️ Wall E Trash Bot – A Quick Design Sketch

I want a robot that can:

- **Detect litter** via a camera and simple object‑detection algorithm
- **Navigate** using DC motors and a skid‑steer chassis
- **Collect waste** in a small bucket that can be emptied at a base station

> **Next Step**: Prototype the wheel assembly and mount a small camera for live feed.

---

# 🎮 Warp Terminal & AI Enhancements

> I’ve used Warp on Windows to fix my Python environment swiftly. On macOS, the AI wizard sometimes rewrites your `platform.ini` file—use it cautiously, but the pattern‑based suggestions save a lot of manual typing.

---

## TL;DR

- Rebuilt a robust power supply for the ESP32 RC car.
- Leveraged MicroPython Wi‑Fi Manager to configure network modes.
- Calibrated 3‑D printer probe offsets and updated firmware.
- Expanded vertical‑farm plugin into a scalable disaster‑relief concept.
- Troubleshot CR‑Touch probe misalignment.
- Discussed AI‑powered terminals and Raspberry Pi UAV frameworks.
- Outlined a practical Wall E‑inspired trash‑collecting robot.

Happy hacking, and keep those code snippets clean! 🚀

```