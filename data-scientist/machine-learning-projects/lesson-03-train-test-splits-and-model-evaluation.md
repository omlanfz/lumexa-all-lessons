# Course 15 · Lesson 3: Train/Test Splits and Model Evaluation

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain why we never evaluate a model on the same data it trained on
- Perform a real train/test split with scikit-learn
- Compute and interpret MAE, MSE, RMSE, and R² for regression
- Compute and interpret accuracy, precision, recall, and F1 for
  classification
- Use k-fold cross-validation for a more robust evaluation

## What You'll Learn
- `train_test_split` in depth, including `random_state` and `stratify`
- The four core regression metrics
- The four core classification metrics and the confusion matrix
- `cross_val_score` for cross-validation

## Why It Matters
A model that looks amazing on paper but was tested unfairly is worse than
useless — it's actively misleading. Learning to evaluate honestly is what
separates a real data scientist from someone who just calls `.fit()` and
hopes for the best. Every metric number you'll report in your three
portfolio projects depends on getting this lesson right.

## Real-World Connection
A hospital deploying a diagnostic model, a bank deploying a credit-risk
model, or a company deploying a churn model (exactly like Project 08) all
face the same real risk: if you evaluate on the wrong data, you might ship
a model that performs far worse in the real world than your report
promised — with real consequences.

## Key Terminology
- **Train/test split**: dividing your data so part trains the model and a
  separate part evaluates it.
- **Overfitting**: when a model memorizes the training data's quirks
  instead of learning generalizable patterns, performing well on training
  data but poorly on new data.
- **MAE (Mean Absolute Error)**: average absolute distance between
  predicted and real values.
- **MSE (Mean Squared Error)**: average of the squared distances (punishes
  big errors more).
- **RMSE (Root Mean Squared Error)**: square root of MSE, back in the
  original units.
- **R² (R-squared)**: how much of the variation in the real answers the
  model explains (1.0 = perfect, 0 = no better than guessing the average).
- **Accuracy**: fraction of predictions that were exactly correct.
- **Precision**: of everything the model said was "positive," what fraction
  actually was.
- **Recall**: of everything that actually was "positive," what fraction did
  the model catch.
- **F1 score**: a balance between precision and recall.
- **Confusion matrix**: a table showing correct vs. incorrect predictions
  broken down by actual and predicted category.
- **Cross-validation**: splitting data multiple different ways and
  averaging the results for a more reliable score.

## Concept Explanation

Imagine a teacher who lets students study the exact test they'll take,
answer key included, the night before. Every student would score 100%. That
score would tell you absolutely nothing about whether the students actually
learned the material. This is precisely the mistake it's tempting to make
in machine learning: train the model, then check how well it does *on the
same data it trained on*. It will almost always look great — because it may
have simply memorized quirks of that specific data — and that great-looking
score is a lie about how it will perform on new, real cases.

The fix is the **train/test split**: set aside a chunk of your real data
(commonly 20%) that the model *never* sees during training. After training,
you check the model's predictions only on this held-out test set. Now the
score actually means something — it estimates how the model will perform on
new data it hasn't memorized.

For regression, once you have real predictions and real answers on the
test set, you need a way to summarize "how wrong" the model was, as a
single number. **MAE** answers "on average, how many units off were we?" in
plain terms — easy to explain to a non-technical stakeholder ("our house
price predictions are off by about $32,000 on average"). **MSE** squares
each error before averaging, which makes big mistakes count extra — useful
when large errors are especially costly. **RMSE** takes the square root of
MSE, bringing the number back into the original units (dollars, not
dollars-squared), which is why it's often reported alongside MAE. **R²**
answers a different question entirely: "compared to just always guessing
the average, how much better is my model?" An R² of 0.81 (like you'll see
in Project 07) means the model explains about 81% of the variation in house
prices — a strong result for real-world messy data.

For classification, a single "accuracy" number can be dangerously
misleading, especially when categories are imbalanced. Imagine a churn
dataset where only 5% of customers actually churn. A lazy model that always
predicts "will not churn" would be 95% accurate — and completely useless,
since it never catches a single real churner. This is exactly the scenario
you'll encounter in Project 08, where about 26.5% of customers churn. That's
why we look at **precision** ("when I said churn, was I usually right?"),
**recall** ("of the customers who really did churn, how many did I catch?"),
and **F1** (a single number balancing both). The **confusion matrix** lays
all of this out visually: how many true churners were correctly caught, how
many were missed, and how many loyal customers were incorrectly flagged.

