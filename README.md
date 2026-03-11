<div align="center">

```
██████╗ ██╗  ██╗██╗███████╗██╗  ██╗    ██████╗ ███████╗████████╗███████╗ ██████╗████████╗
██╔══██╗██║  ██║██║██╔════╝██║  ██║    ██╔══██╗██╔════╝╚══██╔══╝██╔════╝██╔════╝╚══██╔══╝
██████╔╝███████║██║███████╗███████║    ██║  ██║█████╗     ██║   █████╗  ██║        ██║   
██╔═══╝ ██╔══██║██║╚════██║██╔══██║    ██║  ██║██╔══╝     ██║   ██╔══╝  ██║        ██║   
██║     ██║  ██║██║███████║██║  ██║    ██████╔╝███████╗   ██║   ███████╗╚██████╗   ██║   
╚═╝     ╚═╝  ╚═╝╚═╝╚══════╝╚═╝  ╚═╝    ╚═════╝ ╚══════╝   ╚═╝   ╚══════╝ ╚═════╝   ╚═╝   
```

# 🛡️ Visual Phishing Detection via Unsupervised Deep Learning

**Detecting fake websites by what they look like — no labels, no rules, just patterns.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white)](https://tensorflow.org)
[![ResNet50](https://img.shields.io/badge/CNN-ResNet50-764ABC?style=for-the-badge)](https://keras.io/api/applications/resnet/)
[![scikit-learn](https://img.shields.io/badge/sklearn-KMeans%20%7C%20PCA%20%7C%20tSNE-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)](https://scikit-learn.org)
[![University](https://img.shields.io/badge/INF791-University%20of%20Pretoria-003DA5?style=for-the-badge)](https://up.ac.za)

</div>

---

## 🎯 The Problem

> *Phishing costs the world billions annually. Attackers craft convincing fake websites nearly indistinguishable to the human eye. Can a machine detect them — purely from their visual appearance?*

This project answers **yes** — using only website screenshots and zero labels during training.

---

## 📸 Dataset at a Glance

```
archive/screenshots/
├── phishing_site_1/    ← 848 fake websites
└── genuine_site_0/     ← 849 legitimate websites
                          ──────────────────────
                          1,697 PNG screenshots total
```

> Perfectly balanced. Purely visual. No URL analysis, no HTML parsing — screenshots only.

---

## 🏗️ Pipeline Overview

```
 RAW SCREENSHOTS
       │
       ▼
┌─────────────────┐
│  PREPROCESSING  │  resize → 224×224 │ normalize [0,1] │ RGB convert │ augment
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ CNN EMBEDDINGS  │  ResNet50 (pretrained, no top) → 2,048-dim feature vectors
└────────┬────────┘
         │
         ├──────────────────────────────────────────────────┐
         ▼                                                    ▼
┌─────────────────┐                             ┌────────────────────────┐
│  DIM REDUCTION  │  PCA + t-SNE → 2D plots     │      AUTOENCODER       │
└────────┬────────┘                             │  trained on genuine    │
         │                                       │  only → anomaly detect │
         ▼                                       └────────────────────────┘
┌─────────────────┐
│    K-MEANS      │  k=2 + k=3,4,5,6 subgroup discovery
└────────┬────────┘
         │
         ▼
┌─────────────────────┐
│  ASSOCIATION RULES  │  Apriori on 12 binary visual features
└─────────────────────┘
```

---

## 🔬 Methods & Key Results

### 1 · Feature Extraction — ResNet50

| Raw Pixels | CNN Features |
|---|---|
| 150,528 dims per image | **2,048 dims per image** |
| No semantic meaning | Layout, colour, texture, form structure |
| Sensitive to tiny shifts | Robust, transfer-learned representations |

---

### 2 · Dimensionality Reduction

Both **PCA** (linear) and **t-SNE** (non-linear) reduce 2,048 → 2 dimensions for visualization.

```
PCA variance explained:  57.3%
Result: Genuine sites cluster tightly — consistent design patterns
        Phishing sites spread wider  — more design variety
```

---

### 3 · K-Means Clustering

| k | ARI Score | NMI Score | Accuracy |
|---|---|---|---|
| 2 (binary) | 0.35 – 0.45 | 0.25 – 0.35 | **65–75%** |
| 3–6 (subgroups) | — | — | Revealed phishing sub-strategies |

---

### 4 · Autoencoder Anomaly Detection

> Trained **exclusively on genuine websites** — learns what "normal" looks like.

```
Genuine sites  →  mean MSE = 0.0023  ✅  low error  — model knows these
Phishing sites →  mean MSE = 0.0048  🚨  2.1× higher — anomalies flagged
```

---

### 5 · Association Rule Mining (Apriori)

12 binary visual features extracted. Four dominant phishing strategies uncovered:

```
🔴  Urgency Tactics       →  red palettes + warning-style visuals
💡  Attention Grabbing    →  high brightness + extreme contrast
🎭  Professional Mimicry  →  copied genuine layout elements
🌀  Complexity Overload   →  dense, busy designs to overwhelm users
```

---

## ⚡ Quickstart

```bash
# Clone the repo
git clone https://github.com/mailenoah/Evaluation-of-Deep-Learning-Models-on-Image-s-Datasets-Project.git
cd phishing-visual-detection

# Install dependencies
pip install tensorflow scikit-learn pillow matplotlib pandas mlxtend

# Place your dataset
# archive/screenshots/phishing_site_1/  ← phishing PNGs
# archive/screenshots/genuine_site_0/   ← genuine PNGs

# Run the notebook
jupyter lab Project_Code.ipynb
```

---

## 🧰 Tech Stack

| Layer | Tools |
|---|---|
| Image processing | `Pillow`, `TensorFlow ImageDataGenerator` |
| Feature extraction | `ResNet50` via `tensorflow.keras.applications` |
| Dimensionality reduction | `sklearn` PCA + t-SNE |
| Clustering | `sklearn` KMeans, ARI, NMI |
| Anomaly detection | Custom Conv Autoencoder (`tensorflow.keras`) |
| Pattern mining | `mlxtend` Apriori |
| Visualisation | `matplotlib`, `seaborn` |

---

## 📁 Repository Structure

```
phishing-visual-detection/
│
├── Project_Code.ipynb                     # Full pipeline — run end-to-end
├── Project_analysis_and_Insights.pdf      # Full report with findings
├── disegomaile_preprocessed_screenshots.csv  # Preprocessed image metadata
├── genuine_websites_autoencoder.h5        # Saved autoencoder weights
└── README.md
```

---

## 📊 Results Summary

```
┌────────────────────────┬──────────────────────────────────┐
│ Method                 │ Outcome                          │
├────────────────────────┼──────────────────────────────────┤
│ CNN Feature Extraction │ ✅ Excellent — rich 2,048-d reps │
│ Dimensionality Reduc.  │ ✅ Clear visual separation        │
│ K-Means Clustering     │ ⚡ Moderate — 65–75% accuracy    │
│ Autoencoder Detection  │ ✅ Good    — 2.1× phishing error │
│ Association Rules      │ ✅ Excellent — 4 strategies found│
└────────────────────────┴──────────────────────────────────┘
```

---

<div align="center">

**INF791 · Assignment 02 · University of Pretoria · Department of Informatics**

*D. Maile · 21477729 *

</div>
