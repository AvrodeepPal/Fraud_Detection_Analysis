# **Fraud Detection Analysis - Visual Artifacts**

This folder contains key visualizations generated during the end-to-end fraud detection workflow.
The images are ordered to reflect the **natural progression of analysis**, from behavioral exploration to modeling, decisioning, and evaluation.

---

## **1. Transaction Amount Behavior (Log Scale)**

![Log Transaction Amount](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Log%20Transaction%20Amount.png)

**Insight:**
Reveals distinct distributional differences between legitimate and fraudulent transactions, highlighting abnormal spending patterns when viewed on a log scale.

---

## **2. Transaction Activity by Hour of Day**

![Transaction Hour of Day](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Transaction%20Hour%20of%20Day.png)

**Insight:**
Shows temporal fraud anomalies, with elevated fraud density during off-hours—indicative of automated or low-monitoring attack windows.

---

## **3. Precision–Recall Curve (Fraud Detection)**

![PR-AUC Curve](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/PR-AUC%20Curve.png)

**Insight:**
Primary evaluation metric for extreme class imbalance, illustrating the trade-off between fraud recall and false positives.

---

## **4. Confusion Matrix (Risk-Based Thresholding)**

![Confusion Matrix](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Confusion%20Matrix.png)

**Insight:**
Displays classification outcomes at the selected operating threshold, emphasizing fraud capture versus customer friction.

---

## **5. Global Feature Importance (Information Gain)**

![Feature Importance](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Info%20Gain.png)

**Insight:**
Highlights which behavioral, identity, and transaction features most strongly influence the fraud model’s decisions.

---

## **6. SHAP Reasoning — Local Explainability**

![SHAP Reasoning](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/SHAP%20Reasoning.png)

**Insight:**
Provides transaction-level explanations, enabling auditability, manual review reasoning, and regulatory transparency.

---

## **7. Business Cost vs Fraud Probability Threshold**

![Fraud Probability Threshold](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Fraud%20Probability%20Threshold.png)

**Insight:**
Visualizes cost-sensitive threshold optimization, identifying the probability cutoff that minimizes total business loss.

---

## **8. Model Performance Comparison (ROC-AUC)**

![Performance Comparison](https://github.com/AvrodeepPal/Fraud_Detection_Analysis/raw/main/assets/Performance%20Comparison.png)

**Insight:**
Compares individual models and ensemble performance, demonstrating the stability and robustness gained through algorithmic diversity.
