# 🛡️ Anomaly Detection in Computer Logs Using Machine Learning Techniques

*A Comparative Study Using Machine Learning and Deep Learning Models*

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Completed-brightgreen.svg)]()
[![Jupyter](https://img.shields.io/badge/Made%20with-Jupyter-orange.svg)](https://jupyter.org/)

> Summer Research Internship Project — Wadhwani School of Data Science & Artificial Intelligence (WSAI), Indian Institute of Technology Madras

---

## 📌 Overview

Signature-based intrusion detection can only catch attacks it has seen before. This project explores **machine learning– and deep learning–based anomaly detection** as a complementary approach — one that learns what *normal* network behavior looks like and flags deviations from it, making it capable of catching previously unseen (zero-day) attacks.

The study is structured as a **progressive investigation across three scales of data** — small, medium, and large — comparing supervised classifiers against unsupervised anomaly detection methods, and culminating in a deep dive into **Autoencoder-based intrusion detection** under different, realistic training conditions.

---

## 🎯 Objectives

- Study the fundamentals of ML/DL-based network anomaly detection
- Compare supervised classification vs. unsupervised anomaly detection on a small labeled dataset
- Evaluate multiple algorithms on two independent medium-scale benchmarks (supervised on **UNSW-NB15**, unsupervised on **CSE-CIC-IDS2018**)
- Identify the strongest anomaly detection model through rigorous comparative analysis
- Investigate **Deep Autoencoder** performance at scale under two training regimes: benign-only vs. contaminated training
- Analyze robustness across varying anomaly rates: **0.2%, 1%, and 5%**

---

## 🧠 Key Contributions

- End-to-end comparative pipeline spanning **3 datasets**, **11+ algorithms**, and **4 experimental configurations** (baseline / feature-selected / HPO-tuned / both)
- Feature selection via **Mutual Information** and **Variance Threshold**, and hyperparameter optimization via **Optuna**, applied consistently across every experiment for fair comparison
- A systematic, large-scale study of **Autoencoder robustness to training-data contamination** — a practical, real-world concern rarely addressed together with anomaly-rate sensitivity in prior work
- Reproducible, well-documented Jupyter notebooks for every experimental stage

---

## 📊 Datasets

| Dataset | Scale | Records | Task | Role |
|---|---|---|---|---|
| Network Traffic Anomaly Detection Dataset (Kaggle) | Small | ~1,000 | Binary classification | Baseline: supervised vs. unsupervised |
| UNSW-NB15 | Medium | 257,673 | Binary classification | Supervised classifier comparison (feature selection + Optuna) |
| CSE-CIC-IDS2018 (subset) | Medium | 1,048,575 (raw) | Unsupervised anomaly detection | Comparison of 4 anomaly detection algorithms |
| CSE-CIC-IDS2018 (large subset) | Large | 874,750 (cleaned) | Unsupervised anomaly detection | Detailed Autoencoder study — 2 training strategies × 3 anomaly rates |

---

## 🧪 Methods & Models

**Supervised classifiers** (small dataset & UNSW-NB15): Logistic Regression, K-Nearest Neighbours, Decision Tree, Linear SVM, Random Forest, XGBoost, LightGBM, Voting Classifier

**Unsupervised anomaly detection** (small dataset & CSE-CIC-IDS2018): Multivariate Gaussian Model, Isolation Forest, One-Class SVM, Gaussian Mixture Model, Hidden Markov Model, Elliptic Envelope, **Deep Autoencoder**

**Evaluation metrics**: Accuracy, Precision, Recall, F1-score, False Positive Rate, False Negative Rate — with **F1-score** as the primary criterion given class imbalance

---

## 🏆 Highlight Results

| Stage | Best Model | F1-Score |
|---|---|---|
| Small Dataset | XGBoost | 0.930 |
| UNSW-NB15 (Supervised) | XGBoost | 0.910 |
| CSE-CIC-IDS2018 (Medium, Unsupervised) | **Autoencoder** | 0.930 |
| Large-Scale — Benign-only Training | **Autoencoder** | 0.960 |
| Large-Scale — Contaminated Training | **Autoencoder** | 0.927 |

The Deep Autoencoder was consistently the strongest unsupervised model across all scales, ultimately selected for the large-scale study for its ability to model nonlinear feature relationships without requiring labeled attack data — the most realistic assumption for production intrusion detection systems.

---

## 📁 Repository Structure

```
├── Small_Dataset.ipynb                    # Supervised vs. unsupervised baseline comparison
├── Medium_Dataset_1.ipynb                 # UNSW-NB15: supervised classification study
├── Medium_Dataset_2.ipynb                 # CSE-CIC-IDS2018: unsupervised anomaly detection comparison
├── Large_Dataset_Benign.ipynb             # Autoencoder trained on benign-only traffic
├── Large_Dataset_Contamination.ipynb      # Autoencoder trained on contaminated traffic
├── Large_Dataset_SecondBestValue.ipynb    # Supplementary large-scale evaluation runs
├── Project_Report.pdf                     # Full internship report (methodology, results, discussion)
└── README.md
```

---

## ⚙️ Getting Started

### Prerequisites
```bash
Python 3.9+
Jupyter Notebook / JupyterLab
```

### Installation
```bash
git clone https://github.com/RajolKumar2003/Anomaly-Detection-in-Computer-Log-Using-ML-Techniques.git
cd Anomaly-Detection-in-Computer-Log-Using-ML-Techniques
pip install -r requirements.txt
```

### Core Dependencies
```
numpy
pandas
scikit-learn
xgboost
lightgbm
tensorflow / keras
optuna
imbalanced-learn
matplotlib
seaborn
```

### Usage
Open any notebook in Jupyter and run cells sequentially:
```bash
jupyter notebook Small_Dataset.ipynb
```

> **Note:** The medium and large datasets (UNSW-NB15 and CSE-CIC-IDS2018) are large public benchmark files not included in this repository. Download them from their official sources below and place them in a `data/` directory before running the corresponding notebooks.

---

## 🔗 Dataset Sources

- **Small Dataset**: [Network Traffic Anomaly Detection Dataset (Kaggle)](https://www.kaggle.com/datasets)
- **UNSW-NB15**: [UNSW Canberra Research](https://research.unsw.edu.au/projects/unsw-nb15-dataset)
- **CSE-CIC-IDS2018**: [Canadian Institute for Cybersecurity](https://www.unb.ca/cic/datasets/ids-2018.html)

---

## 📄 Full Report

The complete methodology, exploratory analysis, hyperparameter search spaces, and detailed results tables are documented in [`Project_Report.pdf`](./Project_Report.pdf).

---

## 🚀 Future Work

- Extend to additional datasets (CICIDS2017, TON_IoT)
- Investigate multiple attack categories jointly
- Explore Variational Autoencoders, Transformer-based Autoencoders, and Graph Neural Networks
- Adaptive, self-calibrating anomaly thresholds
- Online/incremental learning for evolving traffic patterns
- Explainable AI (XAI) for anomaly detection interpretability
- Federated anomaly detection across organizations

---

## 👤 Author

**Rajol Kumar**
Roll No.: 25MAC2R19
Summer Research Internship, WSAI — IIT Madras

*Under the guidance of Dr. Sri Vallabha Deevi, Adjunct Faculty, Dept. of Data Science & AI, IIT Madras*

---

## 📜 License

This project is available under the [MIT License](LICENSE).

---

⭐ If you find this project useful, consider giving it a star!
