# Course 14: Data Visualisation — Lesson 2
## Matplotlib: Line, Bar, Scatter Plots

**Target Age:** 13–18
**Duration:** 55 minutes (instructor-led)

---

### Learning Objectives
1. Create line, bar, and scatter plots using Matplotlib's object-oriented API.
2. Customize colors, markers, line styles, labels, titles, and legends.
3. Plot multiple series on one chart and interpret when to do so.
4. Save charts to disk as image files for reports and portfolios.
5. Choose between line, bar, and scatter plots based on the shape of a dataset.

### What You'll Learn
- The Matplotlib `Figure`/`Axes` object-oriented interface (`fig, ax = plt.subplots()`).
- How to build line charts (great for trends over time), bar charts (great for comparing categories), and scatter plots (great for relationships between two numeric variables).
- Styling: colors, markers, linewidths, legends, gridlines, and figure size.
- Saving high-resolution chart images with `plt.savefig()`.

### Why It Matters
Matplotlib is the foundational plotting library in the Python data science ecosystem — nearly every other visualisation library in Python (including Seaborn, which you'll learn next lesson) is built on top of it or interoperates with it. Mastering Matplotlib's core chart types gives you full control over every pixel of a chart, which is essential when you need a very specific, publication-quality figure — for a scientific paper, a school project, or a professional portfolio piece.

### Real-World Connection
- **Scientific research**: NASA, CERN, and most academic journals require Matplotlib-quality static figures for publications.
- **Finance**: Analysts use Matplotlib line charts to show stock price history (exactly what you'll do in Project 05).
- **Sports analytics**: Teams use bar charts to compare player stats side-by-side (Project 06).
- **Climate science**: Line charts of CO2 concentration over time (like the famous "Keeling Curve") are built with tools like Matplotlib (Project 04).

### Key Terminology
| Term | Definition |
|---|---|
| **Figure** | The entire window/canvas that holds one or more charts. |
| **Axes** | A single plot area within a figure (not to be confused with "axis," a single x or y line). |
| **Line plot** | A chart connecting data points with lines — ideal for showing trends over a continuous variable like time. |
| **Bar chart** | A chart using rectangular bars to compare quantities across discrete categories. |
| **Scatter plot** | A chart plotting individual points to show the relationship between two numeric variables. |
| **Legend** | A key that explains what each color/marker/line represents. |
| **DPI** | Dots per inch — controls the resolution of a saved image. |

### Concept Explanation
Matplotlib offers two interfaces: the older "pyplot" state-based interface (`plt.plot(...)`) and the modern **object-oriented interface** (`fig, ax = plt.subplots()`), which we use throughout this course because it scales better to multi-chart figures and dashboards. In the object-oriented approach, you first create a `Figure` (the overall canvas) and one or more `Axes` (individual plot areas) and then call plotting methods directly on the `Axes` object, such as `ax.plot()`, `ax.bar()`, or `ax.scatter()`.

Choosing the right chart type depends on the shape of your data and the question you're asking. **Line plots** are best when you have a continuous ordered variable (usually time) and want to show how a value changes — for example, CO2 emissions per year. **Bar charts** are best for comparing a value across distinct categories — for example, total emissions by country in a single year. **Scatter plots** are best when you want to see the relationship or correlation between two numeric variables — for example, GDP versus CO2 emissions, where each point is one country.

Good styling turns a "readable" chart into a "professional" one. This includes: a clear, specific title; labeled axes with units; a legend when there is more than one series; consistent, meaningful use of color (not just Matplotlib's random default color cycle); and appropriate figure size so text isn't crowded. Matplotlib gives full control over every one of these elements, which is why it remains the tool professionals reach for when a chart needs to be exactly right.

### Step-by-Step Instruction
1. Create a line chart showing a trend over time using two series.
2. Create a bar chart comparing categories, including value labels on top of bars.
3. Create a scatter plot showing the relationship between two variables, with point size/color encoding a third variable.
4. Add titles, axis labels, legends, and gridlines to all three.
5. Save all three charts as PNG files.

```python
# lesson_02_line_bar_scatter.py
# Course 14 - Data Visualisation | Lesson 2: Matplotlib line, bar, scatter plots

import matplotlib.pyplot as plt
import numpy as np

# ---------- 1. LINE PLOT: temperature trend over a year for two cities ----------
months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"]
city_a_temp = [3, 4, 8, 13, 18, 22, 25, 24, 20, 14, 8, 4]     # e.g. Berlin-like climate
city_b_temp = [22, 23, 24, 26, 27, 29, 30, 30, 28, 26, 24, 22]  # e.g. Miami-like climate

fig, ax = plt.subplots(figsize=(8, 4.5))
ax.plot(months, city_a_temp, marker="o", color="#1f77b4", linewidth=2, label="City A (temperate)")
ax.plot(months, city_b_temp, marker="s", color="#d62728", linewidth=2, label="City B (tropical)")
ax.set_title("Average Monthly Temperature: Two Cities Compared", fontsize=13, fontweight="bold")
ax.set_xlabel("Month")
ax.set_ylabel("Average temperature (°C)")
ax.legend(loc="lower center", ncol=2, frameon=False)
ax.grid(alpha=0.3)
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson02_line_plot.png", dpi=120)
plt.close(fig)

# ---------- 2. BAR CHART: comparing total rainfall by season ----------
seasons = ["Winter", "Spring", "Summer", "Fall"]
rainfall_mm = [180, 220, 90, 160]

fig, ax = plt.subplots(figsize=(7, 4.5))
bars = ax.bar(seasons, rainfall_mm, color="#2a9d8f", edgecolor="white")
ax.set_title("Total Rainfall by Season (mm)", fontsize=13, fontweight="bold")
ax.set_xlabel("Season")
ax.set_ylabel("Rainfall (mm)")
ax.set_ylim(0, max(rainfall_mm) * 1.2)
for bar in bars:
    height = bar.get_height()
    ax.annotate(f"{height}", xy=(bar.get_x() + bar.get_width() / 2, height),
                xytext=(0, 4), textcoords="offset points", ha="center", fontsize=10)
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson02_bar_chart.png", dpi=120)
plt.close(fig)

# ---------- 3. SCATTER PLOT: relationship between study hours and screen time ----------
rng = np.random.default_rng(42)
study_hours = rng.uniform(0, 10, 40)
screen_time = 8 - 0.6 * study_hours + rng.normal(0, 1, 40)
quiz_scores = 50 + 4.2 * study_hours + rng.normal(0, 5, 40)

fig, ax = plt.subplots(figsize=(7, 5))
scatter = ax.scatter(study_hours, screen_time, c=quiz_scores, cmap="viridis", s=80, edgecolor="white")
ax.set_title("Study Hours vs. Screen Time (colored by quiz score)", fontsize=13, fontweight="bold")
ax.set_xlabel("Study hours per week")
ax.set_ylabel("Screen time per day (hours)")
cbar = plt.colorbar(scatter, ax=ax)
cbar.set_label("Quiz score (%)")
ax.grid(alpha=0.3)
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson02_scatter_plot.png", dpi=120)
plt.close(fig)

print("Saved: lesson02_line_plot.png, lesson02_bar_chart.png, lesson02_scatter_plot.png")
```

### Code Explanation
- **Line plot**: two lists of temperatures are plotted against months using `ax.plot()` with distinct markers (`o` vs `s`) and colors, a legend, and non-zero-starting but honestly-labeled y-axis (temperature naturally can be negative, so zero is not required here).
- **Bar chart**: `ax.bar()` draws one bar per season; a loop over `bars` uses `ax.annotate()` to print the exact value above each bar, which improves precision beyond just reading bar height visually.
- **Scatter plot**: `ax.scatter()` plots 40 simulated points, using the `c=` parameter to color each point by a third variable (quiz score) with the `viridis` colormap, and `plt.colorbar()` adds a legend for that color encoding. `np.random.default_rng(42)` is used with a fixed seed so the "random" data is reproducible for grading/comparison.
- All three charts remove the top/right spines and use `plt.tight_layout()` + `plt.savefig(..., dpi=120)` for clean, portfolio-ready output.

### Expected Output
Three PNG files: `lesson02_line_plot.png` (two colored lines with markers, legend, and gridlines showing temperature trends across 12 months), `lesson02_bar_chart.png` (four green bars with the exact rainfall value labeled above each bar), and `lesson02_scatter_plot.png` (40 scattered points colored from dark purple to yellow according to quiz score, with a colorbar on the right).

### Guided Practice
With the instructor, students:
- Change the bar chart's color to a different hex code and re-run.
- Add a third city's temperature line to the line chart.
- Change the scatter plot's colormap from `"viridis"` to `"plasma"` and discuss how the story feels different.

### Hands-On Activity
Students bring their own small dataset idea (e.g., steps walked per day this week, or favorite game scores across 5 attempts) and build one line chart AND one bar chart from it, following the styling patterns above (title, labels, no unnecessary decoration).

### Student Challenge
Combine all three chart types into a single figure with 3 subplots side-by-side using `fig, axes = plt.subplots(1, 3, figsize=(15, 4))`, plotting the line, bar, and scatter charts above into `axes[0]`, `axes[1]`, `axes[2]` respectively, with a single overall figure title using `fig.suptitle(...)`.

### Common Mistakes
- Forgetting `plt.close(fig)` when creating many figures in a loop, which can cause memory buildup or overlapping plots.
- Using `plt.bar()` (pyplot-style) mixed with `ax.plot()` (object-oriented style) inconsistently, causing confusing bugs about which axes is being drawn on.
- Choosing a bar chart when a line chart would better show a trend over time, or vice versa.
- Not calling `plt.tight_layout()`, resulting in cut-off axis labels in the saved image.
- Using default Matplotlib colors for every chart without intention — always ask "does this color mean something?"

### Debugging Guidance
- If your saved PNG is blank, make sure you call `plt.savefig()` **before** `plt.show()` or `plt.close()` — closing the figure first clears it.
- If bars appear in the wrong order, check that your categories are in the order you intended (Matplotlib plots categories in the order given, not alphabetically).
- If the colorbar looks wrong or is missing, make sure you pass `ax=ax` to `plt.colorbar()` so it attaches to the correct axes.
- If text labels overlap, try `fig.autofmt_xdate()` (for date x-axes) or reduce the number of x-tick labels.

### Mini Quiz
1. Which chart type is best for showing a value changing continuously over time?
2. Which chart type is best for comparing quantities across a small number of categories?
3. What does the `c=` parameter do in `ax.scatter()`?
4. What is the purpose of `plt.tight_layout()`?
5. Name one reason to use a fixed random seed (e.g., `np.random.default_rng(42)`) when generating example data.

**Answer Key**
1. Line plot.
2. Bar chart.
3. It sets the color of each point, often mapped to a third variable via a colormap.
4. It automatically adjusts spacing so labels, titles, and ticks don't get cut off or overlap.
5. It makes the "random" output reproducible — the same code produces the same data every time it runs, which is important for grading, debugging, and comparing results.

### Lesson Recap
We built line, bar, and scatter plots using Matplotlib's object-oriented `fig, ax` interface, learned when each chart type is the right choice, styled charts with titles/labels/legends/colors, and saved them as high-resolution PNG files.

### Homework / Extension
Using any dataset you like (even one you make up but label as "example data"), produce one line chart, one bar chart, and one scatter chart, each following the full styling checklist from Lesson 1 (honest axis, clear title, labeled axes, minimal chart junk).

### Portfolio Project Connection
Project 05 (Market Trends Chart) directly uses a Matplotlib **line chart** to show Apple's stock price over time with moving averages. Project 06 (Sports Comparison Visualiser) uses **bar charts** to compare team/player stats side-by-side. Project 04 (Climate Change Dashboard) uses **line and scatter charts** as the foundation for its interactive Dash version.
