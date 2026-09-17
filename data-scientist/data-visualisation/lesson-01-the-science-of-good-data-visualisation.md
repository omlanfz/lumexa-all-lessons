# Course 14: Data Visualisation — Lesson 1
## The Science of Good Data Visualisation

**Target Age:** 13–18
**Duration:** 50 minutes (instructor-led)

---

### Learning Objectives
By the end of this lesson, students will be able to:
1. Explain why data visualisation is a core skill for data scientists.
2. Identify the perceptual principles that make a chart easy or hard to read.
3. Recognise at least five common visualisation "sins" (chart junk, misleading axes, 3D distortion, etc.).
4. Critique an existing chart using a structured checklist.
5. Set up a Python environment with Matplotlib, Seaborn, and Plotly for the rest of the course.

### What You'll Learn
- What data visualisation is and why humans process visuals faster than tables of numbers.
- The psychology of perception: how position, length, angle, and color are decoded differently by the human eye/brain.
- The difference between exploratory visualisation (for you, the analyst) and explanatory visualisation (for an audience).
- A checklist for evaluating whether a chart is honest, clear, and effective.
- How to install and verify the four core libraries used across this course: Matplotlib, Seaborn, Plotly, and Dash.

### Why It Matters
Every dataset tells a story, but raw numbers rarely reveal it. A spreadsheet with 10,000 rows of climate data means very little to a human eye — but a single well-designed line chart can instantly show a warming trend. Data visualisation is the bridge between raw data and human understanding. It is also one of the most in-demand skills in data science: nearly every data scientist job posting mentions "communicating insights visually" as a core responsibility. Poor visualisations can also *mislead* — a truncated y-axis or a misleading pie chart can make small differences look huge, or hide real problems. Learning to build (and spot) good visualisations is both a technical and an ethical skill.

### Real-World Connection
- **Journalism**: The New York Times and FiveThirtyEight use custom visualisations to explain elections, sports, and the economy to millions of readers.
- **Public health**: During disease outbreaks, dashboards showing case counts and trends (like those built by Johns Hopkins during COVID-19) directly shaped government policy.
- **Climate science**: NASA and Our World in Data publish CO2 and temperature charts that shape international climate agreements.
- **Business**: Companies like Netflix and Spotify use internal dashboards (often built with Dash or Tableau) to track millions of data points and make product decisions daily.

### Key Terminology
| Term | Definition |
|---|---|
| **Data visualisation** | The graphical representation of information and data to make it easier to understand patterns, trends, and outliers. |
| **Exploratory visualisation** | Quick, rough charts made *for yourself* while investigating data. |
| **Explanatory visualisation** | Polished, audience-facing charts made to communicate a specific insight. |
| **Chart junk** | Unnecessary decoration (3D effects, background images, excessive gridlines) that distracts from the data. |
| **Data-ink ratio** | A concept from Edward Tufte: the proportion of a chart's "ink" that represents actual data, versus decoration. Higher is generally better. |
| **Pre-attentive attributes** | Visual properties (color, size, position) that the brain processes in under 250ms, before conscious thought. |
| **Misleading axis** | An axis that is truncated, non-zero, or non-linear in a way that exaggerates or hides differences in the data. |

### Concept Explanation
Human beings are extraordinarily good at detecting visual patterns — much better than we are at scanning tables of numbers. This is rooted in how our visual cortex processes the world: certain properties, called **pre-attentive attributes** (position along a common scale, length, angle, color hue, size), are processed almost instantly and unconsciously. Other properties (color saturation, texture, 3D depth) are processed more slowly and less accurately. This is why a bar chart (which relies on length and position) is almost always easier to read accurately than a pie chart (which relies on angle and area — properties humans are worse at judging).

Statistician and visualisation pioneer William Cleveland ran experiments in the 1980s ranking how accurately people could read different encodings. His ranking, from most to least accurate, is roughly: **position along a common scale > length > angle/slope > area > color/shading > volume**. This is why line charts and bar charts dominate good data visualisation — they use position and length, the two most accurately perceived encodings.

