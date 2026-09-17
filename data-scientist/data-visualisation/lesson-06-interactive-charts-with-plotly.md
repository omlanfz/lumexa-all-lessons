# Course 14: Data Visualisation — Lesson 6
## Interactive Charts With Plotly

**Target Age:** 13–18
**Duration:** 55 minutes (instructor-led)

---

### Learning Objectives
1. Explain the difference between static (Matplotlib/Seaborn) and interactive (Plotly) charts.
2. Build interactive line, bar, and scatter charts using Plotly Express.
3. Add hover tooltips, zoom/pan, and dropdown-style filtering to a chart.
4. Export an interactive chart as a standalone HTML file.

### What You'll Learn
- The `plotly.express` (px) high-level API for quickly building interactive charts.
- Hover tooltips, zooming, panning, and legend-based filtering — all built in by default.
- How to customize interactive charts with titles, colors, and templates.
- Exporting a chart to a self-contained `.html` file that works in any browser, without needing Python installed to view it.

### Why It Matters
Static charts (like the ones built in Lessons 2-5) are perfect for reports, papers, and printed material — but the moment a chart needs to live on a website or dashboard, interactivity becomes hugely valuable. A viewer can hover over a data point to see its exact value, zoom into a busy time period, or click a legend entry to hide/show a series — all without you needing to build ten separate static charts for ten different "what if I zoom in here" questions. Plotly is the standard Python library for this, and it is also the engine that powers Dash (Lessons 7-8), so mastering Plotly here sets up the rest of the course.

### Real-World Connection
- **News interactives**: The New York Times, The Economist, and Reuters build many of their interactive online charts using Plotly or similar JavaScript-based tools (Plotly is unique in giving Python users direct access to that same interactivity).
- **Company dashboards**: Financial analysts, scientists, and product managers use Plotly-based dashboards (often via Dash) to explore huge datasets without needing to code a new static chart for every question.
- **Scientific research**: Plotly is popular in Jupyter notebooks for interactively exploring experimental results before publishing static versions.
- **Portfolio projects**: All three of your Course 14 portfolio projects include a genuinely interactive Plotly (or Dash) component.

### Key Terminology
| Term | Definition |
|---|---|
| **Interactive chart** | A chart that responds to user actions like hovering, zooming, panning, or clicking. |
| **Plotly Express (px)** | The high-level, quick-to-use API within Plotly for building common chart types in one line. |
| **Plotly Graph Objects (go)** | The lower-level Plotly API giving more fine-grained control over every trace and layout element. |
| **Trace** | A single data series within a Plotly figure (e.g., one line, one set of bars). |
| **Hover tooltip** | A small popup showing exact data values when a user hovers their mouse over a point. |
| **Standalone HTML export** | An interactive chart saved as a single `.html` file that works in any browser without needing Python or a server. |

### Concept Explanation
Plotly is built around the idea that a chart is a **figure** made of one or more **traces** (individual data series) plus a **layout** (titles, axis settings, colors, template). The `plotly.express` module (imported as `px`) provides a high-level shortcut: functions like `px.line()`, `px.bar()`, and `px.scatter()` accept a pandas DataFrame directly and automatically configure sensible traces and layout, similar in spirit to how Seaborn simplifies Matplotlib. Compared to Matplotlib, the resulting chart is *interactive by default* the moment it's rendered — hovering shows exact values, dragging zooms into a region, double-clicking resets the zoom, and clicking a legend entry toggles that series on/off.

This interactivity is not just a visual bonus — it changes what kind of questions a chart can answer. A static line chart of ten years of stock prices might look too cluttered to show daily detail, but an interactive Plotly version lets the *viewer themselves* zoom into any specific week or month. This shifts some analytical work from the chart's creator to the chart's viewer, which is powerful for exploratory dashboards where you can't predict every question in advance.

Plotly charts can be exported as a self-contained `.html` file using `fig.write_html("chart.html")`. This file embeds everything needed (a copy of, or a link to, the Plotly JavaScript library, the data, and the layout) so it can be opened directly in any web browser — no Python, no server, and no internet connection required if the JavaScript is embedded. This makes Plotly HTML exports an excellent, lightweight way to share interactive analysis with someone who doesn't code, which is exactly what Project 05 (Market Trends Chart) will do.

