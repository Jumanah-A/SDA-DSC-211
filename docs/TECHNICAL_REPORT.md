# Tamweel Lite - Technical Report

## 1. Problem and scope

Tamweel Lite is an educational tabular machine-learning project. It ranks synthetic loan applications by the risk of a default event within 90 days (`default_within_90d`) and simulates a capacity-limited review queue. It is not a credit-decision system. Its outputs are simulated review flags for training use only.

## 2. Data

- Dataset: `tamweel-lite-1.0` (synthetic)
- Training file: 10,000 rows. Challenge file: 2,500 unlabeled requests.
- Model inputs: 22 features available at application time (identifiers, dates and the target are never inputs).
- Leakage control: `days_past_due_60` is only known after the application and is removed.

| Role | Rows | Customers | Positives | Period |
|---|---:|---:|---:|---|
| Fit and selection pool | 6,576 | 3,931 | 537 | 2022-01-01 to 2024-04-01 |
| Calibration only | 836 | 788 | 78 | 2024-07-01 to 2024-09-30 |
| Excluded from fitting and selection (see the roles table in `artifacts/`) | 2,588 | - | - | - |

## 3. Validation design

- Three forward comparison periods: 2023Q1, 2023Q3 and 2024Q1.
- Training is earlier than validation by enough time for the 90-day outcome to mature, and validation customers are excluded from training.
- A second forward split inside each outer training set learns the averaging weights and the logistic stacker. The meta-model never learns from in-sample base predictions.
- Imputation and scaling are learned inside training only.

The resulting out-of-fold (OOF) evidence supports development and selection. Earlier work in the project already used the training data, so it is not an untouched final test.

## 4. Model comparison

Candidates: LightGBM, XGBoost, Logistic Regression, equal average, weighted average and logistic stacking.

| Candidate | Mean AP | Fold SD | Brier | ECE | Passes gate |
|---|---:|---:|---:|---:|:---:|
| LightGBM | 0.3455 | 0.0435 | 0.0661 | 0.0231 | No |
| XGBoost | 0.3526 | 0.0290 | 0.0657 | 0.0228 | No |
| Logistic | 0.3917 | 0.0298 | 0.0633 | 0.0188 | Best single |
| Equal average | 0.3717 | 0.0326 | 0.0643 | 0.0204 | No |
| Weighted average | 0.3894 | 0.0291 | 0.0633 | 0.0177 | No |
| Stacking | 0.3831 | 0.0295 | 0.0660 | 0.0311 | No |

Gate rule: accept an ensemble only if its AP gain exceeds the best single model's fold standard deviation (0.0298), Brier worsens by at most 0.005 and ECE by at most 0.01. If several pass, prefer the simplest. No ensemble passed, so the decision is **KEEP SINGLE** with Logistic Regression. The fold SD describes three overlapping periods and is not a confidence interval.

## 5. Decision policy

- Loss: 10 x false negatives + 1 x false positives (fictional teaching units).
- Capacity: at most 12% flagged in every period and overall.
- Search: every distinct OOF score of the chosen model.
- Result: raw OOF threshold 0.1689, flagging 245 of 2,155 OOF rows (11.4%), recall 46.9%, precision 34.3%, loss 1,111 units.

## 6. Calibration

The chosen procedure was refit on the eligible pool. One sigmoid mapping was learned on the reserved calibration period and the model was not retrained afterwards. The raw threshold was transported to the calibrated scale (0.1223).

| Calibration-fit diagnostic (836 rows) | Brier | ECE | AP |
|---|---:|---:|---:|
| Raw | 0.0765 | 0.0211 | 0.2878 |
| Sigmoid | 0.0781 | 0.0349 | 0.2878 |

These are measured on the same rows used to learn the mapping. They are diagnostics, not independent performance, and they show no improvement from the sigmoid on this period.

## 7. Challenge predictions

All 2,500 requests were scored. 330 exceeded the transported threshold. The 12% cap (300) was applied once to the full batch, keeping the highest probabilities and never splitting a block of equal scores. 300 requests were flagged and 30 were removed by the cap. Challenge labels are unavailable, so no challenge AP, loss or false-positive rate is claimed.

## 8. Regional diagnostic

| Region | Negatives | Flag rate among negatives | Recall |
|---|---:|---:|---:|
| Central | 489 | 7.98% | 50.0% |
| Western | 486 | 10.29% | 44.7% |
| Eastern | 507 | 6.31% | 45.7% |
| Other | 494 | 8.10% | 46.9% |

The gap between highest and lowest is 3.98 percentage points. It is descriptive, has no confidence interval and is not a fairness certification.

## 9. Interpretation scope

Permutation importance and SHAP were produced for an earlier LightGBM model (Gate 4), where `bureau_score` was the most important feature (permutation AP drop 0.127). They explain what that model relied on. They do not prove causality and are not automatically attributed to the final Logistic Regression model, whose coefficients and calibration should be reviewed separately.

## 10. Limitations

- Synthetic data; not for real financing decisions.
- Development evidence only; no untouched final test.
- Three dependent forward periods; no significance test.
- Review cost is not included in the loss.
- Transporting a threshold to a refit model can change flag volume, so capacity is applied to the whole batch.
