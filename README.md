**Predictive Modeling and Process Variability Framework for Gate-Overlapped Dielectric-Modulated Heterojunction TFET Biosensors**

*Executive Overview*

This repository contains the complete simulation framework, dataset preprocessing pipeline, machine learning benchmarking suite, and process variability evaluation tools for a Gate-Overlapped Dielectric-Modulated Heterojunction Tunnel Field-Effect Transistor ($\text{DM H-TFET}$) Biosensor.

By replacing computationally intensive Technology Computer-Aided Design (TCAD) numerical simulations with high-fidelity machine learning surrogates, this framework models non-linear sensing responses under realistic, non-uniform biomolecule spatial distributions while reducing computational simulation overhead by $\sim 99\%$.

**Two-Phase Research Methodology**


<img width="707" height="413" alt="image" src="https://github.com/user-attachments/assets/e0087730-dbe0-43b2-9f45-1570526cd55a" />


**Phase 1: Device Architecture, Filling Profiles & Model Benchmarking**

*1. Device Architecture & TCAD Simulation Physics*

The proposed biosensor incorporates a Germanium–Silicon ($\text{Ge}$–$\text{Si}$) heterojunction with a $100\text{ nm}$ gate overlap on the source to promote line-tunneling transport perpendicular to the gate interface.

<img width="745" height="521" alt="image" src="https://github.com/user-attachments/assets/380dce36-1667-4cd7-a335-df90de91e8d5" />

Source Region: Heavily doped $\text{p}^+$ Germanium ($N_A = 1 \times 10^{20}\text{ cm}^{-3}$), $L_s = 200\text{ nm}$

Channel Region: Lightly doped $\text{p}^-$ Silicon ($N_A = 1 \times 10^{15}\text{ cm}^{-3}$), $L_{\text{ch}} = 200\text{ nm}$

Drain Region: Heavily doped $\text{n}^+$ Silicon ($N_D = 1 \times 10^{20}\text{ cm}^{-3}$), $L_d = 100\text{ nm}$

Gate Dielectric Stack: $8\text{ nm}$ $\text{HfO}_2$ ($k = 25$) over $1\text{ nm}$ interfacial $\text{SiO}_2$ ($k = 3.9$), $\Phi_m = 4.2\text{ eV}$

Sensing Cavity: Dual dielectric-modulated cavities with nominal $90\%$ fill fraction

*2. Multi-Profile Sensitivity Analysis*

Phase 1 evaluates device response when sweeping Cavity Height ($H_{\text{cavity}}$) across four realistic non-uniform biomolecule spatial distributions:

       CONCAVE                       CONVEX
+-------------------+         +-------------------+
|  air  | air | air |         |     | air |       |
|----+--+-----+-----|         |-----+-----+-------|
|Bio |  |     | Bio |         |     |Bio  |       |
|    |  |     |     |         | Bio |     | Bio   |
+-------------------+         +-------------------+

       RAMP-UP                      RAMP-DOWN
+-------------------+         +-------------------+
| air |             |         |             | air |
|-----+-----+-------|         |-------+-----+-----|
|     | Bio |       |         |       | Bio |     |
|     |     | Bio   |         | Bio   |     |     |
+-------------------+         +-------------------+



Concave Distribution: Peak biomolecule concentration near cavity edges ($x = 0$ and $x = L_{\text{cavity}}$).

Convex Distribution: Maximum biomolecule accumulation centered in the cavity middle.

Ramp-Up Distribution: Linearly increasing density from source-side edge to drain-side edge.

Ramp-Down Distribution: Linearly decreasing density from source-side edge to drain-side edge.

*3. Machine Learning Algorithm Benchmarking*

Five supervised regression models were evaluated across all four filling distributions:

Random Forest Regression (RFR) (Selected Surrogate)

K-Nearest Neighbors Regression (KNN)

eXtreme Gradient Boosting Regression (XGBoost / XGBR)

Gradient Boosting Regression (GBR)

Support Vector Regression (SVR)

Key Phase 1 Conclusions

Optimal Algorithm: Random Forest Regression (RFR) achieved the best operational balance of predictive fidelity ($R^2 = 0.999993$, Physical Accuracy $> 99.89\%$) and rapid inference speed.

Most Sensitive Geometry: The Concave filling profile induced the highest relative shift in electrostatic gate coupling, yielding superior device sensitivity for both $I_{\text{ON}}$ and $V_{\text{th}}$ metrics.

