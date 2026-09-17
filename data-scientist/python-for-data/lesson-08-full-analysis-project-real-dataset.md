# Course 13 · Python for Data — Lesson 8: Full Analysis Project with a Real Dataset

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Combine every skill from Lessons 1–7 (load, inspect, clean, filter, sort, aggregate, pivot, summarize, correlate) into one complete, end-to-end analysis.
- Perform a full real-data investigation on the FiveThirtyEight NBA Elo dataset from raw file to written conclusions.
- Practice writing clear, evidence-based conclusions from real analysis output — not assumptions.
- Present a finished mini-analysis in a way that mirrors the structure of the course's three portfolio projects.

## What You'll Learn
This is the capstone lesson: a complete, real, end-to-end data analysis using everything you've learned this course. Using the real FiveThirtyEight NBA Elo dataset (`nbaallelo.csv`, 126,314 rows spanning every NBA/BAA game since the 1946–47 season), you will load, clean, filter, aggregate, and statistically summarize the data, finishing with genuine, data-backed conclusions — the exact same workflow used in this course's three portfolio projects.

## Why It Matters
Individual skills only become valuable once you can chain them together to answer a real question. This lesson proves you can go from a raw CSV file to a finished, evidence-based conclusion entirely on your own — which is the actual day-to-day job of a data analyst or data scientist.

## Real-World Connection
This exact workflow — load, clean, explore, aggregate, summarize, conclude — is what a sports analytics team performs before every major team decision, what a school district performs before changing curriculum, and what a health agency performs before issuing public guidance. Today's lesson is a realistic, scaled-down version of that professional process.

## Key Terminology
(Review from Lessons 1–7)
- **DataFrame**, **Series**: pandas' table and column structures.
- **Cleaning**: handling nulls, duplicates, and data types.
- **Filtering / Sorting**: narrowing down and ordering rows.
- **Groupby / Pivot table**: summarizing data across categories.
- **Correlation**: the strength/direction of a relationship between two numeric variables.
- **Conclusion**: a clear, written statement of what the analysis actually shows, backed directly by computed numbers.

## Concept Explanation
A full data analysis project always follows the same shape, regardless of the dataset: **load → inspect → clean → explore (filter/sort/aggregate) → summarize statistically → conclude**. Today's dataset, `nbaallelo.csv`, is FiveThirtyEight's complete historical record of NBA and BAA games, including each team's Elo rating (a chess-style skill rating that updates after every game) both before (`elo_i`) and after (`elo_n`) each game, along with points scored, opponent, game result, and season.

We will investigate one real, well-scoped question: **"How have the Boston Celtics performed over their recorded NBA history, and which franchises have won the most games overall?"** This question requires nearly every skill from this course: loading and inspecting the data, cleaning the mostly-empty `notes` column, filtering to a specific team, aggregating wins by franchise, and grouping average points by season — followed by an honest, evidence-based conclusion.

## Step-by-Step Instruction
1. Load `nbaallelo.csv` and inspect its shape, columns, and data types.
2. Clean the dataset: fill missing `notes` values, confirm there are zero duplicate rows, and convert `date_game` to a real datetime.
3. Filter the dataset to the Boston Celtics (`team_id == "BOS"`) and inspect their average points scored across recent seasons.
4. Aggregate total wins by franchise (`fran_id`) across the entire dataset to find the winningest franchises in history.
5. Compute the correlation between Elo rating and points scored across the full dataset.
6. Identify the single highest post-game Elo rating ever recorded, and which team/season it belongs to.
7. Write clear, data-backed conclusions for each finding.