### Step-by-Step Instruction
1. Import `plotly.express` and build an interactive line chart from a small time-series DataFrame.
2. Build an interactive bar chart with hover data showing extra fields beyond what's on the axes.
3. Build an interactive scatter chart with a color dimension and a size dimension.
4. Export each chart as a standalone HTML file.

```python
# lesson_06_interactive_plotly_charts.py
# Course 14 - Data Visualisation | Lesson 6: Interactive charts with Plotly

import plotly.express as px
import pandas as pd
import numpy as np

rng = np.random.default_rng(11)

# ---------- 1. INTERACTIVE LINE CHART: simulated daily app downloads over 90 days ----------
dates = pd.date_range("2024-01-01", periods=90, freq="D")
downloads = np.cumsum(rng.normal(50, 15, 90)) + 500
df_line = pd.DataFrame({"date": dates, "downloads": downloads.round().astype(int)})

fig_line = px.line(
    df_line, x="date", y="downloads",
    title="Daily App Downloads Grew Steadily Over 90 Days",
    labels={"date": "Date", "downloads": "Downloads"},
    template="plotly_white",
)
fig_line.update_traces(line_color="#1f77b4", line_width=2.5)
fig_line.write_html("lesson06_interactive_line.html")

# ---------- 2. INTERACTIVE BAR CHART with extra hover data ----------
countries = ["USA", "Germany", "Japan", "Brazil", "India", "Kenya"]
users = [4200, 1800, 1500, 2100, 3900, 900]
avg_session_min = [12.4, 9.8, 14.2, 8.5, 7.9, 11.1]
df_bar = pd.DataFrame({"country": countries, "users": users, "avg_session_min": avg_session_min})
df_bar = df_bar.sort_values("users", ascending=False)

fig_bar = px.bar(
    df_bar, x="country", y="users",
    hover_data={"avg_session_min": True, "users": True},
    title="The USA and India Lead in Total App Users",
    labels={"country": "Country", "users": "Active users"},
    color="users", color_continuous_scale="Blues",
    template="plotly_white",
)
fig_bar.write_html("lesson06_interactive_bar.html")

# ---------- 3. INTERACTIVE SCATTER CHART with color + size dimensions ----------
n = 60
screen_time = rng.uniform(1, 9, n)
sleep_hours = 9 - 0.4 * screen_time + rng.normal(0, 0.6, n)
age = rng.integers(13, 19, n)
wellbeing_score = 100 - 5 * screen_time + rng.normal(0, 6, n)

df_scatter = pd.DataFrame({
    "screen_time_hours": screen_time,
    "sleep_hours": sleep_hours,
    "age": age,
    "wellbeing_score": wellbeing_score,
})

fig_scatter = px.scatter(
    df_scatter, x="screen_time_hours", y="sleep_hours",
    color="wellbeing_score", size="age",
    color_continuous_scale="Viridis",
    title="More Screen Time Is Linked to Less Sleep",
    labels={"screen_time_hours": "Screen time (hrs/day)", "sleep_hours": "Sleep (hrs/night)",
            "wellbeing_score": "Wellbeing score", "age": "Age"},
    template="plotly_white",
)
fig_scatter.write_html("lesson06_interactive_scatter.html")

print("Saved: lesson06_interactive_line.html, lesson06_interactive_bar.html, lesson06_interactive_scatter.html")
print("Open these files in any web browser to interact with them (hover, zoom, pan, legend click).")
```

### Code Explanation
- The line chart simulates 90 days of cumulative app downloads using a random walk (`np.cumsum` of small daily increments), producing a realistic upward-trending but noisy line, styled with `template="plotly_white"` for a clean look matching earlier lessons.
- The bar chart passes `hover_data={"avg_session_min": True}` so hovering over any country's bar reveals its average session length *in addition to* the bar's height (total users) — something a static bar chart cannot do without cluttering the visual with extra text.
- The scatter chart encodes **four dimensions** at once: x (screen time), y (sleep hours), color (wellbeing score, using a sequential Viridis scale since wellbeing is ordered), and size (age) — demonstrating how interactivity and multi-dimensional encoding work together (a viewer can hover any point to see the exact age/wellbeing/screen time/sleep values).
- `fig.write_html(...)` exports each figure as a fully self-contained, double-clickable HTML file.

