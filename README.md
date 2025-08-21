# SensitiveHUE-AE


[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)


📄 **Original Paper**: [SensitiveHUE: Sensitivity-guided Heterogeneous Uncertainty Estimation for Multivariate Time Series Anomaly Detection (KDD 2024)](https://dl.acm.org/doi/10.1145/3637528.3671870)  
💻 **Official Code**: https://github.com/yuesuoqingqiu/SensitiveHUE

## 🧠 About SensitiveHUE
SensitiveHUE is a multivariate time series anomaly detection (TSAD) method based on reconstruction error and uncertainty estimation. Its core innovations include:

- Sensitivity-Guided Mechanism

1. Introduces a sensitivity score to measure the model’s responsiveness to perturbations at different time positions and across variables.

2. Positions with higher sensitivity receive greater weight in anomaly determination, thereby enhancing detection performance.

- Heterogeneous Uncertainty Estimation

1. Models predictive uncertainty separately for each variable and time position, rather than using a single global σ, enabling more fine-grained anomaly scoring.

2. Implemented via the log_sigma output, which estimates the variance for each channel individually.

- Robust Normalization and Anomaly Score Fusion

1. Employs median + IQR (interquartile range) for robust normalization to mitigate the impact of distributional shifts.

2. Final anomaly scores combine reconstruction error and uncertainty estimation, supporting both patch-level and point-level evaluation.

- Unified Patch-Based Pipeline

1. Segments time series into sliding patches, ensuring compatibility with multiple baseline models for fair and consistent comparison.

## 🔄 Datasets  

| Dataset | #Variables | Sampling Rate | Domain | Description |
|---------|------------|---------------|--------|-------------|
| **SWaT** (Secure Water Treatment) | 51 | 1s | Industrial Control System (ICS) | Collected from a modern ICS testbed simulating a water treatment plant. Contains sensor readings under both normal and attack scenarios. |
| **WADI** (Water Distribution) | 123 | 1s | Industrial Control System (ICS) | Acquired from a larger-scale city water distribution network. Contains approximately half a month of operation data with labeled anomalies. |
| **MSL** (Mars Science Laboratory) | 27 | — | Spacecraft Telemetry | Telemetry data from the Curiosity rover. Anomaly ground truths are derived from Incident Surprise Anomaly (ISA) reports. |
| **SMD** (Server Machine Dataset) | 38 | — | Server Cluster | Resource utilization metrics (e.g., CPU usage) collected from a large internet company’s server cluster under normal and faulty conditions. |

### 📥 Download Links
- **SWaT**: [SWaT Dataset Download](https://itrust.sutd.edu.sg/itrust-labs_datasets/) (registration required)  
- **WADI**: [WADI Dataset Download](https://itrust.sutd.edu.sg/itrust-labs_datasets/) (registration required)  
- **MSL** & **SMD**: [OmniAnomaly Data Repository](https://github.com/NetManAIOps/OmniAnomaly)


## 🔄 Model Workflow
       ┌─────────────────────────────┐
       │  Multivariate Time Series X │
       └──────────────┬──────────────┘
                      ▼
             Patch Segmentation
          (e.g., sliding window)
                      ▼
             Embedding / Encoding
       (e.g., Transformer / Conv / MLP)
                      ▼
      ┌────────────────────────────────┐            
      │            Decoder             │
      │  ┌───────────────────────────┐ │
      │  │  μ (Reconstruction)       │ │
      │  │  log σ² (Uncertainty)     │ │
      │  └───────────────────────────┘ │
      └────────────────────────────────┘
                      ▼
        Compute NLL-like Anomaly Score:  
        
**Training Loss (MTS-NLL form)**： 

$$
L_{\text{MTSNLL}} = \frac{1}{2} \log(|\boldsymbol{\Sigma}|) + \frac{1}{2}(\boldsymbol{x} - \boldsymbol{\mu})^T \boldsymbol{\Sigma}^{-1} (\boldsymbol{x} - \boldsymbol{\mu})
$$


**Anomaly Scoring (NLL form)**：  

$$
S_{i,j} = \frac{(x_{i,j} - \mu_{i,j})^2}{\sigma_{i,j}^2 + \epsilon} + \log(\sigma_{i,j}^2)
$$

where:  
- $$\(x_{i,j}\)$$ = ground truth value of the \(j\)-th channel in the \(i\)-th patch  
- $$\(\mu_{i,j}\)$$ = reconstructed prediction  
- $$\(\sigma_{i,j}^2\)$$ = estimated uncertainty (heterogeneous variance)  


## 🔄 How to Run SensitiveHUE in `patch-anomaly-detector`

This project can be integrated into the [patch-anomaly-detector](https://github.com/nanfzq0087/patch-anomaly-detector) framework.  
Follow these steps to train and evaluate **SensitiveHUE** with the existing unified pipeline.
1. **Clone the main repository**  
2. **Add the backbone/ folder**
3. **Add the SensitiveHUE model file**
   
If you encounter any issues while running the model, feel free to contact me.
