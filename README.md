# ML‑Malware: Adversarial Robustness and Transferability Analysis

This repository contains a **reproducible research pipeline** for evaluating  
**static malware detection models** under **feature‑space adversarial attacks**, with a focus on:

- Binary malware detection (Benign vs Malware)
- Malware family classification
- Adversarial robustness under FGSM and PGD
- Cross‑model transferability (Neural Network → Random Forest)
- Family‑level risk ranking and robustness analysis

The project is designed for **Master’s‑level and PhD‑ready research**, with strict attention to  
**experimental control**, **numerical stability**, and **reproducibility**.

---

## Repository Structure (High‑Level)

```
ML-Malware/
│
├── malrob/                     # Core Python package
│   ├── config.py               # Global experiment configuration
│   ├── data.py                 # EMBER feature extraction
│   ├── model.py                # Binary NN + RF model definitions
│   ├── train.py                # Binary training pipeline
│   ├── adversarial_binary.py   # FGSM / PGD binary adversarial evaluation
│   ├── family_evasion.py       # Family-level evasion analysis
│   └── family_transfer_fgsm.py # NN→RF adversarial transfer (family classification)
│
├── datasets/
│   └── ember2018/              # Generated CSV datasets
│
├── models_full_12k/            # Model artifacts and experiment outputs
│   ├── artifacts_adv/
│   ├── artifacts_family/
│   ├── results_adv/
│   └── results_family/
│
├── notebooks/
│   ├── 01_dataset_generation.ipynb
│   ├── 02_binary_training.ipynb
│   ├── 03_binary_adversarial_attacks.ipynb
│   ├── 04_family_evasion_analysis.ipynb
│   ├── 05_family_risk_ranking.ipynb
│   └── 06_family_adversarial_transfer.ipynb
│
├── requirements.txt
└── README.md
```

---

## Environment Requirements

### Python
- **Python 3.10 or 3.11 recommended**
- Tested on **Windows (PowerShell)**

### System Dependencies
- *(optional)* Git 
- Python (with `venv`)
- C++ Redistributable (If not installed the notebook will allow you to install when it errors)
- *(Optional)* CUDA‑enabled GPU for PyTorch (CPU fallback supported)

---

## Installation & Setup

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd ML-Malware
```

### 2. Create and activate a virtual environment

```bash
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### 3. Install Python dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Register the Jupyter kernel (recommended)

```bash
python -m ipykernel install --user --name malrob
```

In Jupyter:  
**Kernel → Change Kernel → malrob**

> **Note:** Kernel registration ensures reproducibility and avoids environment drift.

### 5. Launch Jupyter

```bash
jupyter lab
```

---

## Global Configuration Design

All dataset sizing, paths, and output locations are controlled from:

```
malrob/config.py
```

### Key variables

```python
TARGET_PER_CLASS = 6000
RANDOM_STATE = 42

DATA_PREFIX = "ember_full"
MODELS_PREFIX = "models_full"
```

The system automatically derives:

- Dataset tags (e.g., `12k`, `36k`)
- Dataset paths
- Model directories
- Results and artifact directories

---

## Notebook Execution Order (IMPORTANT)

The notebooks **must be run in order**, as later stages depend on earlier outputs.

---

### Notebook 01 — Dataset Generation

**File:** `01_dataset_generation.ipynb`

**Purpose:**
- Extract EMBER 2018 static PE features
- Generate:
  - Binary dataset
  - Family‑labelled dataset

**Outputs:**

```
datasets/ember2018/ember_full_12k_flat.csv
datasets/ember2018/ember_full_12k_families_flat.csv
```

---

### Notebook 02 — Binary Model Training

**File:** `02_binary_training.ipynb`

**Purpose:**
- Train:
  - Random Forest (binary)
  - Neural Network (binary)
- Compute clean ROC‑based thresholds (Youden’s J)

**Outputs:**

```
models_full_12k/
├── rf_binary_12k.joblib
├── nn_binary_12k.pt
├── scaler_full_12k.joblib
└── results/
```

---

### Notebook 03 — Binary Adversarial Attacks

**File:** `03_binary_adversarial_attacks.ipynb`

**Purpose:**
- FGSM and PGD feature‑space attacks
- Evaluate:
  - NN evasion
  - NN → RF transferability
- Fixed‑threshold evaluation

**Outputs:**

```
models_full_12k/results_adv/
models_full_12k/artifacts_adv/
```

---

### Notebook 04 — Family‑Level Evasion Analysis

**File:** `04_family_evasion_analysis.ipynb`

**Purpose:**
- Attribute evasion to malware families
- Compute per‑family robustness curves
- Visualise FGSM/PGD family behaviour

**Outputs:**

```
models_full_12k/results_adv/
```

---

### Notebook 05 — Family Risk Ranking

**File:** `05_family_risk_ranking.ipynb`

**Purpose:**
- Aggregate family robustness deltas
- Rank malware families by adversarial risk
- Generate tables for reporting / LaTeX

---

### Notebook 06 — Adversarial Transfer (Family Classification)

**File:** `06_family_adversarial_transfer.ipynb`

**Purpose:**
- Train RF and NN family classifiers
- Craft FGSM attacks on NN
- Measure **transfer to RF**
- Plot accuracy vs epsilon

**Outputs:**

```
models_full_12k/results_family/
models_full_12k/artifacts_family/
```

---

## Reproducibility Guarantees

This repository enforces:

- Deterministic dataset sampling
- Fixed random seeds
- Explicit feature‑column alignment
- Threshold persistence
- CPU fallback for PyTorch
- Single‑source configuration

Results are reproducible across machines, reboots, environments, and examiners.

---

## Dependencies

All dependencies are listed in `requirements.txt`.

Key libraries:

- `numpy`, `pandas`
- `scikit-learn`
- `torch`
- `matplotlib`
- `joblib`
- `jupyterlab`, `ipykernel`

---

## Intended Audience

- Cybersecurity researchers
- Malware ML practitioners
- Master’s / PhD students
- Adversarial ML researchers

---

## Citation / Academic Use

If you use this repository in academic work, please cite:

> *“Adversarial robustness and transferability analysis of static malware detection models using EMBER features.”*

