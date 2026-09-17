# Course 13 · Python for Data — Lesson 3: Loading Data from CSV and Excel

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Explain what a CSV file and an Excel (.xlsx) file are, and how they differ.
- Load a CSV file into a pandas DataFrame using `pd.read_csv()`.
- Load an Excel file into a pandas DataFrame using `pd.read_excel()`.
- Use `.head()`, `.tail()`, `.shape`, `.columns`, and `.info()` to inspect a freshly loaded DataFrame.
- Save a DataFrame back out to a CSV file using `.to_csv()`.

## What You'll Learn
Today you load your first real dataset into pandas. You'll learn what a DataFrame is, how to read data from both CSV and Excel files (the two most common formats data arrives in), and how to do a quick first inspection of any new dataset — the very first thing every data scientist does before any analysis.

## Why It Matters
No analysis can happen until the data is actually loaded into your program. Nearly all real-world data — sales spreadsheets, sports statistics, school records, government population data — arrives as a CSV or Excel file. Knowing how to reliably load and inspect these files is the entry point to every single project you will ever do in data science.

## Real-World Connection
Government agencies (like the World Bank population data used later in this course), sports statisticians, school administrators, and businesses all export their records as CSV or Excel files because those formats are universal — nearly any tool, from Google Sheets to Python, can open them. When a company hires a data analyst, one of the first things they do is load internal spreadsheets and reports exactly the way you will practice today.

## Key Terminology
- **CSV (Comma-Separated Values)**: A plain-text file where each line is a row and commas separate columns.
- **Excel file (.xlsx)**: A spreadsheet file format that can contain multiple sheets, formatting, and formulas.
- **DataFrame**: Pandas' core 2-dimensional table structure, with labeled rows and columns — think "a spreadsheet inside Python."
- **Series**: A single column of a DataFrame (a 1-dimensional labeled array).
- **Header row**: The first row of a file, usually containing column names.
- **`read_csv()` / `read_excel()`**: Pandas functions that load CSV/Excel files into a DataFrame.
- **`.shape`**: A DataFrame attribute showing `(number_of_rows, number_of_columns)`.

## Concept Explanation
A **DataFrame** is the central object in pandas — a table with rows and columns, just like a spreadsheet, but built for fast programmatic access. Every column in a DataFrame is technically a pandas **Series**, and every DataFrame is essentially a dictionary of Series that all share the same row index.

Loading data is almost always the very first step of any project. `pd.read_csv("filename.csv")` reads a comma-separated text file and automatically detects column names from the header row, converting each column to an appropriate data type (numbers become `int64`/`float64`, text becomes `object`/`str`). `pd.read_excel("filename.xlsx")` does the same for Excel workbooks, and can take a `sheet_name` argument if the file has multiple sheets.

Once loaded, before doing *anything* else, professional data scientists always inspect a new dataset: `.head()` shows the first 5 rows (or however many you specify) so you can eyeball the structure; `.tail()` shows the last rows; `.shape` tells you how many rows and columns you're dealing with; `.columns` lists every column name (useful for catching typos or unexpected spacing); and `.info()` gives a full summary — column names, non-null counts, and data types all at once. This step matters enormously: real data almost always has surprises — missing values, unexpected data types, more rows than expected — and catching these early saves hours of confusion later.

Finally, once you've transformed or cleaned data, `.to_csv("output.csv", index=False)` writes your DataFrame back out to a new CSV file, ready to share or use elsewhere. The `index=False` argument is important — without it, pandas writes its own internal row-numbering column into the file, which is almost never what you want.

## Step-by-Step Instruction
1. Locate your dataset file (e.g., a CSV of NBA game data) and note its exact path.
2. In a new Jupyter cell, `import pandas as pd`.
3. Load it: `df = pd.read_csv("path/to/file.csv")`.
4. Run `df.head()` to preview the first 5 rows.
5. Run `df.shape` to see the row/column counts.
6. Run `df.columns` to list every column name.
7. Run `df.info()` to see data types and null counts.
8. Practice loading an Excel file the same way with `pd.read_excel()`.
9. Save a small slice of the DataFrame back out using `.to_csv()`.

## Code Example
```python
import pandas as pd

# Load a real dataset: NBA Elo game-level data (used later in Project 1)
nba = pd.read_csv("nbaallelo.csv")

# First look at the data
print("Shape (rows, columns):", nba.shape)
print("\nColumn names:")
print(nba.columns.tolist())

print("\nFirst 5 rows:")
print(nba.head())

print("\nLast 3 rows:")
print(nba.tail(3))

print("\nDataFrame info:")
nba.info()

# Loading from Excel works the same way (example syntax — requires an .xlsx file)
# school_data = pd.read_excel("grades.xlsx", sheet_name="Sheet1")

# Saving a small slice back out to a new CSV
recent_games = nba[nba["year_id"] == 2015]
recent_games.to_csv("nba_2015_games.csv", index=False)
print("\nSaved", len(recent_games), "rows from the 2015 season to nba_2015_games.csv")
```

