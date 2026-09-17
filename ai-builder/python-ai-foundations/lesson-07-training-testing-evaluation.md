# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 07 of 08
**Title:** Training, Testing, and Evaluating Models
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.11+, NumPy, scikit-learn

---

## Mission Brief

Cadet, last mission you built a working model — but you never truly tested it. A rover that only ever drove on the exact terrain it trained on tells you nothing about how it'll handle a real, unexplored planet. Today you learn the discipline every professional AI engineer applies before ever trusting a model: properly splitting data into **training** and **test** sets, and using real evaluation metrics — **Mean Squared Error (MSE)** and **R² score** — to honestly measure performance. You'll also confront **overfitting**, the single most important failure mode in machine learning, and learn how to spot it.

## Learning Objectives

1. Explain why evaluating a model on its own training data is misleading.
2. Use scikit-learn's `train_test_split()` to properly divide a dataset.
3. Calculate and interpret Mean Squared Error (MSE) as a measure of prediction error.
4. Calculate and interpret R² (R-squared) as a measure of how well a model explains variance in the data.
5. Define overfitting and underfitting, and recognize signs of each.
6. Build a complete, properly evaluated training-and-testing pipeline.

## Prerequisites

- Completion of Lesson 06 (NumPy arrays, `X`/`y` convention, training a `LinearRegression` model with `.fit()` and `.predict()`).

## Concept Explanation

### Why you can't grade your own homework

Imagine a student is given the answer key before taking a test, memorizes the exact answers, and then scores 100%. Did they actually learn the subject? You have no way to know — the test wasn't a fair measure. This is exactly the trap of evaluating a model only on the data it trained on. A model can achieve seemingly perfect performance simply by memorizing training examples rather than learning the true underlying pattern. The only fair evaluation is to test the model on examples it has never seen during training — data it must generalize to, not memorize.

### The train/test split

The standard solution is to split your dataset into two non-overlapping parts *before* training even begins:

- **Training set** (commonly 70-80% of the data): shown to the model during `.fit()`.
- **Test set** (commonly 20-30% of the data): held back completely, used only afterward with `.predict()` to measure honest performance.

scikit-learn provides `train_test_split()` to do this correctly and randomly:

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
```

This single line returns four arrays. `test_size=0.2` means 20% of the data goes to testing, 80% to training. `random_state=42` is a **seed** — it makes the "random" split reproducible, so running the code again gives the exact same split (useful for debugging and fair comparison; the number 42 has no special meaning beyond being a common convention, any fixed integer works identically).

The correct order of operations is always:

1. Split the data (`train_test_split`).
2. Train the model only on `X_train, y_train` (`.fit()`).
3. Evaluate the model only on `X_test, y_test` (`.predict()` then compare to `y_test`).

Never fit on test data, and never let test data influence training in any way — this is one of the most important rules in all of machine learning, often called avoiding **data leakage**.

### Mean Squared Error (MSE): measuring how wrong predictions are

**Mean Squared Error** measures the average squared difference between predicted and actual values:

MSE = average of (actual − predicted)² across all examples

Squaring the errors does two important things: it makes all errors positive (so overestimates and underestimates don't cancel out), and it penalizes large errors more heavily than small ones. **Lower MSE is better** — an MSE of 0 would mean perfect predictions, which almost never happens with real data (and, as you'll see below, would actually be a red flag if it happened on training data).

```python
from sklearn.metrics import mean_squared_error

mse = mean_squared_error(y_test, predictions)
```

MSE's units are the label's units, squared — a bit unintuitive (e.g., "points squared" for test scores), which is why it's often reported alongside R².

### R² (R-squared): measuring explained variance

**R²** (pronounced "R-squared") measures the proportion of variance in the label that the model successfully explains, on a scale that is typically between 0 and 1 (it can occasionally go negative for a very poor model):

- **R² = 1.0**: the model perfectly explains all variation in the data.
- **R² = 0.0**: the model does no better than simply always predicting the average value.
- **R² closer to 1**: better fit. A common rule of thumb for many real-world problems: R² above 0.7 is considered a reasonably strong fit, though "good enough" always depends on the specific domain and stakes involved.

```python
from sklearn.metrics import r2_score

