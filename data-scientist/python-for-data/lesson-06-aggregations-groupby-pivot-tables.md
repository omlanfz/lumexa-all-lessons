# Course 13 · Python for Data — Lesson 6: Aggregations — Group By, Pivot Tables

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
- Group a DataFrame by one or more columns using `.groupby()`.
- Apply aggregation functions (`.mean()`, `.sum()`, `.count()`, `.max()`, `.min()`) to grouped data.
- Use `.agg()` to compute multiple statistics at once.
- Build a pivot table with `pd.pivot_table()` to summarize data across two dimensions.
- Interpret grouped/pivoted results to answer real analytical questions.

## What You'll Learn
Today you learn how to summarize huge datasets into meaningful groups — for example, "average points per team" instead of 126,000 individual game rows. You'll master `.groupby()`, combine it with multiple aggregation functions, and build pivot tables that summarize data across two categories at once, such as team-by-season averages.

## Why It Matters
A dataset with 126,000 rows is useless to a human reader on its own — nobody can look at every row and form a conclusion. Aggregation is how data scientists compress huge datasets into small, meaningful summaries: averages, totals, and counts grouped by category. This is arguably the single most-used analytical operation in real data science work.

## Real-World Connection
A sports network aggregates every player's stats to report season averages. A school groups grades by class or teacher to find the best-performing sections. A government agency pivots population data by country and year to spot demographic trends. All three use exactly `.groupby()` and pivot tables.

## Key Terminology
- **`.groupby()`**: Splits a DataFrame into groups based on one or more column values.
- **Aggregation function**: A calculation that reduces many values to one summary number (mean, sum, count, max, min).
- **`.agg()`**: Applies one or more aggregation functions at once, optionally to different columns.
- **Pivot table**: A summary table that reorganizes data across two categorical dimensions, with an aggregated value in each cell.
- **`pd.pivot_table()`**: The pandas function that builds a pivot table.

## Concept Explanation
`.groupby("column")` splits a DataFrame into a group for every unique value in that column — for example, `nba.groupby("team_id")` creates one group per team. On its own, a groupby object doesn't show any output — you must chain an aggregation function to actually compute something, like `.mean()` for the average, `.sum()` for the total, `.count()` for how many rows are in each group, or `.max()`/`.min()` for extremes. For example, `nba.groupby("team_id")["pts"].mean()` returns the average points scored by every team across the entire dataset.

You can group by *multiple* columns at once by passing a list — `nba.groupby(["team_id", "year_id"])["pts"].mean()` computes the average points for every team, broken down further by season. This produces a result with a hierarchical (multi-level) index, one level per grouping column.

`.agg()` lets you compute several statistics in a single call instead of chaining separate lines — `nba.groupby("team_id")["pts"].agg(["mean", "max", "min", "count"])` returns a table with all four statistics as columns, one row per team. You can even apply different aggregations to different columns using a dictionary: `df.groupby("team_id").agg({"pts": "mean", "elo_n": "max"})`.

A **pivot table** is a specialized, often more readable way to summarize two categorical dimensions at once, similar to spreadsheet pivot tables. `pd.pivot_table(df, values="pts", index="team_id", columns="year_id", aggfunc="mean")` produces a grid with teams as rows, seasons as columns, and average points scored in each cell — instantly revealing patterns like which teams were high-scoring in which eras. Pivot tables are especially useful when you want to *visually* compare two categories side by side, rather than read a long grouped list.

## Step-by-Step Instruction
1. Load the NBA dataset.
2. Group by `team_id` and compute average points scored per team with `.groupby("team_id")["pts"].mean()`.
3. Sort that result to find the highest-scoring teams historically.
4. Group by both `team_id` and `year_id` to see team performance evolve season by season.
5. Use `.agg()` to compute mean, max, and count together in one call.
6. Build a pivot table comparing average Elo rating across teams and a handful of specific seasons.