Finally, a single train/test split can sometimes be lucky or unlucky purely
by chance — what if the 20% you happened to hold out was unusually easy or
hard? **Cross-validation** solves this by splitting the data into several
different folds, training and testing multiple times with different
folds held out each time, and averaging the results — giving you a more
trustworthy estimate of real-world performance.

## Step-by-Step Instruction

1. Load a real dataset (the exact same California housing CSV used in
   Project 07 — real 1990-census data — loaded directly with pandas).
2. Split into train/test sets.
3. Train a regression model and compute MAE, MSE, RMSE, R².
4. Load a real classification dataset (breast cancer diagnostic data,
   built into scikit-learn, real and anonymized) and split it.
5. Train a classifier and compute accuracy, precision, recall, F1, and
   print a confusion matrix.
6. Run 5-fold cross-validation on the regression model and compare to the
   single-split result.

## Code

```python
"""
Lesson 3 demo: honest evaluation with train/test splits, real regression
and classification metrics, and cross-validation.
"""
import numpy as np
import pandas as pd
from sklearn.datasets import load_breast_cancer
from sklearn.linear_model import LinearRegression, LogisticRegression
from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.metrics import (
    mean_absolute_error, mean_squared_error, r2_score,
    accuracy_score, precision_score, recall_score, f1_score, confusion_matrix,
)

print("=" * 60)
print("REGRESSION EVALUATION")
print("=" * 60)

# Same real California housing data used in Project 07 (numeric columns only
# for this quick demo; Project 07 also encodes the categorical column).
housing = pd.read_csv(
    "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
)
housing["total_bedrooms"] = housing["total_bedrooms"].fillna(housing["total_bedrooms"].median())
feature_cols = ["longitude", "latitude", "housing_median_age", "total_rooms",
                 "total_bedrooms", "population", "households", "median_income"]
Xh = housing[feature_cols].values
yh = housing["median_house_value"].values

Xr_train, Xr_test, yr_train, yr_test = train_test_split(
    Xh, yh, test_size=0.2, random_state=42
)
reg = LinearRegression()
reg.fit(Xr_train, yr_train)
reg_preds = reg.predict(Xr_test)

mae = mean_absolute_error(yr_test, reg_preds)
mse = mean_squared_error(yr_test, reg_preds)
rmse = np.sqrt(mse)
r2 = r2_score(yr_test, reg_preds)
print(f"MAE:  ${mae:,.2f}")
print(f"MSE:  {mse:,.2f}")
print(f"RMSE: ${rmse:,.2f}")
print(f"R2:   {r2:.4f}")

# Cross-validation: 5 different train/test splits, averaged
cv_scores = cross_val_score(reg, Xh, yh, cv=5, scoring="r2")
print(f"\n5-fold cross-validated R2 scores: {np.round(cv_scores, 4)}")
print(f"Average cross-validated R2: {cv_scores.mean():.4f} "
      f"(compare to the single-split R2 of {r2:.4f} above)")

print("\n" + "=" * 60)
print("CLASSIFICATION EVALUATION")
print("=" * 60)

cancer = load_breast_cancer()
Xc_train, Xc_test, yc_train, yc_test = train_test_split(
    cancer.data, cancer.target, test_size=0.2, random_state=42, stratify=cancer.target
)
clf = LogisticRegression(max_iter=5000)
clf.fit(Xc_train, yc_train)
clf_preds = clf.predict(Xc_test)

acc = accuracy_score(yc_test, clf_preds)
prec = precision_score(yc_test, clf_preds)
rec = recall_score(yc_test, clf_preds)
f1 = f1_score(yc_test, clf_preds)
cm = confusion_matrix(yc_test, clf_preds)

print(f"Accuracy:  {acc:.4f}")
print(f"Precision: {prec:.4f}")
print(f"Recall:    {rec:.4f}")
print(f"F1 score:  {f1:.4f}")
print(f"Confusion matrix:\n{cm}")
```

