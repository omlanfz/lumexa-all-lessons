# Course 15 · Lesson 5: Decision Trees and Random Forests

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain how a decision tree makes a prediction using a series of yes/no
  questions
- Understand why a single deep tree tends to overfit
- Explain how a random forest combines many trees to make a better,
  more stable prediction
- Train, visualize, and evaluate real decision tree and random forest
  models with scikit-learn

## What You'll Learn
- How trees split data at each node
- Overfitting in trees (and how `max_depth` controls it)
- The "wisdom of crowds" idea behind random forests
- Feature importance from a trained forest

## Why It Matters
Random forests are one of the most reliable, widely used "off-the-shelf"
models in real machine learning work — they handle messy real data well,
rarely need much tuning to get decent results, and are exactly what you'll
train in Projects 07 and 08.

## Real-World Connection
Random forests are used in fraud detection, medical diagnosis support
tools, real estate valuation (Project 07), and customer churn prediction
(Project 08) — anywhere you have real, somewhat messy tabular data and need
a strong, dependable model without excessive tuning.

## Key Terminology
- **Decision tree**: a model that makes predictions by asking a sequence
  of yes/no (or threshold) questions about the features, branching to a
  final answer.
- **Node**: one question/split point in the tree.
- **Leaf**: an endpoint of the tree that holds the final prediction.
- **Overfitting**: memorizing quirks of the training data instead of
  learning generalizable patterns (a single deep tree is very prone to this).
- **Ensemble**: a model built from combining many smaller models.
- **Random forest**: an ensemble of many decision trees, each trained on a
  random subset of the data and features, whose predictions are averaged
  (regression) or voted on (classification).
- **Feature importance**: a score showing how much each feature contributed
  to the forest's decisions overall.

## Concept Explanation

A decision tree makes predictions the same way you might play a guessing
game: "Is the income above $50,000? If yes, is the location near the coast?
If yes, predict a high price. If no, predict a medium price." Each question
is a **node**, and each final answer is a **leaf**. The tree *learns* which
questions to ask, and at what threshold, by looking at the real training
data and finding the splits that best separate examples with different
outcomes.

The appeal of a single tree is that it's easy to understand and explain —
you can literally draw it out and walk someone through the logic. The
problem is that if you let a tree grow deep enough, it can create a
question so specific that it perfectly separates every single training
example, essentially memorizing the training set rather than learning
general patterns. This is a classic case of **overfitting**: the tree will
look flawless on training data and can perform noticeably worse on new
data, because it learned quirks and noise specific to the examples it saw,
not the underlying real-world pattern. This is why we always limit tree
depth (`max_depth`) or the minimum examples required to split further
(`min_samples_split`), and — critically — why we always evaluate on a
held-out test set (Lesson 3) to catch overfitting when it happens.

**Random forests** solve the overfitting problem with a clever trick often
summarized as "the wisdom of crowds." Instead of training one deep,
overconfident tree, a random forest trains *many* trees (often hundreds),
and gives each individual tree a randomly chosen subset of the training
examples and a randomly restricted subset of features to consider at each
split. Each individual tree still might overfit its own particular random
subset in its own particular way — but because each tree's mistakes tend to
be somewhat different and somewhat random, averaging all their predictions
together (for regression) or having them vote (for classification) cancels
out a lot of that individual noise. The result is usually far more
accurate and far more stable than any single tree — exactly what you saw in
Project 07, where the Random Forest dramatically outperformed plain Linear
Regression (RMSE dropped from about $69,127 to about $49,188).

A useful side benefit of random forests is **feature importance** — after
training, the forest can tell you, across all its trees and all their
splits, which features tended to be most useful for making good
predictions. This gives real, data-driven insight into what actually drives
the outcome (e.g. discovering that `median_income` matters far more to
house prices than `housing_median_age` does) — insight you can use to guide
further feature engineering, which is exactly the subject of Lesson 6.

## Step-by-Step Instruction

1. Train a single `DecisionTreeRegressor` on real California housing data
   with no depth limit, and observe it fits training data almost perfectly
   but does much worse on the test set (overfitting in action).
2. Train the same tree with a sensible `max_depth` and see the gap shrink.
3. Train a `RandomForestRegressor` and compare all three.
4. Inspect `feature_importances_` from the forest.

## Code

