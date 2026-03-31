# QCTrojan-Bench: Quantum Circuit Trojan Detection Benchmark

A reproducible benchmark dataset and evaluation pipeline for machine learning-based detection of malicious quantum circuits.

---

## Overview

QCTrojan-Bench is the first systematic, publicly reproducible benchmark for **pre-execution, software-level detection of malicious quantum circuits**. It provides 9,000 quantum circuits across five algorithm families, with two Trojan construction strategies, alongside a complete feature extraction and ML evaluation pipeline.

This dataset and pipeline accompany the paper:

> **Software-Level Detection of Malicious Quantum Circuits: An Empirical Study**
> Zeeshan Ajmal, Kimmo Halunen, Arif Ali Khan
> University of Oulu, Finland
> *Submitted to Empirical Software Engineering (EMSE)*

---

## Dataset Statistics

| Property | Value |
|----------|-------|
| Total circuits | 9,000 |
| Algorithm families | 5 |
| Benign circuits | 3,000 (600 per family) |
| Static tampered circuits | 3,000 |
| Triggered tampered circuits | 3,000 |
| Structural features per circuit | 39 (normalised) |
| Transpilation backend | FakeGuadalupeV2 (16 qubits) |
| Transpiler seed | 12345 |
| Qiskit version | 2.3.1 |

---

## Algorithm Families

| Family | Paradigm | Qubit range |
|--------|----------|-------------|
| Deutsch-Jozsa | Oracle-based decision | 3–8 |
| Grover Search | Amplitude amplification | 4–12 |
| QAOA (MaxCut) | Variational optimisation | 6–14 |
| VQC (TwoLocal) | Variational learning | 4–12 |
| QFT / inverse QFT | Fourier transform | 4–12 |

---

## Trojan Types

**Static (Class A):** Always-active CX–RZ–CX gate motif injected into the circuit body. Corrupts output probability distribution on every execution.

**Triggered (Class B):** MCPhaseGate controlled by an existing data qubit. Payload dormant when control qubit is |0⟩ (normal execution). No ancilla qubits added. Same width as benign circuit.

---

## Key Results

| Model | Balanced Accuracy (IID) | ROC-AUC |
|-------|------------------------|---------|
| Random Forest | 0.953 ± 0.003 | 0.996 ± 0.000 |
| RBF SVM | 0.749 ± 0.008 | 0.913 ± 0.005 |
| Linear SVM | 0.729 ± 0.010 | 0.897 ± 0.005 |
| QSVM (PoC) | 0.414 | 0.592 |

**Holdout evaluation** (leave-one-family-out): RF balanced accuracy range 0.389–1.000. Performance degrades substantially for variational families (QAOA, VQC), revealing a fundamental generalisation gap across the variational/non-variational boundary.

---

## Repository Structure

```
quantum-circuit-trojan-detection/
│
├── notebooks/
│   ├── S1_benign_circuits.ipynb       # Generate 3,000 benign circuits
│   ├── S2_tampered_circuits.ipynb     # Generate 6,000 tampered circuits
│   ├── S3_feature_extraction.ipynb    # Extract 39 normalised features
│   ├── S4_split_index.ipynb           # Source-stratified CV split index
│   ├── S5_models.ipynb                # RF, Linear SVM, RBF SVM training
│   ├── S6_qsvm.ipynb                  # QSVM proof-of-concept baseline
│   └── VIZ_dataset_exploration.ipynb  # Dataset visualisation figures
│
├── dataset/
│   ├── circuits/
│   │   ├── benign/                    # Benign QPY circuits + metadata
│   │   └── tampered/                  # Tampered QPY circuits + metadata
│   └── features/
│       ├── features_v1.csv            # Full feature matrix (9,000 rows)
│       ├── split_index.csv            # CV split assignments
│       └── extractor_manifest.json    # Reproducibility manifest
│
└── results/
    ├── exp_iid/                       # IID CV results + figures
    ├── exp_holdout/                   # Holdout results + figures
    ├── exp_ablation/                  # Feature ablation results
    ├── exp_qsvm/                      # QSVM results
    └── figures/                       # Publication figures
```

---

## Quickstart

### Prerequisites

```bash
pip install qiskit==2.3.1
pip install qiskit-aer==0.17.2
pip install qiskit-machine-learning==0.9.0
pip install scikit-learn networkx pandas matplotlib seaborn statsmodels scipy
```

### Run the pipeline

Run notebooks in order:

```
S1 → S2 → S3 → S4 → S5 → S6 (optional, slow)
```

Each notebook is self-contained and saves outputs before the next stage begins. All paths are relative — no hardcoded paths. Move the project folder anywhere and it still works.

### Explore the dataset

Open `VIZ_dataset_exploration.ipynb` for circuit visualisations and summary statistics without running the full pipeline.

---

## Reproducibility

All randomness is fully seeded:

| Component | Seed |
|-----------|------|
| DJ generation | 1000 |
| Grover generation | 2000 |
| QAOA generation | 3000 |
| VQC generation | 4000 |
| QFT generation | 5000 |
| Split index | 42 |
| All ML models | 42 |
| Transpiler | 12345 |

The `dataset/features/extractor_manifest.json` records the exact backend, seed, and feature definitions used for feature extraction.

---

## Dataset Access

Circuit files (QPY format) are not stored in this repository due to size. To regenerate the full dataset:

1. Clone this repository
2. Install prerequisites
3. Run `S1_benign_circuits.ipynb` then `S2_tampered_circuits.ipynb`

The `features_v1.csv` file (required for model training) is also regenerated by running `S3_feature_extraction.ipynb`.

A Zenodo release with the pre-generated dataset will be available upon paper acceptance.

---

## Citation

If you use this dataset or pipeline, please cite:

```bibtex
@article{ajmal2025qctrojanbench,
  title   = {Software-Level Detection of Malicious Quantum Circuits:
             An Empirical Study},
  author  = {Ajmal, Zeeshan and Halunen, Kimmo and Khan, Arif Ali},
  journal = {Empirical Software Engineering},
  year    = {2025},
  note    = {Under review}
}
```

---

## Licence

Code and notebooks: MIT License
Dataset (circuits and features): CC BY 4.0

---

## Authors

- **Zeeshan Ajmal** — zeeshan.ajmal@oulu.fi

Faculty of Information Technology and Electrical Engineering,
University of Oulu, Finland
