# Silent-Signals-Environmental-Socioeconomic-Drivers-in-Cancer-Malignancy-Prediction

Executive Summary
Objective: Evaluate whether adding environmental (PM2.5, smoking) and socioeconomic (income, uninsured rate, primary-care access) context to tumor features improves malignancy prediction. This report summarizes metrics, figures, and interpretation from the latest run.
Data & Methods
Patient tumor features: Wisconsin Breast Cancer Diagnostic (WBCD) via scikit-learn. Label mapped to malignant=1, benign=0. 
Environmental & socioeconomic exposures: County Health Rankings (national CSV). A population-weighted probabilistic linkage assigns each patient to a real Wisconsin county; exposures are real county values; linkage is synthetic to preserve privacy. 
Feature engineering: env_index = z(PM2.5) + z(Adult Smoking); socio_index = z(Median Income) + z(Some College) + z(PCP per 1k) − z(Unemployment) − z(Uninsured).
Models: Baseline (Logistic Regression, Random Forest) on tumor-only; Extended (Logistic Regression, Random Forest/XGBoost) on tumor + env + socio.
Evaluation: Accuracy, Precision, Recall, F1, ROC-AUC on held-out test split; SHAP/feature importance for interpretability.


Evaluation Metrics
Model	Accuracy	Precision	Recall	F1	ROC-AUC
Baseline-logreg	0.700	0.700	0.700	0.700	0.750
Baseline-rf	0.658	0.646	0.700	0.672	0.718
Extended-logreg	0.850	0.809	0.917	0.859	0.923
Extended-rf	0.800	0.765	0.867	0.812	0.895
Figures
ROC- baseline (tumor-only):
 




ROC -extended (tumor + env + socio):
 










Random Forest importances  extended features:
 
Interpretation
Across all metrics, extended models outperform baseline tumor-only models. The strongest ROC-AUC among extended configurations indicates that contextual factors (air quality, smoking prevalence, access to care, and income/education proxies) add discriminative signal for malignancy prediction. Feature importances commonly highlight higher uninsured rates and PM2.5 as risk-contributing, while higher median income and PCP access tend to be protective.
Robustness & Sensitivity
Re-run with multiple seeds and alternative model families (e.g., Gradient Boosting, XGBoost). Perform ablations (Tumor-only vs Tumor+Env vs Tumor+Socio vs Tumor+Env+Socio) and bootstrap/DeLong tests for AUC gaps.
Limitations
The county linkage is synthetic for privacy (exposures are real; patient-to-county mapping is stochastic). Temporal alignment between CHR year and patient diagnosis may vary; area-level features can proxy unobserved factors.
Ethics & Governance
No PHI used. Models are for research only and must not be used for clinical decisions without rigorous external validation, fairness analysis, and stakeholder oversight.
Reproducibility Notes
The code writes machine-readable metrics to outputs/metrics_summary.json and figures to outputs/figures/. Set a fixed seed (default 7) for deterministic splits and document Python/package versions.












Appendix: Screenshots and Step-by-Step Visual Explanation
This appendix provides concrete screenshots from the Jupyter notebook so that a reader can see exactly what the main plots look like and how to explain them during a presentation or viva.
Figure A1. ROC curves – Baseline tumor-only models
This figure shows ROC curves for Logistic Regression and Random Forest trained only on tumor features. Each curve plots the trade-off between True Positive Rate and False Positive Rate for different classification thresholds. The legend displays the AUC (Area Under the Curve) for each model – higher AUC means better discrimination between malignant and benign cases.
 
How to explain: “In the baseline setup, the models are using only tumor measurements. The ROC curves are above the diagonal, which means they are better than random guessing, but the AUC values are moderate compared to the extended models.”
Figure A2. ROC curves – Extended tumor + environment + socioeconomic models
Here, the models use the extended feature set that includes tumor features plus environmental and socioeconomic context (PM2.5, smoking, income, insurance, primary-care access, and the composite indices env_index and socio_index).
 
How to explain: “Compared to the baseline ROC curves, these extended curves lie closer to the top-left corner, and their AUC values are higher. This visually confirms that adding context improves the model’s ability to separate malignant from benign cases.”
Figure A3. Random Forest feature importances – Extended model
This bar chart shows the top 20 features ranked by their importance in the extended Random Forest model. Features with larger bars contribute more to the model’s split decisions.
 
