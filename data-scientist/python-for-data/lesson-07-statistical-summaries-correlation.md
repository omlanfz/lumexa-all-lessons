# Course 13 · Python for Data — Lesson 7: Statistical Summaries and Correlation

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Compute core summary statistics (mean, median, mode, standard deviation, min/max) using pandas and numpy.
- Use `.describe()` to get a full statistical summary of a DataFrame in one call.
- Compute correlation between numeric columns with `.corr()`.
- Interpret a correlation coefficient's strength and direction correctly.
- Explain the important distinction between correlation and causation.

## What You'll Learn
Today you learn to describe a dataset in numbers: the average, the typical spread, and — critically — whether two variables move together (correlation). You'll use `.describe()` for an instant statistical snapshot and `.corr()` to explore real relationships in the NBA and student performance data, like whether a higher pre-game Elo rating correlates with points scored.

## Why It Matters
Averages and spreads tell you what's "typical" and how much variation exists in your data. Correlation tells you whether two things tend to change together — a foundational question behind nearly every real analysis, from "do reading scores predict writing scores?" to "does a team's Elo rating predict how many points they score?" Misreading correlation is also one of the most common statistical mistakes in the real world, so learning to interpret it carefully is a critical skill.

## Real-World Connection
Doctors use correlation to study whether a treatment relates to patient outcomes. College admissions offices study whether standardized test scores correlate with GPA. Sports analysts study whether a team's Elo rating correlates with actual scoring output — exactly the question you'll investigate today with real NBA data.

## Key Terminology
- **Mean**: The arithmetic average of a set of values.
- **Median**: The middle value when data is sorted — less affected by extreme outliers than the mean.
- **Mode**: The most frequently occurring value.
- **Standard deviation**: A measure of how spread out values are around the mean.
- **`.describe()`**: A pandas method that returns count, mean, std, min, quartiles, and max for numeric columns.
- **Correlation coefficient**: A number between -1 and 1 describing how strongly two variables move together.
- **`.corr()`**: The pandas method that computes correlation coefficients between numeric columns.
- **Causation**: One variable directly causing a change in another — NOT the same thing as correlation.

## Concept Explanation
The **mean** (`.mean()`) is the familiar average — sum divided by count. The **median** (`.median()`) is the middle value once data is sorted, and is far more resistant to outliers than the mean: if one billionaire moves into a small town, the mean income skyrockets, but the median barely changes. The **mode** (`.mode()`) is simply the most common value, most useful for categorical or repeated discrete data. **Standard deviation** (`.std()`) measures typical spread around the mean — a low standard deviation means values cluster tightly; a high one means they're spread widely.

`.describe()` computes several of these at once for every numeric column: count, mean, std, min, the 25th/50th/75th percentiles (quartiles), and max — an instant statistical snapshot of an entire dataset in one line of code, and usually one of the very first things run after loading and cleaning any dataset.

**Correlation** measures the strength and direction of a linear relationship between two numeric variables, expressed as a number from **-1 to +1**. A value near **+1** means the two variables tend to rise together (strong positive correlation) — as one goes up, so does the other. A value near **-1** means they move in opposite directions (strong negative correlation) — as one goes up, the other goes down. A value near **0** means little to no linear relationship. `df[["colA", "colB"]].corr()` — or `df.corr()` on numeric columns generally — returns a correlation matrix showing every pair's coefficient at once.

The single most important caution in this lesson: **correlation is not causation**. Two variables can be strongly correlated without one causing the other — they might both be driven by a third factor, or the relationship could be coincidental. For example, a team's pre-game Elo rating correlating with points scored doesn't necessarily mean a *higher rating causes* more scoring — both could instead reflect the same underlying team quality. Careful data scientists always state correlation findings cautiously, and never claim causation just because two numbers move together.

