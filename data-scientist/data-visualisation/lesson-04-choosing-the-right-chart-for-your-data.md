# Course 14: Data Visualisation — Lesson 4
## Choosing the Right Chart for Your Data

**Target Age:** 13–18
**Duration:** 50 minutes (instructor-led)

---

### Learning Objectives
1. Classify a dataset by its variable types (categorical, numeric, time-based, part-to-whole).
2. Use a decision framework to choose the correct chart type for a given question.
3. Identify when a chart type is being misused for the wrong kind of data.
4. Rebuild a poorly-chosen chart as a better-suited chart type.

### What You'll Learn
- A practical decision framework: "What is my question, and what type of data do I have?"
- Chart types matched to data situations: comparison, distribution, relationship, composition, and trend over time.
- Why pie charts are usually a poor choice, and when (rarely) they're acceptable.
- How to convert data between chart types using pandas + Matplotlib/Seaborn.

### Why It Matters
Knowing how to build a chart (Lessons 2-3) is only half the skill — knowing *which* chart to build is what separates an amateur from a professional data scientist. The exact same dataset can be shown as a bar chart, line chart, pie chart, or scatter plot, but only one or two of those choices will clearly answer the question you're actually trying to answer. Picking the wrong chart type is one of the most common reasons a good dataset produces a confusing or misleading visualisation.

### Real-World Connection
- **Business dashboards**: Executives often ask for "a pie chart" when a bar chart would show the same data far more clearly — a good data scientist can explain why and suggest the better option.
- **News graphics**: Data journalists constantly choose between chart types under time pressure; picking wrong can distort public understanding (e.g., during elections or health crises).
- **Scientific publishing**: Journals have strict guidelines on chart types partly because using the wrong type can misrepresent research findings.
- **Portfolio projects**: All three of your Course 14 portfolio projects (climate, market, sports) each combine several chart types purposefully, not by default.

### Key Terminology
| Term | Definition |
|---|---|
| **Categorical data** | Data split into named groups/categories (e.g., team names, countries). |
| **Numeric (quantitative) data** | Data measured in numbers (e.g., temperature, price, score). |
| **Time series data** | Data recorded at successive points in time. |
| **Part-to-whole data** | Data describing parts making up 100% of a total (e.g., market share). |
| **Composition chart** | A chart (stacked bar, pie, treemap) showing how parts make up a whole. |
| **Chart decision framework** | A structured set of questions used to pick the right chart type for a dataset and goal. |

### Concept Explanation
Every chart-choice decision starts with two questions: **(1) what type of data do I have**, and **(2) what question am I trying to answer about it?** Data generally falls into a few types: categorical (named groups), numeric/quantitative (measured values), time series (values recorded over time), and part-to-whole (parts of a total). Layered on top of the data type is the *goal*: are you comparing categories, showing a trend, exploring a relationship, or showing composition?

A simple, practical framework used by professional data visualisation designers is:
- **Comparison across categories** → bar chart (horizontal bar chart if category names are long).
- **Trend over time** → line chart.
- **Relationship between two numeric variables** → scatter plot.
- **Distribution of one numeric variable** → histogram or box plot.
- **Composition / part-to-whole** → stacked bar chart (preferred) or, in limited cases, a pie chart.