## Code Explanation
- `pd.read_csv("nbaallelo.csv")` reads the file from disk and returns a DataFrame stored in the variable `nba`.
- `.shape` returns a tuple like `(126314, 23)` — 126,314 rows and 23 columns.
- `.columns.tolist()` converts the column-name object into a plain Python list for easy printing.
- `.head()` and `.tail(3)` preview the top and bottom of the table — `tail(3)` shows exactly 3 rows instead of the default 5.
- `.info()` prints every column's name, how many non-null values it has, and its data type (`int64`, `float64`, `object`) — this is the fastest way to spot missing data and confirm columns are typed the way you expect.
- The commented-out `pd.read_excel(...)` line shows the equivalent call for an Excel workbook, including how to target a specific sheet by name.
- `nba[nba["year_id"] == 2015]` filters to only rows from the 2015 season (a preview of Lesson 4's filtering topic), and `.to_csv(..., index=False)` writes that filtered subset to a brand-new file without an extra index column.

## Expected Output
```
Shape (rows, columns): (126314, 23)

Column names:
['gameorder', 'game_id', 'lg_id', '_iscopy', 'year_id', 'date_game', 'seasongame', 'is_playoffs', 'team_id', 'fran_id', 'pts', 'elo_i', 'elo_n', 'win_equiv', 'opp_id', 'opp_fran', 'opp_pts', 'opp_elo_i', 'opp_elo_n', 'game_location', 'game_result', 'forecast', 'notes']

First 5 rows:
   gameorder     game_id lg_id  _iscopy  year_id  date_game  ...
0          1  194611010TRH   NBA        0     1947  11/1/1946  ...
1          1  194611010TRH   NBA        1     1947  11/1/1946  ...
...

Last 3 rows:
(three rows showing the most recent games in the dataset)

DataFrame info:
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 126314 entries, 0 to 126313
Data columns (total 23 columns):
 #   Column        Non-Null Count   Dtype
---  ------        --------------   -----
 0   gameorder     126314 non-null  int64
 ...
 22  notes         5424 non-null    object
dtypes: float64(5), int64(6), object(12)
memory usage: ...

Saved 2460 rows from the 2015 season to nba_2015_games.csv
```
(The `notes` column shows far fewer non-null entries than the others — a real, visible sign of missing data you'll deal with directly in Lesson 5.)

## Guided Practice
1. Change `.head()` to `.head(10)` and observe more rows.
2. Try `nba["team_id"]` alone to view just one column as a Series.
3. Change the filter year from 2015 to a different year present in the data and re-save.

## Hands-On Activity
Load the NBA CSV (or another provided CSV) into a DataFrame, then in separate cells run and screenshot/record the output of `.shape`, `.columns`, `.head()`, and `.info()`. Write one sentence per output describing what it tells you about the dataset.

## Student Challenge
Write a small function `inspect_dataset(df)` that takes any DataFrame and prints its shape, column list, and the number of missing values per column (hint: `df.isnull().sum()`, previewed here and covered fully in Lesson 5). Test it on the NBA dataset.

## Common Mistakes
- **Wrong file path**: `FileNotFoundError` almost always means the path is misspelled or the notebook isn't running from the folder you think it is — use `import os; print(os.getcwd())` to check your current folder.
- **Forgetting `index=False`** when saving, which adds an unwanted extra column to the output file.
- **Assuming Excel and CSV load identically**: Excel files can have multiple sheets — forgetting `sheet_name` may load the wrong one.
- **Not inspecting before analyzing**: Jumping straight into calculations without running `.info()` first, missing obvious data type or null-value problems.

## Debugging Guidance
- `FileNotFoundError`: print `os.listdir(".")` to see what files pandas can actually find from the current working directory.
- `UnicodeDecodeError`: some CSVs use a different encoding — try `pd.read_csv(path, encoding="latin1")`.
- If numeric columns show up as `object` (text) instead of `int64`/`float64`, that usually means the column has stray non-numeric characters (like commas in "1,000") that need cleaning — a preview of Lesson 5.
- If Excel loading fails with a missing-engine error, install the `openpyxl` package: `pip install openpyxl`.

## Mini Quiz
1. What Python function loads a CSV file into a DataFrame?
2. What does `.shape` return?
3. Name two things `.info()` tells you about a DataFrame.
4. Why do we usually pass `index=False` to `.to_csv()`?
5. What is the difference between a DataFrame and a Series?

### Answer Key
1. `pd.read_csv()`.
2. A tuple of `(number_of_rows, number_of_columns)`.
3. Any two of: column names, non-null counts per column, data types per column, total memory usage.
4. To avoid writing pandas' internal row index as an extra, unwanted column in the saved file.
5. A DataFrame is a full 2-dimensional table with multiple columns; a Series is a single 1-dimensional column (or row) of data.

## Lesson Recap
You loaded a real dataset (NBA Elo game data) from a CSV file into a pandas DataFrame, learned the equivalent process for Excel files, and practiced the essential first-look inspection steps every data scientist performs on new data: `.head()`, `.tail()`, `.shape`, `.columns`, and `.info()`. You also saved a filtered slice back out to a new CSV.

## Homework / Extension
Find any CSV file you have access to (a downloaded spreadsheet, exported grades, or one provided by your instructor). Load it into pandas, run the full inspection sequence from today, and write three sentences describing what you learned about the file's structure before doing any real analysis.

## Portfolio Project Connection
This lesson is the direct starting point for **all three portfolio projects** — Sports Performance Analysis (Project 1) loads `nbaallelo.csv`, School Grade Analysis (Project 2) loads `StudentsPerformance.csv`, and City Population Trends (Project 3) loads `population.csv`, all using exactly the `pd.read_csv()` and inspection workflow practiced today.
