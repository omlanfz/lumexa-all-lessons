# Course 14: Data Visualisation — Lesson 8 (Final)
## Your Own Interactive Dashboard

**Target Age:** 13–18
**Duration:** 60 minutes (instructor-led)

---

### Learning Objectives
1. Combine everything from Lessons 1-7 (chart choice, color/storytelling, Matplotlib/Seaborn/Plotly, Dash) into one multi-chart interactive dashboard.
2. Structure a Dash app with multiple linked components (dropdown + graph + summary stat cards).
3. Load and clean a real external CSV dataset for use in a dashboard.
4. Deploy/run a finished personal Dash dashboard project and present it.

### What You'll Learn
- How to plan a dashboard: pick one clear question, choose 2-4 supporting charts, and one or two interactive controls.
- How to load a real CSV dataset with pandas and prepare it for Dash (filtering, renaming, computing derived columns).
- How to combine multiple `dcc.Graph` components and dynamic summary statistics (like a "current value" callout) in one layout.
- How to structure a finished Dash project into a clean folder with `app.py`, a `data/` folder, and a `requirements.txt`.

### Why It Matters
This is the capstone lesson of Course 14: every concept from the past seven lessons — choosing honest chart types, matching color to data, writing headline titles, building charts in Matplotlib/Seaborn/Plotly, and wiring a Dash app together with callbacks — comes together into one finished, presentable dashboard. This is exactly the kind of project a real junior data scientist builds in their first weeks on the job, and exactly what belongs in a strong student portfolio.

### Real-World Connection
- **Job-ready skill**: "Build an interactive dashboard from a real dataset" is one of the most common take-home assignments in real data analyst/data scientist job interviews.
- **Open data projects**: Many real-world data journalism and civic-tech projects are exactly this pattern: real public dataset + Dash app + a few linked charts.
- **Portfolio value**: A working, deployed dashboard (even one running locally) is dramatically more impressive to a college admissions officer, scholarship committee, or employer than a static screenshot.

### Key Terminology
| Term | Definition |
|---|---|
| **Dashboard** | A single page combining multiple charts and controls, usually built around one central question or dataset. |
| **Summary stat / KPI card** | A small, prominent number on a dashboard summarizing a key figure (e.g., "Total CO2: 36.8 Gt"). |
| **Data loading & cleaning** | The process of reading a raw CSV file, checking its columns/types/missing values, and preparing it for analysis. |
| **Linked filtering** | When one interactive control (like a dropdown) updates multiple charts/stats at once. |
| **Project structure** | The organized folder layout (`app.py`, `data/`, `requirements.txt`, `README.md`) that makes a Python project runnable and understandable by others. |

### Concept Explanation
Building a good dashboard starts *before* writing any code: you must pick one clear central question the dashboard answers (e.g., "How have CO2 emissions changed for major countries since 1950?"), then choose 2-4 charts that each answer part of that question from a different angle (a time trend, a country comparison, a per-capita view), and finally choose 1-2 interactive controls (usually a dropdown or a set of checkboxes) that let the viewer explore variations of that question themselves. Trying to cram too many unrelated questions into one dashboard usually makes it confusing — the discipline of *picking one question* is as important as any of the coding skills from previous lessons.

Once the question and chart plan are set, real data must be loaded and inspected before any chart is built. This means using `pandas.read_csv()`, checking `.shape`, `.columns`, `.dtypes`, and `.isnull().sum()` to understand what's actually in the file, and filtering down to only the rows/columns actually needed (for example, a global emissions dataset has 250+ countries and 75+ years of data — a dashboard focused on 6-8 major countries needs to filter down before building any chart, exactly as was done to prepare the real dataset for Project 04).

A finished dashboard project should also be organized so someone else (a teacher, a recruiter, a teammate) can run it themselves: a main `app.py` file containing the Dash app, a `data/` folder holding the CSV(s) used, a `requirements.txt` listing the exact Python packages needed (`pandas`, `dash`, `plotly`, etc.), and a `README.md` explaining what the dashboard does and how to run it. This lesson's example, and the real Project 04 in your portfolio, both follow this exact structure.

### Step-by-Step Instruction
1. Define the dashboard's central question and pick 3 supporting charts + 1 dropdown control.
2. Load and inspect a real CSV dataset with pandas.
3. Build the Dash layout: title, dropdown, a KPI summary row, and multiple `dcc.Graph` components.
4. Write one callback that updates ALL charts and the KPI summary together based on the dropdown.
5. Run the dashboard, verify it works, and present it to a partner.

