# Requirements Checklist

Where each required item lives in this repository. The final self-check notebook (`notebooks/99_final_submission_check.ipynb`) passed on the complete project.

| Requirement | Location |
|---|---|
| Executed notebooks for all five gates | `notebooks/01_baseline_boosting.ipynb` to `notebooks/05_final_model.ipynb` |
| Final overview notebook (setup, all figures, final result) | `Tamweel_Lite_Final_Project.ipynb` |
| Readiness notebook | `notebooks/00_readiness_check.ipynb` |
| Final self-check notebook | `notebooks/99_final_submission_check.ipynb` |
| Outputs from the earlier gates (tables, figures, run records) | `evidence/` (one folder per gate) |
| Final-gate tables, figures, policy and provenance | `artifacts/` |
| Exported final model (JSON/native, no pickle) | `artifacts/final_model/` |
| Frozen policy and metrics | `artifacts/final_policy.json`, `artifacts/final_metrics.json` |
| Challenge predictions (2,500 rows, at most 300 flags) | `submission/submission.csv` |
| Model card | `reports/MODEL_CARD.md` |
| Ensemble decision | `reports/ENSEMBLE_DECISION.md` |
| Released data and contract | `data/` |
| Reproduction code | `scripts/`, `tamweel/` |
| Pinned environment | `requirements.txt`, `requirements-colab.txt`, `constraints.txt`, `config/environment.json` |
| Daily answers | `README.md`, section "Daily answers" |
| Final Project Check workflow | `.github/workflows/final_project.yml` (run from the repository Actions tab) |
| Figures with explanations | `README.md`, section "Figures and explanations"; image files in `reports/` |