**Phase 2: Process Variability Modeling (Concave Profile & RFR)**

With the profile locked to Concave and the surrogate set to Random Forest Regressor (RFR), Phase 2 models structural process variations encountered during microfabrication.

*1. Process Parameter Sweeps*

Cavity Length ($L_{\text{cavity}}$): Evaluated across $100\text{ nm} - 180\text{ nm}$

Silicon Body Thickness ($T_{\text{si}}$): Evaluated across $8\text{ nm} - 12\text{ nm}$

*2. Physical Target Metrics*

$\text{ON}$-Current Sensitivity ($S_{I_{\text{ON}}}$):

$$S_{I_{\text{ON}}} = \frac{I_{\text{ON, bio}} - I_{\text{ON, air}}}{I_{\text{ON, air}}}$$

Threshold Voltage Sensitivity ($S_{V_{\text{th}}}$):

$$S_{V_{\text{th}}} = \vert V_{\text{th, air}} - V_{\text{th, bio}} \vert$$

Zero-Leakage Preprocessing & Evaluation Pipeline

To ensure statistical validity and prevent data leakage, raw dataset processing follows a 4-stage sequential workflow:

+-----------------------------------------------------------------------+
|                       Stage 1: Data Cleaning                          |
|  - Drop NaN and Infinite (\pm \infty) values                           |
|  - Enforce physical boundaries: x_j > 0 and y \ge 0                   |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|                     Stage 2: Dataset Partitioning                     |
|  - Filter by specific filling profile (Concave, Convex, etc.)        |
|  - Split into Train/Test partitions (e.g., 80:20 split)               |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|             Stage 3: Zero-Leakage Feature & Target Scaling             |
|  - Fit MinMaxScaler strictly on Training Partition (X_{\text{train}}) |
|  - Compute y_{\min} and y_{\max} from Training Targets (y_{\text{train}})|
|  - Scale Training and Testing sets independently                      |
+-----------------------------------------------------------------------+
                                   |
                                   v
+-----------------------------------------------------------------------+
|         Stage 4: Inverse Transformation & Metric Evaluation           |
|  - Map predictions back: y_{\text{pred}} = \hat{\hat{y}} \cdot         |
|    (y_{\max} - y_{\min}) + y_{\min}                                   |
|  - Calculate R^2, MSE, RMSE, MAE, and Physical Accuracy %             |
+-----------------------------------------------------------------------+



Normalization and Inverse Formulations

Min-Max Feature & Target Normalization:

$$\hat{y}_i = \frac{y_i - y_{\min}}{y_{\max} - y_{\min}}$$

Exact Physical Inverse Reconstruction:

$$y_{\text{pred}, i} = \hat{\hat{y}}_i \cdot (y_{\max} - y_{\min}) + y_{\min}$$

Performance Evaluation Metrics

Mean Squared Error ($\text{MSE}$):

$$\text{MSE} = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$

Root Mean Squared Error ($\text{RMSE}$):

$$\text{RMSE} = \sqrt{\text{MSE}}$$

Mean Absolute Error ($\text{MAE}$):

$$\text{MAE} = \frac{1}{n} \sum_{i=1}^{n} \vert y_i - \hat{y}_i \vert$$

Coefficient of Determination ($R^2$):

$$R^2 = 1 - \frac{\sum_{i=1}^{n} (y_i - \hat{y}_i)^2}{\sum_{i=1}^{n} (y_i - \bar{y})^2}$$

Physical Accuracy Metric (%):

$$\text{Accuracy (\%)} = \left( 1 - \frac{1}{n} \sum_{i=1}^{n} \frac{\vert y_i - y_{\text{pred}, i} \vert}{y_i} \right) \times 100$$

Experimental Benchmarks & Results

1. Algorithm Benchmarking Comparison ($80:20$ Train-Test Split)

|

