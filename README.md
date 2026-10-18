# Computer Log Anomaly Detection

A multi-stage Machine Learning and Deep Learning project for detecting anomalous network traffic across datasets of increasing scale and complexity.

This project was developed during my **Summer Research Internship at the Wadhwani School of Data Science and Artificial Intelligence (WSAI), IIT Madras**.

The work focuses on supervised classification, unsupervised anomaly detection, feature selection, hyperparameter optimization, threshold calibration, and robustness analysis under contaminated training data.

---

## Project Overview

The project is organized into three stages:

1. **Small-scale experiments**
   - Supervised classification
   - Unsupervised anomaly detection
   - Class imbalance handling
   - Mutual Information feature selection

2. **Medium-scale experiments**
   - Supervised classification on UNSW-NB15
   - Unsupervised anomaly detection on CSE-CIC-IDS2018
   - Feature selection
   - Optuna hyperparameter optimization

3. **Large-scale experiments**
   - Deep Autoencoder training on benign network traffic
   - Independent anomaly-threshold calibration
   - Evaluation at multiple anomaly prevalence levels
   - Training contamination analysis

The evaluation pipeline separates model training, threshold calibration, model development, and final holdout evaluation to provide reliable estimates of model performance.

---

## Repository Structure

```text
Anomaly-Detection-in-Computer-Log-Using-ML-Techniques/
│
├── data/
│   ├── small/
│   │   └── embedded_system_network_security_dataset.csv
│   │
│   ├── medium/
│   │   ├── UNSW_NB15_training-set.csv
│   │   ├── UNSW_NB15_testing-set.csv
│   │   └── 02-15-2018.csv
│   │
│   └── large/
│       └── 02-15-2018.csv
│
├── notebooks/
│   ├── Small_Dataset.ipynb
│   ├── Medium_Dataset_1.ipynb
│   ├── Medium_Dataset_2.ipynb
│   ├── Large_Dataset_Benign.ipynb
│   └── Large_Dataset_Contamination.ipynb
│
├── results/
│   └── Experiment result CSV files
│
├── docs/
│   └── Project_Report.pdf
│
├── README.md
├── requirements.txt
├── .gitignore
└── .gitattributes
```

Dataset files are stored in the `data/` directory. Large files are managed using **Git Large File Storage (Git LFS)**.

---

# Experimental Pipeline

## 1. Small Dataset

The first stage uses a labelled network traffic dataset containing approximately **1,000 observations**.

The experiment compares supervised classification with unsupervised anomaly detection.

### Supervised Models

- Logistic Regression
- Decision Tree
- Random Forest
- Support Vector Machine
- XGBoost

### Unsupervised Models

- Isolation Forest
- Gaussian Mixture Model
- Hidden Markov Model
- Elliptic Envelope
- Autoencoder

### Experimental Workflow

The workflow includes:

- Stratified train-test splitting
- Feature scaling
- Class weighting
- SMOTE
- Mutual Information feature selection
- Hyperparameter tuning

### Final Supervised Result

**Support Vector Machine**

| Metric | Value |
|---|---:|
| Accuracy | 0.5850 |
| Precision | 0.1379 |
| Recall | 0.6000 |
| F1-score | 0.2243 |

### Best Unsupervised Result

**Autoencoder**

| Metric | Value |
|---|---:|
| Recall | 0.2000 |
| F1-score | 0.1404 |

The experiment demonstrates the difficulty of anomaly detection under strong class imbalance and motivates evaluation on larger intrusion-detection datasets.

---

# 2. UNSW-NB15 Supervised Classification

The second stage uses **UNSW-NB15** for supervised binary classification of normal and attack traffic.

The official training and testing partitions are preserved throughout model development.

### Dataset

| Partition | Records |
|---|---:|
| Training | 175,341 |
| Testing | 82,332 |
| Total | 257,673 |

The dataset contains both numerical and categorical network traffic features.

The following columns are excluded from the predictor set:

- `id`
- `attack_cat`

The categorical variables:

- `proto`
- `service`
- `state`

are encoded before model training.

### Models Evaluated

- Logistic Regression
- K-Nearest Neighbours
- Linear SVM
- Random Forest
- XGBoost
- LightGBM
- Voting Classifier

### Feature Selection

Mutual Information is used to rank encoded features.

The selected-feature experiments use the **top 60 features**.

### Hyperparameter Optimization

Optuna is used to optimize the strongest ensemble models.

| Model | Best Validation F1 |
|---|---:|
| LightGBM | **0.9733** |
| XGBoost | 0.9721 |
| Random Forest | 0.9714 |

### Final Official Test Result

**LightGBM**

| Metric | Value |
|---|---:|
| Accuracy | 0.8696 |
| Precision | 0.8196 |
| Recall | 0.9787 |
| F1-score | **0.8921** |

The final evaluation demonstrates strong attack detection while preserving the official benchmark test partition until the final model-selection stage.

---

# 3. CSE-CIC-IDS2018 Unsupervised Anomaly Detection

The next stage uses the **CSE-CIC-IDS2018 `02-15-2018.csv`** network traffic file.

### Raw Dataset

