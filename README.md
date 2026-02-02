Adversarial Transferability in Malware Detection: A Family-Aware Evaluation
An Empirical Study of Feature‑Space Attacks Across Families and Classifiers
 

This repository contains a reproducible research pipeline for evaluating static malware detection models under feature-space adversarial attacks, with a particular focus on:

·         Binary malware detection (Benign vs Malware)

·         Malware family classification

·         Adversarial robustness under FGSM and PGD

·         Cross-model transferability (Neural Network → Random Forest)

·         Family-level risk ranking and robustness analysis

The project is designed to support my ITC571 assessment research, with attention to experimental control, numerical stability, and reproducibility.

 
Repository Structure (high-level; after notebooks have been run and testing completed)
ML-Malware/

│

├── malrob/                     # Core Python package

│   ├── config.py               # Global experiment configuration (single source of truth) *to be implemented

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

├── models_full_12k/             # Model artifacts and experiment outputs (example)

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

 

Environment Requirements
Python

·         Python 3.10 or 3.11 recommended

·         Tested on Windows (PowerShell)

System Dependencies
·         Python (with venv support)

·         Jupyter

·         PyTorch

Installation & Setup
1. Clone the repository
git clone <repo package url>
cd ML-Malware
2. Create and activate a virtual environment
python -m venv venv
.\venv\Scripts\Activate.ps1
3. Install Python dependencies
pip install --upgrade pip
pip install -r requirements.txt
4. Register the Jupyter kernel (RECOMMENDED)
This ensures notebooks always run using the correct environment.

python -m ipykernel install --user --name malrob
In Jupyter:

Kernel → Change Kernel → malrob
While launching Jupyter from an activated venv often works, kernel registration is the best.

Alternatively: launch Jupyter from an activated venv

Running:

cd C:\ML-Malware

.\venv\Scripts\Activate.ps1

jupyter lab

 

 

5. Launch Jupyter
jupyter lab
Global Configuration Design (To be implemented)
All dataset sizing, paths, and output locations are controlled from a single configuration file:

malrob/config.py
Key variables (edit once, affects all notebooks):
TARGET_PER_CLASS = 6000        # Dataset size (per class)
RANDOM_STATE = 42
 
DATA_PREFIX = "ember_full"
MODELS_PREFIX = "models_full"
From these, the system automatically derives:

·         Dataset tag (12k, 36k, etc.)

·         Dataset paths

·         Model directories

·         Results and artifacts directories

This eliminates configuration drift across notebooks.

 

**Currently, each notebook must be set! This is generally found in the first cell of each notebook.

 

Notebook Execution Order (IMPORTANT)
The notebooks must be run in order, as later stages depend on artifacts from earlier ones.

Notebook 01 — Dataset Generation

01_dataset_generation.ipynb

Purpose:

Extract EMBER 2018 static PE features
Generate:
Binary dataset
Family-labelled dataset
Outputs:

datasets/ember2018/ember_full_12k_flat.csv

datasets/ember2018/ember_full_12k_families_flat.csv

Notebook 02 — Binary Model Training

02_binary_training.ipynb

Purpose:

Train:
Random Forest (binary)
Neural Network (binary)
Compute clean ROC-based thresholds (Youden’s J)
Outputs:

models_full_12k/

├── rf_binary_12k.joblib

├── nn_binary_12k.pt

├── scaler_full_12k.joblib

└── results/

Notebook 03 — Binary Adversarial Attacks

03_binary_adversarial_attacks.ipynb

Purpose:

FGSM and PGD attacks in feature space
Evaluate:
NN evasion
NN → RF transferability
Fixed threshold evaluation (clean thresholds)
Outputs:

models_full_12k/results_adv/

models_full_12k/artifacts_adv/

Notebook 04 — Family-Level Evasion Analysis

04_family_evasion_analysis.ipynb

Purpose:

Attribute adversarial evasion to malware families
Compute per-family robustness curves
Visualise FGSM / PGD family behaviour
Outputs:

models_full_12k/results_adv/

(Plots are both saved and displayed inline.)

Notebook 05 — Family Risk Ranking

05_family_risk_ranking.ipynb

Purpose:

Aggregate family robustness deltas
Rank malware families by adversarial risk
Generate tables for reporting / LaTeX export
Notebook 06 — Adversarial Transfer (Family Classification)

06_family_adversarial_transfer.ipynb

Purpose:

Train family-classification models:
RF (family)
NN (family)
Craft FGSM attacks on NN
Measure transfer to RF
Plot accuracy vs epsilon
Outputs:

models_full_12k/results_family/

models_full_12k/artifacts_family/

Reproducibility Guarantees

This repository enforces:

Deterministic dataset sampling
Fixed random seeds
Explicit feature column alignment
Explicit threshold persistence
CPU fallback for PyTorch
Single-source configuration
Results are reproducible across:

Machines
Reboots
Environments
Examiners / markers
Dependencies

All dependencies are listed in:

requirements.txt

Key libraries include:

numpy, pandas
scikit-learn
torch
matplotlib
joblib
jupyterlab, ipykernel
Intended Audience

Cybersecurity researchers
Malware ML practitioners
Master’s / PhD students
Adversarial ML researchers
Citation / Academic Use

If you use this repository for academic work, please cite appropriately and acknowledge:

“Adversarial robustness and transferability analysis of static malware detection models using EMBER features.”

 

 
