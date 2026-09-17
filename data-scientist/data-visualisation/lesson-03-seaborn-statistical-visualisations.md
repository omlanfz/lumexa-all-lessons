# Course 14: Data Visualisation — Lesson 3
## Seaborn: Statistical Visualisations

**Target Age:** 13–18
**Duration:** 55 minutes (instructor-led)

---

### Learning Objectives
1. Explain how Seaborn builds on Matplotlib to simplify statistical charts.
2. Build histograms, box plots, and heatmaps using Seaborn.
3. Use Seaborn's built-in themes to instantly improve chart aesthetics.
4. Work directly with pandas DataFrames to create multi-variable statistical plots.
5. Interpret a correlation heatmap.

### What You'll Learn
- Why Seaborn exists and how it differs from raw Matplotlib.
- `sns.histplot()` for distributions, `sns.boxplot()` for comparing spread across groups, and `sns.heatmap()` for correlation matrices.
- Seaborn themes (`sns.set_theme()`) and color palettes.
- How to pass a pandas DataFrame directly into Seaborn functions using the `data=`, `x=`, `y=`, `hue=` pattern.

### Why It Matters
While Matplotlib gives you full pixel-level control, it can take 10+ lines of code to build a statistically meaningful chart like a box plot with grouped categories. Seaborn is built directly on top of Matplotlib and is designed specifically for statistical data — it can build the same chart in 1–2 lines, with better default styling, and it understands pandas DataFrames natively. This makes Seaborn the go-to tool for the "exploratory data analysis" phase that every real data science project starts with.

### Real-World Connection
- **Medical research**: Researchers use box plots to compare treatment outcomes between patient groups.
- **Sports analytics**: Analysts use heatmaps to show correlations between player statistics (e.g., does more 3-point attempts correlate with more turnovers?) — directly relevant to Project 06.
- **Social science**: Surveys are often visualized with histograms showing distributions of responses (e.g., age, income).
- **Data science interviews**: Seaborn is one of the most common libraries used in take-home data analysis exercises.

### Key Terminology
| Term | Definition |
|---|---|
| **Distribution** | The overall shape/spread of a set of values (are most values clustered, or spread out?). |
| **Histogram** | A chart that bins numeric data into ranges and shows the count in each range. |
| **Box plot** | A chart summarizing a distribution's median, quartiles, and outliers using a box-and-whisker shape. |
| **Outlier** | A data point that is unusually far from most other points. |
| **Heatmap** | A grid of colored cells, often used to show a correlation matrix between multiple variables. |
| **Correlation** | A statistical measure (from -1 to 1) of how strongly two variables move together. |
| **Hue** | In Seaborn, a parameter that colors data points/bars by a categorical variable, effectively adding a third dimension. |

### Concept Explanation
Seaborn was created to fill a gap: Matplotlib is a general-purpose plotting library, but doing *statistical* plotting (distributions, group comparisons, correlations) with it alone requires a lot of manual computation and styling. Seaborn wraps Matplotlib with functions that understand statistical concepts directly. For example, `sns.histplot(data=df, x="score")` automatically bins continuous data and draws a histogram with sensible defaults, and can even overlay a smoothed density curve with one extra argument (`kde=True`).

A **box plot** is one of the most information-dense statistical charts available: in a single small shape, it shows the median (the line in the middle of the box), the interquartile range (the box itself, spanning the 25th to 75th percentile), and outliers (individual points beyond the "whiskers"). Box plots are especially powerful when combined with a `hue` or category on the x-axis, letting you compare the *spread* of a variable across multiple groups at once — for example, comparing NBA team point totals across different seasons, or comparing CO2 emissions per capita across continents.

A **heatmap** of a correlation matrix is a classic first step in almost every real data science project. Correlation values range from -1 (perfectly inverse relationship) to +1 (perfectly direct relationship), with 0 meaning no linear relationship. By coloring a grid of these values (often with a diverging colormap like `"coolwarm"`, where red means positive and blue means negative), analysts can spot which variables are strongly related at a glance — far faster than reading a table of numbers. It's important to remember correlation does not imply causation: two variables can be correlated without one causing the other.

