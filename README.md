# **Fraud Detection Modeling - IEEE-CIS Dataset**

## Overview

This repository presents a **production-oriented fraud detection system** built on the IEEE-CIS transaction dataset.
The project goes beyond predictive modeling to demonstrate **how real-world fraud systems are designed, evaluated, governed, and hardened against adversarial behavior**.

The complete end-to-end workflow is implemented in a single, well-structured notebook:

📌 **Colab Notebook:**
👉 [Open in Google Colab](https://colab.research.google.com/drive/1RPRdHKYfUiyr2KWyc6NasXXU52ZejZ-k?usp=sharing)

---

## Dataset Description

* **Source:** IEEE-CIS Fraud Detection Dataset
* **Records:**

  * Training: 590,540 transactions
  * Test: 506,691 transactions
* **Features:** 400+ transactional, identity, and anonymized behavioral features
* **Target:** `isFraud` (binary)
* **Class Imbalance:** ~3.5% fraud vs ~96.5% legitimate

**Key Challenge:** Fraud is **rare, adversarial, and time-dependent**, making accuracy a misleading metric.

---

## 1. Data Engineering & Ingestion

* Implemented **chunk-based ingestion** to safely process 600MB+ transaction files in memory-constrained environments
* Aggressively optimized memory via numeric downcasting (≈40% reduction)
* Applied **fraud-aware LEFT JOINs** to preserve identity missingness as a behavioral signal
* Verified class imbalance and identity coverage before modeling

**Outcome:** A stable, production-grade data foundation suitable for large-scale fraud analysis.

---

## 2. Behavioral EDA & Fraud Pattern Discovery

* Analyzed **transaction amounts** on a log scale to reveal abnormal fraud distributions
* Identified **time-of-day fraud anomalies**, highlighting off-hour and automated attack patterns
* Compared **product-level volume vs fraud rate** to uncover structurally risky flows
* Demonstrated that **missing device/identity metadata is informative**, not noise

**Key Insight:** Fraud differs from legitimate behavior in *patterns*, not just values.

---

## 3. Feature Engineering — UID, Velocity & Rarity Signals

* Engineered **pseudo-identities (UIDs)** to approximate real-world actors
* Built **velocity and recency features** to detect automated or bursty behavior
* Added **frequency encoding** to capture rarity in cards, devices, and addresses
* Pruned highly correlated anonymized features to reduce noise and overfitting

**Outcome:** Transformed raw logs into **behavioral fingerprints**.

---

## 4. Time-Aware Modeling & Evaluation

* Enforced **chronological train/validation splits** to prevent temporal leakage
* Trained **LightGBM** with imbalance-aware weighting
* Evaluated using **PR-AUC (primary)** and **ROC-AUC (secondary)**

**Performance (Validation Window):**

* ROC-AUC: **0.85**
* PR-AUC: **~0.49**
* Fraud Recall (@ baseline threshold): **~66%**

---

## 5. Explainability, Threshold Optimization & Business Decisioning

### Confusion Matrix (Risk-Based Thresholding)

![Confusion Matrix](assets/Confusion%20Matrix.png)

* Used **SHAP** for transaction-level explainability and auditability
* Converted probabilities into decisions via **cost-sensitive threshold optimization**
* Explicitly modeled asymmetric costs:

  * False Negative ≫ False Positive
* Performed **product-level recall analysis** to detect bias and segmentation gaps

### SHAP Reasoning

![SHAP Reasoning](assets/SHAP%20Reasoning.png)

**Outcome:** A **governable fraud decision system**, not just a classifier.

---

## 6. Production Readiness, Monitoring & Governance

* Serialized the trained model for API / batch deployment
* Simulated **real-time inference** for single transactions
* Implemented **Population Stability Index (PSI)** for drift detection
* Documented **global feature importance** for audit and compliance

**PSI Result:** Stable (no immediate retraining required)

---

## 7. Advanced Modeling & Ensemble Intelligence

### Performance Comparison

![Performance Comparison](assets/Performance%20Comparison.png)

* Trained **LightGBM, XGBoost, and CatBoost** for algorithmic diversity
* Built a **weighted soft-voting ensemble** to reduce variance
* Demonstrated **stacking** as a conditional trust mechanism (architecture-level)
* Improved **PR-AUC to ~0.54**, strengthening rare fraud detection

**Key Insight:** Ensembles improve **robustness and resilience**, not just scores.

---

## Key Takeaways

* Fraud detection is a **behavioral, adversarial, and cost-sensitive problem**
* Time-aware validation is mandatory to avoid false confidence
* Thresholds and policies matter as much as the model itself
* Explainability and monitoring are **non-negotiable** in financial ML
* Ensemble intelligence helps defend against evolving attack strategies

---

## Final Note

This project reflects how **real-world fraud and security systems are built**—where machine learning is one component of a broader **risk management, governance, and decision framework**.

It is relevant for:

* Fraud & Risk Analytics
* Trust & Safety Engineering
* SOC / Cybersecurity roles
* Applied Machine Learning in finance
