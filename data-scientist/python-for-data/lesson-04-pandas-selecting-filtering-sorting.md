# Course 13 · Python for Data — Lesson 4: Pandas — Selecting, Filtering, Sorting

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Select one or more columns from a DataFrame.
- Select rows by position (`.iloc`) and by label/condition (`.loc`).
- Filter rows using boolean conditions, including combining multiple conditions with `&` and `|`.
- Sort a DataFrame by one or more columns, ascending or descending.
- Combine selecting, filtering, and sorting into a single readable analysis chain.

## What You'll Learn
Today is about asking a dataset questions. You'll learn how to pull out exactly the columns and rows you care about, filter down to just the data matching specific conditions (like "only playoff games" or "only students who scored above 90"), and sort results to quickly find the highest, lowest, first, or most recent values.

## Why It Matters
Real datasets are big — the NBA dataset you'll use has over 126,000 rows. You almost never want to look at all of it at once. Selecting, filtering, and sorting are the three most-used tools in any data scientist's daily work, letting you zoom in on exactly the slice of data relevant to the question you're trying to answer.

## Real-World Connection
A sports analyst filters game data down to just one team's playoff games. A school administrator filters student records to find everyone below a passing grade. A city planner sorts population data to find the fastest-growing regions. These are the exact same three skills — select, filter, sort — applied to different real datasets.

## Key Terminology
- **Column selection**: Picking one or more columns, e.g. `df["column_name"]` or `df[["col1", "col2"]]`.
- **Boolean mask**: A Series of `True`/`False` values used to filter rows, e.g. `df["score"] > 90`.
- **`.loc[]`**: Label/condition-based row and column selector.
- **`.iloc[]`**: Integer-position-based row and column selector.
- **`.sort_values()`**: Method that reorders rows by one or more column values.
- **Ascending / Descending**: Sort order from smallest-to-largest or largest-to-smallest.

## Concept Explanation
**Selecting** a single column with `df["column_name"]` returns a Series; selecting multiple columns with `df[["col1", "col2"]]` (note the double brackets — a list inside the selector) returns a smaller DataFrame. This is the pandas equivalent of hiding spreadsheet columns you don't need right now.

**Filtering** rows uses a **boolean mask**: writing a condition like `df["pts"] > 100` doesn't return the matching rows directly — it returns a Series of `True`/`False` values, one per row, showing which rows satisfy the condition. Wrapping that condition in `df[...]` — e.g. `df[df["pts"] > 100]` — then returns only the rows where the mask is `True`. Multiple conditions can be combined using `&` (and) or `|` (or), with each condition wrapped in its own parentheses: `df[(df["pts"] > 100) & (df["is_playoffs"] == 1)]`.

`.loc[]` and `.iloc[]` are two related but different tools. `.loc[]` selects by **label** — row/column names, or boolean conditions — e.g. `df.loc[df["pts"] > 100, ["team_id", "pts"]]` selects specific columns only for rows matching a condition. `.iloc[]` selects by **integer position**, ignoring labels entirely — e.g. `df.iloc[0:5, 0:3]` grabs the first 5 rows and first 3 columns purely by position, useful when you just want "the top few rows" regardless of what they're labeled.

**Sorting** with `.sort_values("column_name")` reorders the whole DataFrame by that column, smallest to largest by default; pass `ascending=False` to reverse it. You can sort by multiple columns at once by passing a list, and pandas will use the second column to break ties in the first.

Together, these three operations chain naturally: filter down to the rows you care about, select just the columns relevant to your question, then sort to bring the most interesting rows to the top.

## Step-by-Step Instruction
1. Load the NBA dataset as in Lesson 3.
2. Select a single column (`nba["pts"]`) and observe the Series output.
3. Select multiple columns (`nba[["team_id", "pts", "year_id"]]`).
4. Build a boolean mask filtering for playoff games only.
5. Combine two conditions with `&` to filter playoff games with more than 120 points scored.
6. Use `.loc[]` to select specific columns only for the filtered rows.
7. Sort the filtered result by points scored, descending.
8. Use `.iloc[]` to grab just the top 5 rows by position.

## Code Example
```python
import pandas as pd

nba = pd.read_csv("nbaallelo.csv")

# --- Selecting columns ---
points_only = nba["pts"]                     # single column -> Series
key_columns = nba[["team_id", "pts", "year_id", "game_result"]]  # multiple columns -> DataFrame
print("Selected columns preview:")
print(key_columns.head())

# --- Filtering rows with boolean masks ---
playoff_games = nba[nba["is_playoffs"] == 1]
print("\nNumber of playoff game-rows:", len(playoff_games))

high_scoring_playoff_games = nba[(nba["is_playoffs"] == 1) & (nba["pts"] >= 130)]
print("Playoff games with 130+ points scored:", len(high_scoring_playoff_games))

# --- Using .loc and .iloc ---
top_scoring_cols = nba.loc[nba["pts"] >= 130, ["date_game", "team_id", "opp_id", "pts"]]
print("\n.loc selection (rows with 130+ points, key columns only):")
print(top_scoring_cols.head())

first_three_rows_first_two_cols = nba.iloc[0:3, 0:2]
print("\n.iloc selection (first 3 rows, first 2 columns):")
print(first_three_rows_first_two_cols)

# --- Sorting ---
highest_scoring_games = nba.sort_values("pts", ascending=False)
print("\nTop 5 highest-scoring team performances of all time:")
print(highest_scoring_games[["date_game", "team_id", "opp_id", "pts"]].head())

# Sorting by multiple columns: by season, then by points within each season
sorted_by_season_and_pts = nba.sort_values(["year_id", "pts"], ascending=[True, False])
print("\nHighest scoring game of the very first season in the dataset:")
print(sorted_by_season_and_pts[["year_id", "team_id", "pts"]].head(1))
```

