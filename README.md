# 🛡️ Fraud Detection Modeling - IEEE-CIS Dataset

[![Python](https://img.shields.io/badge/python-3-blue.svg)](https://www.python.org/)
[![LightGBM](https://img.shields.io/badge/LightGBM-gradient%20boosting-9cf.svg)](https://lightgbm.readthedocs.io/)
[![XGBoost](https://img.shields.io/badge/XGBoost-ensemble-orange.svg)](https://xgboost.readthedocs.io/)
[![CatBoost](https://img.shields.io/badge/CatBoost-ensemble-yellow.svg)](https://catboost.ai/)
[![SHAP](https://img.shields.io/badge/SHAP-explainability-ff0d57.svg)](https://shap.readthedocs.io/)
[![Open in Colab](https://img.shields.io/badge/Open%20in-Colab-F9AB00?logo=googlecolab&logoColor=white)](https://colab.research.google.com/drive/1RPRdHKYfUiyr2KWyc6NasXXU52ZejZ-k?usp=sharing)
[![License](https://img.shields.io/badge/license-MIT-red.svg)](LICENSE)

---

## 📖 Overview

This repository presents a **production-oriented fraud detection system** built on the IEEE-CIS transaction dataset.
The project goes beyond predictive modeling to demonstrate **how real-world fraud systems are designed, evaluated, governed, and hardened against adversarial behavior**.

The complete end-to-end workflow is implemented in a single, well-structured notebook:

📌 **Colab Notebook:**
👉 [Open in Google Colab](https://colab.research.google.com/drive/1RPRdHKYfUiyr2KWyc6NasXXU52ZejZ-k?usp=sharing)

All charts are also collected, in order, in the [visual artifacts gallery](assets/README.md).

---

## 💾 Dataset Description

| Property | Value |
|---|---|
| **Source** | IEEE-CIS Fraud Detection Dataset |
| **Training records** | 590,540 transactions |
| **Test records** | 506,691 transactions |
| **Features** | 400+ transactional, identity, and anonymized behavioral features |
| **Target** | `isFraud` (binary) |
| **Class imbalance** | ~3.5% fraud vs ~96.5% legitimate |

**Key Challenge:** Fraud is **rare, adversarial, and time-dependent**, making accuracy a misleading metric.

---

## ⚙️ 1. Data Engineering & Ingestion

* Implemented **chunk-based ingestion** to safely process 600MB+ transaction files in memory-constrained environments
* Aggressively optimized memory via numeric downcasting (≈40% reduction)
* Applied **fraud-aware LEFT JOINs** to preserve identity missingness as a behavioral signal
* Verified class imbalance and identity coverage before modeling

**Outcome:** A stable, production-grade data foundation suitable for large-scale fraud analysis.

---

## 🔍 2. Behavioral EDA & Fraud Pattern Discovery

* Analyzed **transaction amounts** on a log scale to reveal abnormal fraud distributions (**Figure 1**)
* Identified **time-of-day fraud anomalies**, highlighting off-hour and automated attack patterns (**Figure 2**)
* Compared **product-level volume vs fraud rate** to uncover structurally risky flows
* Demonstrated that **missing device/identity metadata is informative**, not noise

<p align="center">
  <img src="assets/Log%20Transaction%20Amount.png" alt="Density of log transaction amount for legitimate and fraudulent transactions" width="750">
</p>

<p align="center"><em>Figure 1: Log(transaction amount) density by fraud status. Legitimate spend clusters in sharp peaks around common price points, while fraud is spread more evenly across the range, with heavier tails at both low and high amounts.</em></p>

<p align="center">
  <img src="assets/Transaction%20Hour%20of%20Day.png" alt="Density of transaction activity by hour of day for legitimate and fraudulent transactions" width="750">
</p>

<p align="center"><em>Figure 2: Transaction activity by hour of day. Fraud stays elevated through the early-morning trough (roughly 03:00–11:00) when legitimate activity drops off, pointing to automated or low-monitoring attack windows.</em></p>

**Key Insight:** Fraud differs from legitimate behavior in *patterns*, not just values.

---

## 🧬 3. Feature Engineering - UID, Velocity & Rarity Signals

* Engineered **pseudo-identities (UIDs)** to approximate real-world actors
* Built **velocity and recency features** to detect automated or bursty behavior
* Added **frequency encoding** to capture rarity in cards, devices, and addresses
* Pruned highly correlated anonymized features to reduce noise and overfitting

**Outcome:** Transformed raw logs into **behavioral fingerprints**.

---

## 🧠 4. Time-Aware Modeling & Evaluation

* Enforced **chronological train/validation splits** to prevent temporal leakage
* Trained **LightGBM** with imbalance-aware weighting
* Evaluated using **PR-AUC (primary)** and **ROC-AUC (secondary)**

**Performance (Validation Window):**

| Metric | Score |
|---|---|
| ROC-AUC | **0.85** |
| PR-AUC | **~0.49** (**Figure 3**) |
| Fraud Recall (@ baseline threshold 0.5) | **~66%** |

<p align="center">
  <img src="assets/PR-AUC%20Curve.png" alt="Precision-recall curve for the LightGBM fraud model with PR-AUC 0.49" width="600">
</p>

<p align="center"><em>Figure 3: Precision–recall curve on the validation window (PR-AUC = 0.49). Precision stays above 0.9 up to about 20% recall, then trades off steadily as the model is pushed to catch more fraud.</em></p>

---

## ⚖️ 5. Explainability, Threshold Optimization & Business Decisioning

* Used **SHAP** for transaction-level explainability and auditability (**Figure 5**)
* Converted probabilities into decisions via **cost-sensitive threshold optimization** (**Figure 6**)
* Explicitly modeled asymmetric costs:

  * False Negative ≫ False Positive
* Performed **product-level recall analysis** to detect bias and segmentation gaps

<p align="center">
  <img src="assets/Confusion%20Matrix.png" alt="Confusion matrix at threshold 0.5: 102,124 true negatives, 11,360 false positives, 1,565 false negatives, 3,059 true positives" width="520">
</p>

<p align="center"><em>Figure 4: Confusion matrix at the baseline threshold of 0.5. The model catches 3,059 of 4,624 fraudulent transactions (~66% recall) at the cost of 11,360 legitimate transactions flagged for review.</em></p>

<p align="center">
  <img src="assets/SHAP%20Reasoning.png" alt="SHAP mean absolute value bar chart led by TransactionAmt, card6 and V90" width="520">
</p>

<p align="center"><em>Figure 5: SHAP feature attribution (mean |SHAP value|). Transaction amount, card type (<code>card6</code>) and engineered signals such as <code>card1_freq</code> and <code>uid_amt_mean</code> drive individual predictions, giving reviewers a per-transaction explanation.</em></p>

<p align="center">
  <img src="assets/Fraud%20Probability%20Threshold.png" alt="Total estimated business cost against fraud probability threshold, minimised at 0.62" width="750">
</p>

<p align="center"><em>Figure 6: Total estimated business cost against the decision threshold. Cost is minimised at a threshold of 0.62, which replaces the default 0.5 cut-off used in Figure 4.</em></p>

**Outcome:** A **governable fraud decision system**, not just a classifier.

---

## 📡 6. Production Readiness, Monitoring & Governance

* Serialized the trained model for API / batch deployment
* Simulated **real-time inference** for single transactions
* Implemented **Population Stability Index (PSI)** for drift detection
* Documented **global feature importance** for audit and compliance (**Figure 7**)

<p align="center">
  <img src="assets/Info%20Gain.png" alt="Top 15 global LightGBM features by total gain, led by V257, V90 and C14" width="750">
</p>

<p align="center"><em>Figure 7: Top 15 global features by LightGBM total gain. Anonymized Vesta features (<code>V257</code>, <code>V90</code>) and counting features (<code>C14</code>, <code>C1</code>) dominate, alongside device and email-domain identity signals.</em></p>

**PSI Result:** Stable (no immediate retraining required)

---

## 🤝 7. Advanced Modeling & Ensemble Intelligence

* Trained **LightGBM, XGBoost, and CatBoost** for algorithmic diversity
* Built a **weighted soft-voting ensemble** to reduce variance (**Figure 8**)
* Demonstrated **stacking** as a conditional trust mechanism (architecture-level)
* Improved **PR-AUC to ~0.54**, strengthening rare fraud detection

<p align="center">
  <img src="assets/Performance%20Comparison.png" alt="ROC-AUC bar chart comparing LightGBM, XGBoost, CatBoost and the weighted ensemble" width="750">
</p>

<p align="center"><em>Figure 8: ROC-AUC of each base model and the weighted ensemble. The ensemble edges out LightGBM (≈0.855) and clearly beats XGBoost (≈0.82) and CatBoost (≈0.84), so the gain comes from combining models rather than any single learner.</em></p>

**Key Insight:** Ensembles improve **robustness and resilience**, not just scores.

---

## 💡 Key Takeaways

* Fraud detection is a **behavioral, adversarial, and cost-sensitive problem**
* Time-aware validation is mandatory to avoid false confidence
* Thresholds and policies matter as much as the model itself
* Explainability and monitoring are **non-negotiable** in financial ML
* Ensemble intelligence helps defend against evolving attack strategies

---

## 🏁 Final Note

This project reflects how **real-world fraud and security systems are built**: machine learning is one component of a broader **risk management, governance, and decision framework**.

It is relevant for:

* Fraud & Risk Analytics
* Trust & Safety Engineering
* SOC / Cybersecurity roles
* Applied Machine Learning in finance

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
