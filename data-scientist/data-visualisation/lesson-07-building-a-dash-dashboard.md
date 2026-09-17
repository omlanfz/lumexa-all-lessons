# Course 14: Data Visualisation — Lesson 7
## Building a Dash Dashboard

**Target Age:** 13–18
**Duration:** 60 minutes (instructor-led)

---

### Learning Objectives
1. Explain what Dash is and how it turns Plotly charts into a full web dashboard.
2. Build a Dash app with a layout containing text, a dropdown, and a chart.
3. Write a callback function connecting user input (a dropdown) to chart output.
4. Run a Dash app locally and interact with it in a browser.

### What You'll Learn
- The three core pieces of every Dash app: **layout** (what's on the page), **components** (dropdowns, graphs, text), and **callbacks** (functions that update the page when the user interacts with it).
- How to structure a Dash app file (`app.py`) using `dash`, `dash.html`, and `dash.dcc` (Dash Core Components).
- The `@app.callback` decorator pattern: `Input` (what triggers an update) and `Output` (what gets updated).
- How to run a Dash app locally with `app.run(debug=True)` and view it at `http://127.0.0.1:8050`.

### Why It Matters
Everything up to this lesson (Matplotlib, Seaborn, Plotly) produces a chart or a static/interactive file. Dash is different: it turns Python code into a **full interactive web application** — with dropdowns, sliders, buttons, and live-updating charts — without needing to know HTML, CSS, or JavaScript. This is the exact technology real companies use to build internal data dashboards (finance, healthcare, logistics, sports analytics) because it lets a data scientist go from "I have a pandas DataFrame" to "I have a shareable web app" in the same language, Python, from start to finish.

### Real-World Connection
- **Enterprise dashboards**: Companies including Tesla and Roche use Dash internally for engineering, quality control, and clinical dashboards.
- **Public data tools**: Many government and non-profit "explore the data yourself" tools online are built with Dash (e.g., interactive COVID trackers, budget explorers).
- **Sports analytics teams**: NBA/NFL front offices commonly build internal Dash tools letting coaches explore player stats interactively.
- **Portfolio impact**: A working Dash app is one of the most impressive things a student can show in a portfolio — it demonstrates you can build something a non-technical person could actually use, not just a static image.

### Key Terminology
| Term | Definition |
|---|---|
| **Dash** | A Python framework (built by the makers of Plotly) for building interactive web dashboards using only Python. |
| **Layout** | The structure/content of a Dash app's page, built from nested Dash components. |
| **Dash Core Components (dcc)** | Interactive building blocks like `dcc.Dropdown`, `dcc.Slider`, `dcc.Graph`. |
| **Dash HTML Components (html)** | Components mirroring HTML tags, like `html.Div`, `html.H1`, `html.P`. |
| **Callback** | A Python function, decorated with `@app.callback`, that automatically re-runs and updates part of the page whenever a specified input changes. |
| **Input / Output** | In a callback, `Input` specifies which component property triggers the callback; `Output` specifies which component property gets updated with the function's return value. |
| **Component ID** | A unique string identifier (`id="my-dropdown"`) used to reference a specific component in callbacks. |

### Concept Explanation
A Dash app has three moving parts that work together. The **layout** defines what appears on the page — a tree of components, similar to how a webpage is built from nested HTML elements, but written entirely in Python. Dash provides two component libraries: `dash.html` for structural elements (`html.Div`, `html.H1`, `html.P`, mirroring standard HTML tags) and `dash.dcc` ("Dash Core Components") for interactive widgets (`dcc.Dropdown`, `dcc.Slider`, `dcc.Graph` for embedding a Plotly figure).

The real power of Dash comes from **callbacks**. A callback is a normal Python function decorated with `@app.callback(...)`, where the decorator specifies an `Output` (which component property to update, referenced by its `id` and property name, e.g. `Output("my-graph", "figure")`) and one or more `Input`s (which component property change should trigger the function, e.g. `Input("my-dropdown", "value")`). Whenever the input changes — for example, a user picks a new option in a dropdown — Dash automatically calls the function with the new value and updates the output with whatever the function returns. This "reactive" pattern means you never manually write code to update the page; you just describe *what should happen* when an input changes, and Dash handles the rest.

Running a Dash app starts a small local web server (built on Flask) that serves the page at `http://127.0.0.1:8050` by default. During development, `debug=True` enables auto-reloading when you save changes to your code and shows detailed error messages in the browser if a callback fails — extremely useful for learning and debugging. In production (like the real Project 04 dashboard), this same core pattern scales up to dozens of interactive components on one page.

### Step-by-Step Instruction
1. Create a minimal pandas DataFrame to power the dashboard.
2. Build the layout: a title, a dropdown for selecting a category, and a `dcc.Graph` to display a chart.
3. Write a callback function that takes the dropdown's selected value as input and returns an updated Plotly figure as output.
4. Run the app and interact with the dropdown in a browser, watching the chart update live.

```python
# lesson_07_first_dash_dashboard.py
# Course 14 - Data Visualisation | Lesson 7: Building a Dash dashboard

from dash import Dash, dcc, html, Input, Output
import plotly.express as px
import pandas as pd
import numpy as np

# Step 1: Build a small, realistic dataset - monthly signups by app category
rng = np.random.default_rng(21)
months = pd.date_range("2024-01-01", periods=12, freq="MS").strftime("%b %Y")
categories = ["Games", "Education", "Productivity", "Social"]

records = []
base = {"Games": 1200, "Education": 600, "Productivity": 450, "Social": 900}
growth = {"Games": 1.01, "Education": 1.06, "Productivity": 1.03, "Social": 0.99}
for cat in categories:
    value = base[cat]
    for month in months:
        value *= growth[cat]
        records.append({"month": month, "category": cat, "signups": int(value + rng.normal(0, 30))})

df = pd.DataFrame(records)

# Step 2: Create the Dash app and define its LAYOUT
app = Dash(__name__)
app.title = "Lesson 7: Monthly Signups Explorer"

app.layout = html.Div(style={"fontFamily": "Arial, sans-serif", "maxWidth": "900px", "margin": "0 auto"}, children=[
    html.H1("Monthly App Signups Explorer", style={"textAlign": "center"}),
    html.P("Select a category below to see how monthly signups changed in 2024.",
           style={"textAlign": "center", "color": "#555"}),
    html.Div(style={"textAlign": "center", "marginBottom": "20px"}, children=[
        html.Label("Choose a category: ", style={"fontWeight": "bold"}),
        dcc.Dropdown(
            id="category-dropdown",
            options=[{"label": cat, "value": cat} for cat in categories],
            value="Games",
            clearable=False,
            style={"width": "300px", "margin": "10px auto"},
        ),
    ]),
    dcc.Graph(id="signups-graph"),
])

# Step 3: Define the CALLBACK connecting the dropdown to the graph
@app.callback(
    Output("signups-graph", "figure"),
    Input("category-dropdown", "value"),
)
def update_graph(selected_category):
    filtered = df[df["category"] == selected_category]
    fig = px.line(
        filtered, x="month", y="signups", markers=True,
        title=f"Monthly Signups in 2024: {selected_category}",
        labels={"month": "Month", "signups": "New signups"},
        template="plotly_white",
    )
    fig.update_traces(line_color="#1f77b4", line_width=3)
    return fig

# Step 4: Run the app
if __name__ == "__main__":
    app.run(debug=True)
```

### Code Explanation
- The synthetic dataset simulates 12 months of signups for 4 app categories, each with a different growth rate (Education grows fastest at 6%/month, Social slightly declines), producing realistic-looking, distinct trends per category.
- `app.layout` is built from nested `html.Div`, `html.H1`, `html.P`, `html.Label` (structural/text components) and `dcc.Dropdown`/`dcc.Graph` (interactive components), each given a unique `id` where needed for callbacks.
- The `@app.callback(Output(...), Input(...))` decorator wires the dropdown's `value` property to trigger `update_graph()`, whose return value (a Plotly figure) becomes the new `figure` property of the `dcc.Graph` component.
- Inside `update_graph()`, the DataFrame is filtered to only the selected category and a fresh `px.line()` figure is built and returned — this exact function re-runs every single time the dropdown changes.
- `app.run(debug=True)` starts the local development server at `http://127.0.0.1:8050`.

### Expected Output
Running `python lesson_07_first_dash_dashboard.py` starts a local web server. Visiting `http://127.0.0.1:8050` in a browser shows a page titled "Monthly App Signups Explorer" with a centered dropdown (defaulting to "Games") and a line chart below it showing that category's monthly signups across 2024. Selecting a different category from the dropdown (e.g., "Education") immediately updates the chart title and line to reflect the newly selected category's data, with no page reload.

### Guided Practice
With the instructor, students run the app, switch between all four categories, and observe: (1) the chart title updates to match the selection, (2) the y-axis scale may change because different categories have different signup ranges, (3) the page never fully reloads — only the graph component updates.

### Hands-On Activity
Students add a second dropdown option group or a `dcc.RadioItems` component letting the user also toggle between a line chart and a bar chart view of the same filtered data (hint: add an `Input` from the new component to the same callback, and use an `if/else` inside `update_graph()` to choose `px.line` vs `px.bar`).

### Student Challenge
Add a second `dcc.Graph` showing a comparison of ALL FOUR categories at once (not filtered), so students can see the individually-selected category chart alongside a comparison chart. This requires either a second callback, or extending the existing one with a second `Output`.

### Common Mistakes
- Forgetting to give a component an `id`, causing `Output`/`Input` references to fail with a "component not found" error.
- Writing a callback function whose name doesn't match what it's supposed to do (keep this consistent for debugging clarity — `update_graph` should update a graph).
- Returning something other than a Plotly figure object from a callback targeting a `Graph`'s `figure` property (e.g., accidentally returning a DataFrame).
- Not filtering the DataFrame inside the callback and reusing the full, unfiltered `df`, causing the chart to always look the same regardless of dropdown selection.
- Running the file and forgetting that changing `debug=True` reloads automatically, but syntax errors will still stop the server — read the terminal output for tracebacks.

### Debugging Guidance
- If the browser shows "This site can't be reached," confirm the terminal shows the server actually started (look for a line mentioning `Running on http://127.0.0.1:8050`) and that you're using that exact address.
- If the dropdown doesn't change the chart, open the browser console (or check the terminal) for a callback error — a common cause is an `id` mismatch between the layout component and the `Output`/`Input` in the callback.
- If you see `dash.exceptions.NoLayoutException`, check that `app.layout` is assigned before `app.run()` is called.
- If two Python processes try to use port 8050 at once, stop the first one (Ctrl+C in its terminal) or run the second with `app.run(debug=True, port=8051)`.

### Mini Quiz
1. What are the three core parts of every Dash app?
2. What is the purpose of the `@app.callback` decorator?
3. What Dash component would you use to embed a Plotly chart in the page?
4. If a dropdown has `id="category-dropdown"`, how would you reference its selected value as a callback Input?
5. At what local address does a Dash app typically run during development?

**Answer Key**
1. Layout, components, and callbacks.
2. It connects a component's input (e.g., a dropdown selection) to automatically re-run a function and update another component's output (e.g., a graph) whenever that input changes.
3. `dcc.Graph`.
4. `Input("category-dropdown", "value")`.
5. `http://127.0.0.1:8050`.

### Lesson Recap
We learned the layout/components/callbacks model that powers every Dash app, built a working dashboard with a dropdown that filters data and updates an embedded Plotly chart live, and practiced running and debugging a Dash app locally.

### Homework / Extension
Extend the Lesson 7 dashboard with a `dcc.Slider` that filters the data by a numeric range (e.g., only show months where signups exceeded a chosen threshold), wiring it into the existing callback as a second `Input`.

### Portfolio Project Connection
This exact layout -> callback -> Graph pattern is the foundation of **Project 04 (Climate Change Dashboard)**, which uses a country-selector dropdown wired to multiple real CO2-emissions charts built from the OWID dataset — and of the Lesson 8 capstone dashboard.
