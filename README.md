# 🛡️ RansomGuard
## Final year Btech project - Yadnesh Somashe and 3 others
### Adaptive, Explainable, and Lightweight Ransomware Detection for Real-Time Threat Mitigation

![Python](https://img.shields.io/badge/Python-3.10-blue?style=flat-square&logo=python)
![Flask](https://img.shields.io/badge/Flask-Web%20Framework-lightgrey?style=flat-square&logo=flask)
![MongoDB](https://img.shields.io/badge/MongoDB-Database-green?style=flat-square&logo=mongodb)
![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML-orange?style=flat-square&logo=scikit-learn)
![License](https://img.shields.io/badge/License-Academic-red?style=flat-square)

> A real-time, locally deployable ransomware detection system that integrates static and dynamic analysis using advanced Machine Learning (ML) techniques, with full Explainable AI (XAI) support via LIME.

---

## 📖 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [How It Works](#how-it-works)
- [Performance](#performance)
- [Dataset](#dataset)
- [Team](#team)
- [Future Work](#future-work)

---

## Overview

Ransomware is one of the most destructive forms of cyberattack, causing operational disruption, data loss, and financial damage across critical sectors. Traditional detection systems fail to recognize advanced ransomware that leverages obfuscation techniques such as polymorphism, code injection, and behavior masking.

**RansomGuard** addresses these shortcomings by combining:
- **Entropy-Synchronized Neural Hashing (ESNH)** for static fingerprinting of executable files
- **Real-time behavioral monitoring** to capture anomalies in file encryption, API usage, and registry activity
- **Explainable AI (XAI)** via LIME to provide transparency into every detection decision

The system operates entirely **offline**, making it ideal for air-gapped enterprise networks, government labs, and secure environments.

---

## Key Features

- **Hybrid Detection** — Combines static entropy analysis (ESNH) with dynamic behavioral monitoring for superior accuracy
- **Explainable AI** — Every prediction is accompanied by a LIME-generated explanation showing which features triggered the alert
- **Offline / Local Operation** — No cloud dependency; runs entirely on the local machine
- **Real-Time Alerts** — Flask-based dashboard with live detection results and threat logging
- **MongoDB Logging** — All scan results, behavioral traces, and LIME outputs are persistently stored
- **High Accuracy** — Achieves 99.74% detection accuracy with an AUC of 0.9974
- **Low False Positive Rate** — Only 2.8% false positives, lower than the existing system baseline

---

## System Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    FILE INPUT (.exe)                     │
└────────────────────┬────────────────────────────────────┘
                     │
          ┌──────────▼──────────┐
          │   Static Analysis   │  ← Entropy-Synchronized Neural Hashing (ESNH)
          │   (Entropy / PE)    │
          └──────────┬──────────┘
                     │
          ┌──────────▼──────────┐
          │  ML Detection Engine│  ← Random Forest Classifier
          │  (API / Encryption) │
          └──────────┬──────────┘
                     │
          ┌──────────▼──────────┐
          │  Explainability &   │  ← LIME (XAI)
          │  Alert Generation   │
          └──────────┬──────────┘
                     │
     ┌───────────────▼───────────────┐
     │     GUI Dashboard (Flask)     │  ← Live Alerts, Logs, LIME Graphs
     └───────────────┬───────────────┘
                     │
          ┌──────────▼──────────┐
          │   MongoDB Database  │  ← Logs, Scan History, Audit Records
          └─────────────────────┘
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.10 |
| Frontend / Dashboard | Flask, React, Matplotlib, Seaborn |
| Machine Learning | Scikit-learn (Random Forest), PyTorch |
| Neural Hashing | ESNH (Entropy-Synchronized Neural Hashing) |
| Explainable AI | LIME |
| Database | MongoDB + PyMongo |
| File Analysis | pefile, hashlib |
| Testing | PyTest, Postman |
| Dev Tools | Jupyter Notebook, VS Code, Git/GitHub |
| Deployment | Windows 10/11, Ubuntu 22.04, VirtualBox (sandbox) |

---

## Project Structure

```
ransomguard/
│
├── app.py                    # Flask application entry point
├── requirements.txt          # Python dependencies
├── README.md
│
├── static/                   # Frontend assets (CSS, JS)
├── templates/                # HTML templates for Flask dashboard
│   ├── index.html
│   └── results.html
│
├── models/                   # Trained ML models
│   ├── random_forest.pkl
│   └── esnh_model.pt         # PyTorch ESNH neural hashing model
│
├── modules/
│   ├── entropy_analyzer.py   # Shannon entropy extraction + ESNH
│   ├── behavioral_monitor.py # Runtime API / registry / file I/O monitoring
│   ├── feature_fusion.py     # Merges static + dynamic features
│   ├── classifier.py         # Random Forest classification engine
│   ├── explainer.py          # LIME-based XAI explanation generator
│   └── db_logger.py          # MongoDB logging interface
│
├── data/
│   ├── ransomware_samples/   # Ransomware executables (sandboxed)
│   └── benign_samples/       # Clean executable files
│
└── notebooks/
    ├── training.ipynb        # Model training and evaluation
    └── evaluation.ipynb      # Performance metrics and visualization
```

---

## Installation

### Prerequisites

- Python 3.10+
- MongoDB (local instance)
- Windows 10/11 or Ubuntu 22.04
- NVIDIA GPU with CUDA (optional, for faster training)

### Steps

```bash
# 1. Clone the repository
git clone https://github.com/your-org/ransomguard.git
cd ransomguard

# 2. Create a virtual environment
python -m venv venv
source venv/bin/activate        # Linux/macOS
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install -r requirements.txt

# 4. Start MongoDB
mongod --dbpath ./data/db

# 5. Run the Flask application
python app.py
```

Then open your browser at `http://localhost:5000`.

### requirements.txt (core dependencies)

```
flask
pymongo
scikit-learn
torch
pefile
lime
numpy
pandas
matplotlib
seaborn
tqdm
pytest
```

---

## Usage

1. **Open the dashboard** at `http://localhost:5000`
2. **Upload** a `.exe` or `.msi` file using the file picker
3. Click **Start Detection**
4. View the result:
   - ✅ **SAFE** — File is benign (shown in green)
   - ⚠️ **RANSOMWARE DETECTED** — File is malicious (shown in red)
5. Review the **Entropy Score**, **Neural Hash**, **Confidence Score**, and **LIME explanation graph**
6. All results are automatically saved to MongoDB for audit and historical review

---

## How It Works

### 1. Static Analysis — Entropy-Synchronized Neural Hashing (ESNH)

The uploaded executable is divided into fixed-size byte blocks. Shannon entropy is computed for each block to measure randomness. High entropy values indicate encrypted or obfuscated payloads — a hallmark of ransomware. These entropy sequences are passed through an ESNH neural model to generate a unique structural fingerprint.

### 2. Behavioral Monitoring (Dynamic Analysis)

If the file is executed in a sandboxed environment, RansomGuard monitors:
- API call patterns and frequencies
- File system I/O (mass renaming, encryption loops)
- Registry modifications
- Process injection attempts

### 3. Feature Fusion

Static entropy fingerprints and dynamic behavioral logs are merged into a single feature vector that captures both the structural properties and runtime behavior of the file.

### 4. ML Classification

The fused feature vector is fed into a trained **Random Forest** classifier, which outputs:
- A predicted label: `Benign` or `Ransomware`
- A confidence score (0–100%)

### 5. Explainable AI (LIME)

LIME generates a human-readable breakdown of which specific features — such as a spike in entropy at a particular file offset or abnormal registry write counts — contributed most to the detection decision.

---

## Performance

| Metric | Existing System | RansomGuard |
|---|---|---|
| Detection Accuracy | 95.96% | **99.74%** |
| False Positive Rate | 3.2% | **2.8%** |
| Avg. Response Time | 13.6 seconds | **12 seconds** |
| Explainability | Limited | **Yes (LIME)** |
| AUC (ROC Curve) | — | **0.9974** |

Tested on 1,274 files (638 ransomware + 636 benign) sourced from VirusShare and VX-Underground.

---

## Dataset

| Property | Details |
|---|---|
| Sources | VirusShare, VX-Underground |
| Total Samples | 1,274 files |
| Ransomware Samples | 638 |
| Benign Samples | 636 |
| Static Features | File entropy distribution, byte histograms, PE structure (headers, imported DLLs) |
| Dynamic Features | API call traces, file I/O frequency, registry activity |
| Tested Families | WannaCry, Locky, Ryuk (in controlled sandbox) |

> ⚠️ All ransomware samples must be handled in an isolated virtual environment. Never execute malware outside a controlled sandbox.

---

## Team

**K. K. Wagh Institute of Engineering Education and Research, Nashik**
Department of Computer Engineering — A.Y. 2025–2026

| Name | Roll No. | Role |
|---|---|---|
| Hemantkumar Bharambe | B221301015 | Project Lead, System Architecture, ESNH Integration |
| Yadnesh Somashe | B221301143 | XAI (LIME) Integration, Model Evaluation, Feature Fusion |
| Avishkar Jadhav | B221301058 | Dataset Management, Backend Development, MongoDB |
| Rajvardhan Shinde | B221301141 | Frontend (UI), Flask Integration, MongoDB Search |

**Guide:** Prof. P. P. Vaidya
**Head of Department:** Prof. Dr. S. M. Kamalapur

---

## Future Work

- **Cloud-Based Deployment** — Extend RansomGuard to cloud environments for distributed file system scanning
- **Reinforcement Learning** — Adaptive retraining mechanisms to continuously improve model performance against emerging variants
- **Automated Isolation** — Sandbox automation to dynamically isolate detected ransomware processes
- **Dataset Expansion** — Broader training coverage including larger files and newer ransomware families
- **Enhanced Visualization** — Advanced dashboards with behavioral timelines and feature heatmaps
- **SIEM / SOC Integration** — Connect to enterprise Security Information and Event Management platforms

---

## Disclaimer

RansomGuard is developed strictly for **academic and defensive cybersecurity research** purposes. All malware analysis must be conducted in isolated, controlled environments in compliance with applicable laws and institutional policies. The authors do not condone any malicious use of this software.

---

*Department of Computer Engineering, K. K. Wagh Institute of Engineering Education and Research, Nashik, Maharashtra — 2025*

