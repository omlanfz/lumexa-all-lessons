# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 06 of 08
**Title:** Building a First Prediction Model
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.11+, NumPy, scikit-learn

---

## Mission Brief

Cadet, briefing is over — it's time to build a real, working model. Today you install and use **NumPy** and **scikit-learn**, the two libraries that power the vast majority of professional machine learning outside of deep learning. You'll load a dataset, separate features from labels, and train your very first genuine machine learning model: a **linear regression** model that predicts a student's test score from hours studied. This lesson is the technical core of the **Number Prediction Model** project, and everything from here forward is you actually flying the ship, not just reading the manual.

## Learning Objectives

1. Install and import NumPy and scikit-learn.
2. Represent a dataset using NumPy arrays.
3. Separate a dataset into features (X) and labels (y), following standard ML naming convention.
4. Train a linear regression model using scikit-learn's `.fit()` method.
5. Use a trained model to make predictions with `.predict()`.
6. Inspect a trained model's learned parameters (slope and intercept).

## Prerequisites

- Completion of Lesson 05 (features, labels, training data, supervised learning, regression).
- Comfort with lists, loops, and functions from Lessons 3-4.
- Python 3.9+ installed, with permission to install packages via `pip`.

## Concept Explanation

### Installing your AI toolkit

Two new libraries power today's lesson:

- **NumPy** — the foundational library for numerical computing in Python. It provides the `array`, a highly efficient way to store and do math on large collections of numbers, much faster than plain Python lists.
- **scikit-learn** (imported as `sklearn`) — the most widely used general-purpose machine learning library in the world, providing ready-made, well-tested implementations of dozens of ML algorithms.

Install both from the terminal:

```bash
pip install numpy scikit-learn
```

### NumPy arrays: the language of ML data

While Lesson 4's lists of dictionaries were great for readability, machine learning libraries expect data in **NumPy arrays** — grids of numbers optimized for fast math:

```python
import numpy as np

hours_studied = np.array([1, 2, 3, 4, 5, 6, 7])
print(hours_studied)
print(type(hours_studied))    # <class 'numpy.ndarray'>
print(hours_studied.shape)     # (7,) - 7 items, one dimension
```

NumPy arrays support **vectorized math** — operations apply to every element at once, no loop required:

```python
doubled = hours_studied * 2
print(doubled)   # [ 2  4  6  8 10 12 14] - every value doubled instantly
```

### The X and y convention

By near-universal convention in machine learning code, **`X`** (capital letter) holds the features, and **`y`** (lowercase letter) holds the labels. This isn't arbitrary — it echoes the mathematical notation `y = f(X)`, meaning "the label y is some function of the features X" — which is exactly what a model is trying to discover. scikit-learn expects `X` to be a **2D array** (rows = examples, columns = features), even when there's only one feature, so we typically reshape a 1D array into a column:

```python
X = hours_studied.reshape(-1, 1)   # shape becomes (7, 1): 7 rows, 1 column
y = np.array([52, 60, 68, 74, 81, 88, 93])   # the labels, 1D is fine for y
```

`.reshape(-1, 1)` means "figure out the number of rows automatically, but make sure there's exactly 1 column." This reshaping step trips up almost every beginner at least once — expect it, and always double check `X.shape` before training.

### Linear regression: the simplest real model

**Linear regression** finds the straight line that best fits a set of data points — mathematically, it learns two numbers: a **slope** (how much the prediction changes per unit of input) and an **intercept** (the prediction when input is zero). This is exactly the shape of the hand-built formula from Lesson 5 (`45 + hours_studied * 7`) — except now, instead of a human guessing the `45` and the `7`, the algorithm calculates the best possible values directly from the data.

```python
from sklearn.linear_model import LinearRegression

model = LinearRegression()   # create an untrained model
model.fit(X, y)               # train it - this is the entire "learning" step
```

