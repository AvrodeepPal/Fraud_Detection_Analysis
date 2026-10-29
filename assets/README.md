# 🖼️ Fraud Detection Analysis - Visual Artifacts

This folder contains the key visualizations generated during the end-to-end fraud detection workflow.
The figures are numbered in the same order as the [main README](../README.md), following the **natural progression of analysis**: behavioral exploration, modeling, decisioning, governance, and ensembling.

| Figure | Chart | Stage |
|---|---|---|
| 1 | Transaction amount (log scale) | Behavioral EDA |
| 2 | Activity by hour of day | Behavioral EDA |
| 3 | Precision–recall curve | Modeling & evaluation |
| 4 | Confusion matrix (threshold 0.5) | Decisioning |
| 5 | SHAP feature attribution | Explainability |
| 6 | Business cost vs threshold | Decisioning |
| 7 | Global feature importance (gain) | Governance |
| 8 | Model performance comparison | Ensembling |

---

## 1. Transaction Amount Behavior (Log Scale)

<p align="center">
  <img src="Log%20Transaction%20Amount.png" alt="Density of log transaction amount for legitimate and fraudulent transactions" width="750">
</p>

<p align="center"><em>Figure 1: Log(transaction amount) density by fraud status.</em></p>

**Insight:**
Legitimate spend clusters in sharp peaks around common price points, while fraud is spread more evenly across the range, with heavier tails at both low and high amounts. The difference is only visible on a log scale.

---

## 2. Transaction Activity by Hour of Day

<p align="center">
  <img src="Transaction%20Hour%20of%20Day.png" alt="Density of transaction activity by hour of day for legitimate and fraudulent transactions" width="750">
</p>

<p align="center"><em>Figure 2: Transaction activity by hour of day.</em></p>

**Insight:**
Fraud stays elevated through the early-morning trough (roughly 03:00–11:00) when legitimate activity drops off, indicating automated or low-monitoring attack windows.

---

## 3. Precision–Recall Curve

<p align="center">
  <img src="PR-AUC%20Curve.png" alt="Precision-recall curve for the LightGBM fraud model with PR-AUC 0.49" width="600">
</p>

<p align="center"><em>Figure 3: Precision–recall curve on the validation window (PR-AUC = 0.49).</em></p>

**Insight:**
PR-AUC is the primary metric under extreme class imbalance. Precision stays above 0.9 up to about 20% recall, then trades off steadily as the model is pushed to catch more fraud.

---

## 4. Confusion Matrix (Baseline Threshold 0.5)

<p align="center">
  <img src="Confusion%20Matrix.png" alt="Confusion matrix at threshold 0.5: 102,124 true negatives, 11,360 false positives, 1,565 false negatives, 3,059 true positives" width="520">
</p>

<p align="center"><em>Figure 4: Confusion matrix at the baseline threshold of 0.5.</em></p>

**Insight:**
The model catches 3,059 of 4,624 fraudulent transactions (~66% recall) at the cost of 11,360 legitimate transactions flagged for review. This is the fraud-capture versus customer-friction trade-off that Figure 6 optimizes.

---

## 5. SHAP Reasoning - Local Explainability

<p align="center">
  <img src="SHAP%20Reasoning.png" alt="SHAP mean absolute value bar chart led by TransactionAmt, card6 and V90" width="520">
</p>

<p align="center"><em>Figure 5: SHAP feature attribution (mean |SHAP value|).</em></p>

**Insight:**
Transaction amount, card type (`card6`) and engineered signals such as `card1_freq` and `uid_amt_mean` drive individual predictions. This enables auditability, manual-review reasoning, and regulatory transparency.

---

## 6. Business Cost vs Fraud Probability Threshold

<p align="center">
  <img src="Fraud%20Probability%20Threshold.png" alt="Total estimated business cost against fraud probability threshold, minimised at 0.62" width="750">
</p>

<p align="center"><em>Figure 6: Total estimated business cost against the decision threshold.</em></p>

**Insight:**
Cost-sensitive threshold optimization puts the cost minimum at a threshold of **0.62**, replacing the default 0.5 cut-off used in Figure 4.

---

## 7. Global Feature Importance (Information Gain)

<p align="center">
  <img src="Info%20Gain.png" alt="Top 15 global LightGBM features by total gain, led by V257, V90 and C14" width="750">
</p>

<p align="center"><em>Figure 7: Top 15 global features by LightGBM total gain.</em></p>

**Insight:**
Anonymized Vesta features (`V257`, `V90`) and counting features (`C14`, `C1`) dominate, alongside device and email-domain identity signals. This is the model-wide view that complements the per-transaction SHAP view in Figure 5.

---

## 8. Model Performance Comparison (ROC-AUC)

<p align="center">
  <img src="Performance%20Comparison.png" alt="ROC-AUC bar chart comparing LightGBM, XGBoost, CatBoost and the weighted ensemble" width="750">
</p>

<p align="center"><em>Figure 8: ROC-AUC of each base model and the weighted ensemble.</em></p>

**Insight:**
The weighted ensemble edges out LightGBM (≈0.855) and clearly beats XGBoost (≈0.82) and CatBoost (≈0.84), showing the stability gained through algorithmic diversity.
