# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 04 of 08
**Title:** Working with Data and Lists
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.11+

---

## Mission Brief

A single sensor reading is barely useful — but a full log of readings collected over an entire mission tells a story. Real AI systems never work on a single number; they work on **datasets**: organized collections of many data points. Today, Cadet, you learn to organize and manipulate data using Python's **lists**, **tuples**, and **dictionaries**, and you'll practice reading data from files. This is the direct bridge into next lesson's introduction to machine learning — every dataset you feed a model is, underneath the surface, built from exactly these structures.

## Learning Objectives

1. Create, access, and modify Python lists.
2. Use list indexing and slicing to retrieve specific data.
3. Use list methods (`append`, `remove`, `sort`, `len`) to manage collections of data.
4. Use dictionaries to store labeled (key-value) data.
5. Iterate over lists and dictionaries with `for` loops.
6. Read simple structured data from a `.csv` file using Python.

## Prerequisites

- Completion of Lessons 01–03 (variables, conditionals, loops, functions).

## Concept Explanation

### Lists: ordered collections

A **list** is an ordered collection of values, written with square brackets, that can hold any data type (even a mix):

```python
fuel_readings = [92.5, 88.0, 85.5, 79.0, 74.5]
crew_names = ["Nova", "Orion", "Vega", "Lyra"]
```

Each item in a list has a position called an **index**, starting at `0`. You access items using square-bracket notation:

```python
print(crew_names[0])   # "Nova" - the first item
print(crew_names[-1])  # "Lyra" - the last item, using negative indexing
```

**Slicing** lets you grab a sub-section of a list using `list[start:stop]` (stop is exclusive):

```python
print(fuel_readings[1:3])   # [88.0, 85.5]
print(fuel_readings[:2])    # [92.5, 88.0] - from the start
print(fuel_readings[2:])    # [85.5, 79.0, 74.5] - to the end
```

Lists are **mutable**, meaning you can change them after creation:

```python
crew_names.append("Sirius")      # adds to the end
crew_names.remove("Orion")        # removes a specific value
crew_names.sort()                 # sorts alphabetically in place
print(len(crew_names))            # the number of items in the list
```

### Iterating over lists

You've already seen this pattern in Lesson 3 — a `for` loop can walk through each item in a list directly:

```python
for reading in fuel_readings:
    print("Fuel reading:", reading)
```

When you also need the index, use `enumerate()`:

```python
for index, reading in enumerate(fuel_readings):
    print(f"Reading #{index}: {reading}")
```

### Tuples: lists that don't change