How to explain: “We can see a mix of tumor features (e.g., radius_mean, concavity_mean) and contextual features (e.g., pm25, uninsured_pct, median_income, env_index, socio_index). High values of pm25 and uninsured_pct tend to be associated with higher malignancy risk, while higher median_income and pcp_per_1k are more protective.”







Abbreviations and Key Terms
This appendix defines all abbreviations, indices, and key variables used in the notebook and in this report. A reader can use this section as a quick reference while following the figures and the Jupyter notebook.
Abbreviation / Term	Meaning
WBCD	Wisconsin Breast Cancer Diagnostic dataset (source of tumor features).
PM2.5	Fine particulate matter with aerodynamic diameter ≤ 2.5 microns; measure of air pollution.
PCP	Primary-care physician (doctor providing first-contact and ongoing care).
env_index	Composite environmental exposure index: z(PM2.5) + z(adult_smoking_pct).
socio_index	Composite socioeconomic/access index: z(median_income) + z(pcp_per_1k) − z(uninsured_pct).
LR	Logistic Regression classifier used as a baseline model.
RF	Random Forest classifier used as a tree-based ensemble model.
XGB	XGBoost gradient-boosted tree classifier (optional, if installed).
ROC	Receiver Operating Characteristic curve; plots True Positive Rate vs False Positive Rate.
AUC	Area Under the ROC Curve; summary measure of discrimination (1.0 = perfect, 0.5 = random).
ACC	Accuracy; proportion of correctly classified cases.
PREC	Precision; proportion of predicted positives that are truly positive.
REC	Recall (Sensitivity); proportion of actual positives that are correctly identified.
F1	Harmonic mean of precision and recall; balances the two metrics.
PHI	Protected Health Information; not used here because data are synthetic/public-style only.
X_tumor	Feature matrix containing only tumor (clinical/pathology) variables.
X_env	Feature matrix containing only environmental variables and env_index.
X_socio	Feature matrix containing only socioeconomic/access variables and socio_index.
X_ext	Extended feature matrix containing tumor + environmental + socioeconomic variables + indices.
Key Columns and Variables (Datasets)
The table below maps the most important columns in the two main CSV files to their intuitive meanings. This helps the reader connect the code, the visualizations, and the narrative in the report.
Column Name	Dataset	Description
patient_id	tumor_patients.csv	Synthetic identifier for each patient record.
county	tumor_patients.csv / env_counties.csv	County name or code used to link patients with environmental data.
malignant	tumor_patients.csv	Binary label: 1 = malignant tumor, 0 = benign tumor.
radius_mean	tumor_patients.csv	Average tumor radius (tumor size measure).
texture_mean	tumor_patients.csv	Average variation in gray-level intensity across the tumor area.
smoothness_mean	tumor_patients.csv	Measure of how smooth or irregular the tumor boundary is.
perimeter_mean	tumor_patients.csv	Mean perimeter of the tumor region.
area_mean	tumor_patients.csv	Mean area of the tumor region (size).
compactness_mean	tumor_patients.csv	Compactness of the tumor shape; combines perimeter and area.
concavity_mean	tumor_patients.csv	Severity of concave portions of the tumor contour.
concave_points_mean	tumor_patients.csv	Number of concave points on the tumor contour.
symmetry_mean	tumor_patients.csv	How symmetric the tumor shape is.
fractal_dimension_mean	tumor_patients.csv	Roughness of the tumor boundary measured via fractal dimension.
pm25	env_counties.csv	Fine particulate matter concentration in the county (µg/m³).
adult_smoking_pct	env_counties.csv	Percentage of adults in the county who smoke cigarettes.
median_income	env_counties.csv	Median household income in the county.
uninsured_pct	env_counties.csv	Percentage of adults in the county without health insurance.
pcp_per_1k	env_counties.csv	Number of primary-care physicians per 1,000 population.
env_index	merged df	Derived index summarizing pollution and smoking exposure.
socio_index	merged df	Derived index summarizing income, insurance, and access to care.

