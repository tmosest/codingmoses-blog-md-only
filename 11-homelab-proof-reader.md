---
title: Day 11 HomeLab – I Fought the Mouse and the Mouse Won...
description: Part 2 of creating a Vision AI system to detect rodents. Deep dive into YOLO, CVAT, Docker‑Compose, Traefik, and Jupyter on Proxmox.
date: 2025-09-05
categories: [Yolo, VisionAI]
author: Moses
tags: [yolo, cvat, docker, traefik, jupyter, prompox]
hideToc: true
---

# Ozymandias

Back to becoming the **Rodent‑Detection Guru**…

> *I met a traveller from an antique land…*  
> *…two vast and trunkless legs of stone…*  

This time we’re not going to exterminate the mice with a genocidal sweep. Instead, we’ll **partner** with them—opening a tiny, rodent‑friendly restaurant chain, one “mouse‑tastic” bite at a time.  

> *But first we still need to track them down and learn more about them…*

---

## 1. Preparing the Annotation Environment – CVAT on Docker‑Compose

The cornerstone of any Vision AI project is a **high‑quality labeled dataset**. For this, I’m using **CVAT (Computer Vision Annotation Tool)**, a Docker‑based solution that scales from a single VM to a full‑blown cluster.

### 1.1 Quick Start Guide

```bash
# 1. Clone the repo
git clone https://github.com/cvat-ai/cvat.git

# 2. (Optional) Set up environment variables in a .env file
echo "CVAT_HOST=<YOUR-FQDN>" >> .env
echo "ACME_EMAIL=<YOUR-EMAIL>" >> .env

# 3. Start CVAT
docker compose up -d
```

> **Tip:** Run `docker compose up` without `-d` the first time to verify that the stack starts cleanly.

### 1.2 HTTPS with Traefik & Let’s Encrypt

Securing the CVAT interface is essential, especially when handling sensitive data or operating over public networks.

```bash
# Export variables for Traefik
export CVAT_HOST=<YOUR_DOMAIN>
export ACME_EMAIL=<YOUR_EMAIL>

# Launch with Traefik overlay
docker compose -f docker-compose.yml -f docker-compose.https.yml up -d
```

**What this does:**

| Component | Role |
|-----------|------|
| **Traefik** | Reverse proxy that routes traffic to CVAT services |
| **Let’s Encrypt** | Auto‑issued TLS certificates, ensuring HTTPS out‑of‑the‑box |
| **Docker‑Compose** | Orchestrates CVAT, Traefik, and other required services |

For a full walkthrough, consult the official [CVAT installation docs](https://docs.cvat.ai/docs/administration/basics/installation/).

---

## 2. Building the Vision AI Pipeline – YOLO & Data Processing

### 2.1 YOLOv8 for Rodent Detection

I’m leveraging **YOLOv8**, the latest iteration of YOLO (You Only Look Once), which delivers **real‑time object detection** with state‑of‑the‑art accuracy. Key benefits for rodent detection include:

- **Fast inference** (≈70 FPS on a mid‑range GPU)
- **Tiny model size** (≈50 MB) – ideal for edge deployment
- **Built‑in class‑agnostic training** – perfect for custom “mouse” labels

**Training Workflow**

1. **Collect & Annotate**  
   Use CVAT to tag mouse bounding boxes. Export annotations in YOLO format (`.txt` per image).
2. **Data Augmentation**  
   Apply transforms (flip, rotation, brightness) to increase robustness.
3. **Training Script**  
   ```bash
   python train.py \
     --weights yolov8n.pt \
     --cfg yolov8n.yaml \
     --data mice.yaml \
     --epochs 100 \
     --batch 16
   ```
4. **Evaluation**  
   Assess precision‑recall and confusion matrix on a hold‑out set.

---

## 3. Proxmox as the Backbone – Jupyter for Experimentation

Running Jupyter in a Proxmox VM or LXC container provides isolated, reproducible environments for data science and quick prototyping.

### 3.1 Deploying JupyterLab on Proxmox

```bash
# Inside the VM or LXC
sudo apt update && sudo apt install -y python3 python3-pip
pip install virtualenv jupyterlab
virtualenv venv
source venv/bin/activate
jupyter lab --ip=0.0.0.0 --port=8888 --no-browser &
```

- **IP 0.0.0.0** exposes Jupyter to the local network.
- **Port 8888** is the default; change if needed.
- **`--no-browser`** ensures the server doesn’t attempt to open a browser on the headless host.

**Access**: Open `http://<VM_IP>:8888/` in your browser and enter the token displayed in the console.

> **Best Practice:** Store the Jupyter configuration and environment files in your infrastructure-as-code repo (e.g., Terrarare) for repeatability.

---

## 4. Logging the Chaos – Captain’s Log

> *The mice have won… they chewed through the power box and electric lines.*  
> *My phone is the hotspot… a solar charger helps little in the Pacific Northwest where sunlight refuses to wander.*

> *I am stuck… must escape to South America in a secret sub and join the cartel…*  

> *Or to space?*  

> *“My name is Ozymandias, King of Kings; Look on my Works, ye Mighty, and despair!”*  

While the narrative takes a dark turn, the underlying tech story remains: a **robust, containerized pipeline** capable of adapting to the unpredictable environment—be it a rodent‑infested basement or a moon‑base.

---

## 5. Next Steps

1. **Deploy the trained YOLOv8 model** to the CVAT‑annotated video stream for real‑time detection.
2. **Automate model inference** on a GPU‑enabled Docker container behind Traefik, exposing a REST API for downstream services.
3. **Scale** with Proxmox clusters, adding more nodes as the mouse population (and data volume) grows.

---

### Keywords & SEO Highlights

- Rodent Detection  
- YOLOv8  
- Vision AI  
- CVAT Annotation  
- Docker Compose  
- Traefik HTTPS  
- Proxmox Virtualization  
- JupyterLab on Linux  
- Let’s Encrypt  
- Edge AI

Feel free to drop a comment if you want to see a live demo of the mouse‑detection system in action—or if you have a recipe for mouse‑friendly cuisine!