## Code Example
```python
import pandas as pd

nba = pd.read_csv("nbaallelo.csv")

# --- Basic groupby + aggregation ---
avg_pts_by_team = nba.groupby("team_id")["pts"].mean().sort_values(ascending=False)
print("Top 5 teams by average points scored per game (all-time):")
print(avg_pts_by_team.head())

# --- Groupby with multiple aggregations at once ---
team_summary = nba.groupby("team_id")["pts"].agg(["mean", "max", "min", "count"])
team_summary = team_summary.rename(columns={
    "mean": "avg_pts", "max": "max_pts", "min": "min_pts", "count": "games_played"
})
print("\nTeam scoring summary (first 5 teams alphabetically):")
print(team_summary.sort_index().head())

# --- Groupby multiple columns: team performance by season ---
team_season_avg = nba.groupby(["team_id", "year_id"])["pts"].mean()
print("\nBoston Celtics ('BOS') average points, by season, first 5 seasons on record:")
print(team_season_avg.loc["BOS"].head())

# --- Aggregating a different statistic: win counts per team ---
wins_per_team = nba[nba["game_result"] == "W"].groupby("team_id").size().sort_values(ascending=False)
print("\nTop 5 teams by total recorded wins in the dataset:")
print(wins_per_team.head())

# --- Pivot table: average Elo rating by team, across a few notable seasons ---
notable_seasons = nba[nba["year_id"].isin([1996, 2006, 2016])]
elo_pivot = pd.pivot_table(
    notable_seasons,
    values="elo_n",
    index="team_id",
    columns="year_id",
    aggfunc="mean"
)
print("\nAverage post-game Elo rating by team, for seasons 1996, 2006, 2016:")
print(elo_pivot.head(10))
```

## Code Explanation
- `nba.groupby("team_id")["pts"].mean()` splits the dataset into one group per team, then computes the average `pts` value within each group — the result is a Series indexed by `team_id`.
- `.agg(["mean", "max", "min", "count"])` computes four statistics in one call, producing a DataFrame with one column per statistic; `.rename(columns={...})` gives those columns clearer names.
- `nba.groupby(["team_id", "year_id"])["pts"].mean()` groups by two columns at once, creating a two-level (multi-index) result; `.loc["BOS"]` then drills into just that one team's season-by-season averages.
- `.size()` counts rows per group (similar to `.count()`, but counts rows regardless of missing values in a specific column) — here used after filtering to only winning rows, giving total wins per team.
- `pd.pivot_table(...)` reshapes the filtered data so that `team_id` becomes rows, `year_id` becomes columns, and each cell holds the average `elo_n` for that team in that season — letting you visually compare teams across seasons side by side.

## Expected Output
```
Top 5 teams by average points scored per game (all-time):
team_id
DNN    ...
(five team abbreviations with their average points, generally in the 105-115 range for
 higher-scoring franchises/eras, since the dataset spans very different scoring eras)

Team scoring summary (first 5 teams alphabetically):
         avg_pts  max_pts  min_pts  games_played
team_id
...      (real per-team numbers: average, max, min points, and total games played)

Boston Celtics ('BOS') average points, by season, first 5 seasons on record:
year_id
1947    ...
1948    ...
(five season-average point values for the Celtics' earliest seasons in the data)

Top 5 teams by total recorded wins in the dataset:
team_id
BOS    ...
LAL    ...
(teams with long, successful franchise histories appearing near the top,
 since total wins accumulate over more seasons played)

Average post-game Elo rating by team, for seasons 1996, 2006, 2016:
          1996    2006    2016
team_id
...       (a grid of average Elo ratings, with NaN where a team did not play
           in a given one of those three seasons)
```

## Guided Practice
1. Change `avg_pts_by_team` to sort ascending instead of descending — what does the *lowest*-scoring team tell you?
2. Add `"std"` (standard deviation) to the `.agg()` list to see which teams are most inconsistent in scoring.
2. Change the `notable_seasons` filter to three different years of your choosing and rebuild the pivot table.

