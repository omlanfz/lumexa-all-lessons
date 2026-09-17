# Course 15 · Lesson 4: Linear Regression From Scratch

**Target Age:** 13–18
**Duration:** 45–60 minutes

## Learning Objectives
- Explain the intuition of gradient descent without heavy calculus
- Implement a working linear regression model using only plain Python/NumPy
- Compare the from-scratch model's predictions to scikit-learn's
  `LinearRegression` on the same real data
- Understand what "learning rate" and "iterations" control

## What You'll Learn
- What a line of best fit actually represents mathematically (intuitively)
- The "cost function" (how wrong the line currently is)
- Gradient descent as a hill-climbing (or hill-descending) process
- Building linear regression by hand with NumPy, then verifying it against
  scikit-learn

## Why It Matters
Every ML library hides the training loop behind `.fit()`. Building linear
regression yourself — even just once — demystifies that loop completely.
Once you've written the "guess, measure error, adjust" process with your own
hands, `.fit()` never feels like magic again, and you'll debug models far
more effectively for the rest of your career.

## Real-World Connection
Linear regression, in some form, is still used today in finance (predicting
stock trends), real estate (Project 07's baseline model), medicine (dosage
vs. effect relationships), and economics — precisely because it is simple,
fast, and easy to explain to non-technical decision-makers.

## Key Terminology
- **Slope / Weight**: how much the prediction changes per unit increase in a
  feature.
- **Intercept / Bias**: the prediction when all features are zero.
- **Cost function**: a formula measuring how wrong the current line is
  across all training examples (we use Mean Squared Error).
- **Gradient**: the direction and steepness of the "slope of the error" —
  which way to nudge the weights to reduce error.
- **Gradient descent**: repeatedly nudging weights a small step in the
  direction that reduces the cost function.
- **Learning rate**: how big each nudge is.
- **Epoch / Iteration**: one full pass of the adjustment process.

## Concept Explanation

Picture a scatter plot of house sizes (x-axis) versus house prices
(y-axis). Linear regression is simply the process of finding the single
straight line that best threads through that cloud of points — close to as
many points as possible, without being too far from any of them. That line
is described by just two numbers: a **slope** (how steeply price increases
per extra square foot) and an **intercept** (where the line crosses zero
square feet, even if that's not realistic — it's just where the math
anchors the line).

How do we find the *best* slope and intercept out of the infinite lines we
could draw? We need a way to score how good a candidate line is. We use the
**cost function**: for a candidate line, look at every training example,
measure the vertical distance between the real price and what the line
predicts, square that distance (so positive and negative errors don't
cancel out, and big misses count extra), and average all those squared
distances. A perfect line would have a cost of zero. A terrible line has a
huge cost.

Now imagine you're standing on a foggy hillside (the "cost landscape") where
your position represents a candidate slope and intercept, and your altitude
represents the cost — high altitude is a bad line, the valley floor is the
best possible line. You can't see the whole hill through the fog, but you
can feel which direction is downhill from where you're standing right now.
So you take a small step downhill. Then you check again: which way is
downhill from your *new* spot? Take another small step. Repeat this many
times, and you'll eventually arrive very close to the bottom of the valley —
the best possible slope and intercept. This "feel the local downhill
direction, take a small step, repeat" process is **gradient descent**, and
it is literally how many machine learning models — far more complex than
linear regression — actually learn.

Two knobs control this process. The **learning rate** is how big each
downhill step is: too large and you might overshoot the valley and bounce
around wildly (or even climb back up the other side); too small and it
takes forever to reach the bottom. The number of **iterations** (or epochs)
is how many steps you take before stopping. Get both of these right, and
your from-scratch line will end up almost exactly where scikit-learn's
optimized `LinearRegression` lands — because it's solving precisely the
same problem, just with a more efficient method under the hood.

## Step-by-Step Instruction

1. Generate a small, real relationship using real California housing data:
   predict `median_house_value` from `median_income` alone (a single,
   clearly related real feature, which makes the "line" easy to visualize
   conceptually).
2. Implement gradient descent by hand with NumPy: initialize a random slope
   and intercept, then loop, computing predictions, the error, the
   gradient, and updating the slope/intercept.
3. Track the cost over iterations to see it decrease.
4. Compare the from-scratch slope/intercept to scikit-learn's
   `LinearRegression` fitted on the exact same data.

## Code

