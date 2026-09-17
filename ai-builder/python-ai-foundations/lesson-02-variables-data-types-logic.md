# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 02 of 08
**Title:** Variables, Data Types, and Logic
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3.11+

---

## Mission Brief

A spacecraft's onboard computer constantly tracks values: fuel level, hull temperature, distance to target, whether the airlock is sealed. Those values change during the mission, and the computer must make decisions based on them ("IF fuel is below 10%, THEN trigger alert"). Today, Cadet, you'll learn how to store changing values using **variables**, understand the different **data types** those values can take, and write **logic** that lets your programs make decisions — the exact same building blocks every AI system uses to process information and respond to it.

## Learning Objectives

1. Create and use variables to store different kinds of data.
2. Identify Python's core data types: strings, integers, floats, and booleans.
3. Perform arithmetic and string operations using variables.
4. Write conditional statements using `if`, `elif`, and `else`.
5. Use comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) and logical operators (`and`, `or`, `not`).
6. Combine variables and conditionals to build a simple decision-making program.

## Prerequisites

- Completion of Lesson 01 (Python installed, comfortable running `.py` files from the terminal).
- Ability to write and run a basic `print()` program.

## Concept Explanation

### Variables: labeled storage boxes

A **variable** is a named container that holds a value in your computer's memory. Think of it as a labeled storage locker aboard a spaceship — you can put something inside, check what's inside, and replace it with something new at any time. In Python, you create a variable simply by naming it and assigning it a value with the `=` operator:

```python
fuel_level = 87
```

Here, `fuel_level` is the variable name, and `87` is the value stored inside it. Unlike some languages, Python does not require you to declare what *type* of data a variable will hold — it figures that out automatically based on the value you assign. This is called **dynamic typing**.

Variable naming rules: names can contain letters, numbers, and underscores, but cannot start with a number, and cannot contain spaces or be a reserved Python keyword (like `print` or `if`). Good variable names describe what they hold: `fuel_level` is far better than `x` or `f`.

### Data types

Every value in Python has a **type**, which determines what operations can be performed on it. The four fundamental types you need now are:

- **String (`str`)** — text, always wrapped in quotes: `"Aurora-7"`, `'Cadet'`.
- **Integer (`int`)** — whole numbers, positive or negative: `87`, `-12`, `0`.
- **Float (`float`)** — numbers with decimal points: `98.6`, `3.14`, `-0.5`.
- **Boolean (`bool`)** — exactly one of two values: `True` or `False`. Booleans are the backbone of all decision-making in code.

You can check any variable's type using the built-in `type()` function: `print(type(fuel_level))` would output `<class 'int'>`.

### Why data types matter for AI

Machine learning models are essentially very sophisticated mathematical functions, and math requires numbers. When we later build the Number Prediction Model, every "feature" (an input like hours studied) and every "label" (an output like test score) must be a number — usually a float or integer, never raw text. Understanding data types now means you'll immediately recognize, in Lesson 5 onward, why we must convert text categories into numbers before a model can use them.

### Operators: doing things with values

Arithmetic operators work on numbers: `+` (add), `-` (subtract), `*` (multiply), `/` (divide, always returns a float), `//` (floor/integer division), `%` (modulo, gives the remainder), `**` (exponent/power).

```python
distance_km = 384400
speed_kmh = 3600
travel_time_hours = distance_km / speed_kmh
```

Strings also support some operators: `+` concatenates (joins) strings together, and `*` repeats a string a number of times.

### Conditional logic: making decisions

Programs become powerful when they can make decisions. Python's `if` statement checks whether a condition is `True`, and runs a block of indented code only if it is:

```python
if fuel_level < 10:
    print("WARNING: Low fuel")
```

Comparison operators produce booleans: `==` (equal to — note the double equals, since a single `=` is assignment), `!=` (not equal to), `<`, `>`, `<=`, `>=`. You can chain decisions using `elif` (short for "else if") and `else`:

```python
if fuel_level < 10:
    print("CRITICAL: Refuel immediately")
elif fuel_level < 50:
    print("CAUTION: Fuel below half")
else:
    print("Fuel levels nominal")
```

