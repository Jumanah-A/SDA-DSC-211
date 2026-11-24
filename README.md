# SDA-DSC-211

**Day 1 | اليوم الأول**

<img width="822" height="113" alt="Screenshot 2026-11-24 at 10 34 23 AM" src="https://github.com/user-attachments/assets/5c574ef9-c5fe-422f-b88b-c09610745ff1" />

Daily answers | الإجابات اليومية

Daily question · Model comparison

**Which model did you select initially, and what is the strongest result supporting your choice?**

Initially I chose the XGBoost model, based on the table above. The XGBoost model has the highest Average Precision of all 3 models of (0.3338). Since default events don't occur often, I chose the average precision as my metric of choice to compare how the well models perform and then select the best one that would identify defaults.





**Day 2 | اليوم الثاني**

Daily answers | الإجابات اليومية

Daily question · Honest validation


<img width="490" height="656" alt="Screenshot 2026-11-24 at 1 53 08 PM" src="https://github.com/user-attachments/assets/fdc4d9d5-e0f5-4eb2-8b1b-d6de914aa642" />


**What leakage risk did you identify, and how did the validation design prevent it?**

I identified a target leakage risk "days_past_due_60", this field is only available after the application (as it happens in the future) and the model shouldn't be able to access it while training. The validation design to prevent it was to remove the "days_past_due_60" field before training the model.


**Day 3 | اليوم الثالث**

Daily answers | الإجابات اليومية

Daily question · Decision threshold

**Why is a fixed threshold of 0.5 not always suitable for this review policy?**





**Day 4 | اليوم الرابع**

Daily answers | الإجابات اليومية

Daily question · Interpretation and calibration

**Which feature was most important in your explanation, and why does that not prove causality?**





**Day 5 | اليوم الخامس**

Daily answers | الإجابات اليومية

Daily question · Final model

**Why did you choose the single model or the ensemble for the final project?**
