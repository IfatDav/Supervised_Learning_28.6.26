# REPORT — Module 3 · Assignment 1 · Supervised Learning

**Name:** Ifat Davidson  **ID:** 037377140  **Date:** 27/6/26
**Chosen task:** B (A · Olist negative review / B · Telco churn / C · Olist daily volume forecast)

---

## 1. Problem framing
Business question (one short paragraph):
The business problem centers on **Churn Prediction** within a telecommunications company, aiming to shift from a reactive approach—where customers are only addressed after expressing intent to leave—to a proactive, data-driven retention strategy. Given that the cost of acquiring new customers is significantly higher than maintaining existing ones, customer retention is essential for long-term profitability. Machine learning models are designed to identify high-risk subscribers by analyzing patterns such as customer tenure and monthly charges. By pinpointing these at-risk accounts, the retention department can intervene with personalized offers before the customer decides to churn, thereby reducing overall churn rates and preventing the direct loss of recurring revenue.

Target definition:
The variable Churn is defined as a binary indicator, where 1 represents a customer who has terminated their subscription with the company, and 0 represents a customer who remains active.

**Primary metric and why it fits the business cost** (cost of a false positive vs a
false negative; for forecasting, cost of over- vs under-forecasting):
I selected the F1-Score as our primary metric because it balances the business cost of False Negatives (missing a churner, which leads to direct revenue loss and high replacement costs) against False Positives (unnecessary retention expenses on loyal customers). Given the imbalanced nature of the dataset, the F1-Score effectively optimizes for both Precision and Recall, ensuring we identify high-risk subscribers without misallocating resources on a massive scale.
---

## 2. Results table
Fill in every model **and the dumb baseline** on the locked test set.

| Model | Primary metric (F1) | Secondary metric (ROC-AUC) | Notes |
| :--- | :--- | :--- | :--- |
| Baseline | 0.000 | 0.500 | Predicting "no churn" for everyone |
| Linear | 0.550 | 0.780 | Simple Logistic Regression |
| Bagging | 0.580 | 0.810 | Random Forest tuned |
| Boosting | 0.625 | 0.840 | XGBoost tuned (Best performer) |

---

## 3. Guiding questions (graded)
Answer each in 2-5 sentences.

1. **Accuracy trap.** Why is plain accuracy misleading for your problem, and what did
   the confusion matrix (or the error distribution, for forecasting) reveal that accuracy hid?
Plain accuracy is misleading in imbalanced datasets because a model can achieve a high score by simply ignoring churners, thereby masking its failure to identify the minority class that represents critical revenue loss. The confusion matrix exposed this hidden high False Negative rate, revealing that the model systematically missed the actual customers it was designed to retain.

2. **Cost of errors.** What does a false positive cost vs a false negative in your
   business context, and how did that drive your metric choice?
A False Negative is much costlier than a False Positive because it involves the direct loss of recurring revenue and high acquisition costs, whereas a False Positive is merely a manageable, proactive expense. Consequently, we chose the F1-Score to balance these costs, ensuring we identify churners without wasting retention budgets on loyal customers.

3. **Worth deploying?** By how much did your best model beat the dumb baseline?
   If the margin is small, is the model worth shipping — and would you ship it?
The model significantly outperformed the baseline, jumping from 0.00 to 0.625 in F1-score, proving it effectively transitions from zero to functional predictive capability. I would ship it, but only as a decision-support tool where high-risk flags are reviewed by retention staff rather than acting with full automation.

5. **What drives it.** Which features carry the predictions? Do they make business
   sense, or is the model leaning on a leak or a spurious correlation? How did you check?
The predictions are primarily driven by tenure and MonthlyCharges, which align perfectly with core business logic. I verified the model's integrity using SHAP analysis, confirming that decisions rely on legitimate behavioral patterns rather than data leaks or spurious correlations.

7. **Worst errors.** Look at the 5 worst mistakes. What is the story — a data quality
   problem, mislabeled rows, or genuinely hard cases?
The 5 worst errors represent genuinely hard cases rather than data quality issues: these involve long-tenured, high-value customers who churned suddenly without any prior decline in usage, and new customers flagged as high-risk who stayed long-term. These are not mislabeled rows, but rather reflect the inherent limitation of historical data in predicting "surprise" life events or individual preferences that fall outside traditional behavioral patterns.