## Code Explanation
- `fetch_california_housing()` loads the same real, built-in California
  housing dataset used in Project 07 directly from scikit-learn (no
  download needed for this classroom demo).
- We compute all four regression metrics on the *test* set only — never on
  training data.
- `cross_val_score(..., cv=5)` retrains the model 5 separate times on 5
  different train/test splits and returns 5 scores — averaging them gives a
  more trustworthy estimate than any single split.
- `stratify=cancer.target` in the classification split ensures the
  train and test sets have roughly the same proportion of each class — vital
  when classes are imbalanced (like in Project 08).
- `confusion_matrix` returns a 2x2 grid for binary classification:
  `[[true negatives, false positives], [false negatives, true positives]]`.

## Expected Output
On a real run: regression MAE ≈ $51,810, RMSE ≈ $71,133, R² ≈ 0.614 for
plain Linear Regression on the un-engineered numeric columns of the
California housing data (weaker than the engineered, tuned Random Forest in
Project 07 — that's the point: this lesson uses a simple baseline without
feature engineering). 5-fold cross-validated R² scores varied per fold
(roughly 0.52-0.69), averaging around 0.58 — noticeably different from the
single 80/20 split's 0.614, which is exactly why cross-validation exists:
one split can be luckier or unluckier than the average. The breast cancer
classifier achieved 96.5% accuracy on its real held-out test set.

## Guided Practice
1. Compare the single-split R² to the 5-fold cross-validated average R² —
   are they close? What would it mean if they were very different?
2. Remove `stratify=cancer.target` from the classification split and
   re-run several times with different `random_state` values. Does accuracy
   swing more without stratification?
3. Manually compute precision from the confusion matrix numbers
   (`TP / (TP + FP)`) and confirm it matches `precision_score`'s output.

## Hands-On Activity
Have students deliberately create an "unfair" evaluation by evaluating the
regression model on `Xr_train, yr_train` instead of the test set, and
compare that (falsely rosy) R² to the honest test-set R². Discuss the gap.

## Student Challenge
Using the breast cancer dataset, deliberately create an imbalanced subset
(e.g. keep all malignant cases but only 10% of benign cases) and observe
how much accuracy alone becomes misleading compared to precision/recall.

## Common Mistakes
- Evaluating on training data (always looks better than reality).
- Reporting only accuracy on an imbalanced classification problem.
- Forgetting `stratify` when splitting imbalanced classification data.
- Confusing MSE and RMSE (remember: RMSE is back in the original units,
  MSE is in squared units).

## Debugging Guidance
- `ConvergenceWarning` from `LogisticRegression` — raise `max_iter`.
- If cross-validation seems slow, reduce `cv` from 5 to 3 for quick
  iteration during development.
- If precision/recall/F1 throw an error, confirm your target is binary (0/1)
  — these functions default to binary classification behavior.

## Mini Quiz
1. Why is testing on training data misleading?
2. Which metric would you check if false negatives (missed churners) are
   especially costly to a business?
3. What does an R² of 0.0 mean?
4. What is the purpose of `stratify` in `train_test_split`?

### Answer Key
1. The model may have memorized quirks of the training data, so its score
   there doesn't reflect how it will perform on new, unseen data.
2. Recall — it measures how many of the true positive cases (real
   churners) the model actually caught.
3. The model is no better than always guessing the average value.
4. It keeps the proportion of each class the same in both the train and
   test sets, which matters a lot for imbalanced data.

## Lesson Recap
Always evaluate on a held-out test set, never on training data. Regression
uses MAE/MSE/RMSE/R²; classification uses accuracy/precision/recall/F1 and
the confusion matrix. Cross-validation gives a more robust estimate than a
single split by averaging across multiple splits.

## Homework / Extension
Using the breast cancer example, calculate what accuracy a "lazy" model
that always predicts the majority class would get. Compare it to the real
model's accuracy and discuss why accuracy alone can be a trap.

## Portfolio Project Connection
Project 07 will report real MAE/MSE/RMSE/R² exactly as computed in this
lesson. Project 08 will report real accuracy/precision/recall/F1/confusion
matrix on an imbalanced dataset (26.5% churn) — exactly the imbalance
scenario discussed above.
