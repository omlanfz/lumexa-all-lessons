# Course 13 · Python for Data — Lesson 2: Python Refresher and Jupyter Setup

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Install and launch Jupyter Notebook and create/save a `.ipynb` file.
- Distinguish between a code cell and a markdown cell, and run cells in order.
- Refresh core Python syntax needed for data work: variables, lists, dictionaries, loops, conditionals, and functions.
- Import and check the version of `pandas` and `numpy`.
- Explain why Jupyter Notebooks are the standard tool for data science work.

## What You'll Learn
This lesson gets your tools ready. You'll set up Jupyter Notebook (the interactive coding environment data scientists use worldwide), and refresh the core Python skills — variables, lists, dictionaries, loops, functions — that everything in this course builds on. By the end, you'll have a working notebook with `pandas` and `numpy` imported and tested.

## Why It Matters
You can't analyze data without a place to write and run code, and you can't use pandas or numpy without solid Python fundamentals underneath them. Jupyter Notebooks let you run code in small, ordered chunks and see results immediately — numbers, tables, even charts — right below your code. This immediate feedback loop is exactly how professional data scientists work every day, from student researchers to engineers at NASA.

## Real-World Connection
Nearly every real-world data science team — from sports analytics departments to medical researchers to climate scientists — uses Jupyter Notebooks (or the very similar tools built on the same idea) to explore data before building anything more permanent. It is the industry-standard "scratchpad" for data exploration.

## Key Terminology
- **Jupyter Notebook**: An interactive, browser-based tool for writing and running Python code in cells, mixing code with text and output.
- **Cell**: A single block in a notebook — either a **code cell** (runs Python) or a **markdown cell** (formatted text).
- **Kernel**: The background Python process that actually runs your code when you execute a cell.
- **Variable**: A named container that stores a value (`score = 95`).
- **List**: An ordered collection of values (`[1, 2, 3]`).
- **Dictionary**: A collection of key-value pairs (`{"name": "Ana", "score": 95}`).
- **Function**: A reusable, named block of code (`def calculate_average(nums):`).
- **Library / Module**: A collection of pre-written code you can `import` and reuse (like `pandas` and `numpy`).

## Concept Explanation
Jupyter Notebook files (`.ipynb`) are made of individual **cells** you run one at a time, in any order you choose — though top-to-bottom is standard practice. A **code cell** runs Python and shows its output directly underneath it — a printed value, a table, even a chart. A **markdown cell** holds formatted text: headings, bullet points, bold text — used for explaining what your code does, exactly like the notebooks you'll build in this course's projects. Behind every notebook is a **kernel** — the live Python process holding all your variables in memory. This is why cell *order of execution* matters: if you define a variable in cell 5 and then run cell 3, cell 3 can use that variable only if cell 5 already ran — Jupyter tracks this with the run number shown in brackets next to each cell (like `In [3]:`).

Before touching pandas, you need comfortable fluency with core Python. **Variables** store values. **Lists** hold ordered collections you can loop over. **Dictionaries** map keys to values, which is exactly the shape pandas columns take conceptually. **Loops** (`for`) let you repeat an action over every item in a collection. **Conditionals** (`if`/`elif`/`else`) let code make decisions. **Functions** (`def`) let you package logic into a reusable, named block — critical for keeping data science code clean and non-repetitive.

Finally, `pandas` and `numpy` are Python **libraries** — pre-written code that other people built so you don't have to reinvent table-handling or fast math from scratch. You bring them into your notebook with `import pandas as pd` and `import numpy as np` — the `as pd`/`as np` aliases are a universal data science convention you'll see in virtually every real notebook, textbook, and job you ever encounter.

## Step-by-Step Instruction
1. Install Python 3 (if not already installed) and then install Jupyter: `pip install notebook pandas numpy`.
2. Launch Jupyter from a terminal: `jupyter notebook` — this opens a browser tab showing your file system.
3. Create a new notebook, choosing the Python 3 kernel.
4. Rename the notebook (e.g., `lesson-02-practice.ipynb`).
5. In the first cell, type a markdown heading (switch cell type to "Markdown" from the toolbar) — e.g. `# My First Notebook` — and run it with Shift+Enter.
6. In the next cell, write and run `print("Hello, data science!")`.
7. Add a cell that imports pandas and numpy and prints their versions (see code below).
8. Save the notebook (Ctrl+S / Cmd+S).

## Code Example
```python
# --- Python refresher: variables, lists, dictionaries, loops, conditionals, functions ---

# Variables
student_name = "Amara"
quiz_score = 88

# List of scores across the term
quiz_scores = [88, 92, 79, 95, 84]

# Dictionary describing one student
student_record = {
    "name": student_name,
    "scores": quiz_scores,
    "grade_level": 10,
}

# Function: reusable logic to compute an average
def calculate_average(scores):
    """Return the average of a list of numeric scores."""
    return sum(scores) / len(scores)

# Loop + conditional: classify each score as Pass/Fail
passing_threshold = 60
for score in quiz_scores:
    status = "Pass" if score >= passing_threshold else "Fail"
    print(f"Score {score}: {status}")

average_score = calculate_average(quiz_scores)
print(f"\n{student_record['name']}'s average score: {average_score:.1f}")

# Now bring in pandas and numpy, and confirm they're installed correctly
import pandas as pd
import numpy as np

print("\npandas version:", pd.__version__)
print("numpy version:", np.__version__)

# A tiny first pandas object: a Series built from our quiz_scores list
scores_series = pd.Series(quiz_scores, name="quiz_scores")
print("\nAs a pandas Series:")
print(scores_series)
print("\nMean via numpy:", np.mean(quiz_scores))
```