`.fit(X, y)` is where the magic (and math) happens: scikit-learn analyzes every (feature, label) pair and calculates the slope and intercept that minimize the total error across all training examples — using a well-established mathematical method called **least squares**, which finds the line where the sum of squared distances from each point to the line is as small as possible.

After training, you can inspect what the model learned:

```python
print("Slope (coefficient):", model.coef_)         # array, one value per feature
print("Intercept:", model.intercept_)                # a single number
```

### Making predictions (inference)

Once trained, `.predict()` uses the learned slope and intercept to produce output for new input:

```python
new_hours = np.array([[4.5]])          # must be 2D, shape (1, 1)
prediction = model.predict(new_hours)
print("Predicted score:", prediction[0])
```

Notice `.predict()` always expects and returns arrays, even for a single value — this consistency is intentional, since real-world use often predicts many examples at once.

## Key Vocabulary

| Term | Definition |
|---|---|
| **NumPy** | Python's foundational library for fast numerical computing using arrays. |
| **NumPy array (ndarray)** | A grid of numbers supporting fast, vectorized math operations. |
| **scikit-learn (sklearn)** | A widely used Python library providing ready-made machine learning algorithms. |
| **X** | Convention for the variable holding a dataset's features (inputs). |
| **y** | Convention for the variable holding a dataset's labels (correct outputs). |
| **Reshape** | Changing the dimensions/shape of a NumPy array without changing its data. |
| **Linear regression** | An algorithm that fits a straight line (slope + intercept) to data to predict a continuous value. |
| **`.fit()`** | The scikit-learn method that trains a model on given features and labels. |
| **`.predict()`** | The scikit-learn method that produces predictions from a trained model on new data. |
| **Coefficient / Slope** | The learned value describing how much the prediction changes per unit of a feature. |
| **Intercept** | The learned baseline value of the prediction when all features are zero. |

## Code Example: First Prediction Model

```python
# first_prediction_model.py
# Lumexa Mission Control - First Prediction Model
# Trains a linear regression model to predict test scores from hours studied.

import numpy as np
from sklearn.linear_model import LinearRegression

# --- Step 1: Prepare the data ---
# Features: hours studied (input). Must be reshaped into a 2D column.
hours_studied = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
X = hours_studied.reshape(-1, 1)

# Labels: actual test scores achieved (output we want the model to learn).
y = np.array([52, 60, 68, 74, 81, 88, 93, 96, 98, 99])

print("Feature array X shape:", X.shape)   # (10, 1)
print("Label array y shape:", y.shape)      # (10,)

# --- Step 2: Create and train the model ---
model = LinearRegression()
model.fit(X, y)

print("\nModel trained successfully!")
print("Learned slope:", round(model.coef_[0], 2))
print("Learned intercept:", round(model.intercept_, 2))
print(f"Learned formula: score = {model.intercept_:.2f} + {model.coef_[0]:.2f} * hours_studied")

# --- Step 3: Make predictions (inference) on new, unseen input ---
new_students_hours = np.array([[2.5], [4.5], [11], [0]])
predictions = model.predict(new_students_hours)

print("\nPredictions for new students:")
for hours, predicted_score in zip(new_students_hours, predictions):
    print(f"  {hours[0]} hours studied -> predicted score: {predicted_score:.1f}")

# --- Step 4: Compare model predictions against the original training data ---
print("\nHow well does the model fit the original training data?")
training_predictions = model.predict(X)
for actual_hours, actual_score, predicted_score in zip(hours_studied, y, training_predictions):
    print(f"  Hours: {actual_hours} | Actual: {actual_score} | Predicted: {predicted_score:.1f}")
```

Expected output (numbers may vary very slightly by scikit-learn version):