A **tuple** looks like a list but uses parentheses and cannot be modified after creation — useful for data that should stay fixed, like a coordinate pair: `location = (34.05, -118.24)`. You won't use tuples heavily yet, but you'll see them returned by library functions later (e.g., scikit-learn's `train_test_split` returns a tuple of four values).

### Dictionaries: labeled data

A **dictionary** stores data as **key-value pairs**, letting you label each piece of information instead of relying on position:

```python
ship_status = {
    "name": "Aurora-7",
    "fuel": 92.5,
    "crew": 4,
    "systems_nominal": True
}

print(ship_status["name"])       # "Aurora-7"
print(ship_status["fuel"])       # 92.5

ship_status["fuel"] = 88.0        # update a value
ship_status["destination"] = "Europa"  # add a new key
```

You can loop through a dictionary's keys, values, or both:

```python
for key, value in ship_status.items():
    print(key, "->", value)
```

Dictionaries are how real-world data is often structured — for example, a single row of a dataset representing one student might be `{"hours_studied": 3, "test_score": 78}`. This is precisely the "features and labels" structure you'll formalize in Lesson 5.

### Lists of dictionaries: real datasets

Combining lists and dictionaries lets you represent a full dataset — a list where each item is one record:

```python
students = [
    {"name": "Nova", "hours_studied": 2, "test_score": 65},
    {"name": "Orion", "hours_studied": 5, "test_score": 88},
    {"name": "Vega", "hours_studied": 8, "test_score": 95},
]

for student in students:
    print(student["name"], "scored", student["test_score"])
```

This exact shape — a list of records, each with named fields — is how data is structured before it ever reaches a machine learning library. In Lesson 6, you'll load data like this using a library called pandas, which is essentially a highly optimized way of managing structures like the one above.

### Reading data from a CSV file

CSV ("comma-separated values") is the most common plain-text format for storing tabular data. Python's built-in `csv` module can read it:

```python
import csv

with open("students.csv", "r") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["name"], "-", row["test_score"])
```

The `with open(...) as file:` pattern safely opens a file and automatically closes it when the block finishes — the standard, safe way to work with files in Python. `csv.DictReader` reads each row as a dictionary, using the first line of the file as the keys.

## Key Vocabulary

| Term | Definition |
|---|---|
| **List** | An ordered, mutable collection of values written with `[ ]`. |
| **Index** | The numeric position of an item in a list, starting at 0. |
| **Slicing** | Retrieving a sub-section of a list using `[start:stop]`. |
| **Mutable** | Able to be changed after creation (lists are; strings and tuples are not). |
| **Tuple** | An ordered, immutable collection written with `( )`. |
| **Dictionary** | A collection of key-value pairs written with `{ }`. |
| **Key / Value** | A dictionary's label (key) and the data it points to (value). |
| **Dataset** | An organized collection of data records, often a list of dictionaries or rows of a table. |
| **CSV** | Comma-Separated Values — a plain-text tabular file format. |

## Code Example: Mission Log Analyzer

```python
# mission_log_analyzer.py
# Lumexa Mission Control - Mission Log Analyzer
# Demonstrates lists, dictionaries, and iteration on a small dataset.

# A dataset represented as a list of dictionaries - one entry per day.
mission_log = [
    {"day": 1, "fuel_used_liters": 120.5, "distance_km": 850, "alerts": 0},
    {"day": 2, "fuel_used_liters": 118.0, "distance_km": 830, "alerts": 1},
    {"day": 3, "fuel_used_liters": 135.2, "distance_km": 910, "alerts": 0},
    {"day": 4, "fuel_used_liters": 142.8, "distance_km": 875, "alerts": 2},
    {"day": 5, "fuel_used_liters": 110.0, "distance_km": 800, "alerts": 0},
]

# --- Building lists from the dataset ---
fuel_values = [entry["fuel_used_liters"] for entry in mission_log]  # list comprehension
distance_values = [entry["distance_km"] for entry in mission_log]

print("All fuel readings:", fuel_values)
print("Total fuel used:", round(sum(fuel_values), 2), "liters")
print("Average distance/day:", round(sum(distance_values) / len(distance_values), 2), "km")
print("Highest single-day fuel use:", max(fuel_values), "liters")
print("Lowest single-day fuel use:", min(fuel_values), "liters")

# --- Iterating with enumerate for indexed output ---
print("\nDaily Breakdown:")
for index, entry in enumerate(mission_log):
    day_number = entry["day"]
    alert_note = "ALERT" if entry["alerts"] > 0 else "clear"
    print(f"Day {day_number}: {entry['fuel_used_liters']:.1f}L used, "
          f"{entry['distance_km']}km traveled, status: {alert_note}")

# --- Finding the day with the most alerts ---
worst_day = mission_log[0]
for entry in mission_log:
    if entry["alerts"] > worst_day["alerts"]:
        worst_day = entry

print(f"\nDay with most alerts: Day {worst_day['day']} ({worst_day['alerts']} alerts)")

# --- Modifying the dataset: adding a new day's log ---
mission_log.append({"day": 6, "fuel_used_liters": 105.0, "distance_km": 790, "alerts": 0})
print(f"\nMission log now contains {len(mission_log)} days of data.")
```

Expected output:

```
All fuel readings: [120.5, 118.0, 135.2, 142.8, 110.0]
Total fuel used: 626.5 liters
Average distance/day: 853.0 km
Highest single-day fuel use: 142.8 liters
Lowest single-day fuel use: 110.0 liters

Daily Breakdown:
Day 1: 120.5L used, 850km traveled, status: clear
Day 2: 118.0L used, 830km traveled, status: ALERT
Day 3: 135.2L used, 910km traveled, status: clear
Day 4: 142.8L used, 875km traveled, status: ALERT
Day 5: 110.0L used, 800km traveled, status: clear

Day with most alerts: Day 4 (2 alerts)

Mission log now contains 6 days of data.
```

## Instructor-Guided Coding Walkthrough

1. Start with plain lists (`fuel_values`) — show indexing and slicing live with several examples before introducing dictionaries.
2. Build one dictionary (`ship_status`) live, showing key access, update, and adding a new key.
3. Combine into a small list of dictionaries with only 2-3 entries first, so students can trace through the loop by hand before scaling up to the full `mission_log`.
4. Introduce the **list comprehension** line (`fuel_values = [entry["fuel_used_liters"] for entry in mission_log]`) as a shorthand for a `for` loop that builds a list — show the longer, equivalent `for` loop version side by side so students see it's not new magic, just a compact rewrite.
5. Walk through the "find the worst day" loop carefully — this pattern (looping while tracking a "best so far" variable) is one of the most reused patterns in programming and directly parallels how you'll later track the "best model" across multiple training attempts.
6. If time allows, create a small `students.csv` file together and demonstrate `csv.DictReader` reading it.

## Student Mission / Guided Challenge

**Explorer Challenge: Sensor Array Report**

Create `sensor_report.py` that:

1. Builds a list of at least 6 dictionaries, each representing one sensor reading with keys `"sensor_id"`, `"temperature_c"`, and `"battery_percent"`.
2. Uses a list comprehension to build a list of just the temperature values.
3. Calculates and prints the average, highest, and lowest temperature using `sum()`, `max()`, and `min()`.
4. Loops through the sensors and prints a status line for each: `"NORMAL"` if temperature is between 0 and 40, otherwise `"OUT OF RANGE"`.
5. Finds and prints the sensor with the lowest battery percentage using the "track the best/worst so far" loop pattern from the lesson.

**Bonus objective:** Write the sensor data to a `.csv` file using Python's `csv.DictWriter`, then read it back with `csv.DictReader` to confirm it round-trips correctly.

## Common Mistakes

- Off-by-one indexing errors (forgetting index `0` is the first item).
- Trying to access an index that doesn't exist, causing an `IndexError`.
- Using a dictionary key that doesn't exist, causing a `KeyError` — check with `"key" in my_dict` first if unsure.
- Confusing list slicing bounds — remembering that `list[1:3]` does **not** include index 3.
- Forgetting quotes around dictionary keys (`ship_status[name]` instead of `ship_status["name"]`).
- Modifying a list while looping over it directly, which can skip items unexpectedly — safer to build a new list instead.

## Check Your Understanding

1. What is the index of the first item in a Python list?
2. What is the difference between a list and a dictionary?
3. What does `my_list[1:4]` return, and which index is excluded?
4. What error occurs if you try to access a dictionary key that doesn't exist?
5. What is a list comprehension, and what longer structure does it replace?
6. Why is the "list of dictionaries" pattern important for representing real datasets?

## Mini Quiz

1. What does `crew_names[-1]` return?
   a) The first item
   b) An error
   c) The last item
   d) An empty list