## Code Explanation
- `student_record` is a dictionary that nests a list (`quiz_scores`) inside it — this mirrors how real datasets nest many pieces of information about one entity (a student, a game, a country).
- `calculate_average` is a function with a **docstring** (the triple-quoted string) describing what it does — a habit every professional data scientist follows.
- The `for` loop with an inline `if/else` (a "ternary expression") classifies each score in one readable line.
- `import pandas as pd` and `import numpy as np` load the two core libraries for this entire course, using their standard abbreviations.
- `pd.Series(...)` converts our plain Python list into a pandas **Series** — a one-dimensional labeled array, the building block of every pandas DataFrame you'll use starting next lesson.

## Expected Output
```
Score 88: Pass
Score 92: Pass
Score 79: Pass
Score 95: Pass
Score 84: Pass

Amara's average score: 87.6

pandas version: 2.2.2
numpy version: 1.26.4

As a pandas Series:
0    88
1    92
2    79
3    95
4    84
Name: quiz_scores, dtype: int64

Mean via numpy: 87.6
```
(Exact version numbers will vary depending on what is installed on each student's machine — that is expected and fine.)

## Guided Practice
1. Change `passing_threshold` to 90 and re-run — observe how more scores now print "Fail."
2. Add a new student's dictionary and function call to calculate their average.
3. Use `np.max(quiz_scores)` and `np.min(quiz_scores)` to print the highest and lowest scores.

## Hands-On Activity
Students create a notebook with: (1) a markdown title cell, (2) a code cell defining a list of 5 numbers representing something personal (game scores, workout reps, days it rained this month), (3) a function that calculates the average, (4) a loop with a conditional that labels each value (e.g., "above average" / "below average" using the calculated mean), (5) a final cell converting the list to a pandas Series and printing it.

## Student Challenge
Write a function `summarize(scores)` that takes a list of numbers and returns a dictionary with keys `"mean"`, `"max"`, `"min"`, and `"count"`, using `np.mean`, `np.max`, `np.min`, and `len`. Call it on your Hands-On Activity list and print the result neatly.

## Common Mistakes
- **Running cells out of order**: Editing a variable in a later cell but forgetting to re-run an earlier cell that depends on the old value — always check the `In [ ]` numbers.
- **Forgetting `import`**: Using `pd` or `np` before importing them causes `NameError: name 'pd' is not defined`.
- **Mixing up `=` and `==`**: `=` assigns a value; `==` checks equality. Using `=` inside an `if` statement is a syntax error.
- **Indentation errors**: Python uses indentation to define code blocks — inconsistent spacing inside loops/functions causes `IndentationError`.

## Debugging Guidance
- `NameError` almost always means a variable or import is missing or misspelled — check your imports and earlier cells.
- If output looks stale or wrong, use the Jupyter menu "Restart Kernel and Run All" to guarantee a clean, top-to-bottom execution.
- Read tracebacks bottom-up: the last line names the actual error type and message.
- Use `print()` liberally while debugging — printing intermediate variables is the fastest way to see where a calculation goes wrong.

## Mini Quiz
1. What is the difference between a code cell and a markdown cell?
2. Why does the order in which you run cells matter in Jupyter?
3. What Python data structure holds key-value pairs?
4. What is the purpose of `import pandas as pd`?
5. What does a function's docstring do?

### Answer Key
1. A code cell runs Python and shows output; a markdown cell displays formatted text for explanations.
2. Because the kernel remembers variables based on execution order, not the order cells appear on the page — running out of order can use outdated or missing variable values.
3. A dictionary.
4. It loads the pandas library into the notebook and gives it the short alias `pd` so it can be used for the rest of the code.
5. It documents, in the code itself, what the function does — useful for other people (and future you) reading the code.

## Lesson Recap
You installed and launched Jupyter Notebook, learned the difference between code and markdown cells, refreshed core Python (variables, lists, dictionaries, loops, conditionals, functions), and successfully imported pandas and numpy, converting your first list into a pandas Series.

## Homework / Extension
Install Jupyter (or Anaconda) on your own computer if you haven't already, and recreate today's Hands-On Activity notebook from scratch, saving it as `lesson-02-homework.ipynb`. Add one extra function of your own design that uses a loop and a conditional.

## Portfolio Project Connection
Every one of this course's three portfolio projects (Sports, School Grades, City Population) will be built as Jupyter Notebooks using exactly the pandas/numpy setup you completed today — this lesson is the technical foundation all three projects sit on.
