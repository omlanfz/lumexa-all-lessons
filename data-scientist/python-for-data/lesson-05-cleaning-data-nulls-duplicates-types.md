# Course 13 · Python for Data — Lesson 5: Cleaning Data — Nulls, Duplicates, Types

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Detect missing (null) values in a DataFrame with `.isnull()` and `.sum()`.
- Decide between dropping and filling missing values, and apply both with `.dropna()` and `.fillna()`.
- Detect and remove duplicate rows with `.duplicated()` and `.drop_duplicates()`.
- Inspect and convert column data types using `.dtypes` and `.astype()`.
- Explain why data cleaning is often the most time-consuming part of real data science work.

## What You'll Learn
Real data is messy. Today you'll learn the three most common cleaning problems — missing values, duplicate rows, and incorrect data types — and the standard pandas tools to fix each one, using the real NBA dataset's genuinely missing `notes` column as a live example.

## Why It Matters
Analysis performed on dirty data produces wrong or misleading answers — a famous saying in the field is "garbage in, garbage out." Professional data scientists often spend 60–80% of their time cleaning data before ever getting to the "fun" analysis part. Learning to clean data properly is what separates a reliable analysis from a broken one.

## Real-World Connection
Hospitals cleaning patient records before medical research, sports leagues correcting inconsistent team-name spellings across decades of historical data, and schools reconciling grade records exported from different systems all rely on exactly these techniques — detecting nulls, dropping duplicates, and fixing data types — before any real analysis or reporting can begin.

## Key Terminology
- **Null / Missing value (`NaN`)**: A placeholder pandas uses for data that doesn't exist for a given row/column.
- **`.isnull()`**: Returns a DataFrame/Series of `True`/`False` marking which values are missing.
- **`.dropna()`**: Removes rows (or columns) containing missing values.
- **`.fillna()`**: Replaces missing values with a specified value (like 0, the mean, or a placeholder string).
- **Duplicate row**: A row that is an exact (or near-exact) copy of another row.
- **`.drop_duplicates()`**: Removes duplicate rows, keeping only the first occurrence by default.
- **Data type (dtype)**: The kind of value a column holds (`int64`, `float64`, `object`/text, `bool`, `datetime64`).
- **`.astype()`**: Converts a column from one data type to another.

## Concept Explanation
Missing values happen for many real reasons: a sensor failed, a form field was left blank, or — as in the NBA dataset — a "notes" column is only filled in for unusual games (like All-Star games or note-worthy events), leaving it blank for ordinary games. Pandas represents missing values as `NaN` ("Not a Number"). `df.isnull()` returns `True`/`False` for every cell; chaining `.sum()` on top (`df.isnull().sum()`) counts how many missing values exist in *each column* — the single most common first check on any new dataset.

Once you know where the gaps are, you have two main choices. `.dropna()` removes any row (by default) that has at least one missing value — appropriate when missing data is rare and rows can be safely discarded, but dangerous if it silently removes a large fraction of your dataset. `.fillna(value)` instead replaces missing values with something meaningful — a `0`, a text placeholder like `"none"`, or a calculated statistic like the column's mean or median (`df["col"].fillna(df["col"].mean())`). The right choice always depends on *why* the data is missing and what the column represents — a missing "notes" field can reasonably be filled with `"none"`, but a missing test score should never be filled with `0`, since that would falsely suggest the student failed.

**Duplicate rows** occur when the same record appears more than once — from a data export bug, a merge gone wrong, or manual double-entry. `df.duplicated()` flags rows that are exact repeats of an earlier row; `df.drop_duplicates()` removes them, keeping the first occurrence by default. You can also check duplicates based on specific columns only (`df.duplicated(subset=["team_id", "date_game"])`), useful when two rows shouldn't both exist for the same team on the same date even if other columns differ slightly.

Finally, **data types** matter enormously for correct analysis. A column of scores that got loaded as text (`object`) instead of numbers (`int64`) will sort alphabetically instead of numerically, and can't be averaged with `.mean()` without first converting it. `.dtypes` shows every column's current type; `.astype(int)`, `.astype(float)`, or `.astype(str)` converts a column explicitly. A very common real-world type problem is a date stored as plain text — converting it with `pd.to_datetime()` unlocks powerful date-based operations (extracting the year, month, or day, and sorting chronologically) that a text column cannot support.

