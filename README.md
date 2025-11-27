# Silent-Signals-Environmental-Socioeconomic-Drivers-in-Cancer-Malignancy-Prediction

Executive Summary
Objective: Evaluate whether adding environmental (PM2.5, smoking) and socioeconomic (income, uninsured rate, primary-care access) context to tumor features improves malignancy prediction. This report summarizes metrics, figures, and interpretation from the latest run.
Data & Methods
Patient tumor features: Wisconsin Breast Cancer Diagnostic (WBCD) via scikit-learn. Label mapped to malignant=1, benign=0. 
Environmental & socioeconomic exposures: County Health Rankings (national CSV). A population-weighted probabilistic linkage assigns each patient to a real Wisconsin county; exposures are real county values; linkage is synthetic to preserve privacy. 
Feature engineering: env_index = z(PM2.5) + z(Adult Smoking); socio_index = z(Median Income) + z(Some College) + z(PCP per 1k) − z(Unemployment) − z(Uninsured).
Models: Baseline (Logistic Regression, Random Forest) on tumor-only; Extended (Logistic Regression, Random Forest/XGBoost) on tumor + env + socio.
Evaluation: Accuracy, Precision, Recall, F1, ROC-AUC on held-out test split; SHAP/feature importance for interpretability.


