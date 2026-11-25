# بطاقة قرارك — Tamweel Lite

**الحالة:** جاهزة للمراجعة؛ لا تعني اعتمادًا أو درجة
**مصدر الأرقام:** LIVE · **الاستراتيجية:** weighted · **الصفوف:** 5,039 OOF

**المهمة:** الفئة الموجبة `default_within_90d=1` تعني حدث تعثر اصطناعي خلال90 يومًا بعد الطلب. كل طية تحقق طلبات لاحقة، وتستبعد عملاءها من التدريب وتشترط نضج نتيجة التدريب قبل بدايتها. المعرّفات والتاريخ خارج المدخلات.

| الدليل | القيمة |
|---|---:|
| العتبة المقيدة، بالقيمة الكاملة | 0.6583471436014694 |
| عتبة أقل خسارة دون قيد | 0.44863935722081005 |
| Recall | 40.89% |
| Precision | 29.85% |
| AP مجمع منOOF | 0.3100 |
| الإشارات | 526 من 5,039 |
| FN / FP | 227 / 369 |
| الخسارة التعليمية | 2639 وحدة |
| خسارة0.5 | 2403 وحدة؛ ضمن السعة: False |
| التغير عن0.5 | +236 وحدة؛ الموجب زيادة |
| خسارة لكل10,000 طلب، تطبيع حسابي | 5237.15 وحدة |
| فجوة معدل الإنذار الخاطئ بين المناطق | 0.648 نقطة مئوية |

**القاعدة:** درجة ≥ 0.6583471436014694 تعني إشارة مراجعة داخل التمرين؛ غير ذلك بلا إشارة. لا تتخذ موافقة أو رفض تمويل حقيقي. احفظ الدقة الكاملة؛ تقريب العتبة قد يغيّر حجم الطابور.

**السياسة:** FN=10 وFP=1 وحدات تعليمية، وسعة 12% لكل فترة بعد التقريب لأسفل. ليست ريالات فعلية أو رسوم أدوات أو خصمًا من الدرجة.

**دليل السعة:** الفترة 1: 137/195, الفترة 2: 183/200, الفترة 3: 206/207.

## لماذا اخترت هذه العتبة؟
The chosen threshold is defensible because 0.6583 was the lowest loss threshold that stays within the 12% review limit in every period. It flags 526 requests (10.4%) and catches 157 of 384 defaults (recall 41%, precision 30%). Its loss is 2,639 units. The lower threshold of 0.4486 has less loss, but flags too many requests (22.6%). The default 0.5 also flags too many (19.9%).

## الخسارة والسعة
A higher loss was accepted to honor the capacity of the number of reviews.The chosen threshold has a loss of 2,639, compared with 2,275 at the unconstrained threshold and 2,403 at 0.5. Its recall is also lower: 41% instead of 64% at the unconstrained threshold. For the three cost scenarios tested the capacity limit was the driver in the decision.

## فرق المناطق وما يحتاج إلى مراجعة
The false-positive rate was 7.64% in “Other” and 8.29% in the Western region—a gap of 0.65 percentage points. Recall ranged from 35% in the West to 48% in “Other.” These differences are worth reviewing,  but it doesn’t prove the model is fair or unfair.

## حدود النتيجة
This isn’t a final test because I chose the threshold using the same OOF results I used to measure it, so the loss may look a little better than it would on new data. Only 5,039 of the 10,000 rows have OOF predictions; the 4,961 warm-up rows don’t. Because the model used class weighting, its scores may not be reliable probabilities. The data is synthetic, and the loss units aren’t real money. The calculation also leaves out the cost of doing reviews. Staying within capacity in these periods doesn’t guarantee the same result in the future.

OOF تغطي 50.39% من التدريب و100% من الصفوف المؤهلة؛ 4,961 صفًا تمهيديًا بلا تنبؤ. اختيار العتبة وتقدير خسارتها هنا يستخدمان أهدافOOF نفسها؛ هذه نتيجة تطوير لا اختبار نهائي. لم نستخدم التحدي. المقارنة الجغرافية وصفية وليست شهادة عدالة، والأوزان لا تضمن معايرة الدرجات.

## سؤالك الأول: لماذا قد تخدعكAccuracy؟
Only 7.6% of cases defaulted. If the model flagged nobody, it would still be 92.4% accurate—but it would catch no defaults. The unweighted model was 92.6% accurate but caught only 12.5% of defaults. The weighted model was less accurate at 81%, but caught 58% and had lower loss. Since missing a default costs ten times more than a false alarm, accuracy isn’t enough to judge the models.

## سؤالك الثاني: لماذا تختار علىOOF؟
Training predictions can look too good because the model has already seen those examples. OOF predictions are made for examples the model didn’t train on, so they give a fairer check. The time-based folds also keep the data in the right order. The challenge data should stay untouched for the final test; using it to choose a threshold would make that test less reliable.

أدلتك في `artifacts/threshold_metrics.json` و`day3_period_capacity.csv` و`day3_region_audit.csv` و`day3_cost_sensitivity.csv` و`cost_curve.png`. الحساسية سيناريوهات ±20% لخسارةFN، وليست فترات ثقة. راجع السعة والمعايرة عند تغير البيانات؛ لا تفترض ثباتهما مستقبلًا.