## Step-by-Step Instruction
1. Load the NBA dataset and run `nba.isnull().sum()` to see missing-value counts per column.
2. Identify that `notes` is the column with by far the most missing values.
3. Decide (as a class) that filling `notes` with `"none"` makes more sense than dropping ~96% of the dataset's rows.
4. Apply `.fillna("none")` to the `notes` column specifically.
5. Check for duplicate rows with `.duplicated().sum()`.
6. Confirm there are none in this dataset (a real, useful negative finding), then demonstrate `.drop_duplicates()` on a small synthetic example so students see it work.
7. Inspect `.dtypes` and convert the `date_game` column to a real datetime type with `pd.to_datetime()`.

## Code Example
```python
import pandas as pd

nba = pd.read_csv("nbaallelo.csv")

# --- 1. Detecting missing values ---
missing_counts = nba.isnull().sum()
print("Missing values per column:")
print(missing_counts[missing_counts > 0])

# --- 2. Handling missing values ---
# 'notes' is mostly empty because it's only used for special game notes (All-Star games etc.)
# Filling with a clear placeholder preserves all rows instead of deleting most of the dataset.
nba["notes"] = nba["notes"].fillna("none")
print("\nMissing 'notes' values after fillna:", nba["notes"].isnull().sum())

# --- 3. Detecting and removing duplicate rows ---
duplicate_count = nba.duplicated().sum()
print("\nNumber of exact duplicate rows in the real dataset:", duplicate_count)

# Demonstrate drop_duplicates on a small synthetic example, since the real data has none
example = pd.DataFrame({
    "team": ["Lakers", "Celtics", "Lakers"],
    "points": [102, 98, 102],
})
print("\nSynthetic example before dedup:")
print(example)
example_clean = example.drop_duplicates()
print("\nSynthetic example after drop_duplicates():")
print(example_clean)

# --- 4. Data types ---
print("\nData types before conversion:")
print(nba[["date_game", "pts", "elo_i"]].dtypes)

nba["date_game"] = pd.to_datetime(nba["date_game"])
print("\nData types after converting date_game to datetime:")
print(nba[["date_game", "pts", "elo_i"]].dtypes)

# Now that it's a real datetime, we can extract useful parts
nba["game_year"] = nba["date_game"].dt.year
print("\nExtracted game_year sample:")
print(nba[["date_game", "game_year"]].head(3))
```

## Code Explanation
- `nba.isnull().sum()` counts missing values in every column; filtering with `[missing_counts > 0]` shows only the columns that actually have gaps.
- `fillna("none")` replaces every missing `notes` entry with the literal text `"none"`, which is an honest, informative placeholder for a column that's only meaningfully filled in for special games.
- `.duplicated().sum()` counts exact duplicate rows across the *entire* row — the real NBA dataset genuinely has zero, which is itself a useful, reportable finding (the data is already de-duplicated at the source).
- The synthetic `example` DataFrame demonstrates `drop_duplicates()` clearly, since the real dataset has no duplicates to show directly — the code comments are explicit that this part is a teaching example, not a claim about the real data.
- `pd.to_datetime(nba["date_game"])` converts the date column from plain text to a true datetime type, after which `.dt.year` can pull out just the year for each row — something that isn't possible on a plain text column.

## Expected Output
```
Missing values per column:
notes    120890
dtype: int64

Missing 'notes' values after fillna: 0

Number of exact duplicate rows in the real dataset: 0

Synthetic example before dedup:
      team  points
0   Lakers     102
1  Celtics      98
2   Lakers     102

Synthetic example after drop_duplicates():
      team  points
0   Lakers     102
1  Celtics      98

Data types before conversion:
date_game     object
pts            int64
elo_i         float64
dtype: object

Data types after converting date_game to datetime:
date_game    datetime64[ns]
pts                   int64
elo_i               float64
dtype: object

Extracted game_year sample:
   date_game  game_year
0 1946-11-01       1946
1 1946-11-01       1946
2 1946-11-02       1946
```

