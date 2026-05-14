# BrainSeg3D — Few-Shot 3D Brain Tissue Segmentation

![Python](https://img.shields.io/badge/Python-3.10-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0-orange)
![Platform](https://img.shields.io/badge/Platform-Google%20Colab-yellow)
![Dataset](https://img.shields.io/badge/Dataset-MRBrainS13-green)

A few-shot 3D brain MRI segmentation pipeline using a custom **ResUNet3D** architecture trained on the [MRBrainS13](https://dataverse.nl/dataset.xhtml?persistentId=doi%3A10.34894%2F645ZIN) dataset. Includes a full **3-experiment ablation study** with quantitative and qualitative analysis.

---

## 🧠 Overview

This project tackles brain tissue segmentation from 3D MRI scans in a **few-shot setting** — only 5 annotated training subjects available. The goal is to accurately segment:

| Label | Class |
|---|---|
| 0 | Background |
| 1 | Cerebrospinal Fluid (CSF) |
| 2 | Gray Matter (GM) |
| 3 | White Matter (WM) |

---

## 📊 Results

### Ablation Study (5-fold LOOCV)

| Experiment | CSF Dice | GM Dice | WM Dice | Mean Dice |
|---|---|---|---|---|
| A: UNet3D Baseline | 0.786 ± 0.017 | 0.813 ± 0.021 | 0.855 ± 0.027 | 0.818 |
| B: UNet3D + Aug + CombLoss | 0.801 ± 0.015 | 0.824 ± 0.018 | 0.868 ± 0.022 | 0.831 |
| C: ResUNet3D + Aug + Drop + Comb | **0.821 ± 0.017** | **0.841 ± 0.015** | **0.884 ± 0.012** | **0.849** |

---

## 🗂️ Repository Structure

```
BrainSeg3D/
│
├── ECS795P_CW3_Brain_Segmentation_FINAL.ipynb   # Main notebook
└── README.md
```

---

## 🚀 How to Run

### 1. Upload Dataset to Google Drive

Download the MRBrainS13 dataset from:
👉 https://dataverse.nl/dataset.xhtml?persistentId=doi%3A10.34894%2F645ZIN

Upload the file `doi-10.34894-645zin.zip` to your Google Drive root folder.

### 2. Open in Google Colab

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

- Upload `ECS795P_CW3_Brain_Segmentation_FINAL.ipynb` to Google Colab
- Set runtime to **GPU (T4)**
- Run all cells top to bottom

### 3. Steps in Notebook

| Step | Description |
|---|---|
| 1 | Install libraries |
| 2 | Mount Google Drive |
| 3 | Extract dataset |
| 4 | Imports & config |
| 5 | Data loading & normalisation |
| 6 | EDA — data distribution visualisation |
| 7 | Patch extraction with augmentation |
| 8 | Model definitions (UNet3D & ResUNet3D) |
| 9 | Loss functions (Dice & Combined) |
| 10 | Evaluation metrics (Dice, IoU, HD95) |
| 11 | Sliding window inference |
| 12 | Training loop with TensorBoard logging |
| 13 | Experiment A — Baseline |
| 14 | Experiment B — + Augmentation + CombLoss |
| 15 | Experiment C — ResUNet3D (Best Model) |
| 16 | Ablation study comparison table |
| 17-19 | Visualisations (loss curves, bar charts, HD95) |
| 20 | Qualitative analysis (success & failure cases) |
| 21 | Save results to Google Drive |

---

## 🏗️ Model Architecture

### Baseline — UNet3D
- 3 encoder stages: channels `[16, 32, 64]`
- 128-channel bottleneck
- Symmetric decoder with transposed convolutions
- Skip connections
- ~1.2M parameters

### Improved — ResUNet3D
- All ConvBlocks replaced with **ResConvBlocks** (residual shortcuts)
- **Dropout3d** (p=0.2) for regularisation
- Combined **Dice + CrossEntropy** loss with class weighting
- **CosineAnnealingLR** scheduler
- Gradient clipping (max norm 1.0)
- ~1.26M parameters

---

## 📦 Dependencies

```bash
pip install torch nibabel matplotlib scikit-learn scipy pandas
```

All other dependencies are installed automatically in the notebook.

---

## 📁 Dataset

**MRBrainS13** — 5 training + 15 test subjects

Each subject contains:
- `T1.nii` — T1-weighted MRI
- `T1_IR.nii` — T1 Inversion Recovery
- `T2_FLAIR.nii` — FLAIR scan
- `LabelsForTesting.nii` — Ground truth (3 classes)

Download: https://dataverse.nl/dataset.xhtml?persistentId=doi%3A10.34894%2F645ZIN

---

## ⚙️ Training Config

| Parameter | Value |
|---|---|
| Patch size | 32³ |
| Patches per subject | 80 |
| Batch size | 4–8 |
| Epochs | 20–30 |
| Optimizer | Adam (lr=1e-3) |
| Loss | Dice / Dice+CE |
| Evaluation | 5-fold LOOCV |
| GPU | NVIDIA T4 (Google Colab) |

---

## 📈 Metrics

- **Dice (DSC)** — primary overlap metric
- **IoU (Jaccard)** — stricter overlap metric
- **HD95** — 95th percentile Hausdorff distance (boundary error)

---

## 📄 License

This project is for academic purposes only.