There is also an important distinction between **exploratory** and **explanatory** visualisation. When you first load a dataset, you make rough, fast charts to explore it — messy scatter plots, quick histograms — purely to understand the data yourself. This is different from an **explanatory** chart: one that is cleaned up, labeled, titled, and designed specifically to communicate one clear insight to someone else. A common mistake beginners make is showing their messy exploratory charts to an audience without polishing them into explanatory ones.

Finally, "good" visualisation is not just about being pretty — it must be **honest**. Edward Tufte, a foundational figure in this field, argued for maximizing the **data-ink ratio**: every pixel of "ink" in a chart should ideally represent real data, not decoration. He also warned against **chart junk** (unnecessary 3D effects, background clutter, redundant gridlines) and misleading techniques like truncated y-axes, which can make a 2% change look like a 200% change. As data scientists, we have a responsibility to represent data truthfully, even when a "juicier" but misleading chart might get more attention.

### Step-by-Step Instruction
1. **Install the libraries.** Open a terminal and install the four packages used throughout Course 14.
2. **Verify the installation** by importing each library and printing its version.
3. **Build your first exploratory chart** — a simple scatter plot from a tiny built-in dataset — to confirm your environment works end-to-end.
4. **Apply the "5-second test"**: look at a chart for 5 seconds and write down the one thing you noticed first. Discuss whether that's the intended message.
5. **Critique a real chart** using the checklist provided below.

```python
# lesson_01_setup_and_first_chart.py
# Course 14 - Data Visualisation | Lesson 1: The Science of Good Data Visualisation

import sys
import matplotlib
import seaborn as sns
import plotly
import dash

# Step 1: Verify our visualisation toolkit is installed and working
print("Python version:", sys.version.split()[0])
print("Matplotlib version:", matplotlib.__version__)
print("Seaborn version:", sns.__version__)
print("Plotly version:", plotly.__version__)
print("Dash version:", dash.__version__)

import matplotlib.pyplot as plt
import numpy as np

# Step 2: A tiny "hello world" dataset - hours studied vs. quiz score
hours_studied = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8])
quiz_score = np.array([40, 45, 55, 58, 65, 70, 78, 82, 90])

# Step 3: Build a quick EXPLORATORY scatter plot (fast, minimal styling)
fig, ax = plt.subplots(figsize=(6, 4))
ax.scatter(hours_studied, quiz_score, color="steelblue")
ax.set_title("Exploratory chart: hours studied vs. quiz score")
ax.set_xlabel("Hours studied")
ax.set_ylabel("Quiz score (%)")
plt.tight_layout()
plt.savefig("lesson01_exploratory_chart.png", dpi=120)
plt.show()

# Step 4: Now build the same data as an EXPLANATORY chart - polished, titled, labeled
fig, ax = plt.subplots(figsize=(7, 4.5))
ax.plot(hours_studied, quiz_score, marker="o", color="#1f77b4", linewidth=2)
ax.set_title("More Study Time Is Linked to Higher Quiz Scores", fontsize=13, fontweight="bold")
ax.set_xlabel("Hours studied per week")
ax.set_ylabel("Average quiz score (%)")
ax.set_ylim(0, 100)  # honest, zero-based axis
ax.grid(alpha=0.3)
ax.annotate("8 hrs -> 90% avg score", xy=(8, 90), xytext=(4.5, 92),
            arrowprops=dict(arrowstyle="->", color="gray"))
for spine in ["top", "right"]:
    ax.spines[spine].set_visible(False)
plt.tight_layout()
plt.savefig("lesson01_explanatory_chart.png", dpi=120)
plt.show()

print("\nSaved: lesson01_exploratory_chart.png and lesson01_explanatory_chart.png")
```

### Code Explanation
- The first block imports and prints the version of each library used in Course 14, confirming the environment is ready.
- `hours_studied` and `quiz_score` are small NumPy arrays representing a toy dataset (real data will be used starting Lesson 2).
- The **exploratory chart** uses `ax.scatter()` with minimal styling — this is exactly what a data scientist would draw first, just to "see" the data.
- The **explanatory chart** upgrades the same data into a line chart with a bold, insight-driven title ("More Study Time Is Linked to Higher Quiz Scores" instead of a generic label), a zero-based y-axis (honest scale), an annotation pointing at the key data point, and removed top/right spines to raise the data-ink ratio.
- `plt.savefig()` writes each chart to disk as a PNG so it can be reused in reports or portfolios.

