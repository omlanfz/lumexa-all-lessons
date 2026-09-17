# Course 15 · Lesson 2: Types of ML — Regression vs Classification

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Distinguish between regression problems (predicting a number) and
  classification problems (predicting a category)
- Correctly identify which type a real-world problem is
- Train and compare a real regression model and a real classification model
  in the same lesson
- Understand why the choice of model type depends entirely on what kind of
  label you're predicting

## What You'll Learn
- The formal difference between regression and classification
- How to recognize continuous labels vs. categorical labels
- Two real scikit-learn models: `LinearRegression` and
  `LogisticRegression` (used for classification, despite the name)
- Different evaluation approaches for each type (a preview of Lesson 3)

## Why It Matters
Every ML project starts with one crucial decision: is the answer I'm trying
to predict a *number* or a *category*? Get this wrong and the entire rest of
the project — the model choice, the evaluation metric, everything — breaks
down. This decision is the very first thing you'll make in each of your
three portfolio projects.

## Real-World Connection
- **Regression** examples: predicting a house's price (Project 07),
  predicting tomorrow's temperature, predicting how many minutes a delivery
  will take.
- **Classification** examples: predicting whether a customer will churn
  "Yes/No" (Project 08), detecting whether an email is spam, diagnosing
  whether an X-ray shows a fracture.

## Key Terminology
- **Regression**: predicting a continuous numeric value (e.g. price, age,
  temperature) — the answer can be almost any number, including decimals.
- **Classification**: predicting a category out of a fixed, limited set of
  options (e.g. "Yes"/"No", "Cat"/"Dog"/"Bird", "Spam"/"Not Spam").
- **Binary classification**: classification with exactly two possible
  categories.
- **Multiclass classification**: classification with three or more possible
  categories.
- **Continuous variable**: a number that can take any value in a range
  (e.g. 3.7, 3.71, 3.712...).
- **Categorical variable**: a value from a fixed set of labels.

## Concept Explanation

The single most important question to ask before building any ML model is:
"What am I actually trying to predict, and what *kind* of thing is it?"

If the answer is a number that could sensibly be almost anything within a
range — a price in dollars, a temperature in degrees, a delivery time in
minutes — that's a **regression** problem. It doesn't matter if the model
predicts $340,127.42 for a house when the real answer was $338,900; being
close counts, and the error is naturally measured as a distance (how far off
in dollars, degrees, or minutes).

If instead the answer is one of a small, fixed set of labels — "will churn"
or "won't churn," "spam" or "not spam," "cat," "dog," or "bird" — that's a
**classification** problem. There's no natural "closeness" between "cat" and
"dog" the way there is between $340,000 and $338,900. Either the prediction
matches the true category or it doesn't (or, more usefully, we look at the
model's estimated *probability* for each category).

Here's a trick that confuses almost everyone at first: scikit-learn's
`LogisticRegression` is a **classification** algorithm, despite having
"regression" in its name! Historically, logistic regression works by first
computing a number (like a regular regression would), and then squashing
that number into a probability between 0 and 1 using a mathematical curve,
and finally picking whichever category has the higher probability. The name
stuck from the underlying math, but you use it exactly like a classifier —
you'll do this yourself in Project 08. Don't let the name mislead you into
choosing it for a numeric prediction problem.

A good mental test: could the correct answer ever reasonably be something
between two of your candidate answers? "Between $200,000 and $210,000
there's a real house worth $205,000" — yes, that's regression. "Between
'churned' and 'didn't churn' there's no real customer who's half-churned" —
no, that's classification. Once you know which type of problem you have,
the rest of your toolkit — which scikit-learn class to import, which
metric to compute, even how to think about the data — falls into place.

## Step-by-Step Instruction

1. We'll use two small real datasets that ship with scikit-learn: the
   diabetes dataset again (regression — predicting a numeric disease
   progression score) and the real Iris flower dataset (classification —
   predicting which of 3 species a flower is, from real petal/sepal
   measurements).
2. We'll train a `LinearRegression` model on the diabetes data and observe
   that its predictions are continuous numbers.
3. We'll train a `LogisticRegression` model on the Iris data and observe
   that its predictions are always one of exactly 3 categories.
4. We'll compare the *shape* of the output between the two.

## Code

```python
"""
Lesson 2 demo: regression vs classification, side by side, on two real
built-in scikit-learn datasets.
"""
from sklearn.datasets import load_diabetes, load_iris
from sklearn.linear_model import LinearRegression, LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_absolute_error, accuracy_score

print("=" * 60)
print("REGRESSION EXAMPLE: predicting a continuous number")
print("=" * 60)

diabetes = load_diabetes()
Xr_train, Xr_test, yr_train, yr_test = train_test_split(
    diabetes.data, diabetes.target, test_size=0.2, random_state=42
)
reg_model = LinearRegression()
reg_model.fit(Xr_train, yr_train)
reg_preds = reg_model.predict(Xr_test)

print("Sample real disease-progression predictions (regression output):")
for p in reg_preds[:5]:
    print(f"  {p:.2f}")
print(f"Mean Absolute Error: {mean_absolute_error(yr_test, reg_preds):.2f}")
print("Notice: these are continuous numbers, not fixed categories.\n")

print("=" * 60)
print("CLASSIFICATION EXAMPLE: predicting one of a fixed set of categories")
print("=" * 60)

iris = load_iris()
Xc_train, Xc_test, yc_train, yc_test = train_test_split(
    iris.data, iris.target, test_size=0.2, random_state=42
)
clf_model = LogisticRegression(max_iter=200)
clf_model.fit(Xc_train, yc_train)
clf_preds = clf_model.predict(Xc_test)

species_names = iris.target_names
print("Sample real species predictions (classification output):")
for real, pred in zip(yc_test[:5], clf_preds[:5]):
    print(f"  Real: {species_names[real]:<10} Predicted: {species_names[pred]}")
print(f"Accuracy: {accuracy_score(yc_test, clf_preds):.2%}")
print("Notice: predictions are always exactly one of the 3 known species.")

# Also show the *probabilities* behind one classification prediction
proba = clf_model.predict_proba(Xc_test[:1])
print(f"\nProbability breakdown for the first test flower: "
      f"{dict(zip(species_names, proba[0].round(3)))}")
```

