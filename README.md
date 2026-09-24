# Reliability-Aware Nash–Shapley Fusion of Heterogeneous Spatiotemporal Signals

Official code and reproducibility resources for:

**Reliability-Aware Nash–Shapley Fusion of Heterogeneous Spatiotemporal Signals**

Chao Zhang, Xinze Zhao, Rui Li  
School of Computer and Information Technology, Shanxi University

> Paper status: ICASSP 2027 submission / under review.  
> Repository status: reproducibility materials are being prepared.

## Overview

This repository accompanies our work on reliability-aware fusion of heterogeneous spatiotemporal signals for wildfire risk estimation.

The framework is designed to separate three aspects that are often conflated in multimodal fusion:

1. **Temporal structural confidence** — whether the local signal state is temporally coherent and structurally stable.
2. **Current-state predictive utility** — how much each source contributes under the present fusion state.
3. **Coalition-level complementarity** — whether a source provides non-redundant information when combined with other sources.

The proposed pipeline combines temporally regularized fuzzy state modeling, Nash bargaining, Shapley-value analysis, and validation-only Pareto selection.

## Method

The full pipeline consists of four main stages.

### 1. Temporal structural confidence

A temporally regularized fuzzy state model is used to estimate latent spatiotemporal states. State support and temporal stability are combined to construct a structural-confidence prior.

This prior is **not** treated as a calibrated wildfire probability or as a direct source-reliability probability. Instead, it weights disagreement more strongly in structurally stable regions.

### 2. Reliability-aware Nash bargaining

For each source, the framework evaluates:

- leave-one-source-out predictive utility,
- reliability-weighted disagreement with the fused prediction,
- normalized computational cost.

These terms define a source-specific utility function. Nash bargaining is then used to estimate adaptive source weights while discouraging premature collapse to a single source.

### 3. Shapley complementarity analysis

Nash bargaining captures current utility and conflict, but not coalition-level complementarity.

We therefore estimate Shapley contributions over source coalitions using Monte Carlo permutations. The Shapley term measures each source's marginal non-redundant contribution across different source combinations.

The Nash and Shapley weights are combined and iteratively refined until convergence.

### 4. Validation-only Pareto selection

The final operating point is selected from the non-dominated validation configurations using three objectives:

- predictive quality,
- calibration quality,
- computational efficiency.

All fusion hyperparameters and operating-point decisions are selected on the validation set only. The test set is reserved for final reporting.

## Data

The experiments use wildfire observations from Guangxi and Yunnan, China, covering 2015–2022.

Five heterogeneous signal groups are considered:

- Remote sensing
- Meteorological observations
- Ground-station measurements
- Geospatial variables
- Social-media signals

Weekly observations are aggregated on a 1-km grid.

The experimental split is event-aware:

- 70% training
- 10% validation
- 20% testing

All samples from the same wildfire event are assigned to the same partition.

> The raw datasets may be subject to third-party licenses or access restrictions. This repository will document preprocessing steps and provide scripts/configuration files where redistribution is permitted.

## Main Results

| Method | AUC ↑ | PR-AUC ↑ | F1 ↑ | Brier ↓ | ECE ↓ |
|---|---:|---:|---:|---:|---:|
| Single-source | 0.781 | 0.725 | 0.708 | 0.214 | 0.181 |
| Homogeneous fusion | 0.824 | 0.763 | 0.751 | 0.187 | 0.154 |
| Generic DL multimodal | 0.861 | 0.811 | 0.793 | 0.159 | 0.118 |
| **Proposed** | **0.912** | **0.864** | **0.846** | **0.121** | **0.087** |

## Robustness Under Degraded Inputs

Reported degradation relative to the clean-input setting:

| Method | Missing ↓ | Noise ↓ | Delay ↓ | ΔECE ↓ |
|---|---:|---:|---:|---:|
| Single-source | 0.094 | 0.081 | 0.073 | 0.041 |
| Homogeneous fusion | 0.071 | 0.066 | 0.058 | 0.032 |
| Generic DL multimodal | 0.053 | 0.049 | 0.041 | 0.021 |
| **Proposed** | **0.031** | **0.028** | **0.022** | **0.013** |

## Default Experimental Settings

The paper reports the following default settings:

```text
Temporal states:        K = 4
Temporal regularizer:   beta = 0.5
Shapley permutations:   L = 1000
Convergence tolerance:  tau = 1e-4
Random seeds:           42, 52, 62, 72, 82
```

The fusion parameters `lambda_d`, `lambda_c`, `gamma`, and `alpha` are selected on the validation set.

## Planned Repository Structure

```text
reliability-aware-nash-shapley-fusion/
├── README.md
├── LICENSE
├── requirements.txt
├── configs/
│   └── default.yaml
├── data/
│   └── README.md
├── src/
│   ├── temporal_state.py
│   ├── nash_fusion.py
│   ├── shapley.py
│   ├── pareto.py
│   ├── metrics.py
│   └── utils.py
├── scripts/
│   ├── preprocess.py
│   ├── train_sources.py
│   ├── run_fusion.py
│   ├── run_robustness.py
│   └── reproduce_tables.py
└── results/
    └── README.md
```

The exact structure may change as the public release is finalized.

## Installation

A reproducible environment file will be provided with the public code release.

Planned usage:

```bash
git clone https://github.com/lkzyt/reliability-aware-nash-shapley-fusion.git
cd reliability-aware-nash-shapley-fusion

pip install -r requirements.txt
```

## Reproducing the Experiments

The intended reproduction workflow will follow the paper:

```bash
# 1. Prepare aligned weekly inputs
python scripts/preprocess.py

# 2. Train source-specific predictors
python scripts/train_sources.py

# 3. Run reliability-aware Nash–Shapley fusion
python scripts/run_fusion.py

# 4. Evaluate robustness to missing, noisy, and delayed inputs
python scripts/run_robustness.py

# 5. Reproduce tables and summary metrics
python scripts/reproduce_tables.py
```

These commands are placeholders until the corresponding scripts are released.

## Reproducibility Protocol

To match the paper, experiments should use:

- the same event-aware train/validation/test partition,
- the same aligned weekly inputs,
- the same preprocessing pipeline,
- the same early-stopping criterion,
- the same metric conventions,
- the same five random seeds.

Model and fusion parameters must be selected using training/validation data only. Test data should be used only for final reporting.

## Citation

If you use this work, please cite the paper.

```bibtex
@inproceedings{zhang2027reliability,
  title     = {Reliability-Aware Nash--Shapley Fusion of Heterogeneous Spatiotemporal Signals},
  author    = {Zhang, Chao and Zhao, Xinze and Li, Rui},
  booktitle = {Proceedings of the IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP)},
  year      = {2027}
}
```

> Citation metadata will be updated after the final publication record is available.

## License

A license will be added before the full public release. Please do not assume redistribution rights for third-party datasets.

## Contact

For questions about the paper or code:

**Rui Li**  
School of Computer and Information Technology, Shanxi University  
Email: `lirui97@sxu.edu.cn`

## Repository

https://github.com/lkzyt/reliability-aware-nash-shapley-fusion