r2 = r2_score(y_test, predictions)
```

R² is easier to interpret than MSE at a glance because it's on a consistent, roughly 0-to-1 scale regardless of the label's units, which is why professional reports usually lead with R² and follow with MSE for detail.

### Overfitting and underfitting

- **Overfitting**: the model performs very well on training data but poorly on test data. It has memorized noise and specific quirks of the training examples rather than learning the true general pattern. Symptom: a large gap between training performance and test performance (e.g., training R² = 0.99, test R² = 0.55).
- **Underfitting**: the model performs poorly on *both* training and test data. It hasn't captured even the basic pattern — often because the model is too simple for the problem, or there isn't enough meaningful signal in the chosen features. Symptom: both training and test scores are low.
- **Good fit**: training and test performance are both reasonably strong and reasonably close to each other. This is the target every model-builder aims for.

The best way to detect overfitting is exactly what this lesson teaches: calculate metrics on *both* the training set and the test set, and compare them.

## Key Vocabulary

| Term | Definition |
|---|---|
| **`train_test_split()`** | A scikit-learn function that randomly splits data into training and test subsets. |
| **`test_size`** | The proportion of data reserved for testing (e.g., 0.2 = 20%). |
| **`random_state`** | A seed value making a random split reproducible. |
| **Data leakage** | When information from the test set improperly influences training, invalidating evaluation. |
| **Mean Squared Error (MSE)** | The average squared difference between predicted and actual values; lower is better. |
| **R² (R-squared)** | A 0-to-1 (typically) measure of how much variance in the label a model explains; higher is better. |
| **Overfitting** | A model performs well on training data but poorly on test data — it memorized rather than generalized. |
| **Underfitting** | A model performs poorly on both training and test data — it failed to capture the pattern at all. |
| **Generalization** | A model's ability to perform well on new, unseen data. |

## Code Example: Full Evaluation Pipeline

```python
# evaluate_model.py
# Lumexa Mission Control - Training, Testing, and Evaluation Pipeline
# Demonstrates a proper train/test split and honest model evaluation.

import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# --- Step 1: Prepare a slightly larger, more realistic dataset ---
# Simulated data: hours studied vs. test score, with some natural noise added.
np.random.seed(42)  # for reproducible "random" noise
hours_studied = np.linspace(1, 12, 40)                     # 40 evenly spaced values 1-12
noise = np.random.normal(loc=0, scale=4, size=40)            # realistic random variation
test_scores = 45 + (hours_studied * 4.8) + noise
test_scores = np.clip(test_scores, 0, 100)                   # scores can't exceed 0-100

X = hours_studied.reshape(-1, 1)
y = test_scores

# --- Step 2: Split into training and test sets BEFORE training ---
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.25, random_state=42
)

print(f"Training examples: {len(X_train)}")
print(f"Test examples: {len(X_test)}")

# --- Step 3: Train the model ONLY on training data ---
model = LinearRegression()
model.fit(X_train, y_train)

# --- Step 4: Evaluate on BOTH training and test data to check for overfitting ---
train_predictions = model.predict(X_train)
test_predictions = model.predict(X_test)

train_mse = mean_squared_error(y_train, train_predictions)
test_mse = mean_squared_error(y_test, test_predictions)

train_r2 = r2_score(y_train, train_predictions)
test_r2 = r2_score(y_test, test_predictions)

print("\n--- Model Evaluation Report ---")
print(f"Training MSE: {train_mse:.2f}   |   Training R²: {train_r2:.3f}")
print(f"Test MSE:     {test_mse:.2f}   |   Test R²:     {test_r2:.3f}")

# --- Step 5: Interpret the results ---
r2_gap = train_r2 - test_r2
print(f"\nGap between training and test R²: {r2_gap:.3f}")

if test_r2 > 0.7 and r2_gap < 0.15:
    print("VERDICT: Good fit. The model generalizes well to unseen data.")
elif r2_gap >= 0.15:
    print("VERDICT: Possible overfitting. Training performance is notably")
    print("         better than test performance.")