## Code Explanation
- The regression half uses `load_diabetes()` and `LinearRegression`, the
  same pattern from Lesson 1 — its output is a list of continuous numbers.
- The classification half uses `load_iris()`, a real, classic dataset of
  150 real flower measurements labeled with one of 3 species.
  `LogisticRegression` (a classifier, despite its name) is trained the same
  way with `.fit()`, but `.predict()` returns one of exactly 3 category
  codes (0, 1, or 2), which we map back to real species names.
- `predict_proba()` reveals *why* classification models can do more than
  just pick a winner — they estimate a probability for every possible
  category, which is often more useful than the single predicted label.
- `accuracy_score` (fraction of correct predictions) is an appropriate
  metric for classification but would make no sense for the regression
  half — you'll see why in Lesson 3.

## Expected Output
The script prints 5 sample regression predictions (numeric, similar in
range to Lesson 1, MAE around 42), followed by 5 sample classification
predictions where "Real" and "Predicted" should match for most or all of
the 5 shown, an accuracy typically at or near 100% on this easy dataset, and
a probability breakdown for one flower showing one species with a very high
probability (often close to 1.0) and the other two near 0.

## Guided Practice
1. Change `iris.target_names` printing to also print the raw numeric label
   (0, 1, 2). Discuss: why does the model output numbers internally instead
   of strings?
2. Try feeding the diabetes `y` values into `LogisticRegression` (it will
   error or behave strangely, since those are continuous, not categories).
   Discuss what went wrong and why.
3. Look at the probability breakdown for 3 different test flowers. Are they
   always confident, or is it sometimes close between two species?

## Hands-On Activity
Give each pair of students a short real-world scenario on a card (e.g.
"predict how many minutes until the bus arrives," "predict whether an
image contains a stop sign," "predict a student's next test score,"
"predict whether a tweet is positive or negative"). Have them sort the
cards into "Regression" and "Classification" piles and justify each choice
out loud.

## Student Challenge
Load `load_wine()` from `sklearn.datasets` (another real built-in dataset,
178 real wine samples, 3 classes). Train a `LogisticRegression` classifier
on it the same way as the Iris example, and report its accuracy. Is
classification harder or easier on this dataset than on Iris? Why might
that be?

## Common Mistakes
- **Using accuracy on a regression problem**: accuracy only makes sense
  when there's a finite set of exact categories to match.
- **Assuming "regression" always means classification because of the name
  `LogisticRegression`**: reinforce that the *name* is historical, the
  *use* is classification.
- **Forgetting to map numeric class codes back to real names**: this leads
  to confusing output like "Predicted: 2" instead of "Predicted: virginica".

## Debugging Guidance
- If `LogisticRegression` throws a convergence warning, increase
  `max_iter` (we used 200 above; some machines may need more).
- If accuracy looks suspiciously perfect, double check you split data with
  `train_test_split` and didn't test on training data.
- `IndexError` on `species_names[pred]` usually means `pred` isn't an
  integer — check you didn't accidentally feed strings into the model.

## Mini Quiz
1. Is predicting the number of goals scored in a soccer match regression or
   classification?
2. Is predicting whether an image shows a "cat" or "dog" regression or
   classification?
3. What does `LogisticRegression` actually do, despite its name?
4. Name one metric appropriate for classification and one appropriate for
   regression.

### Answer Key
1. Regression — the number of goals is a count that could reasonably be
   0, 1, 2, 3... (any non-negative number).
2. Classification — there's a fixed set of category labels ("cat" or
   "dog").
3. It's a classification algorithm: it computes probabilities for each
   category and picks the most likely one, despite the "regression" name.
4. Classification: accuracy (or precision/recall/F1). Regression: MAE, MSE,
   RMSE, or R².

## Lesson Recap
Regression predicts continuous numbers; classification predicts one of a
fixed set of categories. The type of your label determines which
scikit-learn tools and evaluation metrics you should use — get this
decision right first, before anything else.

## Homework / Extension
For each of the three portfolio projects in this course, write one sentence
stating whether it's regression or classification and why:
Project 07 (House Price Predictor), Project 08 (Customer Churn Predictor),
Project 09 (Movie Recommendation Engine — this one is a bit of a special
case; think about what number or category it's actually predicting).

## Portfolio Project Connection
Project 07 is a pure regression problem (predicting a numeric house price).
Project 08 is a pure binary classification problem (predicting "Yes"/"No"
churn). This lesson's core distinction is the very first design decision
you'll make in both of those projects.