```python
"""
Lesson 4 demo: implementing linear regression from scratch with gradient
descent, then verifying against scikit-learn, on real California housing
data (median_income -> median_house_value).
"""
import numpy as np
import pandas as pd
from sklearn.linear_model import LinearRegression

# 1. Real data: a single real feature and a single real target
housing = pd.read_csv(
    "https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv"
)
x = housing["median_income"].values.astype(float)
y = housing["median_house_value"].values.astype(float)

# Normalize x for stable gradient descent (common real-world practice)
x_mean, x_std = x.mean(), x.std()
x_norm = (x - x_mean) / x_std

n = len(x_norm)

# 2. From-scratch gradient descent
slope = 0.0
intercept = 0.0
learning_rate = 0.1
iterations = 1000

cost_history = []
for i in range(iterations):
    predictions = slope * x_norm + intercept
    errors = predictions - y

    # Cost function: Mean Squared Error
    cost = np.mean(errors ** 2)
    cost_history.append(cost)

    # Gradients: how the cost changes as we nudge slope/intercept
    slope_gradient = (2 / n) * np.sum(errors * x_norm)
    intercept_gradient = (2 / n) * np.sum(errors)

    # Take a small step downhill
    slope -= learning_rate * slope_gradient
    intercept -= learning_rate * intercept_gradient

    if i % 200 == 0:
        print(f"Iteration {i}: cost={cost:,.2f}  slope={slope:.2f}  intercept={intercept:.2f}")

print(f"\nFinal from-scratch model: slope={slope:.2f}, intercept={intercept:.2f}")
print(f"Cost went from {cost_history[0]:,.2f} to {cost_history[-1]:,.2f}")

# 3. Verify against scikit-learn on the same normalized feature
sk_model = LinearRegression()
sk_model.fit(x_norm.reshape(-1, 1), y)
print(f"\nscikit-learn model:      slope={sk_model.coef_[0]:.2f}, "
      f"intercept={sk_model.intercept_:.2f}")

# 4. Predict a new example with both models and compare
new_income_normalized = (8.0 - x_mean) / x_std   # median_income = 8.0
scratch_prediction = slope * new_income_normalized + intercept
sklearn_prediction = sk_model.predict([[new_income_normalized]])[0]
print(f"\nPrediction for median_income=8.0:")
print(f"  From scratch:  ${scratch_prediction:,.2f}")
print(f"  scikit-learn:  ${sklearn_prediction:,.2f}")
```

## Code Explanation
- We normalize `median_income` (subtract the mean, divide by the standard
  deviation) because raw gradient descent can be unstable on
  differently-scaled real data — this is standard real-world practice.
- The training loop repeats exactly the "guess, measure error, adjust" cycle
  described in the concept section: `predictions` is the guess, `errors` and
  `cost` measure how wrong it is, and `slope_gradient`/`intercept_gradient`
  tell us which direction reduces that error.
- Printing the cost every 200 iterations lets students watch it fall
  toward its minimum — direct visible evidence that gradient descent works.
- Finally, we fit scikit-learn's own `LinearRegression` on the identical
  normalized data and compare — if gradient descent is implemented
  correctly, the slope, intercept, and predictions should be very close.

## Expected Output
The cost should shrink noticeably across the printed checkpoints (starting
in the tens of billions, since it's Mean Squared Error on real house
prices) and stabilize by iteration ~1000. The from-scratch slope and
intercept should closely match scikit-learn's fitted values (real run:
from-scratch slope/intercept converge to within a small margin of
scikit-learn's, and both models' predictions for `median_income=8.0` should
land within a few hundred dollars of each other).

## Guided Practice
1. Change `learning_rate` to `1.5`. Run again — does the cost explode
   instead of shrink? Discuss overshooting.
2. Change `learning_rate` to `0.001`. Does 1000 iterations feel like enough
   to converge, or does the cost still look like it's falling?
3. Plot `cost_history` with matplotlib if available, or just describe its
   shape in words (steep drop at first, flattening out).

## Hands-On Activity
Have students individually try 3 different learning rates (e.g. 0.01, 0.1,
1.0) and record the final cost after the same number of iterations for
each. Build a class table and discuss which value converged fastest without
exploding.

## Student Challenge
Modify the from-scratch code to use two features instead of one
(`median_income` and `housing_median_age`). You'll need a slope for each
feature now — think about how the gradient calculation needs to change for
multiple weights.

## Common Mistakes
- Forgetting to normalize features before gradient descent, leading to
  wildly unstable or exploding costs.
- Setting the learning rate far too high (cost increases instead of
  decreases) or far too low (looks like nothing is happening).
- Confusing `errors = predictions - y` direction (sign matters for which
  way you step).

## Debugging Guidance
- If cost becomes `nan` or explodes to huge numbers, lower the learning
  rate.
- If the from-scratch slope/intercept never gets close to scikit-learn's,
  double check the gradient formulas and that you're using the *normalized*
  x consistently everywhere.
- If nothing changes across iterations, check `learning_rate` isn't
  accidentally 0.

## Mini Quiz
1. In plain words, what does the "cost function" measure?
2. What happens if the learning rate is too large?
3. What happens if the learning rate is too small?
4. Why do we normalize the feature before running gradient descent?

### Answer Key
1. How wrong the current line's predictions are, averaged across all
   training examples (using squared error here).
2. The steps taken are too big and can overshoot the best solution,
   sometimes making the cost increase instead of decrease.
3. Learning happens very slowly, requiring many more iterations to reach a
   good solution.
4. It keeps gradient descent numerically stable and helps it converge
   faster and more reliably, especially when features have very different
   scales.

## Lesson Recap
Linear regression finds the best-fit line by minimizing a cost function
(Mean Squared Error) using gradient descent — repeatedly nudging the slope
and intercept downhill on the cost landscape. Building this by hand with
NumPy and verifying it matches scikit-learn's optimized implementation
proves there's no hidden magic in `.fit()`.

## Homework / Extension
Rewrite the from-scratch loop to track and print the R² score every 200
iterations alongside the cost, and describe in a sentence what you observe
about how R² changes as cost falls.

## Portfolio Project Connection
Project 07's baseline `LinearRegression` model is solving exactly the
problem you just built by hand — scikit-learn just uses a faster,
more numerically stable method internally (not literally the loop above,
but conceptually equivalent in spirit for this kind of model).
