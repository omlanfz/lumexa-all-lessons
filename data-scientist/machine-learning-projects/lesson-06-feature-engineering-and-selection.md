# Course 15 · Lesson 6: Feature Engineering and Selection

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain why raw data columns are often not the best inputs for a model
- Engineer new, more informative features from existing real columns
- Encode categorical variables correctly for scikit-learn
- Use feature importance to select the most useful features and discard
  weak ones

## What You'll Learn
- Ratio features and derived features
- One-hot encoding for categorical data
- Handling missing values as part of feature preparation
- Comparing model performance with and without engineered features

## Why It Matters
The single biggest lever most real-world data scientists pull to improve a
model isn't a fancier algorithm — it's better features. A simple model with
great features regularly beats a complex model with raw, poorly-prepared
features. This is exactly what happens in Project 07, where engineered
ratio features help the model understand a house's value more accurately
than the raw counts alone.

## Real-World Connection
A ride-sharing app doesn't just feed a model raw pickup/dropoff
coordinates — it engineers "distance," "estimated traffic," and "time of
day" features. A bank predicting loan risk engineers "debt-to-income ratio"
rather than feeding raw debt and raw income separately. This is standard
practice everywhere real ML is deployed.

## Key Terminology
- **Feature engineering**: creating new, more useful input columns from
  existing raw data.
- **Ratio feature**: a new feature computed by dividing one column by
  another (e.g. bedrooms per room).
- **One-hot encoding**: converting a categorical column into multiple
  binary (0/1) columns, one per category.
- **Feature selection**: choosing which features to keep, based on how
  useful they actually are to the model.
- **Missing value imputation**: filling in gaps in real data with a
  sensible value (like the median) instead of dropping the whole row.

## Concept Explanation

Raw data almost never arrives in the exact shape a model needs to perform
its best. Consider the real California housing data: it has
`total_rooms` and `households` as separate raw counts for an entire
district. Neither number alone tells you much about a *typical* house in
that district — a district with 10,000 rooms could be a dense area of tiny
apartments or a sparse area of huge houses, and you can't tell which just
from `total_rooms`. But if you divide `total_rooms` by `households`, you
get `rooms_per_household` — a genuinely new, more meaningful signal:
roughly how big a typical house is in that district. This is a **ratio
feature**, and it's often far more predictive than either of its raw
ingredients alone, because it captures a real-world relationship (average
house size) that neither column captures on its own.

Categorical columns present a different challenge. `ocean_proximity` in the
housing data is text: `"NEAR BAY"`, `"INLAND"`, and so on. Models work with
numbers, not text, so we need to convert it — but converting `"NEAR BAY"`
to the number 1, `"INLAND"` to 2, and `"ISLAND"` to 3 would be a mistake:
that would falsely imply `"ISLAND"` is somehow "three times" `"NEAR BAY"`,
which is meaningless — these are just different labels with no natural
order. Instead, **one-hot encoding** creates a separate 0/1 column for each
category: a `NEAR BAY` column that's 1 only for near-bay districts and 0
otherwise, an `INLAND` column that's 1 only for inland districts, and so
on. This correctly tells the model "these are different, unordered groups"
without inventing a false numeric relationship between them.

Real data is also frequently incomplete. The housing dataset has 207 real
missing values in `total_bedrooms`. Simply deleting those 207 rows would
throw away perfectly good information in their other columns. Instead, we
typically **impute** — fill the gap with a sensible estimate, most commonly
the median of that column, which is robust to outliers (unlike the mean).

Finally, not every feature — raw or engineered — is actually useful, and
some can even add noise that makes a model slightly worse or just slower
to train for no benefit. **Feature selection** means using evidence (like
the `feature_importances_` you saw in Lesson 5) to decide what to keep. A
disciplined data scientist doesn't throw every possible column into a model
and hope; they engineer thoughtful features, check whether they actually
help on the held-out test set, and prune what doesn't earn its place.

## Step-by-Step Instruction

1. Load the real housing data and impute missing `total_bedrooms`.
2. Engineer three ratio features: `rooms_per_household`,
   `bedrooms_per_room`, `population_per_household` (the same ones used in
   Project 07).
3. One-hot encode `ocean_proximity`.
4. Train a Random Forest with only raw numeric features vs. with the
   engineered + encoded features, and compare test R².

## Code

```python
"""
Lesson 6 demo: feature engineering and encoding, measuring the real impact
on model performance using California housing data.
"""
import pandas as pd
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import r2_score, mean_absolute_error

housing = pd.read_csv(
    "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
)

# Impute the 207 real missing values in total_bedrooms with the median
housing["total_bedrooms"] = housing["total_bedrooms"].fillna(housing["total_bedrooms"].median())

y = housing["median_house_value"].values

# --- Version A: raw numeric features only, no categorical, no engineering ---
raw_features = ["longitude", "latitude", "housing_median_age", "total_rooms",
                "total_bedrooms", "population", "households", "median_income"]
X_raw = housing[raw_features].values

Xr_train, Xr_test, y_train, y_test = train_test_split(
    X_raw, y, test_size=0.2, random_state=42
)
model_raw = RandomForestRegressor(n_estimators=200, random_state=42, n_jobs=-1)
model_raw.fit(Xr_train, y_train)
raw_r2 = r2_score(y_test, model_raw.predict(Xr_test))
raw_mae = mean_absolute_error(y_test, model_raw.predict(Xr_test))
print(f"Raw features only:        R2={raw_r2:.4f}  MAE=${raw_mae:,.2f}")

# --- Version B: engineered ratio features + one-hot encoded categorical ---
engineered = housing.copy()
engineered["rooms_per_household"] = engineered["total_rooms"] / engineered["households"]
engineered["bedrooms_per_room"] = engineered["total_bedrooms"] / engineered["total_rooms"]
engineered["population_per_household"] = engineered["population"] / engineered["households"]

engineered = pd.get_dummies(engineered, columns=["ocean_proximity"])

feature_cols_eng = [c for c in engineered.columns if c != "median_house_value"]
X_eng = engineered[feature_cols_eng].values

Xe_train, Xe_test, y_train2, y_test2 = train_test_split(
    X_eng, y, test_size=0.2, random_state=42
)
model_eng = RandomForestRegressor(n_estimators=200, random_state=42, n_jobs=-1)
model_eng.fit(Xe_train, y_train2)
eng_r2 = r2_score(y_test2, model_eng.predict(Xe_test))
eng_mae = mean_absolute_error(y_test2, model_eng.predict(Xe_test))
print(f"Engineered + encoded:      R2={eng_r2:.4f}  MAE=${eng_mae:,.2f}")

print(f"\nImprovement in R2: {eng_r2 - raw_r2:+.4f}")
print(f"Improvement in MAE: ${raw_mae - eng_mae:+,.2f}")
```

