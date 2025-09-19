---
title: Day 23 HomeLab – Kali Refactor, Voice Clone & ESP‑32 Wake‑Word  
description: A deep dive into upgrading the HomeLab stack – from a clean Kali Linux refactor to a custom voice‑cloning pipeline and a DIY ESP‑32 wake‑word trainer.  
date: 2025-09-17  
categories:  [HomeLab, Security, AI, IoT]
author: Moses  
tags: [kali, katoolin, Python3, voice-clone, voice-ai, esp32, wake-word]
hideToc: true  
---

# 1. Upgrading Bezalel

> “The Lord is my shepherd; I shall not want…”  
> — Psalm 23 (excerpt)

In the midst of a day filled with **foot‑image uploads** that led nowhere, I finally found a clear focus: *upgrade the Core of my HomeLab*.  

## Why Bezalel?

Bezalel is the nickname we give to the central machine that runs all of our security and AI experiments. With an aging OS and outdated tooling, it was time for a serious refresh.

---

# 2. Kali Linux – katoolin Refactor

Kali Linux is the go‑to penetration‑testing distro for our lab. I’ve been using **katoolin**, a wrapper that installs Kali tools on any Debian‑based system. The original repo was written for Python 2 and Debian 8, but my lab runs on **Python 3.9+** and **Debian 12**.  

### 2.1 The Original `katoolin`

```bash
# Original katoolin (Python 2)
git clone https://github.com/instill-lab/katoolin.git
cd katoolin
python2 katoolin.py
```

The script pulls the Kali repositories, installs `apt‑get` wrappers, and lets you install or upgrade Kali tools via `apt`. However, it quickly fell out of sync with the official Kali repositories.

### 2.2 The Refactor (Python 3.9)

| Step | Old | New |
|------|-----|-----|
| Dependency | Python 2 | Python 3.9 |
| Package Manager | `apt‑get` wrappers | Modern `apt` with `python3-apt` |
| Tooling | Manual list updates | Automated string‑indexed mapping |
| Compatibility | Debian 8 | Debian 12 / Debian‑based distros |

#### Key Highlights of the Refactor

- **String‑indexed tool mapping** eliminates brittle numeric indexes.  
- `install_all` now gracefully skips missing tools rather than crashing.  
- The script now uses the **official Kali repositories** (`http://http.kali.org/kali`) for package metadata.  

```python
# Example of the cleaned tool mapping
TOOLS = {
    'bing-ip2hosts': "wget http://www.morningstarsecurity.com/downloads/bing-ip2hosts-0.4.tar.gz && "
                     "tar -xzvf bing-ip2hosts-0.4.tar.gz && "
                     "cp bing-ip2hosts-0.4/bing-ip2hosts /usr/local/bin/",
    'ntop': 'echo ntop is unavailable',
}
```

> *Commit 878589458ac410d15db15d8dab18225933535409 – “Clean, maintainable katoolin for HomeLab”*  

With the refactor in place, `katoolin` now supports **Python 3**, runs flawlessly on the Raspberry Pi, and allows us to deploy a fully‑featured Kali suite on any Debian‑based host.

---

# 2. Voice Clone – Building a Personal AI Assistant

The second pillar of today’s lab work was experimenting with **neural voice cloning**. I have a sizeable dataset of my own voice recordings paired with transcriptions, which is a perfect fit for a *few‑shot* cloning pipeline.

## 2.1 Installing Dependencies on Apple M1

The **pixellib** library (a popular image‑segmentation wrapper) is an example of how I handle Apple M1 constraints:

1. **PyQt5 is not M1‑friendly** – we switch to **Qt 6**.  
2. Install **PyQt6**:  
   ```bash
   python3 -m pip install PyQt6
   ```
3. Grab the wheel for `pixellib‑0.7.1`, unpack it, replace `PyQt5` with `PyQt6` in the `METADATA`, repack and install.  

> *See the detailed walkthrough on Reddit and the official Qt 6 docs for the full recipe.*

## 2.2 Demo CLI – Quick Tests

The `demo_cli.py` script from the **Real‑Time Voice Cloning** repo is now fully compatible with **Python 3.9**:

```bash
git clone https://github.com/IAHispano/Applio.git
cd Applio
python3 demo_cli.py
```

I ran short passages from Psalms and noticed that the script **stalls when memory usage spikes** – a common issue when generating multiple voices in succession. Nevertheless, the quality of the cloned voice improves dramatically when the transcription matches the source material.

> *Key takeaway:* The demo can synthesize a voice in seconds, but consider adding a memory‑clear step after each generation to prevent crashes.

---

# 3. Training a Custom Wake‑Word on ESP‑32

I’ve been dreaming of turning a Furby into a **Alexa‑style personal assistant**. The Raspberry Pi + Picovoice combo was great for proof‑of‑concept, but it’s time to push the boundaries by training a wake‑word model that runs directly on an **ESP‑32**.

## 3.1 Gathering Data

The wake‑word model needs three types of audio:

1. **Target phrase** – recordings of me saying the exact wake phrase.  
2. **Background chatter** – random speech and ambient noise.  
3. **Negative examples** – quiet rooms and unrelated sounds.

Collecting a balanced dataset allows the model to distinguish the phrase from other sounds.

## 3.2 Training Pipeline

1. **Data collection** – use the `wakeword-data-collector` repo to annotate and curate the audio files.  
2. **Model generation** – the `precise-wakeword-model-maker` trains a lightweight DNN with 5‑minute data.  
3. **Edge deployment** – export the model as a flatbuffer and flash it onto the ESP‑32.  

> *Reference:* https://github.com/secretsauceai/precise-wakeword-model-maker

## 3.3 ESP‑32 Setup

- **MicroPython** or **Arduino‑Framework**? I’m leaning towards **Arduino** for stability.  
- Use a **buck converter** (12 V → 5 V → 3.3 V) to power the ESP‑32 and the RC car.  
- Wi‑Fi manager (see Random Nerd Tutorials) for auto‑configuration.  

```python
# Example of Wi‑Fi manager in MicroPython
import network
import wifi_manager

sta_if = network.WLAN(network.STA_IF)
sta_if.active(True)
wifi_manager.start(sta_if)
```

> *Resources:*  
> * [MicroPython Wi‑Fi Manager – ESP32](https://randomnerdtutorials.com/micropython-wi-fi-manager-esp32-esp8266/)  
> * [Xiao ESP32S3 with MicroPython](https://wiki.seeedstudio.com/xiao_esp32s3_with_micropython/)

---

# 4. Hot Wheels RC Spy Race Car – ESP‑32 Edition

I took a **12 V to 5 V** adapter that had fried and replaced it with a reliable buck converter. The car now runs on two breadboards: one for power management, the other for motor control and sensors.

> *Next step:* Implement PID control for the steering servo and integrate the ESP‑32 wake‑word module to trigger the car’s movement with a spoken command.

---

# 5. Closing Thoughts

The day began with a playful detour into **foot‑image uploads**, but it ended on a solid technical note. From a clean, Python‑3‑ready Kali Linux stack, through a quick‑turn voice‑cloning demo, to a DIY ESP‑32 wake‑word trainer, the lab’s productivity has never looked better.

**Next up:** Implementing vision AI on the Furby to recognize other Furbies and deliver customized jokes—yes, even *Yo Furby* jokes. Stay tuned for Day 24!

```