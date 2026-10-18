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
   - Feature selection

2. **Medium-scale experiments**
   - Supervised learning on UNSW-NB15
   - Unsupervised anomaly detection on CSE-CIC-IDS2018
   - Mutual Information and correlation-based feature selection
   - Optuna hyperparameter optimization

3. **Large-scale experiments**
   - Deep Autoencoder training on benign network traffic
   - Threshold calibration using independent benign data
   - Final evaluation at multiple anomaly rates
   - Training contamination analysis

The evaluation pipeline uses separate training, calibration, development, and final holdout data to provide reliable estimates of model performance.

---

## Repository Structure

```text
Computer-Log-Anomaly-Detection/
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
└── .gitignore
```

Raw datasets are excluded from the repository because of their size.

---

# Experimental Pipeline

## 1. Small Dataset

The first stage uses a labelled network traffic dataset containing approximately 1,000 observations.

The experiment compares supervised classification and unsupervised anomaly detection.

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

The small-scale experiment highlights the difficulty of anomaly detection under strong class imbalance and motivates evaluation on larger intrusion-detection datasets.

---

# 2. UNSW-NB15 Supervised Classification

UNSW-NB15 is used for supervised binary classification of normal and attack traffic.

The official training and testing partitions are preserved.

### Dataset

| Partition | Records |
|---|---:|
| Training | 175,341 |
| Testing | 82,332 |

The dataset contains numerical and categorical network traffic features.

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

The selected-feature experiments use the top 60 features.

### Hyperparameter Optimization

Optuna is used to optimize the strongest ensemble models.

| Model | Best Validation F1 |
|---|---:|
| LightGBM | 0.9733 |
| XGBoost | 0.9721 |
| Random Forest | 0.9714 |

### Final Official Test Result

**LightGBM**

| Metric | Value |
|---|---:|
| Accuracy | 0.8696 |
| Precision | 0.8196 |
| Recall | 0.9787 |
| F1-score | 0.8921 |

The final test result demonstrates strong attack detection while preserving the official benchmark test partition for final evaluation.

---

# 3. CSE-CIC-IDS2018 Unsupervised Anomaly Detection

The second medium-scale stage uses the CSE-CIC-IDS2018 `02-15-2018.csv` file.

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

After filtering and exact duplicate removal:

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
| 5% | 0.7136 | 1.0000 | 0.8329 | 0.0211 |

The Autoencoder provides the strongest overall performance among the unsupervised methods and is selected for the large-scale experiments.

---

# 4. Large-Scale Benign-Only Autoencoder

The large-scale experiment evaluates whether additional benign training data improves reconstruction-based anomaly detection.

### Training Setup

```text
Training benign flows:      300,000
Calibration benign flows:    30,000
```

Separate development and final evaluation pools are reserved before hyperparameter optimization.

### Final Holdout Sets

Each final holdout contains 30,000 records.

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
| 5% | 0.8171 | 0.9980 | 0.8986 | 0.0118 |

### Mean Final F1

```text
0.5952
```

At 5% anomaly prevalence, the model achieves a strong balance between Precision and Recall.

---

# 5. Training Contamination Analysis

The final stage studies the effect of GoldenEye attack samples appearing in the Autoencoder training data.

Three training conditions are evaluated while keeping the total training size fixed at 300,000 records.

| Training Contamination | Benign | GoldenEye | Total |
|---:|---:|---:|---:|
| 0.2% | 299,400 | 600 | 300,000 |
| 1% | 297,000 | 3,000 | 300,000 |
| 5% | 285,000 | 15,000 | 300,000 |

### Final Comparison

| Training Condition | Mean Final F1 | 5% Test Precision | 5% Test Recall | 5% Test F1 |
|---|---:|---:|---:|---:|
| Clean benign | 0.5952 | 0.8171 | 0.9980 | 0.8986 |
| 0.2% contamination | 0.2684 | 0.4900 | 0.5380 | 0.5129 |
| 1% contamination | 0.0598 | 0.1891 | 0.0807 | 0.1131 |
| 5% contamination | 0.0067 | 0.0235 | 0.0127 | 0.0165 |