### Expected Output
Three HTML files are created: `lesson06_interactive_line.html` (an interactive line chart of downloads over 90 days — hovering shows exact date and download count, and dragging zooms into any date range), `lesson06_interactive_bar.html` (a blue-shaded bar chart of six countries, sorted by users, where hovering reveals both total users and average session minutes), and `lesson06_interactive_scatter.html` (a scatter plot of 60 points where point color reflects wellbeing score and point size reflects age, with a visible downward-sloping trend between screen time and sleep). Opening any file in a browser demonstrates hover tooltips, zoom/pan, and clickable legends.

### Guided Practice
With the instructor, students open `lesson06_interactive_scatter.html` in a browser and:
- Hover over 3 different points and read the tooltip values aloud.
- Click-and-drag to zoom into the bottom-left cluster of points, then double-click to reset the zoom.
- Try changing `color_continuous_scale="Viridis"` to `"RdBu"` and re-running, discussing whether that's an appropriate diverging choice for this particular data (it's not — wellbeing score doesn't have a natural zero-crossing here, so `Viridis` sequential is more correct, reinforcing Lesson 5).

### Hands-On Activity
Students modify the bar chart example to use their own 5-6 category dataset (favorite games, sports teams, snack preferences among friends) with at least one extra hover field, then export and open the resulting HTML file to confirm the tooltip works.

### Student Challenge
Build a 4th interactive chart type not shown in the example: an interactive **grouped bar chart** using `px.bar(..., color="some_category", barmode="group")` comparing two categories side by side (e.g., app users broken down by both country AND age group). Export it to HTML and verify hovering and legend-clicking both work.

### Common Mistakes
- Forgetting to call `fig.write_html(...)` and expecting the chart to appear automatically outside a Jupyter notebook.
- Using a diverging color scale (`RdBu`) for data that doesn't have a real zero/midpoint, misapplying the Lesson 5 principle.
- Passing a list where a column name (string) is expected, e.g. `x=[...]` instead of `x="date"` when working with a DataFrame — Plotly Express expects column *names*, not raw arrays, when using the `data_frame=` pattern.
- Making a chart with too many hover fields, overwhelming the tooltip — keep to 2-4 relevant fields.

### Debugging Guidance
- If nothing happens when you run the script, remember Plotly Express figures don't automatically pop up outside notebooks — check for the `.html` file being created, or add `fig.show()` if running in an environment with a browser available.
- If the HTML file opens but looks blank, check your data isn't empty (`print(df.head())` before plotting) and that column names in `x=`/`y=`/`color=` exactly match the DataFrame's columns.
- If color/size arguments throw an error, confirm you're passing a column name as a string, not the column's actual values.
- If the file is very large, note that if you don't have internet access when opening it, `include_plotlyjs="cdn"` (the write_html default in recent Plotly can vary) may need to be set to `include_plotlyjs=True` to embed the JS library fully offline.

### Mini Quiz
1. What Python module do we import to use Plotly's high-level charting API?
2. Name two interactions available by default in a Plotly chart that are NOT available in a static Matplotlib chart.
3. What method exports a Plotly figure to a standalone HTML file?
4. In the scatter chart example, which visual property encoded "wellbeing score"?
5. Why might you choose Plotly over Matplotlib for a chart meant to be explored by many different viewers with different questions?

**Answer Key**
1. `plotly.express` (commonly imported as `px`).
2. Any two of: hover tooltips, zooming, panning, clickable/toggleable legends.
3. `fig.write_html("filename.html")`.
4. Color (a sequential Viridis color scale).
5. Because interactivity lets each viewer explore the data according to their own question (zooming, hovering, filtering) without the chart's creator needing to anticipate every possible view in advance.

### Lesson Recap
We learned the difference between static and interactive charts, built interactive line, bar, and scatter charts using Plotly Express with hover data and multi-dimensional encodings, and exported them as standalone HTML files viewable in any browser.

### Homework / Extension
Take one static chart you built in Lesson 2, 3, or 5 and rebuild it as an interactive Plotly version with at least one additional hover field not visible on the static chart's axes. Export it as HTML and write 2-3 sentences on what the interactivity adds.

### Portfolio Project Connection
Project 05 (Market Trends Chart) requires a genuinely interactive Plotly HTML export of Apple's real stock price data. Project 04 (Climate Change Dashboard) and Project 06 (Sports Comparison Visualiser) both build on these exact Plotly techniques (line charts, bar charts, hover data, color/size encoding) as the visual layer inside their interactive Dash apps in Lessons 7 and 8.
