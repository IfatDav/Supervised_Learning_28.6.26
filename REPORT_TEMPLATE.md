# REPORT — Module 3 · Assignment 1 · Supervised Learning

**Name:** ___  **ID:** ___  **Date:** ___
**Chosen task:** ___ (A · Olist negative review / B · Telco churn / C · Olist daily volume forecast)

> Keep this report in English. Be concise and honest. "I don't know, and here is why"
> is a professional answer. Empty confidence is not.

---

## 1. Problem framing
Business question (one short paragraph):

Target definition:

**Primary metric and why it fits the business cost** (cost of a false positive vs a
false negative; for forecasting, cost of over- vs under-forecasting):

---

## 2. Results table
Fill in every model **and the dumb baseline** on the locked test set.

| Model | Primary metric | Secondary metric | Notes |
|---|---|---|---|
| baseline | | | dumb model |
| linear | | | |
| bagging | | | |
| boosting | | | |

---

## 3. Guiding questions (graded)
Answer each in 2-5 sentences.

1. **Accuracy trap.** Why is plain accuracy misleading for your problem, and what did
   the confusion matrix (or the error distribution, for forecasting) reveal that accuracy hid?

2. **Cost of errors.** What does a false positive cost vs a false negative in your
   business context, and how did that drive your metric choice?

3. **Worth deploying?** By how much did your best model beat the dumb baseline?
   If the margin is small, is the model worth shipping — and would you ship it?

4. **What drives it.** Which features carry the predictions? Do they make business
   sense, or is the model leaning on a leak or a spurious correlation? How did you check?

5. **Worst errors.** Look at the 5 worst mistakes. What is the story — a data quality
   problem, mislabeled rows, or genuinely hard cases?

6. **Stability.** How much does the score move across CV folds? Would you hand this
   single number to a stakeholder as "the" performance? Why or why not?

7. **Leakage / time.** (Required for B too, in one line.) How did you guarantee the
   model never saw information from the future or from the test set? For a time-based
   split, what happens to the score compared with a random split, and why?

8. **Monday morning.** If this went live today, what would you monitor, and what signal
   would make you retrain it?

---

## 4. Model Card
Paste the completed Model Card from the notebook here.

```
(Model Card)
```

---

## 5. Reflection
What surprised you? What would you do differently with two more weeks or more data?
How would this approach change if it were part of your mid-term project?