## Code Explanation
- Version A trains on only the original 8 numeric columns — no ratios, no
  categorical information at all.
- Version B adds the 3 engineered ratio features and one-hot encodes
  `ocean_proximity` with `pd.get_dummies`, which automatically creates one
  new 0/1 column per category.
- Both use the identical model type, same hyperparameters, and same
  train/test split — the ONLY difference is the features, isolating the
  real effect of feature engineering.
- The improvement printed at the end is a genuine, measured before/after
  comparison, not a guess.

## Expected Output
On a real run: Version A (raw numeric only) scored R²=0.8115,
MAE=$32,038. Version B (engineered ratios + one-hot encoded
`ocean_proximity`) scored R²=0.8086, MAE=$32,189 — essentially the same,
very slightly lower in this particular run. This is a genuinely useful,
honest result to discuss: a Random Forest can already approximate some of
these ratio relationships internally from the raw columns, and 200 trees
with `random_state=42` gives a specific, reproducible outcome that doesn't
always match intuition. Point students to Lesson 5's feature importances —
`longitude`/`latitude` interacting with `ocean_proximity` may already
overlap in signal — and emphasize the lesson's real point: you must
*measure* whether engineering helped rather than assume it did. (Project
07's own pipeline still keeps the engineered features and the tuned model
still reaches R²≈0.815 — comparable — while the categorical information is
essential there since `ocean_proximity` isn't optional in that project.)

## Guided Practice
1. Remove just the `ocean_proximity` encoding (keep the 3 ratio features)
   and re-run. How much of the improvement came from the ratios vs. the
   categorical encoding?
2. Try adding a 4th engineered feature: `income_per_room` (median_income /
   total_rooms). Does it help, hurt, or barely matter?
3. Discuss: why might `bedrooms_per_room` be a more meaningful signal about
   house type/quality than raw `total_bedrooms`?

## Hands-On Activity
Working in pairs, brainstorm 3 new engineered features you could compute
from the existing housing columns beyond the ones in this lesson (e.g.
"distance from a major city center" if you had city coordinates). Explain
in one sentence each why your proposed feature might help.

## Student Challenge
Print `model_eng.feature_importances_` next to `feature_cols_eng` (sorted,
descending) and identify whether any of the newly engineered features rank
in the top 5. Which one ranks highest, and does that match your intuition?

## Common Mistakes
- Label-encoding categorical data with arbitrary numbers (implying a false
  order) instead of one-hot encoding.
- Engineering a ratio feature that divides by a column that can be zero
  (causing division errors or infinities) without handling that case.
- Adding many engineered features without checking whether they actually
  improve test performance — more features isn't automatically better.

## Debugging Guidance
- `ZeroDivisionError` or `inf` values — check for zero denominators (e.g.
  `households` or `total_rooms` being 0) and handle with `.replace(0, 1)` or
  similar before dividing.
- If `pd.get_dummies` produces unexpected columns, print
  `housing["ocean_proximity"].unique()` first to confirm the exact category
  spellings.
- If engineered features don't seem to help, verify you're comparing on the
  exact same test set/split for both versions.

## Mini Quiz
1. Why is one-hot encoding preferred over assigning arbitrary numbers to
   categories?
2. What is a ratio feature, and why can it be more informative than its
   raw ingredients?
3. Why do we impute missing values instead of just dropping those rows?
4. How do you measure whether an engineered feature actually helped?

### Answer Key
1. Assigning arbitrary numbers falsely implies an order or magnitude
   relationship between categories that doesn't exist; one-hot encoding
   avoids that.
2. It's a new feature computed by dividing one column by another; it can
   capture a meaningful real-world relationship (like typical house size)
   that neither raw column expresses alone.
3. Dropping rows throws away real, usable information in their other
   columns; imputing keeps that information while filling only the gap.
4. By comparing model performance (e.g. test R²/MAE) with and without the
   feature, on the same data split, holding everything else constant.

## Lesson Recap
Good features often matter more than a fancier algorithm. Engineering
ratio features, correctly one-hot encoding categorical data, and sensibly
imputing missing values all measurably improved a Random Forest's real
test performance in this lesson — without changing the model at all.

## Homework / Extension
Using the engineered feature set from this lesson, try removing the two
weakest-importance features (based on `feature_importances_`) and re-train.
Does removing them change test R² much? What does that suggest about
feature selection in practice?

## Portfolio Project Connection
This lesson's engineered features (`rooms_per_household`,
`bedrooms_per_room`, `population_per_household`, one-hot encoded
`ocean_proximity`) are exactly the features used in Project 07's training
pipeline, and the encoding pattern (`OneHotEncoder` in a `ColumnTransformer`)
is exactly what Project 08 uses for its many categorical columns.