Pie charts deserve special attention because they are simultaneously one of the most requested and most misused chart types. Because humans are much worse at accurately judging angles and areas than they are at judging length or position (as covered in Lesson 1), pie charts with more than 3-4 slices, or slices of similar size, are genuinely hard to read accurately. In almost every case, the same part-to-whole data is *more* clearly shown as a simple sorted horizontal bar chart. Professional data visualisation guidelines (including those from the U.S. government's own data.gov style guide, and news outlets like the BBC) generally recommend avoiding pie charts except for very simple two- or three-slice breakdowns where the story is truly "most vs. rest."

Another common mismatch is using a bar chart for time series with many time points (creates a cluttered "picket fence" look better shown as a line), or using a scatter plot for categorical data (which has no numeric relationship to show). Recognizing these patterns takes practice, which is exactly what this lesson's activities focus on.

### Step-by-Step Instruction
1. Review a "bad chart" example and diagnose what data situation it's actually representing.
2. Rebuild the same data as a well-chosen chart type.
3. Practice the "5 data situations" framework on multiple mini-datasets.
4. Compare a pie chart directly against a sorted bar chart on the same data.

```python
# lesson_04_choosing_the_right_chart.py
# Course 14 - Data Visualisation | Lesson 4: Choosing the right chart for your data

import matplotlib.pyplot as plt
import pandas as pd

# ---- Example dataset: market share of programming languages used by students in a coding club ----
languages = ["Python", "JavaScript", "Scratch", "Java", "C++", "Other"]
share_pct = [38, 24, 16, 10, 7, 5]

df = pd.DataFrame({"language": languages, "share_pct": share_pct}).sort_values("share_pct", ascending=False)

# ---- BAD CHOICE (for comparison/discussion): a pie chart with 6 slices ----
fig, ax = plt.subplots(figsize=(6, 6))
ax.pie(df["share_pct"], labels=df["language"], autopct="%1.0f%%", startangle=90,
       colors=plt.cm.Set3.colors)
ax.set_title("Coding Club Language Use (Pie Chart - hard to compare similar slices)", fontsize=11)
plt.tight_layout()
plt.savefig("lesson04_bad_pie_chart.png", dpi=120)
plt.close(fig)

# ---- BETTER CHOICE: a sorted horizontal bar chart ----
fig, ax = plt.subplots(figsize=(7, 4.5))
bars = ax.barh(df["language"], df["share_pct"], color="#4c72b0")
ax.invert_yaxis()  # largest share at the top
ax.set_title("Coding Club Language Use, Ranked", fontsize=13, fontweight="bold")
ax.set_xlabel("Share of students (%)")
for bar in bars:
    width = bar.get_width()
    ax.annotate(f"{width}%", xy=(width, bar.get_y() + bar.get_height() / 2),
                xytext=(4, 0), textcoords="offset points", va="center", fontsize=10)
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson04_better_bar_chart.png", dpi=120)
plt.close(fig)

# ---- Chart-type decision helper function ----
def recommend_chart(data_type: str, goal: str) -> str:
    """A tiny rule-based recommender to reinforce the decision framework."""
    rules = {
        ("categorical", "comparison"): "Bar chart (horizontal if labels are long)",
        ("time_series", "trend"): "Line chart",
        ("numeric_pair", "relationship"): "Scatter plot",
        ("numeric", "distribution"): "Histogram or box plot",
        ("part_to_whole", "composition"): "Stacked bar chart (or pie chart only if <=3 slices)",
    }
    return rules.get((data_type, goal), "Not enough information - clarify data type and goal")

examples = [
    ("categorical", "comparison"),
    ("time_series", "trend"),
    ("numeric_pair", "relationship"),
    ("numeric", "distribution"),
    ("part_to_whole", "composition"),
]
for data_type, goal in examples:
    print(f"Data type = {data_type:15s} | Goal = {goal:12s} -> Recommended chart: {recommend_chart(data_type, goal)}")

print("\nSaved: lesson04_bad_pie_chart.png, lesson04_better_bar_chart.png")
```

### Code Explanation
- The dataset represents part-to-whole data (percentages of students using each language, summing to 100%).
- The pie chart version uses `ax.pie()` with 6 slices — intentionally built as the "bad choice" example so students can see for themselves how hard it is to compare JavaScript (24%) vs. Scratch (16%) by eye.
- The bar chart version uses `ax.barh()` (horizontal bars, better for longer category names), sorted descending and with `ax.invert_yaxis()` so the largest share appears at the top, plus an exact percentage label on each bar.
- The `recommend_chart()` function is a simple rule-based decision helper that encodes the lesson's framework as executable code, reinforcing the mapping from (data type, goal) to chart type.

### Expected Output
Two PNG files: `lesson04_bad_pie_chart.png` (a 6-slice pie chart where the 24% and 16% slices are visually hard to distinguish) and `lesson04_better_bar_chart.png` (a cleanly sorted horizontal bar chart with exact percentage labels, clearly showing Python's dominant 38% share). The console prints a table of five (data type, goal) -> chart type recommendations from the `recommend_chart()` function.

### Guided Practice
With the instructor, students look at both saved images side-by-side and vote (by show of hands) on which chart makes it easier to answer: "Is JavaScript's usage bigger than Scratch's?" Discuss why the bar chart wins even though both charts show identical numbers.

### Hands-On Activity
Given three example scenarios read aloud by the instructor (e.g., "monthly ice cream sales for one year," "top 5 highest scoring video games this month," "relationship between screen brightness and battery life"), students individually write down which chart type they'd choose and why, then share and discuss as a class.

### Student Challenge
Take the `df` language-share example and add a 7th category, "Rust," at 2%. Rebuild the bar chart. Then write two sentences arguing whether a pie chart would ever be acceptable for this specific dataset (hint: think about number of slices and how similar the slice sizes are).

### Common Mistakes
- Defaulting to a pie chart because it "looks fun," even when a bar chart communicates the same data more clearly.
- Using a bar chart for a variable that's actually continuous time (e.g., daily temperature over a year), producing a cluttered "picket fence" of hundreds of bars instead of a clean line.
- Forgetting to sort categorical bar charts by value, forcing the reader to hunt for the largest/smallest category.
- Using 3D pie or bar charts, which distort the perceived size of slices/bars even further (see Lesson 1).

### Debugging Guidance
- If `ax.pie()` slices don't sum visually to a full circle, check that your percentages actually sum close to 100 (small rounding is fine, big gaps indicate a data problem).
- If `ax.barh()` bars appear in reverse of what you expect, remember `ax.invert_yaxis()` flips the display order — remove it if you want ascending order instead.
- If annotation text overlaps the bars, increase the `xytext` offset value or the figure width.

### Mini Quiz
1. What chart type is generally best for showing a trend over time?
2. Why are pie charts hard to read when there are many similar-sized slices?
3. What data type is "team name" — categorical or numeric?
4. When comparing categories, why is sorting bars by value usually preferred over alphabetical order?
5. Name one chart type well-suited to showing the relationship between two numeric variables.

**Answer Key**
1. Line chart.
2. Because humans perceive angle and area less accurately than length/position, making similar-sized slices hard to compare precisely.
3. Categorical.
4. Sorting by value lets the reader instantly see rank order (largest to smallest) without having to scan the whole chart.
5. Scatter plot.

### Lesson Recap
Today we built a practical decision framework for choosing chart types based on data type and goal, saw a direct side-by-side comparison of a misused pie chart versus a clear sorted bar chart, and practiced applying the framework to new scenarios.

### Homework / Extension
Find a chart online that you believe uses the wrong chart type for its data. Screenshot or describe it, explain what data situation it represents, and sketch (on paper or in code) what chart type you would use instead and why.

### Portfolio Project Connection
This decision framework is applied directly across all three portfolio projects: Project 04 uses line charts for CO2 trends over time (not bar charts), Project 05 uses line charts with moving averages for stock price trends, and Project 06 uses bar and radar-style comparisons (never pie charts) for comparing teams/players.
