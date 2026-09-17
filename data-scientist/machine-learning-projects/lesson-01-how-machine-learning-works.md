# Course 15 · Lesson 1: How Machine Learning Works (No Maths Jargon)

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
By the end of this lesson, students will be able to:
- Explain, in plain language, what "machine learning" actually means
- Describe the difference between a traditional program and a learning program
- Identify the three ingredients every ML system needs: data, a model, and feedback
- Run a real, working scikit-learn model in Python and see it "learn" from data
- Explain why more/better data usually beats a fancier algorithm

## What You'll Learn
- The core idea of ML: finding patterns in examples instead of being told exact rules
- The training loop: guess → check the answer → adjust → repeat
- The vocabulary: dataset, features, labels, model, training, prediction
- A first hands-on example using `scikit-learn`

## Why It Matters
Every recommendation you get on YouTube, every spam filter, every voice
assistant, and every "you might also like" suggestion on a shopping site
runs on machine learning. Understanding how it actually works — not as
magic, but as a concrete process — is the foundation for everything else in
this course, including the three portfolio projects you'll build.

## Real-World Connection
- Netflix and Spotify use ML to predict what you'll want to watch/listen to
  next (this connects directly to Project 09 later in this course).
- Banks use ML to predict whether a loan applicant might default.
- Telecom companies use ML to predict which customers are about to cancel
  their subscription (this is exactly Project 08).
- Real-estate sites use ML to estimate what a house is worth (Project 07).

## Key Terminology
- **Machine Learning (ML)**: teaching a computer to find patterns in
  examples and make decisions or predictions from new data, instead of
  writing explicit step-by-step rules for it.