```
Feature array X shape: (10, 1)
Label array y shape: (10,)

Model trained successfully!
Learned slope: 5.42
Learned intercept: 48.53
Learned formula: score = 48.53 + 5.42 * hours_studied

Predictions for new students:
  2.5 hours studied -> predicted score: 62.1
  4.5 hours studied -> predicted score: 72.9
  11 hours studied -> predicted score: 108.2
  0 hours studied -> predicted score: 48.5

How well does the model fit the original training data?
  Hours: 1 | Actual: 52 | Predicted: 54.0
  Hours: 2 | Actual: 60 | Predicted: 59.4
  Hours: 3 | Actual: 68 | Predicted: 64.8
  Hours: 4 | Actual: 74 | Predicted: 70.2
  Hours: 5 | Actual: 81 | Predicted: 75.7
  Hours: 6 | Actual: 88 | Predicted: 81.1
  Hours: 7 | Actual: 93 | Predicted: 86.5
  Hours: 8 | Actual: 96 | Predicted: 91.9
  Hours: 9 | Actual: 98 | Predicted: 97.4
  Hours: 10 | Actual: 99 | Predicted: 102.8
```

Notice the prediction for `11 hours` (108.2) exceeds a realistic maximum test score of 100 — a great, teachable moment: linear regression will happily extrapolate beyond the range of its training data, but real-world constraints (a score can't exceed 100) aren't automatically known to the model. This foreshadows the importance of evaluating models carefully, covered in Lesson 7.

## Instructor-Guided Coding Walkthrough

1. Install the libraries together as a class, verifying with `import numpy; import sklearn; print(sklearn.__version__)` in a quick throwaway script.
2. Build the `X` and `y` arrays first, printing `.shape` for each and discussing why `X` needs reshaping but `y` doesn't.
3. Create the model and call `.fit()`, then immediately print `model.coef_` and `model.intercept_` — ask students to manually compute one prediction using the formula by hand before running `.predict()`, then confirm it matches.
4. Run predictions on new data, specifically including an extreme value (like 11 or 0 hours) to spark the extrapolation discussion above.
5. Compare training predictions to actual labels side-by-side, pointing out that the fit is close but not perfect — a straight line cannot pass through every point exactly, and that's expected and normal, not a bug.
6. If time allows, briefly show what happens with a `LinearRegression()` trained on very noisy or non-linear data (e.g., a U-shaped relationship) to preview why not every problem fits a straight line — a segue into more advanced models students may explore later.

## Student Mission / Guided Challenge

**Explorer Challenge: Fuel Consumption Predictor**

Create `fuel_predictor.py` that:

1. Builds a NumPy feature array `X` representing rover travel distance in km for at least 8 example trips (reshaped correctly to 2D).
2. Builds a NumPy label array `y` representing the fuel consumed (in liters) for each of those trips, following a roughly linear relationship you invent (e.g., approximately 0.15 liters per km, with small variation).
3. Trains a `LinearRegression` model on this data.
4. Prints the learned slope and intercept, and writes out the learned formula as a comment.
5. Predicts fuel consumption for three new distances not in the training data, printing each clearly.
6. Compares the model's predictions on the original training data against the actual values, printing the difference (error) for each.

**Bonus objective:** Add a second feature (e.g., "terrain roughness score" from 1-10) to `X` so the model uses two features instead of one — confirm `X.shape` becomes `(n, 2)` and that `model.coef_` now has two values.

## Common Mistakes

- Forgetting to `.reshape(-1, 1)` on a single-feature `X`, causing a `ValueError` from scikit-learn about expected array dimensions.
- Mixing up `X` and `y` when calling `.fit(X, y)` — always features first, labels second.
- Forgetting that `.predict()` requires a 2D array even for a single new value.
- Expecting a straight-line model to perfectly predict every point — some error is normal and expected; perfect fit on training data is actually a warning sign (covered in Lesson 7 as overfitting).
- Not installing scikit-learn correctly (installing `sklearn` via `pip install sklearn` is deprecated — always use `pip install scikit-learn`, but import it as `import sklearn` / `from sklearn... import ...`).
- Confusing `model.coef_` (a NumPy array, even with one feature) with a plain number — remember to index it with `[0]` when there's only one feature.

## Check Your Understanding

1. Why does scikit-learn expect `X` to be a 2D array even when there's only one feature?
2. What do the two numbers a linear regression model learns represent?
3. What is the difference between `.fit()` and `.predict()`?
4. Why did the model's prediction for 11 hours studied exceed the realistic maximum score of 100, and what does this reveal about linear regression?
5. What does it mean, in this lesson's example, for the model's predictions on training data to not exactly match the real scores?
6. Why is the "X = features, y = labels" naming convention useful, beyond just following tradition?

## Mini Quiz

1. What must you do to a 1D array of features before passing it to scikit-learn as `X`?
   a) Nothing, it works as-is
   b) Convert it to a Python list
   c) Reshape it into a 2D array
   d) Sort it numerically