```python
# lesson_08_final_interactive_dashboard.py
# Course 14 - Data Visualisation | Lesson 8 (Final): Your Own Interactive Dashboard
#
# This lesson example uses a small REALISTIC synthetic CSV (created below) to mirror
# exactly the workflow used in the real Project 04 Climate Change Dashboard, which uses
# the actual Our World in Data CO2 dataset. Students apply this same pattern to real data
# in their portfolio project.

import pandas as pd
import numpy as np
from dash import Dash, dcc, html, Input, Output
import plotly.express as px

# ---------- Step 1 & 2: Create/load and inspect a realistic multi-country dataset ----------
rng = np.random.default_rng(5)
countries = ["Kenya", "Norway", "Vietnam", "Canada", "Chile"]
years = np.arange(2000, 2024)

rows = []
start_co2 = {"Kenya": 8, "Norway": 45, "Vietnam": 60, "Canada": 550, "Chile": 70}
growth = {"Kenya": 1.035, "Norway": 0.995, "Vietnam": 1.07, "Canada": 1.002, "Chile": 1.02}
for country in countries:
    value = start_co2[country]
    for year in years:
        value *= growth[country]
        rows.append({"country": country, "year": int(year), "co2_mt": round(value + rng.normal(0, 1.5), 2)})

df = pd.DataFrame(rows)

# Inspect (this mirrors what students do with the REAL dataset in Project 04)
print("Shape:", df.shape)
print("Columns:", df.columns.tolist())
print("Missing values:\n", df.isnull().sum())
print("Year range:", df["year"].min(), "-", df["year"].max())

# ---------- Step 3: Build the Dash layout ----------
app = Dash(__name__)
app.title = "Lesson 8: CO2 Emissions Explorer"

app.layout = html.Div(style={"fontFamily": "Arial, sans-serif", "maxWidth": "950px", "margin": "0 auto", "padding": "20px"}, children=[
    html.H1("CO2 Emissions Explorer (2000-2023)", style={"textAlign": "center"}),
    html.P("Select a country to explore its CO2 emissions trend, growth rate, and current level.",
           style={"textAlign": "center", "color": "#555"}),

    html.Div(style={"textAlign": "center", "margin": "16px 0"}, children=[
        html.Label("Country: ", style={"fontWeight": "bold"}),
        dcc.Dropdown(
            id="country-dropdown",
            options=[{"label": c, "value": c} for c in countries],
            value="Kenya", clearable=False,
            style={"width": "300px", "margin": "8px auto"},
        ),
    ]),

    # KPI summary row
    html.Div(id="kpi-row", style={"display": "flex", "justifyContent": "center", "gap": "30px", "margin": "20px 0"}),

    dcc.Graph(id="trend-chart"),
    dcc.Graph(id="comparison-chart"),
])


def kpi_card(label, value):
    return html.Div(style={"border": "1px solid #ddd", "borderRadius": "8px", "padding": "12px 20px", "textAlign": "center"}, children=[
        html.Div(label, style={"fontSize": "12px", "color": "#888"}),
        html.Div(value, style={"fontSize": "22px", "fontWeight": "bold"}),
    ])


# ---------- Step 4: One callback updating KPI cards + BOTH charts ----------
@app.callback(
    Output("kpi-row", "children"),
    Output("trend-chart", "figure"),
    Output("comparison-chart", "figure"),
    Input("country-dropdown", "value"),
)
def update_dashboard(selected_country):
    country_df = df[df["country"] == selected_country].sort_values("year")
    latest = country_df.iloc[-1]
    earliest = country_df.iloc[0]
    pct_change = (latest["co2_mt"] - earliest["co2_mt"]) / earliest["co2_mt"] * 100

    kpis = [
        kpi_card("Latest CO2 (Mt)", f"{latest['co2_mt']:.1f}"),
        kpi_card("Change since 2000", f"{pct_change:+.0f}%"),
        kpi_card("Years of data", f"{country_df['year'].nunique()}"),
    ]

    trend_fig = px.line(
        country_df, x="year", y="co2_mt", markers=True,
        title=f"{selected_country}: CO2 Emissions Trend, 2000-2023",
        labels={"year": "Year", "co2_mt": "CO2 emissions (Mt)"},
        template="plotly_white",
    )
    trend_fig.update_traces(line_color="#2a9d8f", line_width=3)

    latest_year = df["year"].max()
    comparison_df = df[df["year"] == latest_year].sort_values("co2_mt", ascending=False)
    colors = ["#e76f51" if c == selected_country else "#adb5bd" for c in comparison_df["country"]]
    comparison_fig = px.bar(
        comparison_df, x="country", y="co2_mt",
        title=f"All Countries Compared in {latest_year} ({selected_country} highlighted)",
        labels={"country": "Country", "co2_mt": "CO2 emissions (Mt)"},
        template="plotly_white",
    )
    comparison_fig.update_traces(marker_color=colors)

    return kpis, trend_fig, comparison_fig


if __name__ == "__main__":
    app.run(debug=True)
```

