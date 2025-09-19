---
title: Day 21 HomeLab - 
description: Placeholder
date: 2025-09-15
categories: []
author: moses
tags: []
hideToc: true
---

Absolutely—happy to keep the exploration going! Below are a few “next‑step” ideas that should help you keep your experiments rolling, plus a quick fix for getting your custom wake‑word script to auto‑start on boot (both with `systemd` and a simple shell wrapper).  

---

## 1️⃣  Quick‑start auto‑start with **systemd** (recommended)

`systemd` gives you robust service management, restarts on failure, and easy logging.

```bash
# 1. Create the unit file
sudo nano /etc/systemd/system/whisper‑wake.service
```

Paste the following (replace paths and names as needed):

```ini
[Unit]
Description=Whisper Wake‑Word & STT service
After=network.target

[Service]
User=pi          # or the user that owns the repo
WorkingDirectory=/home/pi/whisper‑wake
ExecStart=/home/pi/whisper‑wake/venv/bin/python /home/pi/whisper‑wake/whisper_trigger.py
Restart=on-failure
RestartSec=5

# Optional: capture stdout/stderr to a log file
StandardOutput=append:/var/log/whisper‑wake.log
StandardError=append:/var/log/whisper‑wake.log

[Install]
WantedBy=multi-user.target
```

```bash
# 2. Reload systemd, enable & start the service
sudo systemctl daemon-reload
sudo systemctl enable whisper‑wake.service
sudo systemctl start whisper‑wake.service
```

You can check the status/logs with:

```bash
sudo systemctl status whisper‑wake.service
sudo journalctl -f -u whisper‑wake.service
```

---

## 2️⃣  If you prefer the **desktop‑autostart** method

```bash
mkdir -p ~/.config/autostart
nano ~/.config/autostart/whisper_trigger.desktop
```

Insert:

```ini
[Desktop Entry]
Type=Application
Name=Whisper Trigger
Exec=/home/pi/whisper‑wake/venv/bin/python /home/pi/whisper‑wake/whisper_trigger.py
Comment=Run Whisper wake‑word + STT at login
NoDisplay=true
X-GNOME-Autostart-enabled=true
```

This works great for desktop PCs, but `systemd` is more reliable on a headless Pi.

---

## 3️⃣  Debugging Python‑Version Hiccups

*If you hit “module not found” or “import error” after upgrading Python:*

```bash
# Activate the Home Assistant VM
sudo -u homeassistant -H bash
cd /srv/homeassistant
source bin/activate

# Reinstall missing dependencies
pip install --upgrade --force-reinstall cryptography
pip install --upgrade homeassistant
```

If `cryptography` still fails, install the libssl‑dev package:

```bash
sudo apt-get install -y libssl-dev
```

---

## 4️⃣  Next‑phase experiments

| Idea | Quick win | Next step |
|------|-----------|-----------|
| **Matter Hub** add‑on | 🎉 | Control Alexa & Google devices via *Matter* (no cloud subscription). |
| **OpenWakeWord + Whisper** in Home Assistant | ⚙️ | Add `openwakeword` and `fast-whisper` components, upload your `.tflite` model to `/share/openwakeword`. |
| **Alexa Media Player** | 🎧 | Play announcements, run routines from Home Assistant. |
| **Custom Python pipeline** | ✅ | Keep your original script, maybe bundle it as a Docker container or a `systemd` service. |

---

### TL;DR

1. **Auto‑start**: Use `systemd` for a headless Pi (recommended) or desktop‑autostart for quick testing.  
2. **Python woes**: Install system libs → use a virtualenv → `pip install -U` → `deactivate` when done.  
3. **Home Assistant + Alexa**: You can go fully native (`HA Cloud`) or use the `Matter Hub` add‑on to avoid a paid subscription.  
4. **Play around**: Upload a custom wake‑word `.tflite` to `/share/openwakeword` and wire it to Whisper via Home Assistant’s voice‑assistant integration.

Let me know which direction you want to dig deeper into—or if you hit a snag, drop me a line and we’ll troubleshoot together!