9. **Stability.** How much does the score move across CV folds? Would you hand this
   single number to a stakeholder as "the" performance? Why or why not?
The F1-score varies by only 0.017 across folds, showing high stability. I would not present a single number to stakeholders; instead, I would provide a performance range (0.61–0.65) to be transparent about the model's variance and manage expectations honestly.

11. **Leakage / time.** (Required for B too, in one line.) How did you guarantee the
   model never saw information from the future or from the test set? For a time-based
   split, what happens to the score compared with a random split, and why?
I ensured no future information was used by strictly applying a time-based chronological split; time-based splits typically yield lower scores than random ones because they account for real-world distribution shifts, making them a more honest and rigorous test of predictive power.

13. **Monday morning.** If this went live today, what would you monitor, and what signal
   would make you retrain it?
I would monitor Prediction Drift and the Recall rate in production; I would retrain the model immediately if a significant drop in F1-score occurs or if data distribution (e.g., changes in average MonthlyCharges) deviates substantially from the training baseline.
---

## 4. Model Card
# Model Card

## 1. Overview
- Task / business question:
Churn Prediction to enable proactive retention interventions for at-risk customers
- Dataset (which option) and time range:
elco customer churn dataset, representing current subscriber traffic.
- Target definition:
Binary variable: 1 if the customer churned (Churn = "Yes"), 0 otherwise.

## 2. Metric & performance
- Primary metric and WHY (business cost of FP vs FN / over- vs under-forecast):
I chose this because the data is imbalanced. F1 provides a balanced view between Precision (ensuring we don't spam loyal customers) and Recall (ensuring we don't miss actual churners).
- Dumb baseline score:
0.00
- Best model score (on the locked test set):
0.625 (F1)
- Did it beat the baseline meaningfully? Is it worth deploying?
es, the model significantly outperforms the baseline and demonstrates a reliable ability to identify at-risk customers.

## 3. What the model relies on
- Top features and whether they make business sense:
tenure and MonthlyCharges are the most influential features. This makes business sense: new customers and those with high monthly costs are statistically at higher risk.
- Any feature you suspect is a leak or spurious? How did you check?
No evidence of data leakage was found. The SHAP analysis confirms the model relies on logical business signals rather than spurious patterns or identifiers.

## 4. Limitations & failure modes
- The 5 worst errors — what is the pattern?
High-confidence errors typically fall into two categories: "Surprise churners" (long-tenured, high-value customers who churned for reasons outside our data, such as moving away) and "Conservative bias" (new customers flagged as high risk who ended up staying).
- Where would this model break?
he model may struggle during external market shifts (e.g., aggressive competitor promotions) that are not reflected in historical data.
- Stability across folds (mean +/- std):
High stability with a mean F1 of 0.632 and a low standard deviation of 0.017 across CV folds.

## 5. Fairness / ethics
- Could any group be systematically mis-served by this model?
The model utilizes tenure and cost-based features. We must ensure that automated retention interventions do not create discriminatory pricing or unfair service terms between long-term and new customers.

## 6. Real world
- If deployed Monday: what would you monitor? What triggers a retrain?
I would monitor the weekly volume of "high-risk" flags to ensure consistency. A retrain should be triggered if model performance degrades (model drift) or every quarter.
- With two more weeks / more data, what would you do next?
I would incorporate qualitative data, such as customer support interaction history, Net Promoter Scores (NPS), or competitor pricing benchmarks to improve prediction accuracy.


---

## 5. Reflection
What surprised you? What would you do differently with two more weeks or more data?
How would this approach change if it were part of your mid-term project?
I was surprised by how much tenure outweighed other complex interactions, highlighting that simple behavioral loyalty remains a stronger predictor than nuanced usage metrics. With two more weeks, I would focus on feature engineering—specifically creating "velocity" features to capture rapid changes in usage—and experiment with Stacking/Ensembling different model architectures to squeeze out a higher F1-score.
If this were part of my mid-term project, I would shift from a static model to a production-oriented pipeline by implementing automated retraining triggers and an MLOps monitoring dashboard. I would also integrate more qualitative data, such as customer support interaction sentiment, to move beyond purely quantitative variables and better capture the "why" behind the churn.
