# PCOS Prediction
## Multi-Model Clinical Classification

Can clinical and hormonal measurements reliably predict a PCOS diagnosis? This project trains and compares seven classification models on a dataset of 541 patient records, evaluating each on both accuracy and recall to identify the best algorithm for a medical screening context where minimizing missed diagnoses is the priority.

---

## Overview

Polycystic Ovary Syndrome affects an estimated 8 to 13 percent of women of reproductive age and is frequently underdiagnosed due to its heterogeneous clinical presentation. Early detection matters: PCOS is associated with infertility, insulin resistance, metabolic syndrome, and elevated cardiovascular risk.

This project frames PCOS detection as a binary classification problem and benchmarks seven algorithms on the same dataset and train/test split, using recall as the primary evaluation metric. In a screening context, a false negative (predicting no PCOS when PCOS is present) delays treatment and carries higher clinical risk than a false positive, which leads to further confirmatory testing.

---

## Dataset

The dataset contains clinical records from 541 patients with 44 features spanning hormonal markers, physical measurements, ultrasound findings, and reported symptoms.

| Feature Group | Examples |
|---|---|
| Hormonal markers | FSH, LH, AMH, TSH, Prolactin, Estradiol |
| Physical measurements | BMI, waist circumference, hip circumference |
| Ultrasound findings | Follicle count (left/right), ovarian volume |
| Reported symptoms | Skin darkening, hair loss, weight gain, acne |
| Cycle information | Cycle length (days), cycle regularity |
| **Target** | **PCOS (Y/N): 1 = positive diagnosis, 0 = negative** |

---

## Workflow

### Part 1: Data Cleaning
Three columns are dropped before imputation: FSH/LH ratio (redundant with individual measurements), Waist:Hip Ratio (redundant with waist and hip columns), and an empty Excel artifact column. Patient and record identifier columns are removed. One record with a non-numeric AMH entry is filtered as a data entry error. After cleaning, 539 records with 38 features remain.

### Part 2: Exploratory Data Analysis
Pearson correlations with the target variable identify the strongest clinical signals. Follicle count distributions and scatter plots confirm that elevated antral follicle counts on both ovaries are the most discriminating feature, consistent with clinical diagnostic criteria. Cycle length vs. age scatter plots show that longer, irregular cycles cluster strongly with positive diagnoses across all age groups.

### Part 3: Feature Scaling and Train/Test Split
Clinical features span very different numeric ranges (hormone levels in mIU/mL and ng/mL, follicle counts as small integers, BMI as a continuous ratio). StandardScaler normalizes all features to zero mean and unit variance. The scaler is fit only on training data to prevent leakage. An 80/20 split is used consistently across all models.

### Part 4: Multi-Model Evaluation
Seven classifiers are trained and evaluated on the same scaled data. Both accuracy and recall are computed for each. A shared `classification_report` is printed per model showing per-class precision, recall, and F1.

Models evaluated:
- Logistic Regression
- K-Nearest Neighbors
- Support Vector Classifier
- Decision Tree
- Random Forest
- Gradient Boosting
- AdaBoost

### Part 5: Results Comparison
All seven models are ranked in a results table by recall score. Side-by-side bar charts compare accuracy and recall across models. Confusion matrices for the top three models by recall score break down true/false positives and negatives, giving a direct view of where each model makes errors.

---

## Results

| Model | Test Accuracy | Recall Score |
|---|---|---|
| Logistic Regression | ~92.6% | ~91.7% |
| SVC | ~88.0% | ~77.8% |
| KNN Classifier | ~86.1% | ~77.8% |
| Random Forest | varies | varies |
| Gradient Boosting | varies | varies |
| AdaBoost | varies | varies |
| Decision Tree | varies | varies |

Logistic Regression achieves the highest recall on this dataset, making it the preferred model for clinical screening where catching true positive cases is the priority.

---

## Key Concepts

**Recall as the Clinical Priority:** Recall measures the proportion of actual positive cases the model correctly identifies. In PCOS screening, a low-recall model misses diagnoses and delays treatment. Accuracy alone is insufficient as an evaluation criterion.

**Feature Scaling Before Distance-Based Models:** KNN and SVC compute distances between data points. Without scaling, features with large numeric ranges dominate the distance calculation regardless of their clinical relevance. StandardScaler ensures all features contribute equally.

**Seven-Model Benchmark:** Evaluating multiple algorithms on a fixed split provides an empirical answer to which model family fits this data best, rather than relying on general assumptions about algorithm performance.

**Clinical Interpretability:** Logistic Regression coefficients can be examined to understand which features drive the prediction, supporting clinician review and regulatory transparency in medical AI applications.

---

## Stack

- Python 3
- Pandas, NumPy
- Matplotlib, Seaborn
- scikit-learn (LogisticRegression, KNeighborsClassifier, SVC, DecisionTreeClassifier, RandomForestClassifier, GradientBoostingClassifier, AdaBoostClassifier, StandardScaler, ConfusionMatrixDisplay)

---

## File Structure

```
pcos-classification/
├── pcos_classification.ipynb                  # Main project notebook
├── PCOS_data_without_infertility.xlsx         # Clinical dataset
└── README.md
```

---

## How to Run

1. Clone the repository
2. Install dependencies: `pip install pandas numpy matplotlib seaborn scikit-learn openpyxl`
3. Open `pcos_classification.ipynb` in Jupyter and run all cells