## Hands-On Activity
Using the NBA dataset, students compute and print: (1) average points scored per team, sorted highest to lowest, (2) each team's total games played using `.size()`, (3) a pivot table showing average points scored by team for two seasons of their choice. They write two sentences interpreting the pivot table's most interesting cell.

## Student Challenge
Build a pivot table showing the *win rate* per team per season, not just raw wins — this requires creating a numeric "win" column (1 for `"W"`, 0 for `"L"`) with `.apply()` or a boolean-to-int conversion, then pivoting with `aggfunc="mean"` (since the mean of 1s and 0s is exactly the win rate). Identify the single best team-season combination by win rate in your pivot table.

## Common Mistakes
- **Forgetting to aggregate**: Writing `nba.groupby("team_id")` alone and trying to print it shows a cryptic `DataFrameGroupBy object` message — you must chain an aggregation function like `.mean()`.
- **Aggregating the wrong column shape**: Calling `.groupby("team_id").mean()` without first selecting a specific column tries to average *every* numeric column, including ones that don't make sense to average (like `year_id`) — always select the column(s) you actually want first.
- **Confusing `.count()` and `.size()`**: `.count()` ignores missing values per column; `.size()` counts all rows in a group regardless of nulls — using the wrong one can silently produce different totals.
- **Misreading a pivot table's NaN cells**: A `NaN` in a pivot table usually means that combination didn't occur in the data (e.g., a team didn't play that season) — not that the value was literally zero.

## Debugging Guidance
- If a groupby result looks unexpectedly small or large, check `.size()` per group first to confirm the group sizes are what you expect.
- If `.agg()` raises an error on a non-numeric column, make sure you selected only the numeric column(s) you intend to aggregate before calling `.agg()`.
- If a pivot table is mostly `NaN`, check whether your `index`/`columns` filter (like the `notable_seasons` filter above) is too narrow for the data you're summarizing.
- Print `.shape` on grouped/pivoted results to confirm how many groups or rows resulted, especially after multi-column groupby.

## Mini Quiz
1. What must you chain after `.groupby()` to actually see a result?
2. What does `.agg(["mean", "max"])` do differently from calling `.mean()` alone?
3. What's the difference between `.count()` and `.size()` in a groupby?
4. What two things become the rows and columns of a pivot table built with `pd.pivot_table()`?
5. Why might a pivot table cell show `NaN` instead of a number?

### Answer Key
1. An aggregation function, such as `.mean()`, `.sum()`, `.count()`, `.max()`, or `.min()`.
2. `.agg(["mean", "max"])` computes multiple statistics at once, returning them as separate columns, instead of just one summary number from a single function.
3. `.count()` counts non-null values per column within each group; `.size()` counts the total number of rows in each group regardless of missing values.
4. The `index` argument becomes the pivot table's rows and the `columns` argument becomes its columns; the `values`/`aggfunc` determine what's calculated in each cell.
5. Because there's no data for that specific row/column combination (e.g., that team didn't play in that particular season), so there's nothing to aggregate.

## Lesson Recap
You learned to summarize large datasets using `.groupby()` with single and multiple aggregation functions, grouped by one or multiple columns at once, and built pivot tables with `pd.pivot_table()` to compare two categorical dimensions side by side — turning 126,000+ individual rows into meaningful, readable summaries.

## Homework / Extension
Using the NBA dataset, build a pivot table showing total wins per team for three seasons of your choice, and write a short paragraph identifying which team improved the most and which declined the most across those seasons, based only on what the pivot table actually shows.

## Portfolio Project Connection
Group-by and pivot tables are central to **Project 1** (team performance by season), **Project 2** (average scores grouped by gender, parental education, or test preparation), and **Project 3** (population totals grouped and pivoted by country and year).