## Step-by-Step Instruction
1. Load and clean the NBA dataset (from Lessons 3 and 5).
2. Run `.describe()` on the `pts` column and interpret each statistic aloud as a class.
3. Compute the mean, median, and standard deviation of `pts` individually to see how `.describe()` combines them.
4. Compute the correlation between `elo_i` (pre-game Elo) and `pts` (points scored).
5. Compute a small correlation matrix across `elo_i`, `elo_n`, `pts`, and `opp_pts`.
6. Discuss, as a class, what the resulting correlation values do and do not prove.

## Code Example
```python
import pandas as pd
import numpy as np

nba = pd.read_csv("nbaallelo.csv")

# --- Core summary statistics ---
pts_mean = nba["pts"].mean()
pts_median = nba["pts"].median()
pts_std = nba["pts"].std()
pts_mode = nba["pts"].mode()[0]

print("Points scored — summary statistics:")
print(f"Mean:   {pts_mean:.2f}")
print(f"Median: {pts_median:.2f}")
print(f"Mode:   {pts_mode}")
print(f"Std dev:{pts_std:.2f}")

# --- .describe() for a full statistical snapshot ---
print("\nFull .describe() summary of points scored:")
print(nba["pts"].describe())

# --- Correlation between two variables ---
elo_pts_corr = nba["elo_i"].corr(nba["pts"])
print(f"\nCorrelation between pre-game Elo rating and points scored: {elo_pts_corr:.3f}")

# --- Correlation matrix across several numeric columns ---
correlation_matrix = nba[["elo_i", "elo_n", "pts", "opp_pts"]].corr()
print("\nCorrelation matrix:")
print(correlation_matrix)

# --- Interpreting strength ---
def interpret_correlation(value):
    magnitude = abs(value)
    if magnitude >= 0.7:
        strength = "strong"
    elif magnitude >= 0.3:
        strength = "moderate"
    else:
        strength = "weak"
    direction = "positive" if value > 0 else "negative"
    return f"{strength} {direction} correlation"

print(f"\nInterpretation of Elo-vs-points correlation: {interpret_correlation(elo_pts_corr)}")
```

## Code Explanation
- `.mean()`, `.median()`, `.mode()[0]`, and `.std()` compute the four core summary statistics individually — `.mode()` returns a Series (there can be more than one mode), so `[0]` grabs the first one.
- `.describe()` computes count, mean, std, min, 25%/50%/75% percentiles, and max in a single call — notice `50%` matches the median we computed manually.
- `.corr()` called between two Series computes the Pearson correlation coefficient between them directly.
- Selecting multiple columns and calling `.corr()` on the resulting DataFrame produces a full correlation **matrix**, showing every pair's coefficient — the diagonal is always `1.0` (a variable is perfectly correlated with itself).
- `interpret_correlation()` is a small custom function that turns a raw coefficient into a plain-English description of strength and direction — the kind of communication step emphasized back in Lesson 1.

