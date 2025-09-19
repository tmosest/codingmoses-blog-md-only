---
title: "Day 22: Upgrading Bezalel and Expanding the HomeLab"
description: "From 3‑D‑printed feet to a Raspberry Pi‑powered flight controller, this day’s update covers the latest hardware upgrades, DIY projects, and a deep dive into running an LLM on a low‑power device."
date: 2025-09-16
author: moses
tags: ["HomeLab", "Raspberry Pi", "3D‑printing", "flight controller", "autopilot", "LLM", "voice‑assistant"]
hideToc: true
---

# 1️⃣ Upgrading Bezalel

After a week of sourcing parts, I finally pulled all the components needed for **Bezalel**—my custom Raspberry Pi case that doubles as a small air‑frame. The print itself was a beast: 3‑D‑printed in PETG, two × 30 cm, and required several support removals.

## 1.1 Assembly Highlights

* **Fasteners** – Screws everywhere! A quick “screws, screws, screws” reminder that the more hardware you have, the more screws you’ll need.  
* **Fan Replacement** – Swapped the stock 40 mm fan for a larger 60 mm unit. The larger fan improves airflow across the entire lower section of the case, which keeps the Pi cooler under load.  
* **Footprint Options** – After printing the feet, I compared a handful of designs on Thingiverse:

| Design | Link | Notes |
|--------|------|-------|
| Feet with feet lol | <https://www.thingiverse.com/thing:5538153> | Simple, quick to print |
| Most practical | <https://www.thingiverse.com/thing:6672580> | Balanced, good weight distribution |
| Hmmm springs | <https://www.thingiverse.com/thing:3660131> | Adds dampening |
| Elegantly simple | <https://www.thingiverse.com/thing:5160624> | Minimalist, low profile |
| Uncertain balance | <https://www.thingiverse.com/thing:3856997> | Needs further testing |
| ADHD take over… | <https://www.thingiverse.com/thing:3542205> | Multi‑purpose feet |

I’ve chosen the **Most practical** set for now because it offers a good compromise between strength and weight.

> *What took you so long, master…* – <https://youtu.be/_ADoDPp7NP0?si=YNWgrtCsirHIfN_V&t=99>

## 1.2 Functionality Check

Propped Bezalel on an old printer chassis and verified that the stepper drivers home correctly. The system halts when I try to set the Z‑offset—this points to a firmware mismatch or a missing calibration step. I’ll investigate this further, but for now I’ll time‑box the debugging to keep the project moving.

---

# 2️⃣ The “Celebrimbor” Pi Case

I’m still happy with **Celebrimbor** (the secondary Pi case), but the 3‑D‑print failed to match the designed dimensions. Instead of re‑printing, I’ll print new feet to compensate for the mis‑print. Remember: **feet are the “money” of 3‑D‑printed cases**.

> *You can find me on OnlyFans if this doesn’t work out as Big Foot.*  
> (Just a little humor to lighten the engineering grind.)

---

# 3️⃣ Pi Hawk (Red Five) – The Flight Controller

The **Pi Hawk** project is getting closer to completion. It’s a hybrid between a Raspberry Pi and a classic Pixhawk flight controller, designed for hobbyist UAVs.

| Component | Role |
|-----------|------|
| Raspberry Pi 4B (4 GB) | On‑board processing, LIDAR, vision |
| Pixhawk 4 Mini | Autopilot, ESC control, failsafe |
| Radio Telemetry | 915 MHz 2 way link |
| GPS / GLONASS | Positioning |
| Barometer | Altitude |

### 3.1 Software Stack

* **PX4 Firmware** – Primary flight stack for Pixhawk: <https://docs.px4.io/main/en/flight_controller/pixhawk_series>  
* **ArduPilot & AP‑Sync** – Alternative autopilot option with AP‑Sync to sync between Raspberry Pi and Pixhawk: <https://ardupilot.org/dev/docs/apsync-intro.html#apsync-intro>  
* **Video Guide** – “Getting started with Pi Hawk” (YouTube): <https://www.youtube.com/watch?v=nIuoCYauW3s>

