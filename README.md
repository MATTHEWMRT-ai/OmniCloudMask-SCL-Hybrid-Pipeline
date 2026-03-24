# ☁️ OmniCloudMask & SCL Hybrid Pipeline

## 📖 Overview
This repository contains a batch-processing Python script designed to enhance ESA's default Sentinel-2 Scene Classification Layer (SCL). 

The default Sen2Cor algorithm often overestimates clouds and shadows, masking out valuable data. Furthermore, the default SCL is provided at a 20m resolution. This pipeline solves these issues by:
1. Generating a highly accurate **10m cloud mask** using the Deep Learning model **OmniCloudMask (OCM)**.
2. Automatically extracting the original 20m SCL mask from the Sentinel-2 `.SAFE` folder.
3. Upsampling the 20m SCL to 10m using a **Nearest Neighbor** algorithm to strictly preserve categorical integrity (preventing the creation of false artifact classes like "Water" from interpolated decimals).
4. Injecting the high-precision OCM predictions over the SCL mask to create a **10m Hybrid SCL Mask**.

## 🧠 The Hybrid Logic (Transposition Dictionary)
The script preserves valuable ground data (Vegetation, Bare Soil, Water) detected by Sen2Cor while overriding inaccurate cloud/shadow detections with OCM's AI predictions.

| OCM Prediction (10m) | | Hybrid SCL Output Class |
| :--- | :---: | :--- |
| `1` (Thick Cloud) | ➡️ | `9` (High Probability Cloud) |
| `2` (Thin Cloud) | ➡️ | `10` (Cirrus) |
| `3` (Cloud Shadow) | ➡️ | `3` (Cloud Shadow) |
| `0` (Clear Sky) | ➡️ | *Keeps original SCL class (e.g., 4, 5, 6...)* |

## ⚙️ Prerequisites
Ensure you have a working Python environment (e.g., Conda or Pixi) with the following dependencies installed:
```bash
pip install rasterio numpy
# Make sure omnicloudmask is also properly installed in your environment