2. Which method adds an item to the end of a list?
   a) `.add()`
   b) `.append()`
   c) `.insert()`
   d) `.push()`

3. What symbol encloses a dictionary in Python?
   a) `[ ]`
   b) `( )`
   c) `{ }`
   d) `< >`

4. Which of these correctly retrieves a dictionary value for key `"fuel"`?
   a) `ship_status.fuel`
   b) `ship_status["fuel"]`
   c) `ship_status(fuel)`
   d) `ship_status->fuel`

5. True or False: Lists in Python are immutable (cannot be changed after creation).

### Answer Key

1. Index `0`.
2. A list is an ordered collection accessed by numeric position; a dictionary is a collection of key-value pairs accessed by named keys.
3. It returns items at indexes 1, 2, and 3; index 4 is excluded (the stop value is exclusive).
4. A `KeyError`.
5. A list comprehension is a compact one-line way to build a list from a loop and optional condition, replacing a multi-line `for` loop that appends to an empty list.
6. Because real datasets are collections of records (rows), and each record naturally maps to a dictionary of named fields — exactly what a list of dictionaries represents.
7. c) The last item
8. b) `.append()`
9. c) `{ }`
10. b) `ship_status["fuel"]`
11. False — lists are mutable; you can change them after creation.

## Lesson Recap

Today you learned to organize real data using **lists** (ordered collections), **dictionaries** (labeled key-value data), and combinations of both to represent full datasets. You practiced indexing, slicing, iterating, and using built-in functions (`sum`, `max`, `min`, `len`) to summarize data — and you saw how a `.csv` file maps directly onto this same list-of-dictionaries structure. This is the exact shape of data every machine learning library expects, which makes today's lesson the true doorway into the AI-focused lessons ahead.

## Homework / Extension Mission

**Solo Mission: Crew Health Dashboard**

Build `crew_health_dashboard.py` that:

1. Defines a list of at least 6 dictionaries, each representing a crew member with `"name"`, `"heart_rate"`, `"oxygen_saturation"`, and `"hours_awake"`.
2. Uses list comprehensions to extract separate lists for each numeric field.
3. Prints summary statistics (average, max, min) for each field.
4. Loops through the crew and classifies each member as `"FIT FOR DUTY"` or `"NEEDS REST"` based on at least two combined conditions.
5. Identifies and prints the crew member who has been awake the longest.

## Portfolio Connection

The list-of-dictionaries pattern from today is the conceptual foundation for **pandas DataFrames**, the tool you'll use in Lesson 6 and throughout the **Number Prediction Model** and **Data Pattern Finder** projects to load and manage real datasets. When you write `df["hours_studied"]` in a future project to grab one column of data, you are doing exactly what `[entry["fuel_used_liters"] for entry in mission_log]` did today — just with a more powerful, specialized tool. The summary statistics you calculated by hand today (`sum()`, `max()`, `min()`, average) are the same statistics you'll compute automatically when exploring a dataset before training a model.