### Step-by-Step Instruction
1. Set a Seaborn theme for consistent, attractive default styling.
2. Load a small pandas DataFrame of made-up-but-realistic student performance data.
3. Build a histogram with a KDE (kernel density estimate) overlay showing the distribution of quiz scores.
4. Build a box plot comparing quiz scores across three different study groups.
5. Compute a correlation matrix and visualize it as a heatmap.

```python
# lesson_03_seaborn_statistical_plots.py
# Course 14 - Data Visualisation | Lesson 3: Seaborn statistical visualisations

import seaborn as sns
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

# Step 1: Apply a clean, professional Seaborn theme globally
sns.set_theme(style="whitegrid", palette="deep")

# Step 2: Build a small, realistic student-performance dataset
rng = np.random.default_rng(7)
n = 150
study_group = rng.choice(["Morning Study", "Evening Study", "No Fixed Schedule"], size=n, p=[0.4, 0.35, 0.25])
base_scores = {"Morning Study": 78, "Evening Study": 74, "No Fixed Schedule": 65}
quiz_score = np.array([base_scores[g] for g in study_group]) + rng.normal(0, 9, n)
quiz_score = np.clip(quiz_score, 0, 100)
hours_slept = rng.normal(7, 1.1, n)
screen_time = rng.normal(4, 1.5, n)

df = pd.DataFrame({
    "study_group": study_group,
    "quiz_score": quiz_score,
    "hours_slept": hours_slept,
    "screen_time": screen_time,
})

# Step 3: Histogram with KDE overlay - shows the SHAPE of the quiz score distribution
fig, ax = plt.subplots(figsize=(7, 4.5))
sns.histplot(data=df, x="quiz_score", bins=20, kde=True, color="#4c72b0", ax=ax)
ax.set_title("Distribution of Quiz Scores (n=150 students)", fontsize=13, fontweight="bold")
ax.set_xlabel("Quiz score (%)")
ax.set_ylabel("Number of students")
plt.tight_layout()
plt.savefig("lesson03_histogram.png", dpi=120)
plt.close(fig)

# Step 4: Box plot comparing quiz scores across study groups
fig, ax = plt.subplots(figsize=(7.5, 5))
order = ["Morning Study", "Evening Study", "No Fixed Schedule"]
sns.boxplot(data=df, x="study_group", y="quiz_score", order=order, hue="study_group",
            palette="Set2", legend=False, ax=ax)
ax.set_title("Quiz Scores by Study Habit", fontsize=13, fontweight="bold")
ax.set_xlabel("Study group")
ax.set_ylabel("Quiz score (%)")
plt.tight_layout()
plt.savefig("lesson03_boxplot.png", dpi=120)
plt.close(fig)

# Step 5: Correlation heatmap across the numeric variables
numeric_df = df[["quiz_score", "hours_slept", "screen_time"]]
corr = numeric_df.corr()

fig, ax = plt.subplots(figsize=(5.5, 4.5))
sns.heatmap(corr, annot=True, fmt=".2f", cmap="coolwarm", center=0, square=True, ax=ax)
ax.set_title("Correlation Between Study-Related Variables", fontsize=12, fontweight="bold")
plt.tight_layout()
plt.savefig("lesson03_heatmap.png", dpi=120)
plt.close(fig)

print(corr)
print("\nSaved: lesson03_histogram.png, lesson03_boxplot.png, lesson03_heatmap.png")
```

### Code Explanation
- `sns.set_theme(style="whitegrid", palette="deep")` sets a global look (light gridlines, a pleasant default color palette) applied to every chart in the script.
- The synthetic DataFrame simulates 150 students across three study habits, with quiz scores that are intentionally correlated with study group (so the box plot shows a real, visible difference) plus randomly-generated sleep and screen-time columns.
- `sns.histplot(..., kde=True)` draws both the binned histogram bars and a smooth density curve overlay in one call.
- `sns.boxplot(..., order=order, hue="study_group", legend=False)` ensures the three groups appear left-to-right in a logical order (not alphabetical) and colors each box distinctly.
- `numeric_df.corr()` computes Pearson correlation coefficients between all pairs of numeric columns, returning a DataFrame that `sns.heatmap(..., annot=True)` visualizes with both color and printed numeric values in each cell.