```python
"""
Lesson 5 demo: decision trees, overfitting, and random forests on real
California housing data.
"""
import numpy as np
import pandas as pd
from sklearn.ensemble import RandomForestRegressor
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_absolute_error, r2_score
from sklearn.tree import DecisionTreeRegressor

housing = pd.read_csv(
    "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
)
housing["total_bedrooms"] = housing["total_bedrooms"].fillna(housing["total_bedrooms"].median())
feature_cols = ["longitude", "latitude", "housing_median_age", "total_rooms",
                 "total_bedrooms", "population", "households", "median_income"]
X = housing[feature_cols].values
y = housing["median_house_value"].values

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)


def report(name, model):
    model.fit(X_train, y_train)
    train_pred = model.predict(X_train)
    test_pred = model.predict(X_test)
    train_r2 = r2_score(y_train, train_pred)
    test_r2 = r2_score(y_test, test_pred)
    test_mae = mean_absolute_error(y_test, test_pred)
    print(f"[{name}] Train R2={train_r2:.4f}  Test R2={test_r2:.4f}  Test MAE=${test_mae:,.2f}")
    return model


print("Unlimited-depth single tree (prone to overfitting):")
deep_tree = report("Deep Tree", DecisionTreeRegressor(random_state=42))

print("\nDepth-limited single tree:")
shallow_tree = report("Shallow Tree (max_depth=6)",
                       DecisionTreeRegressor(max_depth=6, random_state=42))

print("\nRandom forest (200 trees):")
forest = report("Random Forest",
                 RandomForestRegressor(n_estimators=200, random_state=42, n_jobs=-1))

print("\nTop 5 most important features (from the random forest):")
importances = sorted(zip(feature_cols, forest.feature_importances_),
                      key=lambda t: -t[1])
for name, score in importances[:5]:
    print(f"  {name:<20} {score:.4f}")
```

## Code Explanation
- The unlimited-depth `DecisionTreeRegressor` will show a huge gap between
  train R² (often close to 1.0 — near-perfect on training data) and test R²
  (much lower) — direct visible evidence of overfitting.
- Limiting `max_depth=6` shrinks that gap, trading a little training
  performance for much better generalization.
- `RandomForestRegressor` trains 200 trees on random subsets and averages
  their predictions — it should beat both single trees on test performance,
  and its train/test gap should be much smaller than the deep single tree's.
- `feature_importances_` gives one real, learned number per feature,
  summed to 1.0 across all features, showing what the forest relied on most.

## Expected Output
On a real run: the deep single tree scores train R²=1.0000 but test
R²=0.6275 — a huge gap, clear overfitting. The depth-limited tree
(`max_depth=6`) narrows the gap (train R²=0.6584, test R²=0.6077) but its
test score is barely better, and its MAE ($49,517) is actually worse than
the deep tree's ($44,299) here — a good discussion point: limiting depth
isn't automatically better on every metric. The random forest wins clearly
(train R²=0.9752, test R²=0.8115, test MAE=$32,038), beating both single
trees with a much smaller train/test gap. `median_income` is by far the
most important feature (importance ≈0.52), followed by `longitude`
(≈0.156) and `latitude` (≈0.147).

## Guided Practice
1. Try `max_depth=3`, `max_depth=10`, and unlimited on the single tree.
   Record test R² for each — where's the sweet spot?
2. Change the forest's `n_estimators` from 200 down to 10. Does test
   performance get noticeably worse or about the same?
3. Discuss: why might `n_estimators=10` still beat a single tree, even
   though it's a much smaller forest than 200?

## Hands-On Activity
Have students draw (on paper) a tiny 2-level decision tree by hand for a
simplified scenario: "Predict if a snack is 'healthy' or 'not healthy'
based on sugar content and whether it's fried." Then discuss how a computer
would find the best threshold automatically instead of a human guessing it.

## Student Challenge
Add `ocean_proximity` (one-hot encoded, as in Project 07) as additional
features and retrain the random forest. Does test R² improve? Print the new
top-5 feature importances — does `ocean_proximity` show up?

## Common Mistakes
- Judging a tree only by training performance (always misleadingly high for
  deep trees).
- Assuming more trees in a forest always helps unboundedly — returns
  diminish and training time grows.
- Forgetting `random_state` when comparing configurations, making results
  hard to compare fairly.

## Debugging Guidance
- If training feels slow, reduce `n_estimators` while iterating, then raise
  it for your final run.
- If feature importances are all similar (no clear winner), check that
  your features actually vary meaningfully in the data.
- If the deep tree doesn't overfit as expected, confirm you didn't
  accidentally set `max_depth` on it.

## Mini Quiz
1. What causes a single deep decision tree to overfit?
2. How does a random forest reduce overfitting compared to one tree?
3. What does a high feature importance score tell you?
4. Name one hyperparameter that controls tree depth/complexity.

### Answer Key
1. It keeps splitting until it can separate even the smallest quirks of the
   training data, effectively memorizing it rather than learning general
   patterns.
2. It averages predictions from many trees, each trained on a different
   random subset of data/features, so individual trees' overfitting errors
   tend to cancel out.
3. That feature was frequently useful across the forest's many trees for
   making accurate splits/predictions.
4. `max_depth` (also acceptable: `min_samples_split`, `min_samples_leaf`).

## Lesson Recap
A decision tree predicts via a sequence of learned yes/no questions, but a
single deep tree easily overfits. A random forest trains many trees on
random subsets of data and features and averages their predictions,
producing a far more accurate and stable model — exactly the model used
in Project 07 and considered in Project 08.

## Homework / Extension
Using the same random forest, try `RandomForestRegressor(max_depth=10, ...)`
vs. the default (no max depth) with 200 trees each. Does limiting depth
help, hurt, or barely matter for a forest (compared to how much it mattered
for a single tree)? Write two sentences explaining what you observe.

## Portfolio Project Connection
Project 07's best model is a tuned Random Forest (test RMSE ≈ $49,188,
R² ≈ 0.815) — directly built on everything in this lesson. Project 08 also
trains a Random Forest classifier as one of its candidate models.
