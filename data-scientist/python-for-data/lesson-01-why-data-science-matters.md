# Course 13 · Python for Data — Lesson 1: Why Data Science Matters (Real Examples)

**Target Age:** 13–18
**Estimated Duration:** 45–60 minutes (instructor-led, live)

## Learning Objectives
By the end of this lesson, students will be able to:
- Define "data science" in plain language and describe the three core steps of a data workflow: collect, analyze, communicate.
- Identify at least five real industries where data science drives decisions.
- Explain the difference between raw data, information, and insight.
- Read a small real dataset and describe, in words, one pattern they notice in it.
- Explain, at a beginner level, why Python is the tool this course will use for data work.

## What You'll Learn
This lesson is the "why" before the "how." Before writing a single line of pandas code, you will see real examples of data science solving real problems: sports teams using stats to win championships, streaming services recommending your next show, health researchers tracking disease outbreaks, and cities predicting traffic. You'll look at a tiny real dataset together as a class and practice reading it like a data scientist reads a crime scene — looking for clues, patterns, and stories hidden in numbers.

## Why It Matters
Every swipe, click, game score, and sensor reading on Earth generates data — and someone has to make sense of it. Data science is the skill of turning messy piles of numbers into decisions. It's one of the fastest-growing career fields in the world, but more importantly for you right now: it's a superpower for understanding the world you already live in. If you've ever wondered why your favorite show recommends certain videos, why sports commentators quote strange statistics, or how scientists know the climate is changing, the answer is always the same: data science.

