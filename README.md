# oulad-engagement-audit
Auditing engagement as a proxy signal for learning outcomes: code and results on the OULAD dataset
# Auditing Engagement as a Proxy Signal for Learning Outcomes

Code and results accompanying the study:

> **Engagement as a Useful but Incomplete Signal:  
> Auditing a Proxy Metric in University Education with OULAD**

---

## Overview

In educational systems, learning is the target, but the metric that is easiest to measure — student engagement — often becomes the proxy that gets optimized. This repository contains the full analysis pipeline for auditing engagement as a proxy signal for at-risk student identification, using the **Open University Learning Analytics Dataset (OULAD)**.

The study addresses three questions:

1. How well does engagement alone predict at-risk status (fail or withdrawal), and how much does adding assessment scores help?
2. How do the two signals evolve over the course?
3. Is predictive performance uniform across student subgroups (gender, age, disability, deprivation)?

---

## Key findings

At the primary prediction point (day 56, ~week 8), with models trained on 2013 presentations and tested on 2014:

| Feature set | AUC (95% CI) |
|---|---|
| Engagement only | 0.750 (0.741–0.758) |
| Assessment only | 0.767 (0.758–0.774) |
| Engagement + Assessment | 0.793 (0.786–0.800) |

- Engagement alone is a **useful but incomplete** signal. Adding assessment scores improves AUC by **0.044** (95% CI 0.037–0.050).
- The difference between assessment and engagement alone is **small** (0.017; 95% CI 0.008–0.026): statistically reliable but of limited practical significance.
- Engagement and assessment carry **complementary information**; the combined model is consistently best.
- **32.0% of at-risk students are flagged as "highly engaged"** by the engagement-only model — a third of the at-risk population is invisible to a proxy-only system.
- Group audit at the top 20% of the risk ranking reveals a **gender gap of 0.100** (M–F, 95% CI 0.077–0.125) and an **age gap of 0.107** (35–55 vs. 0–35, 95% CI −0.134 to −0.081) for the engagement-only model. Adding assessment reduces both gaps but does not eliminate them.
- **Robustness checks** (rank-normalization within course, and exclusion of module CCC) leave the main conclusions intact.

![ROC at day 56](outputs/fig_main_roc_and_time.png)

---

## Dataset

The study uses the **Open University Learning Analytics Dataset (OULAD)** (Kuzilek et al., 2017), publicly released under CC BY 4.0.

- **32,593 enrollments** across **28,785 unique students**, **7 modules**, and **22 presentations** (2013–2014).
- Overall at-risk rate: **52.8%**; range across presentations: 27.4%–65.8%.
- Train/test split: presentations from **2013** for training, **2014** for testing.
- Cutoff points (days after course start): 14, 28, 42, **56 (primary)**, 84, 112, 140.

The dataset is downloaded automatically via `kagglehub` (see notebook cell 3).

---

## Methods

- **Features (Engagement):** log total clicks, active days, log clicks in last 14 days, log clicks per active day.
- **Features (Assessment):** number of non-exam submissions, mean score, submission rate.
- **Models:** `HistGradientBoostingClassifier` (max_depth=4, learning_rate=0.05, max_iter=200) and `LogisticRegression` as a benchmark. No hyperparameter tuning was performed.
- **Evaluation:** AUC, Average Precision (AP), Precision@10%, recall at the top 20% of the risk ranking.
- **Uncertainty:** 95% confidence intervals from 1,000 bootstrap resamples.
- **Robustness:** (a) rank-normalization of engagement features within each module-presentation; (b) exclusion of module CCC (which appears only in 2014 and is never seen in training).

---

## Repository structure

```
.
├── notebooks/
│   └── OULAD_analysis.ipynb        # Full analysis pipeline
├── outputs/
│   ├── fig1_roc.png
│   ├── fig2_auc_over_time.png
│   ├── fig3_engagement_vs_outcome.png
│   ├── fig4_group_audit.png
│   ├── fig_main_roc_and_time.png
│   ├── table_dataset_description.csv
│   ├── table_results_all.csv
│   ├── table_main_cutoff.csv
│   ├── table_robustness.csv
│   ├── table_group_audit.csv
│   ├── summary.txt
│   ├── bootstrap_summary.txt
│   └── bootstrap_groups.txt
├── requirements.txt
└── README.md
```

---

## Reproducibility

```bash
git clone https://github.com/<user>/oulad-engagement-audit.git
cd oulad-engagement-audit
pip install -r requirements.txt
jupyter notebook notebooks/OULAD_analysis.ipynb
```

The notebook downloads the dataset automatically via `kagglehub`, runs all models, and writes figures and tables to `outputs/`.

Fixed random seed: **42**.

**requirements.txt:**
```
numpy
pandas
scikit-learn
matplotlib
kagglehub
jupyter
```

---

## Scope and limitations

- OULAD is behavioral data from a **distance-learning university in the UK**; generalization to in-person universities or other educational systems is not claimed.
- The dataset is **not** AI-chatbot data. The study measures the quality of a proxy signal, not the behavior of an optimized AI system. Findings apply to any system — from a simple early-warning model to an advanced AI tutor — that relies on engagement as a proxy for learning.
- The "at-risk" label combines **fail** and **withdrawal**, which may have different underlying mechanisms.
- Group audits for **disability** and **deprivation (IMD)** are descriptive only and were not statistically tested.
- Bootstrap confidence intervals for group-level recall assume independent rows; some students appear in multiple modules, so intervals are slightly optimistic.

---

## Citation

If you use this code or results, please cite:

```bibtex
@misc{oulad_engagement_audit_2026,
  author = {[Author Name]},
  title  = {Auditing Engagement as a Proxy Signal for Learning Outcomes (OULAD)},
  year   = {2026},
  url    = {https://github.com/<user>/oulad-engagement-audit}
}
```