```text
1,048,575 rows
80 columns
```

### Original Label Distribution

| Class | Records |
|---|---:|
| Benign | 996,077 |
| DoS attacks-GoldenEye | 41,508 |
| DoS attacks-Slowloris | 10,990 |

The experiments retain:

- Benign traffic
- DoS attacks-GoldenEye

After class filtering and exact duplicate removal:

- Benign: **994,414**
- GoldenEye: **41,455**

### Data Preparation

The workflow includes:

- Exact duplicate removal
- Timestamp removal
- Infinite-value handling
- Training-based median imputation
- Feature scaling
- Correlation-based feature filtering
- Independent calibration data
- Separate development and final holdout pools

### Models Evaluated

- Multivariate Gaussian
- Isolation Forest
- One-Class SVM
- Deep Autoencoder

### Final Autoencoder Results

| Anomaly Rate | Precision | Recall | F1-score | FPR |
|---:|---:|---:|---:|---:|
| 0.2% | 0.0856 | 1.0000 | 0.1577 | 0.0214 |
| 1% | 0.3212 | 1.0000 | 0.4862 | 0.0213 |
| 5% | 0.7136 | 1.0000 | **0.8329** | 0.0211 |

The Autoencoder provides the strongest overall performance among the evaluated unsupervised models and is selected for the large-scale experiments.

---

# 4. Large-Scale Benign-Only Autoencoder

The large-scale experiment evaluates the effect of increasing the amount of representative benign training data for reconstruction-based anomaly detection.

### Training Setup

```text
Training benign flows:      300,000
Calibration benign flows:    30,000
```

Separate development and final evaluation pools are reserved before hyperparameter optimization.

### Final Holdout Sets

Each final holdout contains **30,000 records**.

| Anomaly Rate | Benign | GoldenEye | Total |
|---:|---:|---:|---:|
| 0.2% | 29,940 | 60 | 30,000 |
| 1% | 29,700 | 300 | 30,000 |
| 5% | 28,500 | 1,500 | 30,000 |

### Autoencoder Optimization

Optuna searches over:

- Encoding dimension
- Hidden layer size
- Number of hidden layers
- Learning rate
- Dropout
- L2 regularization
- Batch size
- Anomaly-threshold target FPR

### Final Results

| Anomaly Rate | Precision | Recall | F1-score | FPR |
|---:|---:|---:|---:|---:|
| 0.2% | 0.1460 | 1.0000 | 0.2548 | 0.0117 |
| 1% | 0.4622 | 1.0000 | 0.6322 | 0.0118 |
| 5% | **0.8171** | **0.9980** | **0.8986** | 0.0118 |

### Mean Final F1

```text
0.5952
```

At 5% anomaly prevalence, the optimized Autoencoder achieves a strong balance between Precision and Recall.

---

# 5. Training Contamination Analysis

The final stage studies the effect of attack samples appearing in the Autoencoder training data.

Three contamination levels are evaluated while keeping the total training size fixed at **300,000 observations**.

| Training Contamination | Benign | GoldenEye | Total |
|---:|---:|---:|---:|
| 0.2% | 299,400 | 600 | 300,000 |
| 1% | 297,000 | 3,000 | 300,000 |
| 5% | 285,000 | 15,000 | 300,000 |

### Final Comparison

| Training Condition | Mean Final F1 | 5% Test Precision | 5% Test Recall | 5% Test F1 |
|---|---:|---:|---:|---:|
| Clean benign | **0.5952** | **0.8171** | **0.9980** | **0.8986** |
| 0.2% contamination | 0.2684 | 0.4900 | 0.5380 | 0.5129 |
| 1% contamination | 0.0598 | 0.1891 | 0.0807 | 0.1131 |
| 5% contamination | 0.0067 | 0.0235 | 0.0127 | 0.0165 |

The results show that increasing training contamination substantially reduces the Autoencoder's ability to distinguish GoldenEye traffic from benign traffic.

As attack samples become more common in the training distribution, the Autoencoder increasingly learns to reconstruct them, reducing their reconstruction error and making them more difficult to identify as anomalies.

---

# Key Findings

- Supervised ensemble models provide strong performance when representative attack labels are available.
- LightGBM achieves an official UNSW-NB15 test F1-score of **0.8921**.
- The Deep Autoencoder provides the strongest unsupervised performance on the CSE-CIC-IDS2018 experiments.
- Increasing representative benign training data improves large-scale Autoencoder performance.
- The benign-only Autoencoder achieves **0.8986 F1-score** with **0.9980 Recall** on the final 5% anomaly holdout.
- Training contamination strongly degrades reconstruction-based anomaly detection.
- Precision, Recall, F1-score, FPR, and FNR provide more useful evaluation than Accuracy alone under severe class imbalance.
- Independent calibration and final holdout evaluation are important for reliable anomaly-detection experiments.

---

# Methodology

The project follows a structured experimental design that separates model development from final evaluation.

## Train-Test Separation

Train-test splitting is performed before learned preprocessing operations whenever applicable.

## Training-Only Preprocessing

Operations such as:

- Scaling
- Imputation
- Feature selection
- Oversampling

are learned from training data only.

## Official Benchmark Split

The official UNSW-NB15 training and testing partitions are preserved.

## Independent Threshold Calibration

Autoencoder anomaly thresholds are calibrated using independent benign reference data.

## Development and Final Holdout Separation

Hyperparameter optimization is performed on dedicated development sets.

Final holdout samples are not used during:

- Model fitting
- Feature selection
- Threshold calibration
- Hyperparameter optimization

## Evaluation Under Class Imbalance

The project reports:

- Accuracy
- Precision
- Recall
- F1-score
- False Positive Rate
- False Negative Rate

This provides a more complete assessment of anomaly-detection performance than Accuracy alone.

---

# Technologies Used

## Programming and Data Processing

- Python
- NumPy
- Pandas
- SciPy

## Machine Learning

- Scikit-learn
- Imbalanced-learn
- XGBoost
- LightGBM
- hmmlearn

## Deep Learning

- TensorFlow
- Keras

## Hyperparameter Optimization

- Optuna

## Visualization

- Matplotlib

## Development Tools

- VS Code
- Jupyter Notebook
- Git
- GitHub
- Git LFS

---

# Environment

The final experiments were executed locally using Python 3.12.

Core environment versions included:

```text
Python       3.12.7
NumPy        2.5.3
Pandas       3.0.6
TensorFlow   2.21.0
Optuna       5.0.0
```

---

# Installation

## 1. Install Git LFS

Large dataset files are managed using **Git Large File Storage**.

Install and initialize Git LFS before cloning:

```bash
git lfs install
```

## 2. Clone the Repository

```bash
git clone https://github.com/RajolKumar2003/Anomaly-Detection-in-Computer-Log-Using-ML-Techniques.git
cd Anomaly-Detection-in-Computer-Log-Using-ML-Techniques
```

Git LFS automatically downloads the large dataset objects during cloning.

If required, download the LFS objects manually using:

```bash
git lfs pull
```

## 3. Create a Virtual Environment

On Windows:

```powershell
py -3.12 -m venv .venv
```

Activate it:

```powershell
.\.venv\Scripts\Activate.ps1
```

## 4. Install Dependencies

Upgrade pip:

```powershell
python -m pip install --upgrade pip
```

Install the project dependencies:

```powershell
pip install -r requirements.txt
```

---

# Dataset Structure

The datasets used in the project are stored under the `data/` directory.

```text
data/
│
├── small/
│   └── embedded_system_network_security_dataset.csv
│
├── medium/
│   ├── UNSW_NB15_training-set.csv
│   ├── UNSW_NB15_testing-set.csv
│   └── 02-15-2018.csv
│
└── large/
    └── 02-15-2018.csv
```

Large CSV files are managed through **Git LFS**.

---

# Running the Project

Open the repository in VS Code:

```powershell
code .
```

Select the project `.venv` environment as the Jupyter kernel.

Run the notebooks in the following order:

```text
1. Small_Dataset.ipynb
2. Medium_Dataset_1.ipynb
3. Medium_Dataset_2.ipynb
4. Large_Dataset_Benign.ipynb
5. Large_Dataset_Contamination.ipynb
```

The notebooks are located inside:

```text
notebooks/
```

---

# Results

Experiment outputs generated during model development are stored in:

```text
results/
```

The directory contains outputs such as:

- Model comparison results
- Cross-validation results
- Feature-selection results
- Hyperparameter optimization summaries
- Final holdout metrics
- Contamination experiment results

---

# Internship Report

The complete internship report is available at:

```text
docs/Project_Report.pdf
```

The report contains the detailed methodology, experimental design, model comparisons, results, discussion, limitations, and future directions.

---

# Project Conclusions

This project presents a systematic study of network anomaly detection across supervised, unsupervised, and deep-learning settings.

The experiments progress from a small labelled dataset to large-scale CSE-CIC-IDS2018 traffic and investigate not only predictive performance but also the effects of:

- Dataset scale
- Class imbalance
- Feature selection
- Hyperparameter optimization
- Threshold calibration
- Anomaly prevalence
- Training-data contamination

Supervised ensemble methods demonstrate strong performance when representative labelled attacks are available.

For unsupervised anomaly detection, the Deep Autoencoder provides the strongest overall performance in the CSE-CIC-IDS2018 experiments.

The large-scale benign-only Autoencoder achieves an F1-score of **0.8986** and Recall of **0.9980** on the final 5% anomaly holdout.

The contamination experiments reveal an important limitation of reconstruction-based anomaly detection: when attack behaviour becomes part of the training distribution, the Autoencoder increasingly learns to reconstruct it, substantially reducing detection performance.

The results emphasize the importance of maintaining representative benign reference data, using independent threshold calibration, preserving strict train-test separation, and evaluating anomaly detectors with metrics appropriate for highly imbalanced data.

---

# Author

**Rajol Kumar**

M.Sc. Mathematics and Scientific Computing  
National Institute of Technology Warangal

Summer Research Intern  
Wadhwani School of Data Science and Artificial Intelligence  
Indian Institute of Technology Madras