## Real-World Connection
- **Sports**: NBA teams use player tracking data and win-probability models (like the Elo rating system you'll use later in this course) to decide who to draft, how to substitute players, and when to take risky shots.
- **Streaming & Social Media**: Netflix and YouTube analyze billions of watch-time data points to recommend content.
- **Public Health**: During disease outbreaks, scientists use case-count data to predict spread and guide policy — this is exactly what happened worldwide during COVID-19.
- **Climate Science**: NASA and NOAA track decades of temperature and population data to study climate change.
- **City Planning**: Population data (like the real dataset you'll use in Project 3 of this course) helps governments plan schools, hospitals, and transportation.
- **Video Games**: Game studios analyze player behavior data to balance difficulty and design levels people enjoy.

## Key Terminology
- **Data**: Raw facts and numbers with no context yet (e.g., `72`, `"female"`, `1998`).
- **Dataset**: An organized collection of data, usually in rows and columns.
- **Data Science**: The practice of collecting, cleaning, analyzing, and communicating data to answer questions and support decisions.
- **Insight**: A meaningful conclusion drawn from analyzing data.
- **Variable / Column / Feature**: A single measured attribute in a dataset (e.g., "points scored").
- **Row / Record / Observation**: A single entry in a dataset (e.g., one basketball game).
- **Python**: A programming language widely used for data science because of its readability and powerful libraries.
- **Pandas / NumPy**: Python libraries (toolkits) built specifically for working with data — you'll meet them properly in Lesson 2.

## Concept Explanation
Data science is often described with a simple pipeline: **collect → clean → analyze → communicate**. Data is collected from somewhere — a scoreboard, a sensor, a form, a website. That raw data is almost always messy: missing values, inconsistent formats, duplicate entries. Before anyone can learn anything from it, it has to be cleaned. Only after cleaning can real analysis happen: sorting, filtering, grouping, calculating averages, finding correlations. Finally, and just as important as the math, the findings have to be communicated — usually through a clear chart, table, or written summary — because an insight that nobody understands is not useful to anyone.

It helps to think about the difference between **data**, **information**, and **insight**. The number `82` by itself is just data — it means nothing without context. Once you learn it's "82 wins in an NBA season," that's information. Once you compare it across ten years and notice a team's win count has been rising every season since a coaching change, that's insight — a story the data tells that a person can act on.

Data science is not just for scientists and big companies. It's a way of thinking critically about numbers you see every day — sports box scores, school grade reports, population statistics in the news. Learning Python and pandas gives you the tools to test your own hunches against real evidence instead of guessing.

## Step-by-Step Instruction
1. As a class, look at a small table of real data together (below) — five NBA teams' win totals across three imaginary-but-realistic seasons.
2. Instructor reads the numbers aloud; students are asked "what's one thing you notice?"
3. Instructor introduces the pipeline: collect → clean → analyze → communicate, mapping each step to what just happened in the room.
4. Instructor opens Python (or a simple text-based table) and shows the same data loaded into a table structure, previewing what pandas will do starting next lesson.
5. Class discusses two more real-world data science examples from the list above, in small groups, then reports back.

## Code Example
Even though we haven't installed pandas yet (that's Lesson 2), here is a first look at how Python can already work with a tiny dataset using plain Python lists and dictionaries — showing what "data" looks like before we bring in more powerful tools.

```python
# A tiny, real-style dataset: NBA team win totals over three seasons
team_wins = {
    "Boston Celtics": [51, 57, 64],
    "Golden State Warriors": [67, 73, 58],
    "Miami Heat": [54, 44, 53],
    "Los Angeles Lakers": [50, 33, 43],
    "San Antonio Spurs": [55, 61, 48],
}

seasons = ["2014-15", "2015-16", "2016-17"]

print("Team win totals across three NBA seasons:\n")
for team, wins in team_wins.items():
    total = sum(wins)
    average = total / len(wins)
    print(f"{team:25s} wins: {wins}  average: {average:.1f}")

# Find the team with the highest average wins
best_team = max(team_wins, key=lambda t: sum(team_wins[t]) / len(team_wins[t]))
print(f"\nHighest average win total: {best_team}")
```

## Code Explanation
- We store the data in a Python **dictionary**, where each team name is a key and its list of win totals is the value — this mirrors how a spreadsheet column works.
- The `for` loop walks through every team, calculates the `total` and `average` wins using the built-in `sum()` and `len()` functions, and prints a neatly formatted line using an f-string (`f"..."`).
- `max(team_wins, key=lambda t: ...)` finds the dictionary key (team name) that produces the highest average — this is a first taste of how Python can automatically find patterns instead of us checking each team by hand.
- This is intentionally still "manual" Python — starting in Lesson 2 you'll see how pandas replaces this whole block with a few lines of much more powerful code.

## Expected Output
```
Team win totals across three NBA seasons:

Boston Celtics            wins: [51, 57, 64]  average: 57.3
Golden State Warriors     wins: [67, 73, 58]  average: 66.0
Miami Heat                wins: [54, 44, 53]  average: 50.3
Los Angeles Lakers        wins: [50, 33, 43]  average: 42.0
San Antonio Spurs         wins: [55, 61, 48]  average: 54.7

Highest average win total: Golden State Warriors
```

## Guided Practice
With the instructor, modify the code above to:
1. Add a sixth team of the students' choosing with three made-up-but-plausible win totals.
2. Print which team had the *lowest* average wins instead of the highest (hint: change `max` to `min`).
3. Print the difference in average wins between the best and worst team.

## Hands-On Activity
In pairs, students create their own dictionary of data about something they care about — e.g., their favorite video game characters' win rates, or their class's quiz scores over three weeks. They write a loop that prints each entry's total and average, then identify the highest and lowest, following the pattern from the guided practice.

## Student Challenge
Extend your Hands-On Activity dictionary to at least 6 entries, and write code that prints a one-sentence "insight" in plain English based on what the numbers show (e.g., `"Player X has the most consistent scores, staying between 80 and 85 every week."`). This connects the technical output back to a communicated insight, closing the data science loop.

## Common Mistakes
- **Confusing data with insight**: Printing raw numbers is not the same as explaining what they mean — always add a sentence of interpretation.
- **Off-by-one errors in averages**: Forgetting that `average = total / len(wins)` needs `len(wins)` (the count of seasons), not a hardcoded number.
- **KeyError typos**: Misspelling a dictionary key when trying to look up a team name.
- **Not testing edge cases**: Assuming every dictionary value has the same number of entries when writing more complex loops later.

## Debugging Guidance
- If you see `KeyError`, print `team_wins.keys()` to check exactly how each key is spelled.
- If an average looks wrong, add a `print(total, len(wins))` right before the calculation to see the two numbers being divided.
- If `max()` throws an error, double check the `lambda` syntax — a missing colon or parenthesis is the most common cause.
- Read error messages from the bottom up — the last line usually names the actual problem (e.g., `KeyError: 'Bostn Celtics'`).

## Mini Quiz
1. What are the four steps in the data science pipeline described in this lesson?
2. What is the difference between "data" and "insight"?
3. Name two real-world industries that use data science, other than sports.
4. In the code example, what does the `average` variable represent?
5. Why is "communicating" findings considered as important as "analyzing" them?

### Answer Key
1. Collect, clean, analyze, communicate.
2. Data is raw, uninterpreted facts (like a single number); insight is a meaningful conclusion drawn by analyzing and interpreting that data in context.
3. Any two of: streaming/social media recommendations, public health, climate science, city planning, video game design (accept other reasonable real examples).
4. It represents the mean number of wins for a team across the three seasons (total wins divided by number of seasons).
5. Because an insight that isn't clearly communicated can't be understood or acted on by anyone else — the value of data analysis is lost if it stays hidden in code or numbers no one can interpret.

## Lesson Recap
Today you learned that data science is the process of turning raw numbers into decisions through collecting, cleaning, analyzing, and communicating data. You saw real examples from sports, streaming, health, climate, and city planning, and you got a first hands-on look at organizing and summarizing data in Python using dictionaries and loops — a preview of the much more powerful pandas tools coming in Lesson 2.

## Homework / Extension
Find one real number in the news, a game, or an app this week (e.g., a sports score, a step count, a game high score) and write three sentences: (1) what the raw data is, (2) what additional context turns it into information, (3) what insight or decision it could support. Bring your example to the next class.

## Portfolio Project Connection
This lesson sets up the mindset behind **all three** portfolio projects in this course: Sports Performance Analysis (Project 1), School Grade Analysis (Project 2), and City Population Trends (Project 3). Every one of those projects follows the same collect → clean → analyze → communicate pipeline introduced here, using real data just like the NBA win-totals example above.