## Code Explanation
- `nba["pts"]` returns a Series (one column); `nba[["team_id", "pts", "year_id", "game_result"]]` returns a smaller DataFrame with only those four columns, in that order.
- `nba["is_playoffs"] == 1` creates a boolean mask; wrapping it in `nba[...]` filters to only playoff rows.
- The combined filter uses `&` to require *both* conditions be true simultaneously — each condition is wrapped in its own parentheses, which is required by Python's order of operations here.
- `.loc[condition, [columns]]` filters rows *and* selects specific columns in a single step — very common in real analysis code.
- `.iloc[0:3, 0:2]` ignores column names entirely and grabs strictly by position — the first 3 rows, first 2 columns.
- `.sort_values("pts", ascending=False)` reorders the entire dataset from highest to lowest points scored in a single team-game performance.
- `.sort_values(["year_id", "pts"], ascending=[True, False])` sorts primarily by season (oldest first) and, within each season, by points scored (highest first) — a two-level sort.

## Expected Output
```
Selected columns preview:
  team_id  pts  year_id game_result
0     TRH   66     1947           L
1     NYK   68     1947           W
2     CHS   63     1947           W
3     PRO   50     1947           L
4     BOS   53     1947           L

Number of playoff game-rows: (a count in the low tens of thousands, since many decades of playoff games are included)
Playoff games with 130+ points scored: (a small count, since 130+ points in a single game is a relatively rare high-scoring performance)

.loc selection (rows with 130+ points, key columns only):
(a small table of high-scoring games with date, teams, and points columns only)

.iloc selection (first 3 rows, first 2 columns):
   gameorder     game_id
0          1  194611010TRH
1          1  194611010TRH
2          2  194611020CHS

Top 5 highest-scoring team performances of all time:
(rows showing some of the highest-scoring games in NBA history, typically from fast-paced eras)

Highest scoring game of the very first season in the dataset:
(one row showing year_id 1947 and the highest points value recorded that season)
```

## Guided Practice
1. Change the points threshold from 130 to 140 and observe how the filtered count shrinks.
2. Filter for games where `game_result == "W"` and count how many rows match.
3. Sort by `elo_n` (post-game Elo rating) descending and view the top 5 teams by rating.

## Hands-On Activity
Using the NBA dataset, students write a short chain of code that: filters to only regular-season games (`is_playoffs == 0`), selects the columns `team_id`, `pts`, `opp_pts`, `game_result`, and sorts by `pts` descending, printing the top 10 rows. They write one sentence describing what the result shows.

## Student Challenge
Write a filter that finds all "blowout" games — where the winning team scored at least 30 more points than the opponent (hint: you'll need to compare `pts` and `opp_pts` and also check `game_result`). Sort the result by the point difference, descending, and print the top 5 biggest blowouts in the dataset.

## Common Mistakes
- **Single brackets vs. double brackets**: `df["col"]` returns a Series; `df[["col"]]` returns a one-column DataFrame — mixing these up causes confusing errors later.
- **Missing parentheses in combined conditions**: `df[df["a"] > 1 & df["b"] < 2]` raises an error — each condition needs its own parentheses: `df[(df["a"] > 1) & (df["b"] < 2)]`.
- **Using `and`/`or` instead of `&`/`|`**: Python's plain `and`/`or` don't work element-wise on pandas Series — always use `&` and `|` for filtering.
- **Confusing `.loc` and `.iloc`**: `.loc` uses labels/conditions; `.iloc` uses pure integer position — mixing them up gives wrong or error-raising results.

## Debugging Guidance
- `ValueError: The truth value of a Series is ambiguous` almost always means you used `and`/`or` instead of `&`/`|`, or forgot parentheses around combined conditions.
- If `.loc[]` raises a `KeyError`, double check the exact column name spelling with `df.columns`.
- If sorting doesn't look right, confirm the column's data type with `.dtype` — sorting a text column that looks numeric (e.g., `"100"` as a string) sorts alphabetically, not numerically.
- Use `.shape` before and after filtering to sanity-check how many rows survived.

## Mini Quiz
1. What does `df[["colA", "colB"]]` return, and how is it different from `df["colA"]`?
2. What symbol combines two filter conditions where both must be true?
3. What is the key difference between `.loc[]` and `.iloc[]`?
4. What parameter reverses `.sort_values()` to largest-to-smallest?
5. What does a boolean mask actually contain?

### Answer Key
1. It returns a DataFrame with just those two columns; `df["colA"]` alone returns a single column as a Series, not a DataFrame.
2. `&` (the "and" operator for combining boolean masks).
3. `.loc[]` selects by label or boolean condition; `.iloc[]` selects purely by integer position, regardless of labels.
4. `ascending=False`.
5. A Series of `True`/`False` values, one for each row, indicating whether that row satisfies the given condition.

## Lesson Recap
You practiced the three most common daily operations in data science: selecting specific columns, filtering rows with boolean conditions (including combining conditions with `&`/`|`), and sorting results using `.sort_values()`. You also learned the difference between `.loc[]` (label-based) and `.iloc[]` (position-based) selection.

## Homework / Extension
Using the NBA dataset (or a dataset of your choice), write code that filters for one specific team's games (e.g., `team_id == "BOS"`), sorts them by season, and selects only the columns needed to answer: "did this team's points scored trend up or down over time?" Write two sentences summarizing what you find.

## Portfolio Project Connection
This lesson's skills are used directly in **Project 1 (Sports Performance Analysis)** to filter specific teams/seasons and sort by Elo rating or points, and equally in **Project 2 (School Grade Analysis)** and **Project 3 (City Population Trends)** to filter and sort by scores, subjects, countries, or years.
