# Course 15 · Lesson 7: Model Tuning and Improving Accuracy

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain what a hyperparameter is and how it differs from a learned
  parameter
- Use `GridSearchCV` and `RandomizedSearchCV` to systematically search for
  better hyperparameters
- Understand why tuning must always be validated with cross-validation, not
  a single split
- Compare an untuned model to a tuned model with real, measured metrics

## What You'll Learn
- The difference between parameters the model learns and hyperparameters
  you choose
- Grid search vs. randomized search, and when to use each
- How cross-validation inside a search protects against overfitting to one
  lucky split
- XGBoost as a strong, tunable model

## Why It Matters
Two data scientists can use the exact same algorithm and get very different
results depending on how well they tune it. Systematic tuning — not
guesswork — is what turns a decent model into a genuinely strong one, and
it's the difference between Project 07's untuned Random Forest (RMSE
≈$49,500) and its tuned version (RMSE ≈$49,188), and between Project 08's
untuned and tuned XGBoost models.

## Real-World Connection
Companies running ML in production regularly re-tune their models as new
data arrives, using automated search tools much like the ones in this
lesson, because a model tuned on last year's data can quietly become
stale.

## Key Terminology
- **Parameter**: a value the model learns automatically during training
  (e.g. the slope in linear regression, or a tree's split thresholds).
- **Hyperparameter**: a setting you choose *before* training that controls
  how the model learns (e.g. `n_estimators`, `max_depth`, `learning_rate`).
- **Grid search**: trying every combination from a specified set of
  hyperparameter values.
- **Randomized search**: trying a random sample of hyperparameter
  combinations, useful when the full grid would be too large to try
  exhaustively.
- **Cross-validation (inside tuning)**: each candidate hyperparameter
  combination is scored using multiple train/validation splits, not just
  one, to avoid picking a combination that got lucky on a single split.

## Concept Explanation

So far, every model you've trained had hyperparameters set to whatever
default (or whatever value we typed) without much justification —
`n_estimators=200`, `max_depth=6`, and so on. These are not learned by the
model; they're choices *you* make before training even starts, and
different choices can lead to meaningfully different results. The obvious
question becomes: how do you find good values, rather than guessing?

**Grid search** is the most literal answer: you specify a list of candidate
values for each hyperparameter (say, `n_estimators: [100, 200, 300]` and
`max_depth: [None, 10, 20]`), and the search tries *every possible
combination* of them, training and evaluating a full model each time. With
2 hyperparameters and 3 values each, that's 9 combinations; add a third
hyperparameter with 3 values and you're at 27. Grid search is thorough but
can get very slow very fast as the number of hyperparameters and values
grows — a problem known as the "combinatorial explosion."

**Randomized search** solves this by randomly sampling a fixed number of
combinations from the possible space (say, 10 random combinations out of
thousands of possible ones) instead of trying all of them. It won't
guarantee finding the single best combination, but in practice it usually
finds a very good one in a fraction of the time — which is why Project 07
uses `RandomizedSearchCV` and Project 08 uses a smaller, exhaustive
`GridSearchCV` (its search space is small enough to try completely).

Crucially, both search tools don't just train once per candidate and
check a single split — they use **cross-validation internally** (commonly
`cv=3` or `cv=5`), training and validating each candidate hyperparameter
combination on multiple different folds of the training data and averaging
the scores. This protects against picking a hyperparameter combination that
merely got a lucky validation split — a subtle but important detail: the
"best" combination reported is the one that performed well *consistently*
across multiple folds, not the one that got lucky once.

One more essential rule: this entire tuning process happens using only the
*training* data (further split internally into cross-validation folds).
The test set from Lesson 3 must stay completely untouched until the very
end, when you check the final tuned model's real performance exactly once.
If you tune based on test-set performance, you're back to the "seeing the
answer key" problem from Lesson 3 — just one level more subtle.

## Step-by-Step Instruction

1. Train an untuned `RandomForestRegressor` on real housing data as a
   baseline.
2. Define a hyperparameter grid and run `RandomizedSearchCV` with
   cross-validation.
3. Compare the tuned model's test RMSE to the untuned baseline's, using the
   test set exactly once, at the end.
4. Print the best hyperparameters found.

## Code

```python
"""
Lesson 7 demo: hyperparameter tuning with RandomizedSearchCV on real
California housing data.
"""
import numpy as np
import pandas as pd
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import RandomizedSearchCV, train_test_split
from sklearn.metrics import mean_squared_error, r2_score

housing = pd.read_csv(
    "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
)
housing["total_bedrooms"] = housing["total_bedrooms"].fillna(housing["total_bedrooms"].median())
feature_cols = ["longitude", "latitude", "housing_median_age", "total_rooms",
                 "total_bedrooms", "population", "households", "median_income"]
X = housing[feature_cols].values
y = housing["median_house_value"].values

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# --- Baseline: untuned model ---
baseline = RandomForestRegressor(n_estimators=200, random_state=42, n_jobs=-1)
baseline.fit(X_train, y_train)
baseline_rmse = np.sqrt(mean_squared_error(y_test, baseline.predict(X_test)))
print(f"Untuned baseline test RMSE: ${baseline_rmse:,.2f}")

# --- Tuning: search a range of hyperparameters using cross-validation ---
param_dist = {
    "n_estimators": [100, 200, 300],
    "max_depth": [None, 10, 20, 30],
    "min_samples_split": [2, 5, 10],
    "min_samples_leaf": [1, 2, 4],
    "max_features": ["sqrt", "log2", None],
}

search = RandomizedSearchCV(
    RandomForestRegressor(random_state=42, n_jobs=-1),
    param_distributions=param_dist,
    n_iter=10,          # try 10 random combinations out of 3*4*3*3*3 = 324 possible
    cv=3,               # 3-fold cross-validation for each candidate
    scoring="neg_mean_squared_error",
    random_state=42,
    n_jobs=-1,
)
search.fit(X_train, y_train)

print(f"\nBest hyperparameters found: {search.best_params_}")

tuned_model = search.best_estimator_
tuned_rmse = np.sqrt(mean_squared_error(y_test, tuned_model.predict(X_test)))
tuned_r2 = r2_score(y_test, tuned_model.predict(X_test))
print(f"Tuned model test RMSE: ${tuned_rmse:,.2f}")
print(f"Tuned model test R2:   {tuned_r2:.4f}")
print(f"\nImprovement in RMSE: ${baseline_rmse - tuned_rmse:+,.2f}")
```

## Code Explanation
- The baseline model uses one reasonable-looking but arbitrary
  hyperparameter set (`n_estimators=200`, everything else default).
- `param_dist` defines a *range* of values to consider for each
  hyperparameter — note this covers 324 total possible combinations
  (3×4×3×3×3).
- `n_iter=10` tells `RandomizedSearchCV` to only try 10 of those 324
  combinations, randomly sampled — far faster than a full grid search while
  still likely to find a strong combination.
- `cv=3` means every one of those 10 candidates is evaluated with 3-fold
  cross-validation internally, using only the training data.
- Only after the search is complete do we touch the test set — exactly
  once — to get an honest final number.

## Expected Output
This is the same exact setup used in Project 07: the untuned baseline
achieves test RMSE ≈ $49,499.52, and the tuned model (found via
`RandomizedSearchCV`) achieves test RMSE ≈ $49,188.04 and test R² ≈ 0.8154
— a modest but real improvement, with best parameters typically favoring
`max_features='sqrt'` and a moderate `min_samples_leaf`. (Exact numbers can
vary slightly by scikit-learn version but should be very close since
`random_state` is fixed throughout.)

## Guided Practice
1. Increase `n_iter` from 10 to 20. Does the tuned RMSE improve further?
   Is the extra runtime worth it?
2. Change `cv` from 3 to 5. Does the chosen "best" hyperparameter
   combination change?
3. Try `scoring="neg_mean_absolute_error"` instead of MSE-based scoring.
   Does the search choose a different best combination?

## Hands-On Activity
In small groups, have students each pick a different `n_iter` value (5,
10, 20, 30) and time how long the search takes alongside the resulting
tuned RMSE. Build a class table of "time vs. improvement" and discuss the
point of diminishing returns.

## Student Challenge
Replace `RandomForestRegressor` with `xgboost.XGBRegressor` in the search
(tune `n_estimators`, `max_depth`, and `learning_rate` instead). Does
XGBoost's tuned RMSE beat the Random Forest's?

## Common Mistakes
- Tuning using the test set instead of cross-validation on the training
  set (silently "cheating," inflating your reported performance).
- Making the hyperparameter grid so large that a full grid search takes
  hours — a randomized search is often the pragmatic choice.
- Forgetting `random_state` inside the search, making results
  non-reproducible between runs.

## Debugging Guidance
- If tuning is very slow, reduce `n_iter` and/or `cv`, or set `n_jobs=-1`
  to use all CPU cores.
- If the "tuned" model isn't actually better than the baseline, that's a
  real and valid result — not every dataset benefits hugely from tuning a
  given model; report it honestly rather than assuming something's broken.
- `search.best_params_` returning an unexpected combination is normal —
  it reflects genuine cross-validated performance on your training data,
  not necessarily what "should" work in theory.

## Mini Quiz
1. What is the difference between a parameter and a hyperparameter?
2. Why is randomized search often preferred over grid search for large
   search spaces?
3. Why must cross-validation (not a single split) be used while tuning?
4. When should you touch the test set during this whole process?

### Answer Key
1. A parameter is learned automatically by the model during training; a
   hyperparameter is a setting chosen before training that controls how
   learning happens.
2. It samples a manageable number of combinations instead of trying every
   possible one, which is far faster while still usually finding a strong
   combination.
3. To avoid picking a hyperparameter combination that merely got lucky on
   one particular split, rather than one that performs consistently well.
4. Only once, at the very end, after tuning is completely finished, to get
   an honest final performance estimate.

## Lesson Recap
Hyperparameters are choices you make before training, and systematically
searching for good ones (with cross-validation, using `GridSearchCV` or
`RandomizedSearchCV`) can meaningfully improve a model — as it did in
Project 07, improving Random Forest test RMSE from about $49,500 to about
$49,188.

## Homework / Extension
Look up scikit-learn's documentation for `RandomForestRegressor` and find
one hyperparameter not used in this lesson's `param_dist`. Write a sentence
explaining what it controls and whether you think it's worth including in a
future search.

## Portfolio Project Connection
This exact tuning pattern — baseline model, `param_dist`, `RandomizedSearchCV`
with `cv=3`, evaluate once on the test set — is the real code used in
Project 07's `train.py`. Project 08 uses the same idea with `GridSearchCV`
to tune its XGBoost classifier.