## Full Worked Code Example
```python
import pandas as pd

# --- Step 1: Load and inspect ---
nba = pd.read_csv("nbaallelo.csv")
print("Dataset shape:", nba.shape)
print("Columns:", nba.columns.tolist())
print("\nData types:")
print(nba.dtypes)

# --- Step 2: Clean ---
print("\nMissing values before cleaning:")
print(nba.isnull().sum()[nba.isnull().sum() > 0])

nba["notes"] = nba["notes"].fillna("none")
print("\nDuplicate rows:", nba.duplicated().sum())

nba["date_game"] = pd.to_datetime(nba["date_game"])
nba["game_year"] = nba["date_game"].dt.year

# --- Step 3: Filter — Boston Celtics recent seasons ---
celtics = nba[nba["team_id"] == "BOS"]
celtics_recent_avg = celtics.groupby("year_id")["pts"].mean().tail(5)
print("\nBoston Celtics average points scored, most recent 5 seasons on record:")
print(celtics_recent_avg.round(1))

# --- Step 4: Aggregate — winningest franchises of all time ---
wins_by_franchise = (
    nba[nba["game_result"] == "W"]
    .groupby("fran_id")
    .size()
    .sort_values(ascending=False)
)
print("\nTop 10 franchises by total recorded wins:")
print(wins_by_franchise.head(10))

# --- Step 5: Statistical summary + correlation ---
print("\nPoints scored - overall summary statistics:")
print(nba["pts"].describe().round(2))

elo_pts_corr = nba["elo_i"].corr(nba["pts"])
print(f"\nCorrelation between pre-game Elo rating and points scored: {elo_pts_corr:.3f}")

# --- Step 6: Highest Elo rating ever recorded ---
top_elo_game = nba.sort_values("elo_n", ascending=False)[["fran_id", "year_id", "elo_n"]].head(1)
print("\nHighest single post-game Elo rating ever recorded:")
print(top_elo_game)

# --- Step 7: Written conclusions (based only on the numbers above) ---
print("\n--- CONCLUSIONS ---")
print("1. The dataset contains 126,314 team-game rows spanning every NBA/BAA season since 1946-47.")
print("2. The 'notes' column was almost entirely empty (120,890 of 126,314 rows), which is expected")
print("   since it is only used to flag unusual games; it was filled with 'none' rather than dropped")
print("   to avoid losing 95.7% of the dataset. No duplicate rows were found.")
print("3. The Los Angeles Lakers and Boston Celtics have the two highest all-time win totals of any")
print("   franchise in the dataset (3,658 and 3,517 recorded wins respectively), reflecting their")
print("   long, historically successful franchise histories.")
print("4. Across the entire dataset, points scored per team-game average about 102.7, with a standard")
print("   deviation of about 14.8 — most single-team scoring performances fall roughly between 88 and 118 points.")
print("5. Pre-game Elo rating and points scored in that same game are only weakly positively correlated")
print("   (about 0.09), showing that a team's overall season-long strength rating does not strongly")
print("   predict how many points they will score in any single game.")
print("6. The single highest post-game Elo rating ever recorded in this dataset belongs to the 1996")
print("   Chicago Bulls, reflecting their historically dominant 72-win regular season.")
```

## Code Explanation
- Every step maps directly to a lesson from this course: loading/inspecting (Lesson 3), cleaning (Lesson 5), filtering/sorting (Lesson 4), aggregating with groupby (Lesson 6), statistical summary and correlation (Lesson 7).
- `celtics.groupby("year_id")["pts"].mean().tail(5)` shows the five most recent seasons of Celtics scoring data available in the dataset, using real numbers.
- `wins_by_franchise` aggregates every win across the dataset's entire history by franchise, using `.size()` on rows already filtered to `game_result == "W"`.
- The correlation and `.describe()` calls reuse exactly the syntax from Lesson 7, applied to the full, real, cleaned dataset.
- The final print statements are **written conclusions**, each one directly backed by a specific number computed above them — never a made-up or assumed claim.

## Expected Output
```
Dataset shape: (126314, 23)
Columns: ['gameorder', 'game_id', 'lg_id', '_iscopy', 'year_id', 'date_game', 'seasongame',
'is_playoffs', 'team_id', 'fran_id', 'pts', 'elo_i', 'elo_n', 'win_equiv', 'opp_id', 'opp_fran',
'opp_pts', 'opp_elo_i', 'opp_elo_n', 'game_location', 'game_result', 'forecast', 'notes']

Missing values before cleaning:
notes    120890
dtype: int64

Duplicate rows: 0

Boston Celtics average points scored, most recent 5 seasons on record:
year_id
2011     96.3
2012     91.3
2013     95.5
2014     96.2
2015    101.1
Name: pts, dtype: float64

Top 10 franchises by total recorded wins:
fran_id
Lakers      3658
Celtics     3517
Sixers      2933
Knicks      2855
Pistons     2761
Hawks       2753
Warriors    2646
Spurs       2539
Kings       2511
Bulls       2257
dtype: int64

Points scored - overall summary statistics:
count    126314.00
mean        102.73
std          14.81
min           0.00
25%          93.00
50%         103.00
75%         112.00
max         186.00
Name: pts, dtype: float64

Correlation between pre-game Elo rating and points scored: 0.090

Highest single post-game Elo rating ever recorded:
      fran_id  year_id      elo_n
78591   Bulls     1996  1853.1045

--- CONCLUSIONS ---
(the six numbered conclusions printed exactly as written in the code above)
```
(All figures shown here are the real, actual computed values from running this exact code against the genuine `nbaallelo.csv` dataset.)