| Algorithm | R2 Score | MSE | RMSE | MAE | Physical Accuracy (%) |
| Random Forest Regression (RFR) | 0.999993 | $1.0 \times 10^{-6}$ | $7.92 \times 10^{-4}$ | $5.23 \times 10^{-4}$ | 99.8989% |
| K-Nearest Neighbors (KNN) | $0.999993$ | $1.0 \times 10^{-6}$ | $7.92 \times 10^{-4}$ | $5.21 \times 10^{-4}$ | $99.8993\%$ |
| eXtreme Gradient Boosting (XGBR) | $0.999970$ | $3.0 \times 10^{-6}$ | $1.637 \times 10^{-3}$ | $1.147 \times 10^{-3}$ | $99.7628\%$ |
| Gradient Boosting Regression (GBR) | $0.999967$ | $3.0 \times 10^{-6}$ | $1.699 \times 10^{-3}$ | $1.283 \times 10^{-3}$ | $99.6983\%$ |
| Support Vector Regression (SVR) | $0.999527$ | $4.2 \times 10^{-5}$ | $6.452 \times 10^{-3}$ | $5.474 \times 10^{-3}$ | $98.2628\%$ |

2. Phase 2 RFR Performance Across Train-Test Split Ratios (Concave Profile)

| Train : Test Ratio | MAE | R2 Score | MSE | Physical Accuracy (%) |
| 50 : 50 | $2.3490 \times 10^{-4}$ | $0.999996$ | $3.6 \times 10^{-7}$ | $99.9534\%$ |
| 60 : 40 | $1.9639 \times 10^{-4}$ | $0.999996$ | $2.9 \times 10^{-7}$ | $99.9618\%$ |
| 70 : 30 | $1.8371 \times 10^{-4}$ | $0.999996$ | $2.9 \times 10^{-7}$ | $99.9653\%$ |
| 80 : 20 | $1.7327 \times 10^{-4}$ | 0.999997 | $2.8 \times 10^{-7}$ | 99.9683% |
| 90 : 10 | $1.5788 \times 10^{-4}$ | $0.999997$ | $2.5 \times 10^{-7}$ | $99.9720\%$ |

3. Computational Speedup & Overhead Reduction

| Execution Phase | TCAD Simulation (Silvaco ATLAS) | Machine Learning Framework (RFR) | Speedup / Acceleration |
| Dataset Generation ($50\text{k}$ Runs) | $\sim 120\text{ CPU Hours}$ | N/A (Offline baseline) | — |
| Single-Point Inference | $\sim 8.5\text{ Minutes}$ per point | $< 0.001\text{ s}$ | $> 500{,}000\times$ Speedup |
| Full Parameter Sweep | $\sim 48\text{ Hours}$ | $\sim 1.2\text{ s}$ | $\sim 99.99\%$ Overhead Reduction |

Repository Structure

├── docs/
│   └── sequential_data_preprocessing_pipeline.md  # Detailed data pipeline specification
├── logs/
│   └── pipeline_execution.log                     # Pipeline execution verification log
├── monte_carlo_runner/
│   ├── batch_generator.py                         # Silvaco ATLAS MC input deck generator
│   └── extract_sensitivity.py                     # Output logs parser for S_Ion & S_Vth
├── notebooks/
│   ├── 01_phase1_profile_screening.ipynb          # Profile screening & H_cavity variations
│   ├── 02_phase1_model_benchmarking.ipynb         # RFR vs GBR vs XGBR vs KNN vs SVR
│   └── 03_phase2_concave_rfr_analysis.ipynb       # L_cavity & T_si process variability
├── src/
│   ├── preprocessing_pipeline.py                  # Zero-leakage 4-stage preprocessing class
│   ├── models.py                                  # Machine learning surrogates & RFR setup
│   ├── metrics.py                                 # Statistical error & physical accuracy metrics
│   └── visualization.py                           # Tolerance-filtered overlay plotting scripts
├── jsr_research_paper_phase1_phase2.md            # Complete integrated research paper draft
├── requirements.txt                               # Environment dependencies specification
└── README.md                                      # Unified project documentation



Setup & Execution Guide

Prerequisites

Python 3.8+

Silvaco ATLAS TCAD (v5.20.0.R or higher) (Optional: required only for generating raw TCAD deck datasets)

Installation

Clone the repository and install required packages:

pip install -r requirements.txt



Run the Zero-Leakage Preprocessing Pipeline Verification:

python src/preprocessing_pipeline.py --target S_Ion --test_ratio 0.20



Execute Phase 1 Multi-Algorithm Benchmarking:

python src/models.py --phase 1 --param H_cavity --data_path data/phase1_dataset.csv



Execute Phase 2 Process Variability Modeling (Concave Profile + RFR):

python src/models.py --phase 2 --profile concave --model rfr --params L_cavity T_si --data_path data/phase2_concave_dataset.csv

