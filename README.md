<h1>ML-Malware: Adversarial Robustness and Transferability Analysis</h1>

      This repository contains a <strong>reproducible research pipeline</strong> for evaluating
      <strong>static malware detection models</strong> under <strong>feature-space adversarial attacks</strong>,
      with a particular focus on:
    <ul>
      <li>Binary malware detection (Benign vs Malware)</li>
      <li>Malware family classification</li>
      <li>Adversarial robustness under FGSM and PGD</li>
      <li>Cross-model transferability (Neural Network → Random Forest)</li>
      <li>Family-level risk ranking and robustness analysis</li>
    </ul>

   
      The project is designed to support <strong>Master’s-level and PhD-ready research</strong>, with strict
      attention to <strong>experimental control</strong>, <strong>numerical stability</strong>, and
      <strong>reproducibility</strong>.
   

    <h2>Repository Structure (High-Level)</h2>
    <code>ML-Malware/
│
├── malrob/                     # Core Python package
│   ├── config.py               # Global experiment configuration (single source of truth)
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
├── models_full_12k/            # Model artifacts and experiment outputs (example)
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
└── README.md</code>

    <h2>Environment Requirements</h2>

    <h3>Python</h3>
    <ul>
      <li><strong>Python 3.10 or 3.11 recommended</strong></li>
      <li>Tested on <strong>Windows (PowerShell)</strong></li>
    </ul>

    <h3>System Dependencies</h3>
    <ul>
      <li>Git</li>
      <li>Python (with venv support)</li>
      <li>(Optional) CUDA-enabled GPU for PyTorch (CPU fallback supported)</li>
    </ul>

    <h2>Installation &amp; Setup</h2>

    <h3>1. Clone the repository</h3>
    <pre><code>git clone &lt;your-repo-url&gt;
cd ML-Malware</code></pre>

    <h3>2. Create and activate a virtual environment</h3>
    <pre><code>python -m venv venv
.\venv\Scripts\Activate.ps1</code></pre>

    <h3>3. Install Python dependencies</h3>
    <pre><code>pip install --upgrade pip
pip install -r requirements.txt</code></pre>

    <h3>4. Register the Jupyter kernel (RECOMMENDED)</h3>
    <p>This ensures notebooks always run using the correct environment.</p>
    <pre><code>python -m ipykernel install --user --name malrob</code></pre>
    <p>In Jupyter:</p>
    <pre><code>Kernel → Change Kernel → malrob</code></pre>

    <div class="note">
      <strong>Note:</strong> Launching Jupyter from an activated venv often works, but kernel registration is strongly
      recommended for reproducibility, marking, and long-term research stability.
    </div>

    <h3>5. Launch Jupyter</h3>
    <pre><code>jupyter lab</code></pre>

    <h2>Global Configuration Design</h2>
    <p>
      All dataset sizing, paths, and output locations are controlled from a <strong>single configuration file</strong>:
      <span class="kbd">malrob/config.py</span>
    </p>

    <h3>Key variables (edit once, affects all notebooks)</h3>
    <pre><code>TARGET_PER_CLASS = 6000        # Dataset size (per class)
RANDOM_STATE = 42

DATA_PREFIX = "ember_full"
MODELS_PREFIX = "models_full"</code></pre>

    <p>From these, the system automatically derives:</p>
    <ul>
      <li>Dataset tag (<span class="kbd">12k</span>, <span class="kbd">36k</span>, etc.)</li>
      <li>Dataset paths</li>
      <li>Model directories</li>
      <li>Results and artifacts directories</li>
    </ul>

    <h2>Notebook Execution Order (IMPORTANT)</h2>
    <p>The notebooks <strong>must be run in order</strong>, as later stages depend on artifacts from earlier ones.</p>

    <h3>Notebook 01 — Dataset Generation</h3>
    <p><strong>01_dataset_generation.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>Extract EMBER 2018 static PE features</li>
      <li>Generate:
        <ul>
          <li>Binary dataset</li>
          <li>Family-labelled dataset</li>
        </ul>
      </li>
    </ul>
    <p>Outputs:</p>
    <pre><code>datasets/ember2018/ember_full_12k_flat.csv