## Guided Practice
1. Change the franchise filter from `"BOS"` to any other team abbreviation present in the data and re-run the season averages.
2. Compute the same win totals aggregation but for playoff games only (`is_playoffs == 1`).
3. Try correlating `elo_n` (post-game Elo) with `pts` instead of `elo_i` and compare the result.

## Hands-On Activity
Students pick one franchise other than the Celtics or Lakers and produce their own mini version of Steps 3–7 for that team: recent-season scoring average, win total, and one written conclusion backed by their own computed numbers.

## Student Challenge
Extend the analysis to answer: "Has average points-per-game across the entire NBA league gone up or down since the year 2000?" (hint: filter `nba["year_id"] >= 2000`, then group by `year_id` and compute the mean of `pts`, and describe the trend you observe in a written conclusion, being careful to describe only what the numbers show.)

## Common Mistakes
- **Writing conclusions not backed by the actual output**: Always re-check that every claim in your written conclusion matches a specific number you actually computed.
- **Skipping the cleaning step**: Forgetting to fill `notes` or convert `date_game` before analysis can cause confusing downstream errors or misleading date sorting.
- **Overgeneralizing from a single number**: A correlation of 0.09 should be described as "weak," not asserted as "no relationship at all" or "a strong effect."
- **Forgetting to re-inspect after cleaning**: Not re-running `.isnull().sum()` or `.dtypes` after cleaning steps to confirm the fixes actually worked.

## Debugging Guidance
- If `.tail(5)` on a groupby result shows fewer than 5 rows, that team may not have data for 5 distinct seasons in the dataset — check `.shape` on the filtered data first.
- If win totals look too low, confirm the filter `game_result == "W"` matches the exact capitalization and spelling used in the real column (`nba["game_result"].unique()`).
- If correlation looks wildly different than expected, confirm you didn't accidentally correlate a categorical/ID-like column instead of a genuinely numeric one.
- When in doubt, print `.head()` after every single step to visually confirm the DataFrame looks the way you expect before moving to the next step.

## Mini Quiz
1. What are the six steps of a full data analysis, in order, as covered in this lesson?
2. Which franchise had the most recorded wins in the dataset, and how many?
3. What was the correlation between pre-game Elo rating and points scored, and how should it be interpreted?
4. Why is it important that every conclusion is backed by a specific computed number?
5. What percentage of the `notes` column was missing, and why was it filled rather than dropped?

### Answer Key
1. Load, inspect, clean, explore (filter/sort/aggregate), statistically summarize, conclude.
2. The Los Angeles Lakers, with 3,658 recorded wins.
3. About 0.09 — a weak positive correlation, meaning pre-game Elo rating only slightly relates to how many points are scored in that specific game.
4. Because an unbacked conclusion is just a guess — data science conclusions must be traceable directly to the analysis that produced them, so others can verify and trust the finding.
5. About 95.7% (120,890 of 126,314 rows) were missing; it was filled with `"none"` rather than dropped because dropping would have discarded almost the entire dataset, and the missingness itself is expected (the column is only used for unusual games).

## Lesson Recap
You completed a full, real, end-to-end data analysis on the FiveThirtyEight NBA Elo dataset — loading, inspecting, cleaning, filtering, aggregating, statistically summarizing, and concluding — using every technique from Lessons 1 through 7 in a single connected workflow, exactly mirroring how this course's portfolio projects are built.

## Homework / Extension
Choose a different real question about the NBA dataset (e.g., "Which team had the single best individual-game scoring performance in a playoff game?") and produce your own full mini-analysis following the same six-step structure, ending with at least two written, number-backed conclusions.

## Portfolio Project Connection
This lesson **is** effectively a compact version of **Project 1: Sports Performance Analysis** — the full portfolio project notebook extends exactly this workflow with more visualizations, more franchises, and deeper season-by-season trend analysis on the same real dataset.