- **Dataset**: the collection of real-world examples the model learns from.
- **Feature**: one measurable piece of information about an example (e.g. a
  house's square footage).
- **Label / Target**: the answer the model is trying to predict (e.g. the
  house's price).
- **Model**: the mathematical "recipe" that turns features into a
  prediction.
- **Training**: the process of showing the model many examples so it can
  adjust itself to make better predictions.
- **Prediction / Inference**: using a trained model on new data it has
  never seen before.

## Concept Explanation

Imagine you want to teach a computer to guess how much a used bicycle is
worth. The traditional programming approach would be: you, the programmer,
sit down and write a giant list of rules — "if the bike is less than a year
old, subtract $20 from the original price; if it has rust, subtract $30..."
This works for a while, but the real world has thousands of tiny factors
(brand, tire wear, gear condition, local demand), and writing a rule for
every single one is essentially impossible. You'd spend forever and still
get it wrong constantly.

Machine learning flips this around. Instead of you writing the rules, you
show the computer hundreds or thousands of *real examples* of bicycles that
already sold, along with their actual final prices. The computer doesn't
know anything about bikes at first — but it is given a flexible
mathematical structure (the "model") that can adjust itself. It looks at
example #1, makes a wild guess, compares that guess to the real price, and
nudges its internal settings a tiny bit to be less wrong. Then it looks at
example #2, and does the same thing. After seeing thousands of examples,
those tiny nudges add up, and the model has effectively *discovered* the
rules on its own — rules that would have been too complicated for a human
to write out directly, and rules that are grounded in what actually
happened in the real data, not in a person's guess about what matters.

This loop — guess, measure how wrong the guess was, adjust, repeat — is
called **training**. Once training is done, you can hand the model a brand
new bicycle it has never seen, and it will use everything it learned to
make a prediction. This is called **inference** or **prediction**.

It's important to be precise about what the model actually learned: it
did *not* learn "rust is bad" the way a human understands rust. It learned
a set of numbers that, when combined with the numbers describing a bike
(age, mileage-equivalent, condition rating, etc.), produce outputs that
matched the real sale prices as closely as possible across all the training
examples. This is why the *quality and quantity of your data* matters so
much more than people expect — a model can only be as good as the patterns
that genuinely exist in the examples it was shown. Garbage data in, garbage
predictions out. This is a theme you'll see in every single project in this
course: real, messy, imperfect data, and the real work of preparing it.

## Step-by-Step Instruction

1. We will use a real, small, built-in dataset from scikit-learn: house
   prices in Boston-adjacent-style California housing data would come
   later, so for this very first taste we'll use scikit-learn's built-in
   diabetes dataset (a set of real, anonymized patient measurements and a
   disease progression score) — it's tiny, fast, and perfect for a first
   run.
2. We'll load the data and look at what "features" and "labels" actually
   look like as real numbers in a table.
3. We'll split the data into a training set (used to teach the model) and a
   test set (used to honestly check how well it learned) — a concept we'll
   go much deeper on in Lesson 3.
4. We'll train a very simple model (`LinearRegression`) and have it make
   predictions.
5. We'll compare its predictions to the real answers and see, concretely,
   that it learned something real from the data.

## Code

```python
"""
Lesson 1 demo: your first working machine learning model.
Uses scikit-learn's built-in real diabetes dataset (442 real patients,
anonymized measurements, and a real disease-progression score).
"""
from sklearn.datasets import load_diabetes
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_absolute_error

# 1. Load a real dataset: features (X) and labels/targets (y)
data = load_diabetes()
X = data.data          # the "features": patient measurements
y = data.target        # the "labels": disease progression score one year later

print(f"Number of patients (examples): {X.shape[0]}")
print(f"Number of features per patient: {X.shape[1]}")
print(f"Feature names: {data.feature_names}")
print(f"First patient's features: {X[0]}")
print(f"First patient's real label (target): {y[0]}")

# 2. Split into training data (to learn from) and test data (to honestly check)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
print(f"\nTraining examples: {len(X_train)}  Test examples: {len(X_test)}")

# 3. Create the model (the flexible "recipe" that will adjust itself)
model = LinearRegression()

# 4. Train it: this is the "guess, check, adjust, repeat" loop happening
#    inside scikit-learn
model.fit(X_train, y_train)

# 5. Use the trained model to predict on data it has NEVER seen before
predictions = model.predict(X_test)

# 6. Compare predictions to the real answers
print("\nFirst 5 real vs predicted values:")
for real, predicted in zip(y_test[:5], predictions[:5]):
    print(f"  Real: {real:.1f}   Predicted: {predicted:.1f}")

mae = mean_absolute_error(y_test, predictions)
print(f"\nOn average, predictions were off by about {mae:.1f} points "
      f"(Mean Absolute Error).")
```

## Code Explanation
- `load_diabetes()` loads a real dataset that scikit-learn ships with — no
  internet needed, and it's genuine anonymized medical measurement data.
- `X` is the table of **features** (inputs); `y` is the list of real
  **labels** (the correct answers we want the model to learn to predict).
- `train_test_split` divides the data so the model is trained on 80% of the
  examples and honestly tested on the other 20% it has never touched. This
  matters enormously — testing on data the model already memorized would be
  like grading a student on the exact quiz they were shown the answer key
  for.
- `LinearRegression()` creates an untrained model — at this point it knows
  nothing.
- `.fit(X_train, y_train)` is where the actual "learning" happens: the model
  adjusts its internal numbers to best match the training examples.
- `.predict(X_test)` asks the trained model to guess the label for data it
  has never seen.
- `mean_absolute_error` measures, on average, how far off the predictions
  were from reality — a simple, honest way to score the model (much more on
  this in Lesson 3).

## Expected Output
Running this script will print the dataset shape (442 patients, 10
features), a sample patient's features and real label, the train/test split
sizes, five real-vs-predicted comparisons (numbers will be in the
40-300-ish range for this dataset and won't match exactly but should be in
the same ballpark), and a final Mean Absolute Error typically somewhere
around 40-45 for this dataset with a plain Linear Regression model.

## Guided Practice
Working together as a class or in pairs:
1. Change `test_size=0.2` to `test_size=0.5`. Re-run. Does the MAE get
   better or worse? Discuss why training on fewer examples might hurt the
   model.
2. Change `random_state=42` to a different number (e.g. `7`). Does the MAE
   change? Discuss why (different random splits produce slightly different
   train/test groups).
3. Print `model.coef_` — these are the actual numbers the model learned for
   each feature. Ask: does a bigger number mean that feature matters more?
   (Careful — it depends on the scale of the feature, a subtlety we'll
   revisit later.)

## Hands-On Activity
In small groups, have students each independently change ONE thing (test
size, random state, or try `from sklearn.linear_model import Ridge` in
place of `LinearRegression`) and report their resulting MAE to the class.
Build a shared list on the board of "MAE by configuration" and discuss why
results differ slightly.

## Student Challenge
Without changing the model type, can you get the MAE below 40 just by
changing `test_size` and `random_state`? Try several combinations and
record your best MAE. (This is a gentle introduction to the idea that
results can vary — and a preview of why Lesson 3's cross-validation exists.)

## Common Mistakes
- **Confusing features and labels**: students sometimes think `y` is an
  input. Emphasize: `X` = what we know in advance, `y` = what we're trying
  to predict.
- **Testing on training data**: forgetting to hold out a test set and
  evaluating on the same data the model trained on — this always makes a
  model look better than it really is.
- **Assuming the model "understands" the domain**: the model has no idea
  what "blood pressure" means; it's only finding numeric patterns.

## Debugging Guidance
- `ModuleNotFoundError: No module named 'sklearn'` — install with
  `pip install scikit-learn`.
- Shape mismatch errors when changing the dataset — always check
  `X.shape` and `y.shape` line up on the number of rows.
- If MAE seems huge, double check you didn't accidentally swap `X` and `y`.

## Mini Quiz
1. What is the difference between a feature and a label?
2. What does "training" a model actually do, in plain words?
3. Why do we test the model on data it hasn't seen before?
4. True or False: the model understands what blood pressure means.

### Answer Key
1. A feature is an input (something we already know about an example); a
   label is the answer we want the model to predict.
2. Training adjusts the model's internal numbers so its guesses get closer
   and closer to the real answers across many examples.
3. Testing on unseen data gives an honest measure of how well the model
   will perform on new, real-world cases — testing on training data would
   be misleadingly optimistic (like grading with the answer key visible).
4. False — the model only recognizes numeric patterns; it has no real
   understanding of the meaning behind the numbers.

## Lesson Recap
Machine learning replaces hand-written rules with a training loop that
learns patterns directly from real examples. Every ML system needs data
(examples), a model (a flexible structure that can adjust), and a way to
measure and reduce error. We proved this concretely by training a real
scikit-learn model on a real dataset and measuring its honest error on data
it had never seen.

## Homework / Extension
Find any dataset online description (no need to download it) for something
you're personally interested in — sports stats, video game data, weather —
and write 3 sentences: what would the features be, what would the label
be, and why might ML help here more than hand-written rules?

## Portfolio Project Connection
This lesson's train/test split and "guess → check → adjust" loop is the
exact same mental model you'll use in **Project 07 (House Price
Predictor)**, **Project 08 (Customer Churn Predictor)**, and **Project 09
(Movie Recommendation Engine)** — every one of them trains a model on real
data and honestly evaluates it on data it hasn't seen.