**Next steps:** Write a ROS‑based wrapper to convert Pixhawk telemetry into ROS topics so the Pi can run computer‑vision or autonomous navigation algorithms.

---

# 4️⃣ Furby Five – The Musical Drone

Picture a 1990s Furby perched on top of a quadcopter, *“weee do it again”* blasting overhead. Turning the Furby into a musical instrument opens a playground for generative music and real‑time audio processing.

* **Microcontroller** – ESP32‑C (dual‑core, Wi‑Fi/BLE)  
* **Audio Output** – 3‑W speaker via MAX98357A amplifier  
* **Sensors** – 9‑DoF IMU for dance‑style motion sync  
* **Control** – MIDI over Wi‑Fi to a DAW

> *Stay tuned for 44 Furby delivery drones coming soon!* (TODO)

---

# 5️⃣ Hot Wheels Spy Car – Mini RC Car

I disassembled a Hot Wheels car and re‑engineered it into a compact remote‑controlled vehicle.

| Component | Description | Link |
|-----------|-------------|------|
| DC Motor (12 V) | Drives rear wheels | [Amazon] |
| Linear Servo | Turns front wheels | [Amazon] |
| Battery | 7.4 V LiPo | [Amazon] |
| ESP32‑C | Controls motor drivers | [Amazon] |
| Male‑to‑Male Connectors | For prototyping | [Amazon] |

(Links to be added.)

---

# 6️⃣ Pip Boy – Voice Assistant & LLM on a Pi

I’m turning the **Pip Boy** into a pocket voice‑assistant powered by an on‑device LLM.

## 6.1 Voice Detection

* **Speech‑to‑Text Engine** – Vosk (offline)  
* **Microphone** – USB condenser mic  
* **Result** – Text is streamed over MQTT to the local LLM server.

## 6.2 Running LLM with Ollama

Ollama provides a lightweight wrapper for running large language models on Raspberry Pi. With 16 GB RAM (Raspberry Pi 4B), you can comfortably run:

| Model | Parameters | Notes |
|-------|------------|-------|
| Llama2‑13b | 13 b | Stable, good general‑purpose |
| Phi‑3 Mini | 14 b | Efficient, lower RAM footprint |
| TinyLlama / Phi | <1 b | Fast inference, lightweight |

```bash
ollama run llama2:13b
```

**Sample Output**

```
With 16 GB of RAM, your Raspberry Pi can comfortably handle larger language models than the standard 8 GB model. Here are some options:
- Llama2:13b: A robust model with 13 billion parameters that performs well on a 16 GB system.
- Phi-3 Mini (14b): A 14 billion parameter model that is efficient for its size.
- Smaller models: You can also run smaller, faster models like phi or tinyllama.
```

I tested the 7 GB variant and it still handled Llama2‑13b with acceptable latency, thanks to the model quantization in Ollama.

## 6.3 Integrating with Kali Linux

After installing Kali Linux on the Pi, I needed to migrate `katoolin` to Python 3:

```bash
git clone https://github.com/tmosest/katoolin
cd katoolin
python3 setup.py install
```

This streamlined the workflow: the Pip Boy performs speech‑to‑text locally, forwards the text to the Ollama server, and receives the LLM response in real time.

---

## 7️⃣ Summary & Next Steps

| Project | Status | Immediate Goal |
|---------|--------|----------------|
| Bezalel | Completed parts | Debug Z‑offset calibration |
| Celebrimbor | Failed print | Print new feet |
| Pi Hawk | Hardware assembled | Implement ROS integration |
| Furby Five | Concept | Build prototype |
| Hot Wheels Spy Car | Parts list | Assemble RC car |
| Pip Boy | Voice+LLM | Deploy local LLM service |

**SEO Boost** – By focusing on terms such as “Raspberry Pi 3‑D‑printing”, “Pixhawk flight controller”, “LLM on Raspberry Pi”, and “DIY RC car”, this article is tailored for enthusiasts searching for detailed DIY tech guides.

Happy hacking, and stay tuned for more HomeLab updates!