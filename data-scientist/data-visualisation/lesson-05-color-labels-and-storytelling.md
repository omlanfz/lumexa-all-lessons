# Course 14: Data Visualisation — Lesson 5
## Color, Labels, and Storytelling

**Target Age:** 13–18
**Duration:** 55 minutes (instructor-led)

---

### Learning Objectives
1. Apply intentional, accessible color choices (sequential, diverging, categorical palettes).
2. Write titles and annotations that state an insight rather than just a label.
3. Use annotations and callouts to guide a viewer's eye to the key takeaway.
4. Consider colorblind-accessible palettes and design for accessibility.
5. Build one complete "story chart" combining all these techniques.

### What You'll Learn
- The three main palette types: **sequential** (for ordered/low-to-high data), **diverging** (for data with a meaningful zero/midpoint, like positive vs. negative), and **categorical/qualitative** (for unordered groups).
- Why color choice must match data type — using a categorical rainbow palette for ordered data (or vice versa) confuses viewers.
- How to design for colorblind accessibility (roughly 1 in 12 men and 1 in 200 women have some form of color vision deficiency).
- The anatomy of a strong chart title: specific, insight-driven, not generic.
- How to add annotations (`ax.annotate`, `ax.text`) that highlight the most important data point.

### Why It Matters
A technically correct chart can still fail to communicate if its colors are confusing or its title is generic. "Data storytelling" is the practice of designing a chart so a viewer walks away with the *one insight* you intended — not a jumble of numbers they have to decode themselves. This is one of the highest-leverage skills in data visualisation: the same data, well-titled and well-colored, can be the difference between a chart that gets ignored and one that changes a decision.