## Guided Practice
1. Check how many missing values remain across the entire dataset after cleaning `notes` (should be 0).
2. Try filling a numeric example column's missing values with its mean instead of a fixed value.
3. Extract `.dt.month` in addition to `.dt.year` and preview the result.

## Hands-On Activity
Students run `.isnull().sum()` on the NBA dataset before and after their own `fillna()` choice for the `notes` column, and write a short explanation of why they chose to fill rather than drop (or vice versa), referencing the actual percentage of missing data (`120890 / 126314 ≈ 95.7%`).

## Student Challenge
Write code that converts `year_id` to a proper category by creating a new column `era` that labels each row `"early"` (year_id < 1980), `"middle"` (1980–2000), or `"modern"` (after 2000), using a function applied with `.apply()`. This combines cleaning-adjacent thinking (creating a clean, meaningful category) with conditional logic from Lesson 2.

## Common Mistakes
- **Dropping too aggressively**: Calling `.dropna()` on the whole NBA DataFrame without a subset would delete ~96% of all rows, since `notes` is missing almost everywhere — always check *which* column has nulls before deciding to drop.
- **Filling numeric data with an inappropriate constant**: Filling missing test scores with `0` incorrectly implies a failing grade rather than "unknown."
- **Forgetting to reassign the result**: `nba["notes"].fillna("none")` on its own does NOT change the DataFrame — you must reassign it: `nba["notes"] = nba["notes"].fillna("none")`.
- **Converting types without checking for errors first**: Running `.astype(int)` on a column with unexpected text values raises a `ValueError` — inspect the column with `.unique()` first.

## Debugging Guidance
- If `.isnull().sum()` shows unexpected nulls after cleaning, confirm you reassigned the column instead of just viewing the result.
- `ValueError: could not convert string to float` when using `.astype()` means the column has non-numeric text somewhere — use `.unique()` or `.value_counts()` to find the culprit values.
- If `pd.to_datetime()` raises a parsing error, check for inconsistent date formats in the source data and consider the `format=` argument to specify the exact pattern.
- Always re-run `.dtypes` after any conversion to confirm the change actually took effect.

## Mini Quiz
1. What does `.isnull().sum()` tell you about a DataFrame?
2. When might filling missing values be a better choice than dropping them?
3. What method removes duplicate rows, keeping the first occurrence?
4. Why did the NBA dataset's `notes` column show ~120,890 missing values?
5. What does `pd.to_datetime()` allow you to do that a plain text date column does not?

### Answer Key
1. It shows how many missing (null) values exist in each column of the DataFrame.
2. When dropping rows would remove too much valuable data, or when the missing value can be reasonably estimated or replaced with a meaningful placeholder (like a column mean or the label "none").
3. `.drop_duplicates()`.
4. Because `notes` is only filled in for unusual or noteworthy games (like All-Star games); for the vast majority of ordinary games it is left blank, which is an accurate reflection of how the source data is structured, not an error.
5. It allows extracting parts of the date (like `.dt.year` or `.dt.month`) and sorting or comparing dates chronologically, which plain text cannot support correctly.

## Lesson Recap
You learned to detect and handle missing values (`.isnull()`, `.fillna()`, `.dropna()`), detect and remove duplicate rows (`.duplicated()`, `.drop_duplicates()`), and inspect and fix column data types (`.dtypes`, `.astype()`, `pd.to_datetime()`) — using the real NBA dataset's genuinely missing `notes` column as a live example.

## Homework / Extension
Pick any dataset you've worked with (or the population dataset if available) and produce a short "data cleaning report": list every column with missing values and how many, state your decision (fill or drop) for each with one sentence of reasoning, and confirm the final null count is what you expect.

## Portfolio Project Connection
This lesson's techniques are applied directly in all three projects: filling the NBA `notes` column in **Project 1**, checking (and confirming the absence of) nulls/duplicates in the student performance data for **Project 2**, and handling any gaps in country-year population coverage in **Project 3**.