### Code Explanation
- The synthetic dataset is built to mirror the real structure of the Project 04 OWID dataset (country, year, co2 value) so the workflow transfers directly; console `print()` statements demonstrate the required data-inspection step (`.shape`, `.columns`, `.isnull().sum()`, year range).
- The layout combines a title, a dropdown, a **KPI row** (`html.Div(id="kpi-row", ...)`, populated dynamically), and two `dcc.Graph` components — a trend line and a comparison bar chart.
- `kpi_card()` is a small reusable helper function that returns a styled `html.Div` "card" — demonstrating how Python functions can generate reusable Dash UI pieces instead of repeating layout code.
- The single callback has **one Input** (the dropdown) and **three Outputs** (`kpi-row.children`, `trend-chart.figure`, `comparison-chart.figure`) — every one of them updates together whenever the country selection changes, demonstrating "linked filtering."
- The comparison bar chart highlights the selected country in a different color (`"#e76f51"`) than the rest (`"#adb5bd"`), applying Lesson 5's storytelling/color principles directly inside a Dash callback.

### Expected Output
Running the script starts a local server at `http://127.0.0.1:8050`. The page shows a title, a country dropdown (default "Kenya"), three KPI cards (latest CO2 value, percent change since 2000, number of years of data), a line chart showing that country's emissions trend from 2000-2023, and a bar chart comparing all five countries' most recent emissions with the selected country highlighted in orange. Switching the dropdown to another country (e.g., "Canada") instantly updates all three KPI cards and both charts to reflect the new selection, with Canada now highlighted in the comparison chart.

### Guided Practice
With the instructor, students switch through all five countries and, for each, state out loud: which country has the fastest growth (Vietnam), which has the highest absolute emissions (Canada), and which has the lowest (Kenya) — verifying the dashboard correctly answers its central question for every possible selection.

### Hands-On Activity
Students add a fourth KPI card showing the **average annual growth rate** (percent change per year, computed as `pct_change / number_of_years`) and confirm it updates correctly for every country.

### Student Challenge
Extend the dropdown to a `multi=True` Dash Dropdown allowing 2+ countries to be selected at once, and update the trend chart to show one line per selected country (hint: filter `df` to `df["country"].isin(selected_countries)` and use `color="country"` in `px.line`).

### Common Mistakes
- Putting too many unrelated charts on one dashboard instead of picking one focused question.
- Forgetting that a callback with multiple `Output`s must `return` a tuple in the exact same order the `Output`s were declared.
- Not sorting a comparison bar chart, making it hard to read rank order.
- Reusing a single global filtered DataFrame (`country_df`) across all chart-building steps without re-filtering fresh inside the callback, causing stale data bugs after the dropdown changes.
- Skipping the data inspection step (`.shape`, `.isnull().sum()`) and later being surprised by missing or malformed values in a real dataset.

### Debugging Guidance
- If you get an error like "expected 3 outputs but got 1," check that your function's `return` statement returns exactly as many values as declared `Output()`s, in matching order.
- If the KPI numbers look wrong, print `country_df` inside the callback temporarily to inspect exactly what's being computed.
- If the app runs but one chart never updates, verify that chart's `dcc.Graph` `id` matches exactly the `Output` id used in the callback.
- If the app is slow, check you're not reloading or recomputing the entire raw dataset from disk inside the callback — load data once at the top of the file, then just filter inside the callback.

### Mini Quiz
1. Why is it important to pick one clear central question before building a dashboard?
2. In the example, how many `Output`s does the callback have, and what does each update?
3. What pandas methods help you inspect a dataset before building charts from it?
4. What does "linked filtering" mean in the context of a dashboard?
5. What four items make up a well-organized finished dashboard project folder?

**Answer Key**
1. Because trying to answer too many unrelated questions in one dashboard makes it confusing and hard to follow; a focused question keeps the chart choices and controls coherent.
2. Three: the KPI row's children, the trend chart's figure, and the comparison chart's figure.
3. `.shape`, `.columns`, `.dtypes`, `.isnull().sum()`, `.head()`.
4. When one interactive control (like a dropdown) updates multiple charts and/or statistics on the page at the same time.
5. `app.py` (the Dash app), a `data/` folder (the dataset), `requirements.txt` (dependencies), and `README.md` (documentation).

### Lesson Recap
In this capstone lesson, we combined every skill from Course 14 — chart-type selection, storytelling color, Matplotlib/Seaborn/Plotly chart-building, and Dash's layout/callback model — into one complete, linked, multi-chart interactive dashboard driven by a country dropdown, following the same structure used in the real Project 04 portfolio piece.

### Homework / Extension
Using the pattern from this lesson, sketch (on paper or in a planning doc) the central question, chart plan, and interactive controls for a completely new dashboard idea of your own choosing (e.g., your school's sports team stats, a hobby you track data about, your own screen-time log). This becomes the starting plan for extending your Project 04/05/06 portfolio work beyond the course.

### Portfolio Project Connection
This lesson's full "layout + KPI cards + linked callback" pattern is the direct blueprint for **Project 04 (Climate Change Dashboard)**, which applies this exact structure to the real, complete Our World in Data CO2 dataset across 8 real countries plus the World total. Completing this lesson means you are fully ready to build (and understand) Project 04's `app.py`.