2. Which method actually trains a scikit-learn model?
   a) `.train()`
   b) `.learn()`
   c) `.fit()`
   d) `.predict()`

3. What does `model.coef_` represent in a linear regression model?
   a) The intercept
   b) The learned slope(s)
   c) The test data
   d) The error rate

4. What is the correct pip install command for scikit-learn?
   a) `pip install sklearn`
   b) `pip install scikit-learn`
   c) `pip install scikit_learn_ml`
   d) `pip install sklearn-learn`

5. True or False: `.predict()` can only be called on data the model already saw during training.

### Answer Key

1. Because scikit-learn's API is designed to handle multiple features at once, so it always expects a table shape (rows = examples, columns = features), even when there's only one column.
2. The slope (how much the prediction changes per unit of the feature) and the intercept (the baseline prediction when the feature is zero).
3. `.fit()` trains the model on known features and labels; `.predict()` uses an already-trained model to produce output for new input.
4. Because linear regression fits a straight, unbounded line — it has no built-in knowledge that scores are capped at 100, so it happily extrapolates beyond realistic limits outside the range of its training data.
5. It means the straight line the model learned is a close approximation of the pattern, not an exact match to every point — some prediction error is normal and expected, since real data rarely falls perfectly on a straight line.
6. It mirrors standard mathematical notation (`y = f(X)`) and makes machine learning code instantly recognizable and readable to any other ML practitioner, improving consistency and clarity across the entire field.
7. c) Reshape it into a 2D array
8. c) `.fit()`
9. b) The learned slope(s)
10. b) `pip install scikit-learn`
11. False — `.predict()` is specifically meant to be used on new, previously unseen data; that's the entire point of inference.

## Lesson Recap

Today you crossed from theory into practice: you installed NumPy and scikit-learn, structured a real dataset into `X` (features) and `y` (labels), and trained your first genuine machine learning model — a linear regression model that discovered its own slope and intercept directly from data, no hand-guessing required. You used `.fit()` to train and `.predict()` to perform inference on brand-new input, and you saw firsthand both the power and the limits of a simple linear model (like unrealistic extrapolation). This is the technical engine behind the Number Prediction Model project.

## Homework / Extension Mission

**Solo Mission: Solar Panel Output Predictor**

Build `solar_predictor.py` that:

1. Creates a feature array `X` of "hours of sunlight" for at least 10 example days (reshaped to 2D).
2. Creates a label array `y` of "kilowatt-hours generated" following a roughly linear pattern you invent.
3. Trains a `LinearRegression` model and prints the learned slope/intercept as a readable formula.
4. Predicts output for 5 new sunlight values, including at least one unrealistic edge case (like 0 hours or 24 hours), and comments on whether the prediction makes physical sense.
5. Writes a short paragraph (as a comment block) explaining, in your own words, what `.fit()` did mathematically to produce the slope and intercept.

## Portfolio Connection

Today's code is, almost line for line, the training script you will build and expand in the **Number Prediction Model** project: loading data into `X` and `y`, calling `LinearRegression().fit(X, y)`, and using `.predict()` for new input. The project's `src/train.py` follows this exact structure, and `src/predict.py` mirrors today's inference step. In Lesson 7, you'll add the missing safety step this lesson deliberately left out: properly splitting data into training and test sets so you can honestly measure how good this model really is — rather than just eyeballing the comparison table as we did today.