### Real-World Connection
- **Public health communication**: During COVID-19, dashboards using clear diverging color scales (red = rising cases, blue = falling) helped the public and policymakers act quickly.
- **Accessibility law**: Many countries require public-facing digital content (including government dashboards) to be accessible to colorblind users — data visualisation professionals must know how to design around this.
- **Journalism headlines**: The best data journalism (FiveThirtyEight, The Pudding, NYT's The Upshot) always pairs a chart with a headline-style title stating the actual finding, not a generic label.
- **Portfolio & interviews**: Interviewers evaluating a data science portfolio routinely judge candidates on whether their chart titles state an insight — this is a fast, visible signal of communication skill.

### Key Terminology
| Term | Definition |
|---|---|
| **Sequential palette** | A color scale that goes from light to dark (or one hue's intensity), for ordered numeric data (e.g., `"Blues"`, `"viridis"`). |
| **Diverging palette** | A color scale with two contrasting hues meeting at a neutral midpoint, for data with a meaningful zero (e.g., `"coolwarm"`, `"RdBu"`). |
| **Categorical (qualitative) palette** | A set of distinct, unordered colors for unordered categories (e.g., `"Set2"`, `"tab10"`). |
| **Colorblind-safe palette** | A palette chosen so its colors remain distinguishable for people with common color vision deficiencies (e.g., `"colorblind"` in Seaborn, `"viridis"` in Matplotlib). |
| **Annotation** | Text and/or an arrow added to a chart to highlight a specific data point or insight. |
| **Headline title** | A chart title that states the specific finding (e.g., "CO2 Emissions Tripled Since 1970") rather than a generic label (e.g., "CO2 Data"). |

### Concept Explanation
Color is one of the most powerful — and most easily misused — tools in data visualisation. The type of palette you choose must match the *type* of data you're encoding. **Sequential palettes** (like Matplotlib's `viridis` or Seaborn's `"Blues"`) use a single hue that gets darker/lighter as a value increases — ideal for ordered numeric data like population or temperature, where "more" should visually read as "more intense." **Diverging palettes** (like `"coolwarm"` or `"RdBu"`) use two contrasting hues meeting at a neutral middle color — ideal for data with a meaningful zero point, such as temperature anomaly (above/below average) or profit/loss (positive/negative). **Categorical palettes** (like `"Set2"` or `"tab10"`) use visually distinct, unordered colors — ideal for categories with no inherent order, like country names or team names. Using the wrong palette type is a subtle but common mistake: a rainbow categorical palette applied to ordered data (like years) implies false distinctness between adjacent values that are actually just "slightly more" or "slightly less."

Accessibility matters here too. Roughly 8% of men have some form of red-green color blindness, the most common type. Red/green combinations — extremely common in "bad = red, good = green" dashboards — are often indistinguishable to these viewers. Colorblind-safe alternatives (like using blue/orange instead of red/green, or `viridis`, which was specifically designed to be perceptually uniform and colorblind-safe) should be a default choice, not an afterthought.

Beyond color, the single highest-impact change a chart can get is a better **title**. Compare "Quarterly Sales" (generic label) to "Sales Grew 34% After the March Marketing Campaign" (headline title stating the actual insight). The second version tells the viewer exactly what to look for and why the chart matters, turning passive viewing into active understanding. Annotations (arrows and short text callouts pointing at specific data points) reinforce this further, directing the eye exactly where the story lives — the sharpest jump, the highest point, the year something changed.

### Step-by-Step Instruction
1. Build the same line chart three times with three different (deliberately mismatched, then correctly matched) palette types, to see the effect.
2. Rewrite a generic chart title as a headline title.
3. Add an annotation pointing at the most important data point.
4. Compare a red/green chart against a colorblind-safe blue/orange version.

```python
# lesson_05_color_labels_storytelling.py
# Course 14 - Data Visualisation | Lesson 5: Color, labels, and storytelling

import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns

sns.set_theme(style="whitegrid")

years = np.arange(2010, 2025)
# A fictionalized but realistic-shaped "renewable energy share (%)" trend
renewable_share = np.array([9, 10, 11, 13, 14.5, 16, 18, 20.5, 23, 26, 29.5, 33, 37, 41.5, 46])

# ---------- 1. GENERIC TITLE, no annotation, default color cycle ----------
fig, ax = plt.subplots(figsize=(7.5, 4.5))
ax.plot(years, renewable_share, marker="o")
ax.set_title("Energy Data")  # generic - what we want to AVOID
ax.set_xlabel("Year")
ax.set_ylabel("Renewable share (%)")
plt.tight_layout()
plt.savefig("lesson05_generic_version.png", dpi=120)
plt.close(fig)

# ---------- 2. HEADLINE TITLE + annotation + intentional sequential color ----------
fig, ax = plt.subplots(figsize=(8, 5))
cmap = plt.get_cmap("viridis")
ax.plot(years, renewable_share, marker="o", color=cmap(0.3), linewidth=2.5)
ax.fill_between(years, renewable_share, color=cmap(0.3), alpha=0.12)

ax.set_title("Renewable Energy Share More Than Quintupled From 2010 to 2024",
             fontsize=13, fontweight="bold")
ax.set_xlabel("Year")
ax.set_ylabel("Share of total energy from renewables (%)")
ax.set_ylim(0, 55)

# Annotate the key data points: start and end
ax.annotate(f"{renewable_share[0]}% in 2010", xy=(years[0], renewable_share[0]),
            xytext=(years[0] + 1.5, renewable_share[0] + 8),
            arrowprops=dict(arrowstyle="->", color="gray"))
ax.annotate(f"{renewable_share[-1]}% in 2024", xy=(years[-1], renewable_share[-1]),
            xytext=(years[-1] - 6, renewable_share[-1] - 10),
            arrowprops=dict(arrowstyle="->", color="gray"))

for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson05_storytelling_version.png", dpi=120)
plt.close(fig)

# ---------- 3. Diverging palette example: temperature ANOMALY (above/below average) ----------
anomaly_years = np.arange(2000, 2025)
rng = np.random.default_rng(3)
anomaly = np.linspace(-0.2, 1.1, len(anomaly_years)) + rng.normal(0, 0.08, len(anomaly_years))

fig, ax = plt.subplots(figsize=(8, 4.5))
colors = ["#d62728" if v >= 0 else "#1f77b4" for v in anomaly]  # diverging: red=above avg, blue=below avg
ax.bar(anomaly_years, anomaly, color=colors)
ax.axhline(0, color="black", linewidth=1)
ax.set_title("Global Temperature Anomaly Has Shifted From Below to Above Average",
             fontsize=12, fontweight="bold")
ax.set_xlabel("Year")
ax.set_ylabel("Temperature anomaly (°C vs. 20th-century average)")
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson05_diverging_palette.png", dpi=120)
plt.close(fig)

# ---------- 4. Colorblind-safe comparison: red/green vs blue/orange ----------
categories = ["Team A", "Team B"]
values_good_bad = [72, 45]

fig, axes = plt.subplots(1, 2, figsize=(9, 4))
axes[0].bar(categories, values_good_bad, color=["#2ca02c", "#d62728"])  # red/green - risky for colorblind viewers
axes[0].set_title("Red/Green (harder for colorblind viewers)", fontsize=10)
axes[1].bar(categories, values_good_bad, color=["#1f77b4", "#ff7f0e"])  # blue/orange - colorblind-safe
axes[1].set_title("Blue/Orange (colorblind-safe)", fontsize=10)
for ax in axes:
    ax.set_ylabel("Score")
plt.tight_layout()
plt.savefig("lesson05_colorblind_comparison.png", dpi=120)
plt.close(fig)

print("Saved: lesson05_generic_version.png, lesson05_storytelling_version.png,")
print("       lesson05_diverging_palette.png, lesson05_colorblind_comparison.png")
```

### Code Explanation
- Chart 1 intentionally uses a vague title ("Energy Data") and default styling to show the "before" state.
- Chart 2 rebuilds the exact same data with a **headline title** stating the actual insight, a single intentional color from a sequential colormap (`viridis`, appropriate since the data is ordered and always positive), a soft area fill for emphasis, and two annotations pointing at the start and end values — directly demonstrating the "story" technique.
- Chart 3 demonstrates a **diverging palette** use case: temperature anomaly data has a meaningful zero (the historical average), so bars are colored red above zero and blue below zero, with a bold horizontal line at zero for reference.
- Chart 4 places a red/green bar chart next to a blue/orange version of the identical data, letting students directly compare which pairing is easier to distinguish (this is best experienced by also viewing both through a colorblind simulation tool or grayscale filter during class discussion).

### Expected Output
Four PNG files: `lesson05_generic_version.png` (a plain, vaguely-titled line chart), `lesson05_storytelling_version.png` (the same data with a bold headline title, an area fill, and two arrow annotations marking the 2010 and 2024 values), `lesson05_diverging_palette.png` (a bar chart of temperature anomaly with bars colored red above zero and blue below, and a zero reference line), and `lesson05_colorblind_comparison.png` (two small bar charts side by side, one red/green and one blue/orange, using identical data).

### Guided Practice
With the instructor, students:
- View `lesson05_colorblind_comparison.png` and try squinting or converting it to grayscale (many phone cameras/apps have a "grayscale" or "colorblind simulation" filter) to see how much harder the red/green version becomes to read.
- Rewrite three more generic chart titles ("Sales Report," "Data 2024," "Chart 3") into headline titles for a hypothetical dataset described by the instructor.

### Hands-On Activity
Students take last lesson's bar chart (language usage in the coding club) and rewrite its title as a headline that states the actual finding ("Python Is Used by Over a Third of Coding Club Members"), then add one annotation pointing at the Python bar specifically.

### Student Challenge
Design a chart using a diverging palette for a dataset you invent where zero has real meaning (e.g., profit/loss by month, temperature change, score above/below a passing grade). Your chart must use red/blue (or another clearly diverging pair, not red/green) with a visible zero reference line, and a headline title.

### Common Mistakes
- Using a rainbow/categorical palette on ordered numeric data, implying false distinctness between similar values.
- Defaulting to red = bad / green = good without considering colorblind accessibility.
- Writing a chart title that just restates the axis labels ("Value vs. Year") instead of the actual insight.
- Adding so many annotations that the chart becomes cluttered — annotate only the 1-2 most important points.
- Using a diverging palette for data that doesn't actually have a meaningful zero/midpoint.

### Debugging Guidance
- If your annotation arrow points to the wrong spot, double check the `xy=` argument matches the exact data coordinate, and `xytext=` is the label's position (they are often confused).
- If colors look muted or wrong, confirm you're passing valid hex codes (e.g., `"#1f77b4"`) or named colors, not typos.
- If `plt.get_cmap("viridis")` throws a deprecation warning in newer Matplotlib versions, use `matplotlib.colormaps["viridis"]` instead — both work, but the newer API is preferred going forward.

### Mini Quiz
1. What kind of palette should you use for data with a meaningful zero/midpoint, like profit vs. loss?
2. Why is red/green a risky color pairing for accessibility?
3. What is a "headline title" and how does it differ from a generic title?
4. Give an example of when a sequential palette is the right choice.
5. What is one purpose of a chart annotation?

**Answer Key**
1. A diverging palette (e.g., red/blue).
2. Roughly 8% of men have red-green color blindness, making that specific pairing hard or impossible for them to distinguish.
3. A headline title states the specific insight or finding in the data (e.g., "Sales Grew 34%"), while a generic title just names the topic (e.g., "Sales Data").
4. Any ordered numeric data without a meaningful zero-crossing, such as population size, age, or renewable energy share over time.
5. To draw the viewer's attention to a specific important data point and explain why it matters.

### Lesson Recap
We learned to match palette type (sequential, diverging, categorical) to data type, design with colorblind accessibility in mind, write headline titles that state an actual insight, and use annotations to guide the viewer's eye to the most important part of a chart.

### Homework / Extension
Take any chart you've built in a previous lesson and give it a full "storytelling upgrade": rewrite its title as a headline, pick an intentional palette matching its data type, and add at least one annotation. Submit both the "before" and "after" versions.

### Portfolio Project Connection
Every chart in Projects 04, 05, and 06 must use a headline-style title and an intentional, accessible color palette — for example, Project 04's climate charts use a sequential palette for emissions-over-time and a diverging palette for temperature anomaly; Project 06 uses a consistent categorical palette to distinguish compared teams/players across every chart.
