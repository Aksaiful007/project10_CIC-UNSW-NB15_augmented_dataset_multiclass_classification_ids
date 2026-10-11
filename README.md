Project 10 — Imbalance-Aware Multiclass Network Intrusion Detection
> **Research focus:** multiclass network-intrusion classification under severe class imbalance using machine learning.
This project explores how machine-learning models classify network-flow records into benign traffic and nine intrusion categories using the CIC-UNSW-NB15 augmented dataset. The workflow includes exploratory data analysis (EDA), data-quality checks, leakage-aware data splitting, model comparison and tuning, and a final held-out test evaluation.
The repository is organized to preserve the research workflow while making routine reproduction lighter: the full experimental notebook documents exploration and model selection, while the compact notebook runs the selected model workflow without repeating every experiment.
---
Table of contents
Research objective
Dataset and target classes
Workflow
Models explored
Final results
Interpretation and limitations
Repository structure
Reproduce the final workflow
Outputs
Data provenance note
Responsible use
Research objective
The main objective is to build and evaluate a multiclass network-intrusion classifier while accounting for the dataset's severe class imbalance.
Specific goals:
Inspect feature quality, duplicate records, class distribution, and feature relationships.
Compare candidate models using metrics that reflect performance on minority classes.
Reduce train/test contamination by grouping identical feature vectors during splitting.
Select model settings using development data rather than the final test set.
Evaluate the selected model once on a held-out test partition.
Preserve a repeatable final workflow and export predictions and metrics for further analysis.
This is an experimental research project, not a production-ready intrusion prevention system.
Dataset and target classes
The modeling workflow uses 76 numerical network-flow features and a target with 10 classes.
Label	Class
0	Benign
1	Analysis
2	Backdoor
3	DoS
4	Exploits
5	Fuzzers
6	Generic
7	Reconnaissance
8	Shellcode
9	Worms
The class distribution is highly imbalanced. In the EDA record, Benign accounts for approximately 80% of the observations; Exploits and Fuzzers are the next largest groups, while Analysis, Backdoor, and Worms are rare. This is why the project reports Macro Precision, Macro Recall, and Macro F1 alongside Accuracy and Weighted F1.
Dataset availability: the raw/merged dataset is not included in this repository by default. Obtain it from the appropriate dataset source and follow its terms and license. Do not commit large or restricted datasets to GitHub.
Workflow
1. Exploratory data analysis
The EDA notebook investigates:
Dataset dimensions, data types, and missing values.
Target distribution and class imbalance.
Exact duplicate rows and repeated feature vectors.
Mutual Information and feature relationships/correlation.
Potentially redundant predictors and data-quality concerns.
2. Data preparation and leakage safeguards
The modeling-stage notes record a dataset of 306,173 rows × 77 columns after exact duplicate removal, comprising 76 predictors and one target. They also record 141,742 exact duplicate rows removed and 571 identical feature vectors with conflicting labels retained rather than arbitrarily relabeled.
The modeling workflow uses a seeded, group-aware split based on hashes of feature vectors. The purpose is to keep identical feature vectors together and check that no feature groups overlap between the training and test partitions. Preprocessing is kept inside the model pipeline so imputation is learned from training data.
A 120,000-record development subset was used in the completed experiments for model comparison and tuning. The held-out test partition was reserved for final evaluation.
3. Model experiments and selection
The full experimental workflow compared baseline and advanced approaches, including Logistic Regression, Decision Tree, Random Forest, class-weighted Random Forest, HistGradientBoosting, XGBoost, and class-weighted XGBoost. Grouped cross-validation and randomized hyperparameter search were used during experimentation.
The Tuned Balanced Random Forest was selected as the final candidate based on development-validation Macro F1. Weighted XGBoost was also considered because it produced higher validation Macro Recall, but its validation Macro F1 was lower.
The compact notebook retains the selected-model workflow and skips completed experiments and tuning searches during routine reproduction.
Models explored
The completed experiments included:
Logistic Regression
Decision Tree
Random Forest
Balanced/class-weighted Random Forest
HistGradientBoosting
XGBoost
Class-weighted XGBoost
Grouped cross-validation and randomized hyperparameter search
Not every experiment is repeated in the compact notebook. Use the full experimental notebook as the historical record of comparisons and tuning; use the compact notebook for routine final-model reproduction.
Final results
The selected Tuned Balanced Random Forest was refit on the training partition and evaluated on the held-out test set. The recorded test support is 61,227 samples.
Metric	Final test result
Accuracy	0.9045
Macro Precision	0.6197
Macro Recall	0.6780
Macro F1	0.6338
Weighted F1	0.9085
Training time (recorded run)	148.66 seconds
Prediction time (recorded run)	1.76 seconds
Per-class test performance
Class	Precision	Recall	F1-score	Support
Benign	0.9987	0.9675	0.9829	44,953
Analysis	0.3418	0.7976	0.4786	84
Backdoor	0.6905	0.5979	0.6409	97
DoS	0.5847	0.4074	0.4802	864
Exploits	0.8536	0.7171	0.7794	6,197
Fuzzers	0.6151	0.8424	0.7111	5,337
Generic	0.7792	0.8090	0.7938	890
Reconnaissance	0.5504	0.6563	0.5987	2,389
Shellcode	0.3487	0.4240	0.3827	375
Worms	0.4340	0.5610	0.4894	41
Metrics are reproduced from the completed modeling notes. Runtime is hardware- and environment-dependent.
Interpretation and limitations
Accuracy is 90.45%, but Macro F1 is 0.6338. This gap shows why accuracy alone is not sufficient for a severely imbalanced multiclass task.
Benign classification is strong. Generic and Exploits also show comparatively strong F1 scores.
Fuzzers recall is 0.8424.
DoS and Shellcode remain difficult classes, with recall of 0.4074 and 0.4240 respectively.
Worms has only 41 test examples, so its class-level metrics are uncertain.
Results apply to the evaluated dataset and split; they do not establish performance on other networks, future traffic, or a live production environment.
Reproducibility depends on using the same dataset version, row alignment, preprocessing, grouping and split procedure, and library versions.
Repository structure
A recommended GitHub layout is:
```text
project10-intrusion-detection/
├── README.md
├── notebooks/
│   ├── project10_EDA.ipynb
│   ├── project10_mdldev_draft1.ipynb
│   └── Project10_Compact_Reproducible.ipynb
├── report/
│   └── Project10_IEEE_Initial_Draft.tex
├── outputs/                 # Generated locally; usually not committed
├── data/                    # Keep raw/large data out of Git unless permitted
├── requirements.txt
└── .gitignore
```
Adjust filenames to match the files you actually commit. Keep only the notebooks and report versions you intend to maintain publicly, and avoid committing multiple confusing copies of the same notebook.
Reproduce the final workflow
Option A — Google Colab
Upload `Project10_Compact_Reproducible.ipynb` to Google Colab.
Put the required merged CSV dataset in your Google Drive.
Update the `DATA_PATH` variable in the notebook to point to the dataset's actual location.
Run the notebook from top to bottom.
Review the final metrics and generated outputs.
The compact notebook installs `imbalanced-learn` only if needed; Colab generally includes the core scientific Python stack. The exact required packages are determined by the imports in the notebook.
Option B — Local Jupyter environment
Create and activate a Python environment.
Install the notebook's dependencies, including NumPy, pandas, scikit-learn, Matplotlib, joblib, and imbalanced-learn as required by the notebook.
Place the dataset at the configured path or update `DATA_PATH`.
Run `Project10_Compact_Reproducible.ipynb` from top to bottom.
For strict reproducibility, record your Python version, package versions, dataset source/version, and dataset file checksum. The same random seed does not guarantee identical results across all library versions or hardware environments.
Outputs
The compact notebook is designed to save:
`tuned_balanced_random_forest.joblib` — fitted model pipeline, including preprocessing.
`final_test_predictions.csv` — true and predicted test labels.
`final_test_metrics.json` — final summary metrics.
A normalized confusion-matrix visualization in the notebook.
Generated model files and predictions can be large and should generally be kept out of Git unless there is a clear reason to publish them. The notebook and README document how to regenerate them.
Data provenance note
Resolve this before treating the repository as a finalized research release. The earlier EDA notebook/README and modeling-stage notes contain inconsistent intermediate row counts:
The EDA record reports 413,489 rows in `Dataset.csv` and 447,915 rows in `Label.csv`, followed by index-based concatenation and a 284,011-row deduplicated dataset.
The modeling-stage notes report an aligned 447,915-row feature/label merge, followed by exact-duplicate removal to 306,173 rows.
These figures cannot be silently treated as the same data lineage. Before publication, verify the exact feature and label files, row alignment, merge procedure, duplicate-removal logic, and dataset version. Update the notebooks and README once the discrepancy is resolved. Until then, interpret the final metrics as results from the recorded modeling workflow, not as proof that all repository data-preparation records are fully reconciled.
Responsible use
This project is intended for learning and research. Intrusion-detection predictions can be wrong, especially for rare attack classes. Do not use this model as the sole basis for blocking traffic or making security decisions. Validate it against representative, current, independently collected traffic and use appropriate human oversight before any operational deployment.
---
Acknowledgements and citations
Add verified citations for the exact dataset release, the original dataset paper, and any relevant methods or libraries before academic publication. Do not add unverified references or claim affiliation/endorsement by the dataset authors.
