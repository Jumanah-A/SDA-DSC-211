# تقريرك: التفسير والمعايرة — Tamweel Lite

**الحالة:** جاهز للمراجعة؛ لا يعني اعتمادًا أو درجة

**مصدر التفسير:** LIVE. **النموذج والمعايرة:** LIVE. **السعة:** CAPACITY_REVIEW_REQUIRED.

## النموذج والأدوار
LightGBM موزون، 80 شجرة. الهدف حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. الأدوار منفصلة زمنيًا وبالعملاء: تدريب 2516، معايرة 584 (40 موجب)، سياسة 589، تقييم 1733. الفجوات والتداخلات مستبعدة. سبق استخدام بيانات التقييم في الدورة، فهي ليست اختبارًا نهائيًا لم يمسّ.

## التفسير العام والمحلي
Permutation يقيس انخفاضAP على التقييم؛ إشارات المنطقة تُبدّل معًا. SHAP يفسر النموذج الخام بوحدةlog-odds وخلفية مسارات أشجار التدريب. base+sum(SHAP)=raw margin، ثمsigmoid للمجموع فقط. القيم ليست نقاط احتمال ولا تفسيرًا مباشرًا للنموذج المعاير.

Across the 1,733 evaluation requests, bureau_score was the most important feature: shuffling it lowered Average Precision by 0.127 across three runs. In the 300-request SHAP sample, it also had the highest average SHAP value (0.904 log-odds), followed by dti (0.544). For TR-009585, the biggest positive contributions were bureau_score (+2.27), dti (+1.20), and loan_amount_sar (+0.20). The global results describe the model overall; the local results explain just this one request.

SHAP values are in log-odds (raw margin), not probability points: raw margin = base value + sum of SHAP values. Calibration then adjusts that probability: for TR-009585, 0.9031 raw became 0.4795 calibrated.The background is the training tree-path distribution, and sigmoid(base_value) is not necessarily the mean predicted probability. The additivity error was very small (5.7e-15).

الطلب الاصطناعي TR-009585: الدرجة الخام 0.90308 والاحتمال المعاير 0.47952. اختير أعلى درجة داخل عينةSHAP دون استخدام النتيجة الفعلية.
- استخدم النموذج درجة ائتمانية اصطناعية عند الطلب بالقيمة 497 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+2.2693 log-odds؛ قيمة معوضة: False)
- استخدم النموذج نسبة الالتزام مع القسط المقترح إلى الدخل بالقيمة 1.2806 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+1.1981 log-odds؛ قيمة معوضة: False)
- استخدم النموذج مبلغ التمويل المطلوب بالقيمة 93437.3 لرفع درجته الخام؛ لا يثبت ذلك السببية. (SHAP=+0.2007 log-odds؛ قيمة معوضة: False)

The three reason statements show which features pushed the model’s score up for this request. They do not explain why someone would default, and changing a feature does not guarantee a different result. Related features can share information, so their individual importance may look smaller. Changing bureau_score by one point did not change this request’s top three reasons, but that is only a small check on one request.

## دليل المعايرة
على 1733 صفًا و139 موجب: Brier 0.113027 → 0.067112؛ ECE 0.146871 → 0.022486. عشر حاويات متساوية العرض مع أعدادها فيday4_reliability_bins.csv. AP 0.258677 → 0.258677؛ ROC-AUC 0.770804 → 0.770804. هذه نتائج هذه العينة وليست ضمانًا لتحسن مستقبلي.

On the evaluation set (1,733 requests, 139 defaults), calibration improved the Brier score from 0.1130 to 0.0671, ECE from 0.1469 to 0.0225, and log loss from 0.358 to 0.247. ROC-AUC stayed at 0.7708 and Average Precision stayed at 0.2587 because calibration kept the ranking the same. ECE also went down in Q3 (0.134 to 0.032 across 836 requests) and Q4 (0.159 to 0.024 across 897 requests). ECE depends on how probability ranges are grouped, so results from groups with few requests should be treated carefully.

## الاستقرار
200 تكرارbootstrap صالح بسحب العملاء؛ فترات مئينية95% مع تثبيت النموذج والمعاير. لا تشمل تعلم النموذج أو المعايرة أو الانجراف المستقبلي، ولا تصف احتمال فرد. انحرافAP بين ربعي التقييم وصفي فقط. اختبارbureau_score±1 نُفذ؛ راجع day4_local_stability.csv.

The paired customer-cluster bootstrap gives a 95% percentile interval for AP of 0.197 to 0.338 and for Brier change of -0.054 to -0.037, so the improvement is consistently negative. The interval holds the model and calibrator fixed, so it excludes training uncertainty, calibration-fitting uncertainty and future drift. It is not a confidence interval for any one applicant's probability, and the two quarters are not independent folds.

## العتبة ومنطقة المراجعة
العتبة الخام 0.5881953696965011 اختيرت علىpolicy بخسارة10×FN+FP وسقف12% ثم نُقلت إلى 0.17331013263107387. لم تعدل باستخدام التقييم. المنطقة[0.15331, 0.19331] تشخيصية بعرض±0.02 وليست فترة ثقة. الاتحاد يحسب الطلب مرة واحدة.
- 2024Q3: السقف 100، الإشارات 97، اتحاد المراجعة 109.
- 2024Q4: السقف 107، الإشارات 109، اتحاد المراجعة 122.

The threshold was selected on the policy set using the 10×FN+FP loss and a 12% capacity limit. It flagged 68 of 589 requests, within the limit of 70. The raw threshold was 0.588 and the calibrated threshold was 0.173. On evaluation, the combined review group exceeded capacity in both quarters: 109 of 836 requests in Q3 (limit 100) and 122 of 897 in Q4 (limit 107). Risk flags alone were already over capacity in Q4 (109). The result is CAPACITY_REVIEW_REQUIRED, so I did not retune the threshold on evaluation data.

عند تجاوز السعة، وثّق الحاجة إلى تصميم سياسة جديدة على بيانات تطوير وتقييمها بدليل جديد. لا ترفع السقف ولا تقص الحالات بعد رؤية النتيجة. التفسير ليس سببية أو شهادة عدالة، والخسارة وحدات تعليمية لا رسوم أو خصم درجات. لا يستخدم هذا التمرين لتمويل حقيقي.
