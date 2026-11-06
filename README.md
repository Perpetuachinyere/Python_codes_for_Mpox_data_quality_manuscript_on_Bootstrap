# Python_codes_for_Mpox_data_quality_manuscript_on_Bootstrap
Reproducible Python code for evaluating country-level reporting bias and machine learning classification performance on global Mpox surveillance data.

# Mpox classifier analysis (revised) — reproducibility package

All data-dependent preprocessing (imputation, scaling, Country target encoding) is fitted inside training folds (scikit-learn Pipeline).
Data: `monkey_pox_dataset.xlsx` (Kaggle: gianetan/monkeypox-epidemiological-data-daily). Set `MPOX_DATA=/path/to/file.xlsx` or place it in this folder.

Run in order (each stage reads and updates `results_v2.pkl`; stages are sized to finish in under ~5 minutes on one CPU core):

| Stage | Script | Produces |
|---|---|---|
| 1 | v2_stage1.py | Model A (with Country): nested grid search, out-of-fold metrics, bootstrap CIs, out-of-sample calibration |
| 2 | v2_stage2.py | Model B (without Country), same outputs |
| 3 | v2_stage3.py | Paired bootstrap of AUC(A)-AUC(B); leave-one-country-out validation |
| 4 | v2_stage4.py | Within-country analysis with bootstrap CIs; sensitivity analyses (MICE, reporting month, confirmed-vs-discarded) |
| 5 | v2_stage5.py | Permutation test (60 refits) |
| 6 | v2_stage6.py | Selected hyperparameters, feature importances, feature-wise K-S |
| 7 | v2_stage7.py | Predictor-group decomposition of Model B |
| 8 | v2_stage8.py | Audit of all 35 fields; extended metrics with CIs; country-cluster bootstrap; repeated-CV variance |
| 9 | v2_stage9.py | Single-field models (Source, Location, entry dates) |
| 10 | v2_stage10.py | Temporal validation by database entry date; complete-case analyses |
| 11 | v2_stage11.py | Symptom-only and age/gender-only models within the 157 symptom-recorded records |
| 12 | v2_stage12.py | Unseen-country cluster bootstrap with refitting and tuning (run as `python v2_stage12.py 36`, about 4 minutes) |
| – | v2_export_json.py | `results_v2.json`: every number quoted in the manuscript |
| – | v2_figs.py | Figures 1-9 (PNG + TIFF, 300 DPI, white background) |

Notes
* Random seed 42 throughout. Software versions used are recorded in `results_v2.json` (python 3.12, scikit-learn 1.8, xgboost 3.4).
* Permutation test, leave-one-country-out, sensitivity analyses and repeated CV use histogram-based gradient boosting (fast, comparable across variants); the main tables use the tuned classifiers.
* Stage 4 and 8 contain the only analyses of fields other than the 15 modelled predictors.
* Known limits: temporal validation orders by database entry date, which is itself an outcome proxy (the confirmation date exists only for confirmed records); the unseen-country bootstrap uses a small tuning grid and 36 replicates.