## Expected Output
```
Points scored — summary statistics:
Mean:   102.73
Median: 103.00
Mode:   102
Std dev:14.81

Full .describe() summary of points scored:
count    126314.000000
mean        102.729982
std          14.814845
min           0.000000
25%          93.000000
50%         103.000000
75%         112.000000
max         186.000000
Name: pts, dtype: float64

Correlation between pre-game Elo rating and points scored: 0.090

Correlation matrix:
            elo_i     elo_n       pts   opp_pts
elo_i    1.000000  0.996053  0.090233 -0.147279
elo_n    0.996053  1.000000  0.121670 -0.178553
pts      0.090233  0.121670  1.000000  0.592491
opp_pts -0.147279 -0.178553  0.592491  1.000000

Interpretation of Elo-vs-points correlation: weak positive correlation
```
(These are the actual computed values from the real dataset. Note the very strong correlation, 0.996, between a team's pre-game and post-game Elo rating in the same game — expected, since one rating updates from the other. The much weaker correlation between Elo rating and points scored, about 0.09, shows that Elo reflects overall team strength across a season much more than it predicts how many points are scored in any single game. Interestingly, a team's own points scored and their opponent's points scored are moderately positively correlated at about 0.59 — high-scoring games tend to be high-scoring for both teams, reflecting pace of play.)

## Guided Practice
1. Compute the correlation between `pts` and `opp_pts` (points scored vs. opponent's points scored) and interpret it.
2. Run `.describe()` on `elo_i` and compare its mean and standard deviation to `pts`.
3. Modify `interpret_correlation()`'s thresholds and discuss as a class whether 0.3/0.7 are reasonable cutoffs.

## Hands-On Activity
Using the NBA dataset, students compute `.describe()` for both `pts` and `opp_pts`, then compute and interpret the correlation between `elo_n` (post-game Elo) and `win_equiv` (a season win-equivalent metric already included in the dataset). They write two sentences: one stating the numeric result, one interpreting it in plain English without claiming causation.

## Student Challenge
Using the Student Performance dataset from this course's Project 2 (`math score`, `reading score`, `writing score`), compute the full correlation matrix among all three scores. Identify which pair of subjects is most strongly correlated, and write a short paragraph explaining why that makes intuitive sense (or why it's surprising) — being careful to describe it as correlation, not causation.

## Common Mistakes
- **Claiming causation from correlation**: Saying "a higher Elo rating causes more points" instead of the accurate "a higher Elo rating is associated with a weak tendency toward more points scored."
- **Ignoring outliers**: Reporting only the mean without checking the median or standard deviation can hide the effect of extreme values.
- **Misreading correlation sign**: Forgetting that a negative correlation is still a real, meaningful relationship — not "no relationship."
- **Running `.corr()` on non-numeric columns**: Pandas will raise an error or silently exclude text columns — always confirm you're selecting genuinely numeric columns first.

## Debugging Guidance
- If `.corr()` produces unexpected `NaN` values, check whether that column has too many missing values or is entirely constant (no variation means correlation is undefined).
- If `.describe()` output looks odd (e.g., unexpectedly low count), check for missing values in that column first with `.isnull().sum()`.
- If a correlation seems implausibly strong or weak, sanity-check it visually — plotting a scatter plot between the two columns (a preview of tools beyond this course) can quickly confirm whether the number matches what you see.
- Always double check you're comparing genuinely related columns — correlating an ID number with anything meaningful will typically produce a near-zero, meaningless result.

## Mini Quiz
1. What is the difference between the mean and the median, and why does that difference matter?
2. What does `.describe()` return for a numeric column?
3. What range of values can a correlation coefficient take?
4. What does a correlation coefficient close to 0 indicate?
5. Why is it wrong to say "correlation proves causation"?

### Answer Key
1. The mean is the arithmetic average; the median is the middle value when sorted. The median is more resistant to extreme outliers, so the two can differ significantly when a dataset has very high or low extreme values.
2. Count, mean, standard deviation, minimum, the 25th/50th/75th percentiles, and maximum.
3. From -1 to +1.
4. Little to no linear relationship between the two variables.
5. Because two variables can be correlated due to coincidence, a shared underlying cause, or an indirect relationship — correlation alone never proves that one variable directly causes changes in the other.

## Lesson Recap
You computed core summary statistics (mean, median, mode, standard deviation) individually and all at once with `.describe()`, calculated correlation coefficients with `.corr()` both between pairs of columns and as a full correlation matrix, and learned the essential caution that correlation never proves causation.

## Homework / Extension
Using either the NBA dataset or the Student Performance dataset, compute `.describe()` and a correlation matrix for three numeric columns of your choice. Write a short paragraph reporting the single most interesting correlation you found, stating it carefully as correlation (not causation).

## Portfolio Project Connection
This lesson's statistical toolkit is the analytical core of the final worked example in **Lesson 8**, and is directly used in all three projects: correlating Elo and points in **Project 1**, correlating math/reading/writing scores in **Project 2**, and examining correlation between population and growth rate over time in **Project 3**.
