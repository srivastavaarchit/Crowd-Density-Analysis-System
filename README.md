# 🧠 AI-Based Crowd Density Analysis System

![Python](https://img.shields.io/badge/Python-3.12-blue?style=flat-square&logo=python)
![YOLOv8](https://img.shields.io/badge/YOLOv8m-Ultralytics≥8.2-00e6b4?style=flat-square)
![PyTorch](https://img.shields.io/badge/PyTorch-2.10%2Bcu128-EE4C2C?style=flat-square&logo=pytorch)
![OpenCV](https://img.shields.io/badge/OpenCV-4.13.0-5C3EE8?style=flat-square&logo=opencv)
![Device](https://img.shields.io/badge/Device-CUDA%20GPU-76B900?style=flat-square&logo=nvidia)
![Platform](https://img.shields.io/badge/Platform-Kaggle-20BEFF?style=flat-square&logo=kaggle)

> Crowded places can get dangerous fast — and by the time someone notices, it's already too late.
> This system watches live video, counts people in each area, and fires an alert the moment a zone gets too packed. It tracks every individual as they move, logs everything with timestamps, and never misses a thing.
> **Simple idea. Serious impact.**

---

## 📌 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Pipeline](#pipeline)
- [Dataset](#dataset)
- [Results](#results)
- [Installation](#installation)
- [Notebook Structure](#notebook-structure)
- [Outputs](#outputs)

---

## 🔍 Overview

This project builds a real-time crowd monitoring system using **YOLOv8m** for person detection and **ByteTrack** for persistent multi-object tracking. It divides the scene into virtual monitoring zones, estimates crowd density per zone, and raises alerts when thresholds are breached — all while logging every event with timestamps.

| Property | Value |
|---|---|
| Model | YOLOv8m (medium) |
| Confidence Threshold | 0.40 |
| Monitoring Zones | 4 (A, B, C, D) |
| Alert Threshold | ≥ 12 persons / zone |
| Target Precision | ≥ 90% (person class) |
| Compute | CUDA GPU (Kaggle T4) |

---

## ✨ Features

- 🎯 **High-Precision Detection** — 93.2% person-class precision, exceeding the 90% target on COCO128 val
- 🔁 **Persistent Tracking** — ByteTrack assigns stable IDs across frames, no double-counting
- 🗺️ **Virtual Zone Analysis** — Four polygon zones (Left, Centre, Right, Entry) with independent density counts
- 🌡️ **Density Heatmaps** — Gaussian kernel smoothing produces spatial density maps overlaid on frames
- 🚨 **Overcrowding Alerts** — Configurable threshold triggers visual alerts and logs events instantly
- 📊 **Real-Time Dashboard** — Matplotlib analytics with bar charts and per-zone stats updated per frame
- 🗂️ **Event Logging** — CSV + JSON logs capture every frame's zone counts and alert states
- ⚡ **GPU-Accelerated** — Runs at 5–6 FPS on 848×478 video on a T4 GPU

---

## ⚙️ Pipeline

| Stage | Component | Purpose |
|---|---|---|
| 01 | YOLOv8m | Person detection (conf ≥ 0.40) |
| 02 | ByteTrack | Persistent multi-object ID assignment |
| 03 | Polygon Zones | Spatial partitioning into zones A–D |
| 04 | Gaussian Kernel | Spatial density heatmap generation |
| 05 | Threshold Logic | Overcrowding detection (≥ 12 persons/zone) |
| 06 | CSV + JSON Logger | Timestamped density event logging |
| 07 | Matplotlib Dashboard | Real-time analytics visualisation |

---

## 📂 Dataset

Two real crowd-scene videos used as input:

| # | File | Duration | Resolution | FPS | Frames | Tracked Persons |
|---|---|---|---|---|---|---|
| 1 | WhatsApp_Video…9.48.37_PM.mp4 | 14.9 s | 848×478 | 30.0 | 448 | 91 |
| 2 | WhatsApp_Video…9.51.59_PM.mp4 | 4.0 s | 848×478 | 30.1 | 120 | 29 |

> Videos are auto-located from `/kaggle/input/` via recursive scan — no manual path setup needed.

---

## 📊 Results

### Accuracy — COCO128 Val (Person Class)

| Metric | Score | Target |
|---|---|---|
| **Precision** | **93.2%** ✅ | ≥ 90% |
| Recall | 69.7% | — |
| mAP@50 | 68.7% | — |
| mAP@50-95 | 55.8% | — |

### Zone Density Summary

| Video | Zone | Mean | Max | Min |
|---|---|---|---|---|
| Video 1 | Zone-A (Left) | 2.66 | 6 | 0 |
| Video 1 | Zone-B (Centre) | 0.69 | 4 | 0 |
| Video 1 | Zone-C (Right) | 0.84 | 3 | 0 |
| Video 1 | Zone-D (Entry) | 0.00 | 0 | 0 |
| Video 2 | Zone-A (Left) | 3.50 | 6 | 2 |
| Video 2 | Zone-B (Centre) | 6.33 | 8 | 5 |
| Video 2 | Zone-C (Right) | 5.54 | 7 | 4 |
| Video 2 | Zone-D (Entry) | 0.00 | 0 | 0 |

> Total log rows: **452** &nbsp;|&nbsp; Alert events: **0** (density stayed within limits across both videos)

---

## 🚀 Installation

Dependencies are **auto-installed** at runtime by Cell 1 — just run the notebook top-to-bottom.

```python
# Packages installed automatically
ultralytics >= 8.2   # YOLOv8 framework
supervision          # ByteTrack wrapper
opencv-python        # Frame processing
scipy                # Gaussian heatmap kernel
matplotlib seaborn   # Dashboard & plots
scikit-learn pandas  # Stats & logging
```

```
Runtime Environment (Kaggle)
PyTorch  : 2.10.0+cu128
Device   : cuda  ← T4 GPU recommended
OpenCV   : 4.13.0
Python   : 3.12
```

> ⚠️ **GPU Required.** Enable in Kaggle: `Settings → Accelerator → GPU T4 x2`. CPU mode works but is significantly slower (~1 FPS).

---

## 📓 Notebook Structure

```
notebook.ipynb
├── §1  Environment Setup          — auto-install & GPU init
├── §2  Input Videos — Dataset     — recursive video scan
├── §3  Model Loading              — YOLOv8m weights
├── §4  Zone Configuration         — polygon zones A–D
├── §5  Detection Engine           — YOLOv8 inference loop
├── §6  ByteTrack Integration      — persistent ID tracking
├── §7  Density Estimation         — per-zone head-counts
├── §8  Heatmap Generation         — Gaussian kernel overlay
├── §9  Alert System               — threshold-based alerts
├── §10 Dashboard & Visualisation  — real-time matplotlib
├── §11 Video Output               — annotated .mp4 export
├── §12 Accuracy Validation        — COCO128 val metrics
├── §13 Log Analysis               — CSV event review
└── §14 Final Summary              — boxed stats report
```

---

## 📦 Outputs

| Artefact | Description |
|---|---|
| `output_video_1_*.mp4` | Annotated video — bounding boxes, IDs, zone overlays, density HUD |
| `output_video_2_*.mp4` | Same for second input video |
| `crowd_density_log.csv` | 452 rows — timestamp, zone, count, is_alert per frame |
| `accuracy_metrics.png` | Bar chart — Precision / Recall / mAP@50 / mAP@50-95 |
| Console Summary | ASCII table — model config, per-video stats, accuracy results |

---

<div align="center">
  <b>AI Crowd Density Analysis</b> &nbsp;·&nbsp; YOLOv8m + ByteTrack &nbsp;·&nbsp; Python 3.12 &nbsp;·&nbsp; Kaggle
</div>