### Expected Output
Three PNG files: `lesson03_histogram.png` (a bell-ish shaped histogram of quiz scores with a smooth KDE curve overlaid), `lesson03_boxplot.png` (three box-and-whisker plots side by side, with "Morning Study" showing a visibly higher median than "No Fixed Schedule"), and `lesson03_heatmap.png` (a 3x3 colored grid showing correlation values between quiz_score, hours_slept, and screen_time, with quiz_score and hours_slept likely showing a mild positive correlation). The console also prints the raw correlation matrix as a table.

### Guided Practice
With the instructor:
- Change `sns.set_theme(style="whitegrid")` to `style="darkgrid"` and re-run all charts, discussing the visual difference.
- Add a fourth study group to the simulated data and re-run the box plot.
- Change the heatmap's `cmap` from `"coolwarm"` to `"viridis"` and discuss why a diverging colormap (coolwarm) is more appropriate for correlation data than a sequential one (viridis).

### Hands-On Activity
Using the `df` DataFrame already built, students create a new box plot comparing `hours_slept` across `study_group`, then write 2-3 sentences interpreting whether students who study at a fixed time also tend to sleep more.

### Student Challenge
Add a new synthetic column called `extra_credit_hours` correlated with `quiz_score`, recompute the correlation matrix including it, and rebuild the heatmap. Identify which pair of variables has the strongest correlation and explain what that means (and doesn't mean) in one paragraph.

### Common Mistakes
- Confusing correlation with causation — a strong correlation between sleep and quiz scores does not prove sleep *causes* better scores.
- Forgetting to call `.corr()` only on numeric columns — Seaborn/pandas will error or behave unexpectedly if text columns are included.
- Using too many bins in a histogram, making it noisy, or too few, hiding real structure — try a few different `bins=` values.
- Not sorting/ordering categorical groups explicitly, resulting in a confusing default (often alphabetical) order in box plots.

### Debugging Guidance
- If `sns.heatmap()` throws an error about non-numeric data, check that you called `.corr()` only on a DataFrame subset of numeric columns.
- If box plots show a `FutureWarning` about `hue` without `legend=False`, add `hue=<same column as x>` and `legend=False` as shown above (this is the modern recommended Seaborn pattern for coloring a single categorical variable).
- If nothing appears different after `sns.set_theme()`, make sure it's called before creating any figures.
- If your KDE curve looks like a flat line, check that your data actually has variation (not all identical values).

### Mini Quiz
1. What does a box plot's middle line represent?
2. What does a correlation of -0.9 mean, in words?
3. Why is Seaborn often faster to use than raw Matplotlib for statistical charts?
4. What is a KDE?
5. Name one risk of interpreting a correlation heatmap.

**Answer Key**
1. The median of the data.
2. A strong negative (inverse) relationship — as one variable increases, the other tends to decrease strongly.
3. Because it understands statistical concepts and pandas DataFrames natively, letting you build complex statistical charts in one or two lines instead of many.
4. Kernel Density Estimate — a smoothed curve approximating the probability distribution of the data, often overlaid on a histogram.
5. Mistaking correlation for causation, or over-interpreting a correlation that's actually driven by a hidden third variable.

### Lesson Recap
We learned how Seaborn builds on Matplotlib for statistical plotting, built a histogram with KDE, a grouped box plot, and a correlation heatmap, all directly from a pandas DataFrame, and practiced interpreting correlation values responsibly.

### Homework / Extension
Using the `df` from class (or your own dataset), build one more Seaborn chart type not covered today: a **violin plot** (`sns.violinplot`). Research what it shows differently from a box plot and write a short explanation.

### Portfolio Project Connection
Project 06 (Sports Comparison Visualiser) uses correlation/statistical thinking when comparing team and player metrics over time. The box plot and heatmap techniques from this lesson are directly reusable for exploring the real RAPTOR and Elo datasets before building the final interactive comparisons.
