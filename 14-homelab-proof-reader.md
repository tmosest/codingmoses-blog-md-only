---
title: Day 14 HomeLab – Octoprinting NAS’s
description: Learn how to set up Octoprint on an Ender 5, integrate a BL‑Touch probe, automate 3D‑print monitoring, and build a multi‑bay NAS with RAID for Plex streaming.
date: 2025-09-08
categories: [octoprint, appleScript, nas, plex]
author: Moses
tags: [octoprint, leetcode, applescript, nas, plex]
hideToc: true
---

# The Big Bang!

I came home after a walk with my dog to find a huge explosion in the garage.  
It wasn’t a transformer that had blown; it was a swarm of squirrels, the kind you only read about in sci‑fi.  
They had chewed through the wiring of a UPS and were ready to take over the whole house.  
To survive, I decided it was time to arm my HomeLab: a **3‑D printer**, a **NAS**, and a little bit of *Octoprint* magic.

> “There’s a fine line between wrong and visionary. Unfortunately, you have to be a visionary to see it.” – Sheldon Cooper

---

## Octoprint on a Modified Ender 5

### Why Octoprint?

Octoprint is a free, open‑source web interface that turns a Raspberry Pi into a 3‑D‑printing remote control center.  
Key benefits include:

| Feature | What it does |
|---------|--------------|
| Web UI | Start, pause, cancel, and monitor prints from any device |
| Plugin ecosystem | Extend functionality (e.g., Twilio alerts, Google Drive uploads) |
| API | Integrate with custom scripts or third‑party services |

### Hardware Overview

- **Printer**: Creality Ender 5 (modified for a Raspberry Pi power feed)
- **Controller**: Raspberry Pi 4 (running Octoprint)
- **Probe**: BL‑Touch for automatic bed leveling
- **Networking**: Static IP for reliable remote access

### Firmware & Bootloader

1. **Flash new image**  
   Use the **Raspberry Pi Imager** to write the latest Octoprint OS image to an SD card.  
   *(If you prefer a custom build, you can download the latest OS from the Octoprint GitHub repository.)*

2. **Reconfigure BL‑Touch**  
   Follow the detailed guides:  
   - [All3DP – Octoprint BLTouch guide](https://all3dp.com/2/octoprint-bltouch-guide/)  
   - [SimplyPrint – Ender 5 Plus compatibility](https://simplyprint.io/compatibility/creality-ender-5-plus/octoprint-setup)

3. **Firmware updates**  
   If you’ve edited the Marlin firmware previously, recompile it to include your BL‑Touch support:  
   - [Ender 5 BL Touch: Marlin tutorial](https://www.youtube.com/watch?v=Jmu5Fh_nPtw)

### Networking & Security

- Assign the Pi a **static IP** to avoid losing connection when the printer restarts.
- Enable HTTPS on Octoprint for secure remote access (Octoprint’s built‑in configuration wizard can help).

### Troubleshooting

When I tried to home the printer after installing a handful of plugins, the firmware killed the job.  
The likely cause was a missing or incompatible firmware build.  
Switching to a brand‑new Ender 5 in the closet resolved the issue immediately—no firmware re‑flash needed.

---

## Automating Print Monitoring with Twilio

I want the printer to be a *sentinel* for my house.  
If the temperature sensor on the Pi (or the printer’s hot‑end) spikes, I’d like an automatic alert and an optional call to 911.

1. **Twilio Integration**  
   - Create a free Twilio sandbox account.  
   - Install the `twilio` Python library on the Pi.  
   - Write a lightweight Octoprint plugin that reads the temperature sensor, checks a threshold, and triggers a Twilio SMS or voice call.

2. **Camera Backup**  
   Connect an IP camera to the Pi.  
   When a temperature alert is fired, the plugin can capture a snapshot and send it via email or push notification.

3. **Google Drive/YouTube Uploads**  
   For every completed print, automatically upload the G‑Code or an image of the print to Google Drive, and optionally post a video to a private YouTube channel.  
   This is useful for archival and proof‑of‑completion.

---

## Local Minimums – LeetCode Problem Generator Overhaul

While waiting for the Pi image to flash, I refactored my LeetCode random‑problem script.

### Original Challenges

| Problem | Issue |
|---------|-------|
| Relies on the editorial section | Editorials aren’t always available. |
| Random number generator (1–20) | Skewed probability when there are thousands of problems. |
| Heavy HTML parsing | Adds unnecessary load and complexity. |
| Does not store Java solutions | Limits reproducibility. |

### Improvements

- **Direct API calls** to the LeetCode *public* endpoints (now possible with proper headers).  
- **Uniform random selection** across the full problem set.  
- **Plain‑text extraction** of solution code, discarding irrelevant HTML.  
- **Optional Java support** – now stores the Java source in the repo alongside the solution.  

These changes not only make the script more robust but also enable future work, such as feeding problem data into an LLM to generate new algorithmic solutions.

---

## Building a Multi‑Bay NAS – “NASTY”

The home lab already had a 1 TB NAS that kept crashing.  
I built a new multi‑bay NAS with a focus on reliability and expandability.

### Hardware

- **Chassis**: 4‑bay Synology DS2419+ (or similar)  
- **Drives**: 4 × 4 TB NAS‑grade HDDs (Western Digital Red Pro)  
- **Network**: 10 GbE uplink to the router  

### RAID Configuration

| RAID | Pros | Cons |
|------|------|------|
| **RAID 0** | Highest performance (striping) | No redundancy |
| **RAID 1** | Full mirroring | 50 % storage efficiency |
| **RAID 5** | Parity over 3+ drives | Write penalty, single‑drive failure |
| **RAID 6** | Dual parity, two‑drive fault tolerance | Reduced write speed |

I chose **RAID 6** to protect against two simultaneous drive failures while maintaining decent performance for media streaming.

### Media Server – Plex

1. Install **Plex Media Server** on the NAS.  
2. Add media libraries (movies, music, photos).  
3. Configure DLNA and AirPlay for the TV, Apple TV, and other devices.  
4. Enable **remote access** over the internet with Plex’s secure tunnel.

Now I can stream MP4s from the NAS to my living‑room TV, my iPad, and even my smart speaker.

---

## Takeaway

- **Octoprint** turns a Raspberry Pi into a powerful 3‑D‑printing hub.  
- **BL‑Touch** and custom firmware make automatic bed leveling a breeze.  
- **Twilio alerts** can turn your printer into a home‑security sentinel.  
- **LeetCode automation** is now lightweight, accurate, and extensible.  
- A **RAID 6 NAS** provides reliable media storage and streaming via Plex.

Happy building, and may your prints always stay level—and your home stay squirrel‑free!