Python evaluates conditions from top to bottom and stops at the first one that is `True`. Indentation (typically 4 spaces) is not just style in Python — it is required syntax that defines which lines belong to which block. This is different from many other languages, which use curly braces `{}`.

### Logical operators: combining conditions

Sometimes a decision depends on more than one condition. Logical operators let you combine booleans:

- `and` — True only if both sides are True.
- `or` — True if at least one side is True.
- `not` — flips a boolean's value.

```python
oxygen_ok = True
fuel_ok = False

if oxygen_ok and fuel_ok:
    print("Cleared for launch")
else:
    print("Launch aborted - check systems")
```

## Key Vocabulary

| Term | Definition |
|---|---|
| **Variable** | A named container that stores a value which can change. |
| **Data type** | The category of value a variable holds (string, integer, float, boolean). |
| **String (str)** | Text data, wrapped in quotes. |
| **Integer (int)** | A whole number. |
| **Float** | A number with a decimal point. |
| **Boolean (bool)** | A value that is either `True` or `False`. |
| **Operator** | A symbol that performs an operation, e.g. `+`, `==`, `and`. |
| **Conditional statement** | Code that runs different blocks depending on whether a condition is true (`if`/`elif`/`else`). |
| **Indentation** | Consistent spacing that defines code blocks in Python. |

## Code Example: Launch Readiness Checker

```python
# launch_readiness.py
# Lumexa Mission Control - Launch Readiness Checker
# Demonstrates variables, data types, and conditional logic.

# --- Variables of different data types ---
ship_name = "Aurora-7"          # string
crew_count = 4                  # integer
fuel_level = 92.5                # float
oxygen_ok = True                 # boolean
hull_temperature = -15           # integer (can be negative)

# --- Displaying variable info ---
print("Ship:", ship_name)
print("Crew count:", crew_count)
print("Fuel level:", fuel_level, "%")
print("Data type of fuel_level:", type(fuel_level))

# --- Arithmetic with variables ---
fuel_used_per_hour = 4.5
estimated_flight_hours = fuel_level / fuel_used_per_hour
print("Estimated flight time:", round(estimated_flight_hours, 2), "hours")

# --- Conditional logic: fuel status ---
if fuel_level < 10:
    fuel_status = "CRITICAL"
elif fuel_level < 50:
    fuel_status = "CAUTION"
else:
    fuel_status = "NOMINAL"

print("Fuel status:", fuel_status)

# --- Combining conditions with logical operators ---
hull_ok = hull_temperature > -40 and hull_temperature < 120
launch_clear = oxygen_ok and hull_ok and fuel_status != "CRITICAL"

if launch_clear:
    print("ALL SYSTEMS GO. Cleared for launch.")
else:
    print("LAUNCH ABORTED. Review system status above.")
```

Expected output (values will vary slightly based on rounding):

```
Ship: Aurora-7
Crew count: 4
Fuel level: 92.5 %
Data type of fuel_level: <class 'float'>
Estimated flight time: 20.56 hours
Fuel status: NOMINAL
ALL SYSTEMS GO. Cleared for launch.
```

## Instructor-Guided Coding Walkthrough

1. Live-code the variable section first. Ask students to predict the `type()` output before running it.
2. Deliberately change `fuel_level` to `5` and re-run, showing the conditional branch changes to `CRITICAL`, then to how that flows into `launch_clear` becoming `False`.
3. Ask: "What happens if I use a single `=` instead of `==` inside the `if`?" Try it live — Python will actually raise a `SyntaxError` in an `if` condition, reinforcing that assignment and comparison are different operations.
4. Change `hull_temperature` to `150` and observe how `hull_ok` becomes `False`, which cascades into `launch_clear` becoming `False` even though fuel and oxygen are fine. This demonstrates how `and` requires every condition to hold.
5. Have students add a `print(type(launch_clear))` to confirm it is a `bool`.

## Student Mission / Guided Challenge

**Explorer Challenge: Airlock Safety Panel**

Create `airlock_safety.py` that:

1. Defines variables for `outside_pressure` (float), `inside_pressure` (float), `door_sealed` (boolean), and `crew_ready` (boolean).
2. Calculates `pressure_difference` as the absolute difference between the two pressures (hint: use `abs(outside_pressure - inside_pressure)`).
3. Uses an `if`/`elif`/`else` chain to set an `airlock_status` variable to `"SAFE TO OPEN"` only if `pressure_difference` is less than `0.5` AND `door_sealed` is `True` AND `crew_ready` is `True`; otherwise `"DO NOT OPEN"`.
4. Prints a clear final message using the `airlock_status` variable.
5. Test your program twice: once with values that pass, and once with values that fail, showing both outputs.

**Bonus objective:** Add a fourth condition checking `crew_count > 0` before allowing the airlock to open.

## Common Mistakes

- Using a single `=` when a double `==` comparison is intended.
- Forgetting the colon `:` at the end of an `if`, `elif`, or `else` line.
- Inconsistent indentation (mixing tabs and spaces, or using different numbers of spaces) causing an `IndentationError`.
- Comparing a string to a number, e.g. `"5" == 5`, which is always `False` because the types differ.
- Chaining `elif` conditions in the wrong order so an earlier, broader condition "catches" cases meant for a later branch.
- Forgetting that `and` requires *all* conditions to be true, mistakenly expecting it to behave like `or`.

## Check Your Understanding

1. What is the difference between `=` and `==` in Python?
2. Name the four basic data types covered today and give an example value of each.
3. Why does Python require consistent indentation inside `if` blocks?
4. What is the difference between `and` and `or`?
5. If `fuel_level = 45`, which branch of the `launch_readiness.py` example runs, and why?
6. Why does AI/machine learning code depend heavily on numeric data types like `int` and `float`?

## Mini Quiz

1. What data type is the value `True`?
   a) String
   b) Integer
   c) Boolean
   d) Float

2. Which operator checks if two values are equal?
   a) `=`
   b) `==`
   c) `!=`
   d) `<>`

3. What will `10 / 3` return in Python?
   a) `3`
   b) `3.3333333333333335`
   c) An error
   d) `"3.33"`

4. In `if a and b:`, when does the block run?
   a) When either a or b is True
   b) Only when both a and b are True
   c) Only when both are False
   d) Never

5. True or False: Python allows variable names to start with a number.

### Answer Key

1. `=` assigns a value to a variable; `==` compares two values for equality.
2. String (`"Aurora-7"`), Integer (`87`), Float (`98.6`), Boolean (`True`).
3. Because indentation defines which lines belong to the `if` block — Python uses it as syntax, not just style.
4. `and` requires both conditions to be True; `or` requires at least one to be True.
5. The `elif fuel_level < 50` branch runs, setting `fuel_status` to `"CAUTION"`, because 45 is not less than 10 but is less than 50.
6. Because machine learning models perform mathematical calculations on inputs, and math operations require numeric types, not text.
7. c) `3.3333333333333335`
8. b) Only when both a and b are True
9. False — variable names cannot start with a number.

## Lesson Recap

Today you gave your programs memory and decision-making ability. Variables let you store changing information, data types define what kind of information that is, and conditional logic lets your code branch based on real-time conditions — exactly how spacecraft systems, and machine learning models, respond to incoming data. You practiced arithmetic and comparison operators and combined multiple conditions with `and`/`or` to build increasingly realistic decision systems.

## Homework / Extension Mission

**Solo Mission: Rover Terrain Analyzer**

Build `terrain_analyzer.py` that:

1. Stores variables for `slope_degrees` (float), `rock_density` (float, rocks per square meter), and `battery_level` (integer, percent).
2. Uses conditional logic to classify terrain as `"SAFE TO TRAVERSE"`, `"PROCEED WITH CAUTION"`, or `"HALT - REROUTE"` based on at least three combined conditions of your choosing (e.g., steep slope + high rock density = halt).
3. Prints a full status report using all variables and the final classification.
4. Includes at least four comments explaining your logic choices.

## Portfolio Connection

The conditional logic you practiced today — checking numeric thresholds and combining conditions — is the exact skill you'll use in Lesson 7 when evaluating whether a trained model's accuracy is "good enough" (e.g., `if accuracy > 0.9: print("Model ready for deployment")`). Variables and data types are the raw materials of every dataset you'll load in the **Number Prediction Model** and **Data Pattern Finder** projects: every column of data you'll work with is fundamentally a collection of typed variables, just like `fuel_level` and `ship_name` today.
