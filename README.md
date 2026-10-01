# AI-Driven Design and Optimisation of Antenna Arrays

MSc dissertation project combining full-wave electromagnetic simulation with
machine learning to accelerate microstrip antenna design.

## Overview

Antenna design via full-wave electromagnetic simulation (COMSOL Multiphysics)
is accurate but computationally expensive — a single high-frequency design
study can take hours to days. This project builds ML surrogate models that
predict antenna performance directly from geometry, cutting evaluation time
from hours to milliseconds.

Three antennas were modelled and simulated in COMSOL as part of the broader
study:

- A circular microstrip patch antenna with coaxial feed (5.8 GHz)
- A dual-port planar inverted-F antenna / PIFA (2.1–2.9 GHz)
- An **18 GHz rectangular linear microstrip patch array** (6 elements)

The circular patch and PIFA were used for EM analysis and benchmarking only.
**The machine learning models were trained exclusively on the 18 GHz linear
microstrip array (LMA).** A parametric sweep of ~2,700 design variations was
generated in COMSOL and used to train three supervised regression models —
Artificial Neural Network (ANN), Support Vector Regression (SVR), and Random
Forest — predicting 8 performance metrics (S11, VSWR, real/imaginary
impedance, gain, directivity, total radiated power, and efficiency) from 11
geometric and material input parameters.

## Results

| Model         | Test R² (overall) | Notes                                    |
|---------------|--------------------|-------------------------------------------|
| **ANN**       | ~0.985             | Best overall; most stable, lowest error   |
| Random Forest | ~0.96              | Strong, slightly behind ANN               |
| SVR           | ~0.93–0.95         | Weaker on directivity/efficiency          |

A held-out verification design (unseen geometry) was predicted by the Random
Forest model with ~9.3% average error against a full COMSOL simulation —
demonstrating that the trained models can replace repeated full-wave
simulations during design exploration.

## Repository Structure

```
├── notebooks/
│   ├── ANN_training.ipynb            # Artificial Neural Network (forward + inverse models)
│   ├── RandomForest_training.ipynb   # Random Forest regressor
│   └── SVR_training.ipynb            # Support Vector Regression
├── data/
│   └── LMA_Dataset.csv               # 18 GHz linear microstrip array parametric sweep (2,700 designs)
├── report/
│   └── Dissertation_Report.pdf       # Full MSc dissertation
├── requirements.txt
└── README.md
```

## COMSOL Model

The `.mph` COMSOL model file (~184 MB) is provided as a GitHub Release
rather than in the repository directly, since it exceeds GitHub's file size
limits — see **[Releases](../../releases)**.

## Running the Notebooks

```bash
pip install -r requirements.txt
jupyter notebook notebooks/ANN_training.ipynb
```

Each notebook loads `../data/LMA_Dataset.csv` using a relative path, so no
configuration is needed as long as the repository structure is kept intact.

## Methodology Summary

1. **Simulation** — Antennas modelled in COMSOL Multiphysics using the
   Electromagnetic Waves, Frequency Domain interface; parametric sweeps run
   over geometry parameters (patch length/width, substrate height, feed
   dimensions, element spacing).
2. **Data processing** — Simulation outputs exported to CSV, cleaned
   (duplicates/missing values handled), and split 70/15/15 into
   train/validation/test sets.
3. **Model training** — Three regression models trained on the same data
   split, with hyperparameters tuned via grid search and 5-fold
   cross-validation (ANN, SVR).
4. **Validation** — Model predictions compared against COMSOL outputs for
   both known and unseen geometries.

## Tech Stack

- **Simulation:** COMSOL Multiphysics (Electromagnetic Waves, Frequency Domain)
- **ML / Data:** Python, scikit-learn, pandas, NumPy, Matplotlib

## Author

Abishek Srinivasan Moorthy
MSc Robotics & AI, University of Glasgow

**Supervisors:** Kaveh Delfanzari, Priyanka Dhiwa
**Submitted:** November 2025