### Expected Output
Two PNG files are created: `lesson01_exploratory_chart.png` (a plain, quick scatter plot) and `lesson01_explanatory_chart.png` (a polished line chart with a bold headline title, annotation arrow pointing at the 8-hour/90% point, and a clean zero-based y-axis from 0–100). The console prints the installed version numbers of Matplotlib, Seaborn, Plotly, and Dash.

### Guided Practice
Working with the instructor, students modify the explanatory chart to:
- Change the headline title to a different, still-honest insight.
- Add a second annotation for the lowest data point (0 hours -> 40%).
- Try setting `ax.set_ylim(35, 95)` instead of `0, 100` and discuss out loud whether this makes the trend look more dramatic — and whether that's honest.

### Hands-On Activity
In pairs, students find one chart online (news article, social media, textbook) and apply the **5-Point Chart Checklist**:
1. Does the y-axis start at zero, or is it truncated?
2. Is there a clear, specific title (not just "Sales Data")?
3. Are the axis labels present and readable?
4. Is there unnecessary decoration (3D, background images, extra gridlines)?
5. Does the visual encoding (bar length, pie angle, color) match how important the difference actually is?

Each pair presents their chart and checklist scores to the class in 1 minute.

### Student Challenge
Recreate the "explanatory chart" example above, but invent your own honest small dataset (5–10 points) about a topic you care about (e.g., practice time vs. video game skill, sleep hours vs. mood). Your chart must include: a bold insight-driven title, a zero-based or clearly justified axis, at least one annotation, and no unnecessary decoration.

### Common Mistakes
- **Truncating the y-axis without a clear reason**, which exaggerates small differences.
- **Using 3D charts** for 2D data — 3D perspective distorts perceived size and position.
- **Vague titles** like "Chart 1" or "Data" instead of a title that states the insight.
- **Too many colors** in a chart with only one series of data (Seaborn/Matplotlib default color cycles can tempt students to use multiple colors even when a series doesn't need them).
- **Forgetting axis labels or units** (e.g., is that axis in dollars, percent, or thousands?).

### Debugging Guidance
- If `import dash` fails with `ModuleNotFoundError`, run `pip install dash` in the same Python environment you're using to run the script.
- If `plt.show()` does nothing in a notebook, add `%matplotlib inline` at the top of the notebook cell, or just rely on the saved PNG file.
- If the saved PNG looks blank or cut off, check that `plt.tight_layout()` is called before `plt.savefig()`.
- If version numbers print as very old (e.g., Matplotlib < 3.0), upgrade with `pip install --upgrade matplotlib seaborn plotly dash`.

### Mini Quiz
1. Which visual encoding is generally perceived MOST accurately, according to Cleveland's ranking: position, angle, or color?
2. What is "chart junk"?
3. True or False: A truncated (non-zero) y-axis can make a small difference look dramatic.
4. What is the key difference between an exploratory chart and an explanatory chart?
5. Name one library we will use later in this course for building fully interactive dashboards.

**Answer Key**
1. Position along a common scale.
2. Unnecessary decorative elements (3D effects, background clutter, excess gridlines) that don't represent data and distract from it.
3. True.
4. Exploratory charts are quick and rough, made for yourself while investigating data; explanatory charts are polished and designed to communicate one clear insight to an audience.
5. Dash (built on Plotly).

### Lesson Recap
Today we learned why data visualisation matters, how the human brain perceives different visual encodings with different accuracy, the difference between exploratory and explanatory charts, and a checklist for spotting dishonest or cluttered charts. We also confirmed our Python environment (Matplotlib, Seaborn, Plotly, Dash) is ready for the rest of the course.

### Homework / Extension
Find two more charts from different sources (a textbook, a news site, a social media post) and score them with the 5-Point Chart Checklist. Write 3–5 sentences per chart explaining what you would change to make it more honest and clear.

### Portfolio Project Connection
This lesson's checklist and principles (honest axes, clear titles, minimal chart junk) will be applied directly to all three portfolio projects: **Project 04 (Climate Change Dashboard)**, **Project 05 (Market Trends Chart)**, and **Project 06 (Sports Comparison Visualiser)** — every chart you build in those projects should pass this lesson's 5-point checklist.
