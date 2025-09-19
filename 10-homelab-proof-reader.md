---
title: Day 10 HomeLab – Robots vs. Mouse?
description: Setting up a Proxmox container to run VOXEL51 and CVAT for Vision‑AI training.
date: 2025-09-04
categories: [Yolo, VisionAI]
author: moses
tags: [yolo]
hideToc: true
---

# To a Mouse (and a Robot)

While doing laundry today I spotted a mouse in my house. It reminded me of the old poem *To a Mouse* by Robert Burns – a reminder that even the smallest creature can be a big mystery.  
[Read the poem](https://en.wikipedia.org/wiki/To_a_Mouse)

> Wee, sleekit, cow'rin, tim'rous beastie,  
> O, what a panic's in thy breastie!  
> Tho' need's na start awa sae hasty,  
> Wi' bickering brattle!  

---

## Why Track a Mouse?

My house has become a playground for rodents and squirrels – the kind of wildlife that turns a quiet evening into a frantic chase. By tracking their movements we can:

1. Identify their nesting spots.  
2. Deploy targeted deterrents (broad‑band sound, motion‑activated sprays).  
3. Use the data to train an AI model that could predict rodent behavior in real time.

The plan is to use a **Proxmox** virtual host to run a Docker stack containing **VOXEL51** (an industry‑grade visual‑data analytics platform) and **CVAT** (a powerful annotation tool). Once the data pipeline is in place, we’ll feed the mouse‑tracking footage into YOLOv8/YOLOv11 for real‑time object detection.

---

## The Tech Stack

| Component | Purpose | Key Tech |
|-----------|---------|----------|
| **Proxmox VE** | Bare‑metal hypervisor for running VMs and containers | KVM + LXC |
| **Docker Compose** | Orchestrates containers for CVAT and VOXEL51 | Docker Engine |
| **VOXEL51** | Visual data analytics, event extraction, and AI model management | Python SDK, REST API |
| **CVAT** | Web‑based annotation tool for video and image datasets | Web UI, WebSocket |
| **YOLOv8 / YOLOv11** | State‑of‑the‑art object detector | Ultralytics PyTorch |

### Proxmox on a HomeLab Server

We spin up a lightweight Debian LXC container:

```bash
pct create 101 local:vztmpl/debian-12-standard_12.0-1_amd64.vztmpl \
  --hostname voxel51-proxmox \
  --cores 4 --memory 4096 --net0 name=eth0,bridge=vmbr0,ip=dhcp
pct start 101
```

After logging in, we install Docker:

```bash
apt update && apt install -y docker.io docker-compose
systemctl enable --now docker
```

---

## Docker‑Compose File

```yaml
version: "3.8"

services:
  cvat:
    image: cvat/cvat:latest
    container_name: cvat
    environment:
      - DJANGO_SUPERUSER_USERNAME=admin
      - DJANGO_SUPERUSER_PASSWORD=admin
      - DJANGO_SUPERUSER_EMAIL=admin@example.com
    ports:
      - "8080:8080"
    volumes:
      - ./cvat_data:/var/lib/cvat

  voxel51:
    image: voxel51/voxel51:latest
    container_name: voxel51
    environment:
      - VOXEL51_API_KEY=YOUR_API_KEY
    ports:
      - "5000:5000"
    volumes:
      - ./voxel51_data:/data
```

After saving the file as `docker-compose.yml`, run:

```bash
docker compose up -d
```

Both services will be reachable on:

- CVAT: `http://<proxmox-host-ip>:8080`
- VOXEL51: `http://<proxmox-host-ip>:5000`

---

## VOXEL51 Overview

VOXEL51 offers a high‑performance, end‑to‑end visual analytics platform. Key features:

- **Event extraction** from live video streams.  
- **Object tracking** and trajectory analysis.  
- **API integration** for model inference and data export.  
- **FiftyOne integration** for visualizing predictions.

> “I'm in love with a voxel… great software… high quality… the best software ever.” – *A fictional AI voice actor*  

---

## CVAT (Computer Vision Annotation Tool)

CVAT lets you:

- Annotate video frames or still images.  
- Label objects with bounding boxes, polygons, or polylines.  
- Export annotations in COCO, YOLO, PASCAL VOC, and more.  

### Quick Start

```bash
# Create a new project
cvat create_project --name "Mouse Tracking" --tasks 1

# Add tasks, upload video, and annotate
cvat create_task --project "Mouse Tracking" --video <video_path>
```

Once annotated, export the dataset in YOLO format:

```bash
cvat export --project "Mouse Tracking" --format yolov5
```

---

## YOLOv8 / YOLOv11 Integration

Ultralytics’ YOLO models are trivial to use:

```python
!pip install ultralytics

from ultralytics import YOLO

# Load a YOLOv8 small model
model = YOLO("yolov8s.pt")

# Predict on an image
results = model("mouse.jpg")

# Export predictions to FiftyOne
results.export(format="yolov5")
```

### Integrating with VOXEL51

VOXEL51’s FiftyOne SDK allows you to run the YOLO model on a stream and store the predictions:

```python
from fiftyone import View
from fiftyone.models import YOLO

dataset = fiftyone.load_dataset("mouse_tracking")
model = YOLO("yolov8s.pt")
dataset.apply_model(model, label_field="YOLOv8")
```

The output is instantly visualizable in the VOXEL51 UI and can be fed into downstream analytics (e.g., heat‑map of mouse activity).

---

## Bringing It All Together

1. **Capture**: Use a networked camera to stream footage to the Proxmox host.  
2. **Annotate**: Upload clips to CVAT, label rodents, and export YOLO annotations.  
3. **Train**: Train or fine‑tune a YOLOv8/YOLOv11 model on the annotated data.  
4. **Deploy**: Load the model into VOXEL51 via FiftyOne and run real‑time inference.  
5. **Act**: Generate alerts, heat‑maps, and automated deterrents based on the detected mouse trajectories.

---

## SEO Checklist

| SEO Element | Implementation |
|-------------|----------------|
| **Title** | “Vision‑AI Training on HomeLab: Robots vs. Mouse” |
| **Meta Description** | “Learn how to set up a Proxmox + Docker stack with VOXEL51, CVAT, and YOLO for real‑time rodent tracking.” |
| **Header Tags** | H1, H2, H3 hierarchy with keywords “Vision AI”, “YOLO”, “VOXEL51”, “CVAT”. |
| **Internal Links** | Link to Blog 9 (Proxmox setup), Blog 11 (Deployment notes). |
| **External Resources** | Links to official docs for Proxmox, Docker, VOXEL51, CVAT, YOLO. |

---

## Final Thoughts

With the stack in place, I no longer need to reinvent every component. The combination of **Proxmox**, **Docker**, **CVAT**, **VOXEL51**, and **YOLO** gives us a production‑grade pipeline that can be reused for other object‑tracking projects—be it rodents, wildlife, or even industrial robots.

Happy hacking!  
— *Moses (HomeLab Enthusiast)*
```