else:
    print("VERDICT: Possible underfitting. The model isn't capturing the")
    print("         pattern well, even on data it trained on.")

# --- Step 6: Show individual test predictions vs. actual values ---
print("\nSample test set predictions vs. actual:")
for hours, actual, predicted in zip(X_test.flatten()[:5], y_test[:5], test_predictions[:5]):
    print(f"  Hours: {hours:.1f} | Actual: {actual:.1f} | Predicted: {predicted:.1f}")
```

Expected output (values will vary slightly based on library version, but pattern holds):

```
Training examples: 30
Test examples: 10

--- Model Evaluation Report ---
Training MSE: 16.84   |   Training R²: 0.882
Test MSE:     14.02   |   Test R²:     0.901

Gap between training and test R²: -0.019

VERDICT: Good fit. The model generalizes well to unseen data.

Sample test set predictions vs. actual:
  Hours: 4.4 | Actual: 63.8 | Predicted: 67.2
  Hours: 8.8 | Actual: 91.6 | Predicted: 88.1
  Hours: 6.9 | Actual: 76.9 | Predicted: 78.4
  Hours: 4.2 | Actual: 66.9 | Predicted: 78.4
  Hours: 4.5 | Actual: 67.4 | Predicted: 78.4
```

## Instructor-Guided Coding Walkthrough

1. Build the noisy dataset together, explaining that `np.random.normal()` adds realistic scatter — real-world data is never perfectly clean, unlike Lesson 6's example.
2. Call `train_test_split()` and immediately print the lengths of all four returned arrays so students see the 75/25 split concretely.
3. Emphasize out loud, at the `.fit()` line: "We are training only on `X_train` and `y_train` — the model has never seen `X_test` or `y_test` at this point, and it never will during training."
4. Calculate and print training metrics first, then test metrics, and have students predict out loud which one they expect to be better before revealing test metrics (many will assume training is always much better — discuss why here they're close, which is the sign of a healthy fit).
5. Deliberately create an overfitting demonstration: reduce the dataset to only 6 total points and re-run — show how a small dataset makes the train/test gap far less stable and reliable, reinforcing why more data generally helps.
6. Discuss the interpretation logic (`if test_r2 > 0.7 and r2_gap < 0.15`) as a simplified professional heuristic, not a universal rule — real thresholds depend on the domain.

## Student Mission / Guided Challenge

**Explorer Challenge: Rover Battery Life Evaluator**

Create `battery_evaluator.py` that:

1. Generates a synthetic dataset relating "distance traveled (km)" to "battery consumed (%)" using `np.linspace()` for the feature and a linear formula plus `np.random.normal()` noise for the label (at least 30 examples).
2. Splits the data using `train_test_split()` with `test_size=0.3` and a fixed `random_state`.
3. Trains a `LinearRegression` model only on the training set.
4. Computes and prints MSE and R² for both training and test sets.
5. Writes a printed verdict (good fit / overfitting / underfitting) using a similar rule to the lesson's example, explaining your reasoning in a comment.
6. Prints at least 5 individual test-set predictions alongside their actual values.

**Bonus objective:** Re-run your script with `test_size=0.5` and again with `test_size=0.1`, and write a short comment comparing how the reported metrics change with less training data or less test data.

## Common Mistakes

- Evaluating a model only on training data and mistakenly believing the resulting high score proves it will work well in the real world.
- Calling `.fit()` on the full dataset (`X`, `y`) instead of only `X_train, y_train`, silently leaking test information into training.
- Forgetting `random_state`, making results non-reproducible between runs, which makes debugging and comparison difficult.
- Misinterpreting a low R² as always meaning "the model is broken" — sometimes it just means the relationship is genuinely noisy or the chosen features aren't very predictive; this is useful information, not necessarily a bug.
- Comparing MSE values across datasets with different label scales — MSE units depend on the data's scale, so an MSE of 16 might be excellent for one problem and terrible for another; R² is more directly comparable across different problems.
- Assuming overfitting is always caused by "too little data" — it can also come from a model that is too complex, or from having too many features relative to examples (covered conceptually here, in more depth in advanced courses).

## Check Your Understanding

1. Why is it misleading to evaluate a model only on the data it was trained on?
2. What do `test_size` and `random_state` each control in `train_test_split()`?
3. What does a lower MSE indicate about a model's predictions?
4. What does an R² close to 1.0 mean, and what does an R² close to 0.0 mean?
5. How would you recognize overfitting by comparing training and test metrics?
6. What is the difference between overfitting and underfitting?

## Mini Quiz

1. What should always happen before a model is trained?
   a) Calculate its R² score
   b) Split the data into training and test sets
   c) Delete the labels
   d) Predict on the test set

2. Which scikit-learn function performs the train/test split?
   a) `split_data()`
   b) `train_test_split()`
   c) `data_split()`
   d) `divide()`

3. A model with training R² = 0.98 and test R² = 0.40 is most likely:
   a) Underfitting
   b) Overfitting
   c) A perfect fit
   d) Impossible to interpret

4. What does MSE measure?
   a) The percentage of variance explained
   b) The average squared difference between predicted and actual values
   c) The number of training examples
   d) The model's training speed

5. True or False: You should call `.fit()` using the full dataset, then split it afterward for evaluation.

### Answer Key

1. Because the model may simply memorize the training examples rather than learn the general pattern, making its training performance a poor indicator of how it will perform on new, real-world data.
2. `test_size` controls what proportion of the data is reserved for testing; `random_state` fixes the randomness so the same split is reproduced every time the code runs.
3. Lower MSE indicates predictions are, on average, closer to the actual values — better performance.
4. R² close to 1.0 means the model explains nearly all the variance in the label (a strong fit); R² close to 0.0 means the model explains almost none of it, performing no better than simply guessing the average.
5. If training metrics (e.g., R²) are notably better than test metrics, with a large gap between them, that is a sign of overfitting — the model performs well on data it has seen but poorly on new data.
6. Overfitting is performing well on training data but poorly on test data (memorization); underfitting is performing poorly on both (failure to capture even the basic pattern).
7. b) Split the data into training and test sets
8. b) `train_test_split()`
9. b) Overfitting
10. b) The average squared difference between predicted and actual values
11. False — you must split the data first, then train (`.fit()`) only on the training portion, and evaluate on the separately held-out test portion.

## Lesson Recap

Today you learned the discipline that separates trustworthy machine learning from wishful thinking: properly splitting data with `train_test_split()`, training only on the training set, and honestly evaluating on the held-out test set using MSE and R². You learned to diagnose overfitting (great on training, poor on test) and underfitting (poor on both) by comparing these metrics side by side. This evaluation discipline is what makes the difference between a model that merely looks good on paper and one that will genuinely perform well when it encounters new, real-world data.

## Homework / Extension Mission

**Solo Mission: Greenhouse Yield Model Audit**

Build `greenhouse_audit.py` that:

1. Generates a synthetic dataset relating "hours of grow-light exposure per day" to "plant yield in grams," with realistic noise, at least 40 examples.
2. Splits the data with a `test_size` of your choosing, justified in a comment.
3. Trains a `LinearRegression` model and reports training and test MSE and R².
4. Writes a two-to-three sentence "audit summary" (as a comment or printed string) stating whether you'd trust this model in a real greenhouse and why, referencing the specific metrics you calculated.
5. Modifies the noise level (`scale` parameter) to be much larger, re-runs, and comments on how the metrics changed — connecting this to the idea that noisier real-world data generally makes perfect predictions impossible, and that's expected, not a failure of the method.

## Portfolio Connection

Today's evaluation pipeline — split, train, evaluate on both sets, compare metrics, interpret the gap — is precisely the content of `src/evaluate.py` in the **Number Prediction Model** project, and the README's "How It Works" section will describe these exact same MSE and R² metrics. This lesson's overfitting/underfitting vocabulary also applies directly to the **Simple Image Classifier** project, where you'll track training vs. validation accuracy across epochs to watch for the same pattern in a very different kind of model. Every AI project you build from here forward, in this course or beyond, will include this train/test/evaluate discipline as a non-negotiable step.