datasets/ember2018/ember_full_12k_families_flat.csv</code></pre>

    <h3>Notebook 02 — Binary Model Training</h3>
    <p><strong>02_binary_training.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>Train:
        <ul>
          <li>Random Forest (binary)</li>
          <li>Neural Network (binary)</li>
        </ul>
      </li>
      <li>Compute clean ROC-based thresholds (Youden’s J)</li>
    </ul>
    <p>Outputs:</p>
    <pre><code>models_full_12k/
├── rf_binary_12k.joblib
├── nn_binary_12k.pt
├── scaler_full_12k.joblib
└── results/</code></pre>

    <h3>Notebook 03 — Binary Adversarial Attacks</h3>
    <p><strong>03_binary_adversarial_attacks.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>FGSM and PGD attacks in feature space</li>
      <li>Evaluate:
        <ul>
          <li>NN evasion</li>
          <li>NN → RF transferability</li>
        </ul>
      </li>
      <li>Fixed threshold evaluation (clean thresholds)</li>
    </ul>
    <p>Outputs:</p>
    <pre><code>models_full_12k/results_adv/
models_full_12k/artifacts_adv/</code></pre>

    <h3>Notebook 04 — Family-Level Evasion Analysis</h3>
    <p><strong>04_family_evasion_analysis.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>Attribute adversarial evasion to malware families</li>
      <li>Compute per-family robustness curves</li>
      <li>Visualise FGSM / PGD family behaviour</li>
    </ul>
    <p>Outputs:</p>
    <pre><code>models_full_12k/results_adv/</code></pre>
    <p>(Plots are both saved and displayed inline.)</p>

    <h3>Notebook 05 — Family Risk Ranking</h3>
    <p><strong>05_family_risk_ranking.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>Aggregate family robustness deltas</li>
      <li>Rank malware families by adversarial risk</li>
      <li>Generate tables for reporting / LaTeX export</li>
    </ul>

    <h3>Notebook 06 — Adversarial Transfer (Family Classification)</h3>
    <p><strong>06_family_adversarial_transfer.ipynb</strong></p>
    <p>Purpose:</p>
    <ul>
      <li>Train family-classification models:
        <ul>
          <li>RF (family)</li>
          <li>NN (family)</li>
        </ul>
      </li>
      <li>Craft FGSM attacks on NN</li>
      <li>Measure <strong>transfer to RF</strong></li>
      <li>Plot accuracy vs epsilon</li>
    </ul>
    <p>Outputs:</p>
    <pre><code>models_full_12k/results_family/
models_full_12k/artifacts_family/</code></pre>

    <h2>Reproducibility Guarantees</h2>
    <p>This repository enforces:</p>
    <ul>
      <li>Deterministic dataset sampling</li>
      <li>Fixed random seeds</li>
      <li>Explicit feature column alignment</li>
      <li>Explicit threshold persistence</li>
      <li>CPU fallback for PyTorch</li>
      <li>Single-source configuration</li>
    </ul>
    <p>Results are reproducible across machines, reboots, environments, and examiners/markers.</p>

    <h2>Dependencies</h2>
    <p>All dependencies are listed in <span class="kbd">requirements.txt</span>.</p>
    <p>Key libraries include:</p>
    <ul>
      <li><span class="kbd">numpy</span>, <span class="kbd">pandas</span></li>
      <li><span class="kbd">scikit-learn</span></li>
      <li><span class="kbd">torch</span></li>
      <li><span class="kbd">matplotlib</span></li>
      <li><span class="kbd">joblib</span></li>
      <li><span class="kbd">jupyterlab</span>, <span class="kbd">ipykernel</span></li>
    </ul>

    <h2>Intended Audience</h2>
    <ul>
      <li>Cybersecurity researchers</li>
      <li>Malware ML practitioners</li>
      <li>Master’s / PhD students</li>
      <li>Adversarial ML researchers</li>
    </ul>

    <h2>Citation / Academic Use</h2>
    <p>If you use this repository for academic work, cite appropriately and acknowledge:</p>
    <div class="note">
      “Adversarial robustness and transferability analysis of static malware detection models using EMBER features.”
    </div>

    <hr style="border:0;border-top:1px solid var(--border);margin:34px 0 0;" />
    <p style="color:var(--muted); margin-top:14px;">
      Optional enhancements (not required): add an architecture diagram, a reproducibility checklist appendix, or version pinning for archival submission.
    </p>
  </main>
</body>
</html>