The results show that increasing training contamination substantially reduces the Autoencoder's ability to distinguish GoldenEye traffic from benign traffic.

As attack samples become more frequent during training, their reconstruction behaviour becomes increasingly similar to the learned training distribution.

---

# Key Findings

- Supervised ensemble models perform strongly when representative attack labels are available.
- LightGBM achieves an official UNSW-NB15 test F1-score of **0.8921**.
- The Deep Autoencoder is the strongest unsupervised model evaluated on CSE-CIC-IDS2018.
- Increasing the amount of representative benign training data improves large-scale Autoencoder performance.
- The benign-only Autoencoder achieves **0.8986 F1-score** with **0.9980 Recall** on the final 5% anomaly holdout.
- Training contamination has a strong negative effect on reconstruction-based anomaly detection.
- Precision, Recall, F1-score, FPR, and FNR are more informative than Accuracy alone for highly imbalanced intrusion-detection problems.
- Independent calibration and final holdout evaluation are important for reliable anomaly-detection experiments.

---

# Methodology

The project follows a structured evaluation pipeline designed to separate model development from final testing.

### Train-Test Separation

Train-test splitting is performed before learned preprocessing operations whenever applicable.

### Training-Only Preprocessing

Operations such as:

- Scaling
- Imputation
- Feature selection
- Oversampling

are learned using training data only.

### Official Benchmark Split

The official UNSW-NB15 training and testing partitions are preserved.

### Threshold Calibration

Autoencoder anomaly thresholds are calibrated using an independent benign reference set.

### Development and Final Holdout Separation

Hyperparameter optimization is performed on dedicated development sets.

Final holdout samples are not used during:

- Model fitting
- Feature selection
- Threshold calibration
- Hyperparameter optimization

### Evaluation Under Class Imbalance

The project reports:

- Accuracy
- Precision
- Recall
- F1-score
- False Positive Rate
- False Negative Rate

This provides a more complete assessment than Accuracy alone.

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

Clone the repository:

```bash
git clone <repository-url>
cd Computer-Log-Anomaly-Detection
```

Create a virtual environment:

```powershell
py -3.12 -m venv .venv
```

Activate the environment on Windows:

```powershell
.\\.venv\\Scripts\\Activate.ps1
```

Upgrade pip:

```powershell
python -m pip install --upgrade pip
```

Install dependencies:

```powershell
pip install -r requirements.txt
```

---

# Dataset Setup

Raw datasets are not included in the repository because of their size.

Create the following local directory structure:

```text
data/
├── small/
│   └── embedded_system_network_security_dataset.csv
│
└── medium/
    ├── UNSW_NB15_training-set.csv
    ├── UNSW_NB15_testing-set.csv
    └── 02-15-2018.csv
```

The notebooks expect the datasets at these locations.

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

---

# Results

Experiment outputs generated by the notebooks are stored in:

```text
results/
```

The results directory contains model-comparison tables, final holdout metrics, feature-selection outputs, and hyperparameter summaries generated during the experiments.

---

# Internship Report

The complete internship report is available at:

```text
docs/Project_Report.pdf
```

The report contains the detailed methodology, experimental design, results, analysis, limitations, and future directions.

---

# Project Conclusions

The experiments show that supervised machine learning is highly effective when representative attack labels are available, while Deep Autoencoders provide a strong alternative for settings where anomaly detection must rely primarily on normal traffic.

The large-scale benign-only Autoencoder demonstrates strong GoldenEye attack detection, particularly at the 5% anomaly prevalence level.

The contamination study also reveals an important limitation of reconstruction-based anomaly detection: when malicious traffic becomes part of the training distribution, the Autoencoder increasingly learns to reconstruct attack behaviour, reducing its ability to identify that behaviour as anomalous.

This emphasizes the importance of:

- Representative benign training data
- Independent threshold calibration
- Careful train-test separation
- Robust evaluation under class imbalance
- Monitoring the quality of training data

---

# Author

**Rajol Kumar**

M.Sc. Mathematics and Scientific Computing  
National Institute of Technology Warangal

Summer Research Intern  
Wadhwani School of Data Science and Artificial Intelligence  
Indian Institute of Technology Madras
"""
