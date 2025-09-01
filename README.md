# Student Performance — Early Risk Detection (UCI Dataset)

End-to-end project to (A) clean & transform student records, (B/C/D) explore & visualize drivers of performance, (E) segment behavior with K-Means, and (F/G) predict pass/fail risk with multiple ML models under two leakage variants (with/without G1/G2), then (H) convert insights into recommendations and ethical guidance.

> **Datasets**: UCI ML Repository — “Student Performance” (CC BY 4.0).  
> **Subjects**: Math (`student-mat.csv`), Portuguese (`student-por.csv`) — identical schema.

---

## Table of Contents

- [Project Structure](#project-structure)
- [Setup](#setup)
- [How to Run (Step-by-Step)](#how-to-run-step-by-step)
  - [A) Data Preparation & Transformation](#01_data_preparation.ipynb)
  - [B/C/D) EDA & Visualization](#02_eda_visualization.ipynb)
  - [E) K-Means Segmentation](#03_kmeans_segmentation.ipynb)
  - [F/G) Classification & Evaluation](#04_classification_evaluation.ipynb)
  - [H) Storytelling & Ethics](#05_storytelling_ethics.ipynb)
- [Artifacts & Outputs](#artifacts--outputs)
- [Configuration Details](#configuration-details)
- [Reproducibility & Versions](#reproducibility--versions)
- [Troubleshooting](#troubleshooting)
- [Dataset & License](#dataset--license)

---

## Project Structure

FinalProject/
├─ data/ # raw CSVs from UCI (student-mat.csv, student-por.csv)
├─ artifacts/
│ ├─ figures/ # saved plots (EDA, heatmaps, KMeans, ROC, etc.)
│ ├─ results/ # CSVs with metrics, tables, exports (e.g., tuned_all_binary.csv)
│ ├─ models/ # saved trained models (optional)
│ ├─ kmeans/ # clustering artifacts (centroids, sizes, profiles)
│ ├─ processed/ # processed datasets saved from Notebook A
│ └─ active_dataset.json # central config used by all notebooks (paths + selection)
├─ notebooks/
│ ├─ 01_data_preparation.ipynb
│ ├─ 02_eda_visualization.ipynb
│ ├─ 03_kmeans_segmentation.ipynb
│ ├─ 04_classification_evaluation.ipynb
│ └─ 05_storytelling_ethics.ipynb
├─ Report/
│ └─ Final_Report.pdf
├─ Slides/
│ └─ Slides.pptx
├─ requirements.txt
└─ README.md

---

## How to Run (Step-by-Step)

Run notebooks in order. Each notebook reads/writes from artifacts/.
Figures/results are saved automatically; metrics and configs persist between notebooks.

# A) Data Preparation & Transformation

Open:
notebooks/01_data_preparation.ipynb
Steps:

Dataset selection (dropdown): choose student-mat, student-por, or merged.

The notebook:
Validates schema & types, checks duplicates/missing.
Computes outliers (IQR) and lets you optionally cap/handle them.
Encodes categoricals with One-Hot (drop first) and scales numerics for ML/KMeans.
Feature engineering: e.g., attendance proxy from absences, avg_grade, pass (G3≥10).

Saves:
Cleaned & processed dataset to artifacts/processed/...csv
artifacts/active_dataset.json with keys:
active_dataset (your selection)
processed_path (path to processed CSV)
optionally clean_path (if you saved a clean-only version)
Important: All later notebooks (02–04) read the active dataset and path from artifacts/active_dataset.json.

# B/C/D) EDA & Visualization

Open:
notebooks/02_eda_visualization.ipynb

What it does:
Loads artifacts/active_dataset.json and processed_path.
Descriptive stats, correlation analysis (heatmap & top features for G3).
Group comparisons (e.g., studytime, failures, schoolsup_yes vs G3).

Saves plots to artifacts/figures/ and tables to artifacts/results/.

# E) K-Means Segmentation

Open:
notebooks/03_kmeans_segmentation.ipynb

What it does:
Loads processed dataset from config.
Selects behavior features (e.g., studytime, absences, goout, freetime, famsup_yes, schoolsup_yes).
Scales, runs KMeans for several k, computes elbow and silhouette.
Pick k based on curves; fit final model.
Profiles clusters (sizes, centroids in scaled & original units, average G3/pass rate).

Saves:
Figures: artifacts/figures/kmeans_elbow.png, .../kmeans_silhouette.png, .../kmeans_clusters_pca.png
Tables: artifacts/kmeans/cluster_sizes.csv, .../cluster_centroids_original.csv, etc.
(Optional) labeled dataset: artifacts/processed/student_clusters.csv

# F/G) Classification & Evaluation

Open:
notebooks/04_classification_evaluation.ipynb

What it does:
Loads processed dataset from config.
Target: default binary pass = (G3 ≥ 10).
Two variants (to manage leakage):
with_G1G2: includes G1/G2 when predicting G3/Pass.
no_G1G2: excludes G1/G2 for early-warning realism.
Models: Logistic Regression, Decision Tree/Random Forest, SVM.
Hold-out split + 5-fold CV, basic hyper-parameter tuning.
Metrics: Accuracy, Precision, Recall, F1, ROC-AUC (binary).

Saves:
Results table: artifacts/results/tuned_all_binary.csv
(Optional) feature importance/coefs, confusion matrices, ROC curves → artifacts/figures/ & artifacts/results/.

# H) Storytelling & Ethics

Use the saved figures & tables to craft narrative:
Drivers of performance, early signals, at-risk profiles.

Recommendations: attendance interventions, tutoring, parental engagement, study-skills workshops, dashboards.

Ethics: privacy, fairness audits, explainability, avoiding stigmatization.

Artifacts & Outputs
Figures → artifacts/figures/
Heatmaps, histograms, box/violin, scatter, elbow/silhouette, PCA clusters, ROC, confusion matrices.

Results → artifacts/results/
tuned_all_binary.csv (final comparison table for F/G)
Any descriptive stats, group means, top correlations.

KMeans → artifacts/kmeans/
Cluster sizes/centroids (scaled & original units), profiling tables.

Processed Data → artifacts/processed/
Final processed dataset (the one referenced by processed_path in config).

Config → artifacts/active_dataset.json

The single source of truth for which dataset is active and where the processed CSV lives.

Configuration Details

Example artifacts/active_dataset.json:

{
"active_dataset": "merged",
"processed_path": "../artifacts/processed/merged_processed.csv",
"clean_path": "../artifacts/processed/merged_clean.csv"
}

Dataset & License:
Source: UCI ML Repository — Student Performance (649 rows across two subjects; 30+ features).
License: CC BY 4.0 for the dataset.

Quick Start

# 1) Install

pip install -r requirements.txt

# 2) Launch Jupyter

jupyter lab # or: jupyter notebook # or: VS_code

# 3) Run notebooks in order

# 01 → choose dataset & save processed

# 02 → EDA & figures

# 03 → KMeans segmentation

# 04 → Classification & results

# Outputs will appear under artifacts/
