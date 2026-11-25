# قرار التجميع

KEEP SINGLE — Logistic

I kept the single Logistic Regression model. It had the highest mean AP across the three forward folds: 0.392 (fold SD 0.030), compared with 0.389 for the weighted average, 0.383 for stacking, and 0.372 for equal averaging. The ensembles' AP lifts over Logistic were zero or negative, and none passed the worth-it gate. The differences are also much smaller than the fold SD, so I can't claim an ensemble is better. Logistic's Brier score was 0.0633 and ECE was 0.0188. Stacking had a worse ECE of 0.0311; the weighted average's ECE was slightly lower at 0.0177, but it still had no AP gain. The ensembles require more models and, for stacking, a meta-model, so the extra complexity wasn't justified.

I excluded the leaky days_past_due_60 field. The nested forward OOF comparison used 2,155 predictions from three later-period folds. Training rows were old enough for their 90-day labels to mature, validation customers were kept out of training, and ensemble weights and the stacker were learned from inner OOF predictions only. There were no outer OOF predictions for the warm-up rows, so 2,155 of the 6,576 fit-and-selection rows were evaluated. There were only three folds, and their training histories overlap. The fold SD of about 0.03 is similar to the differences between the top models; it is descriptive, not a confidence interval. The threshold and loss were also selected on these OOF predictions, so they are development results, not an independent final test. The data are synthetic, and the course had used the training data earlier.

الدليل: artifacts/ensemble_comparison.csv وday5_ensemble_gate.json. SD وصفي، وليس اختبار دلالة.
