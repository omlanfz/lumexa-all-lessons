# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 03 of 08
**Title:** Loops, Functions, and Modules
**Duration:** 60 minutes
**Difficulty:** Beginner-Intermediate
**Technology:** Python 3.11+

---

## Mission Brief

A mission control system doesn't check fuel level once and stop — it monitors continuously, running the same checks over and over. And it doesn't reinvent its diagnostic procedures every time — it reuses tested, named routines. Today, Cadet, you master **loops** (repeating actions) and **functions** (reusable named procedures), and you learn to bring in pre-built toolkits called **modules**. These three concepts are what turn short scripts into real software — and they are the exact mechanisms that power the training loops and reusable pipelines inside every AI system you'll build later in this course.

## Learning Objectives

1. Use `for` loops to repeat actions over a range of numbers or a collection.
2. Use `while` loops to repeat actions until a condition changes.
3. Define and call custom functions using `def`, including parameters and `return` values.
4. Understand variable scope (local vs. global).
5. Import and use built-in Python modules (e.g., `math`, `random`).
6. Combine loops and functions to build a repeatable, reusable mini-program.

## Prerequisites

- Completion of Lessons 01–02 (comfortable with variables, data types, and `if` statements).

## Concept Explanation

### Loops: doing things repeatedly

Loops let you repeat a block of code without rewriting it. Python has two main loop types.

**The `for` loop** repeats a fixed number of times, or once for each item in a collection. The built-in `range()` function generates a sequence of numbers:

```python
for i in range(5):
    print("Systems check", i)
```

This runs 5 times, with `i` taking values `0, 1, 2, 3, 4` (range starts at 0 by default and stops *before* the number given).

**The `while` loop** repeats as long as a condition remains `True`, checked before every pass:

```python
countdown = 5
while countdown > 0:
    print(countdown)
    countdown = countdown - 1
print("Liftoff!")
```

It is critical that something inside a `while` loop eventually makes the condition `False` — otherwise you create an **infinite loop** that never stops. This is one of the most common beginner bugs, and one you should show students deliberately (with an easy way to stop it, like Ctrl+C in the terminal).

Loops can be controlled with `break` (exit the loop immediately) and `continue` (skip to the next iteration).

### Functions: reusable named procedures

A **function** is a named, reusable block of code. You've already used one: `print()`. Now you'll define your own using the `def` keyword:

```python
def greet_crew(name):
    print("Welcome aboard, " + name + "!")

greet_crew("Nova")
greet_crew("Orion")
```

The value passed inside the parentheses (`name`) is called a **parameter** — a placeholder for information the function needs. When you call the function, you supply an **argument** (`"Nova"`) that fills that placeholder.

Functions can also **return** a value using the `return` keyword, which sends a result back to whatever called the function, so it can be stored or used further:

```python
def calculate_fuel_needed(distance_km, efficiency_km_per_liter):
    return distance_km / efficiency_km_per_liter

fuel_needed = calculate_fuel_needed(9000, 12)
print("Fuel needed:", fuel_needed, "liters")
```

Functions are essential in AI programming because every machine learning workflow is broken into small, reusable functions: one function loads data, another trains a model, another evaluates it, another makes a prediction. You'll write functions exactly like this starting in Lesson 6.

### Variable scope

A variable created inside a function is **local** — it only exists within that function and disappears once the function finishes running. A variable created outside any function, at the top level of your script, is **global** and can be read from anywhere (though modifying a global from inside a function requires the `global` keyword, which we generally avoid in favor of returning values instead — a cleaner practice).

```python
def compute_score(hours_studied):
    bonus = 5          # local variable - only exists inside this function
    return hours_studied * 10 + bonus

print(compute_score(3))
# print(bonus)  # This would cause an error - bonus does not exist out here
```

### Modules: importing pre-built toolkits

A **module** is a file of pre-written Python code you can import and reuse instead of writing from scratch. Python ships with a large **standard library** of built-in modules. Two you'll use often:

- `math` — mathematical functions and constants (`math.sqrt()`, `math.pi`).
- `random` — generating randomness (`random.randint()`, `random.choice()`).

```python
import math
import random

print(math.sqrt(144))          # 12.0
print(math.pi)                  # 3.141592653589793
print(random.randint(1, 100))   # a random whole number between 1 and 100
```

Later, you'll import specialized third-party modules like `numpy`, `sklearn`, and `tensorflow` the exact same way — the `import` keyword is your gateway to the entire world of professional AI tools.

## Key Vocabulary

| Term | Definition |
|---|---|
| **Loop** | A structure that repeats a block of code multiple times. |
| **`for` loop** | Repeats a fixed number of times or once per item in a collection. |
| **`while` loop** | Repeats as long as a condition remains true. |
| **Infinite loop** | A loop whose condition never becomes false, causing it to run forever. |
| **Function** | A named, reusable block of code, defined with `def`. |
| **Parameter** | A named placeholder for input a function expects. |
| **Argument** | The actual value passed into a function when it is called. |
| **Return value** | The result a function sends back using `return`. |
| **Scope** | The region of code where a variable can be accessed (local or global). |
| **Module** | A file of pre-written, reusable Python code, brought in with `import`. |

## Code Example: Mission Diagnostic Toolkit

```python
# diagnostic_toolkit.py
# Lumexa Mission Control - Diagnostic Toolkit
# Demonstrates loops, functions, and modules working together.

import random

def run_system_check(system_name, min_value=0, max_value=100):
    """Simulates checking one ship system and returns a reading (0-100)."""
    reading = random.randint(min_value, max_value)
    return reading

def classify_reading(reading):
    """Turns a numeric reading into a human-readable status."""
    if reading < 20:
        return "CRITICAL"
    elif reading < 60:
        return "CAUTION"
    else:
        return "NOMINAL"

def run_full_diagnostics(systems):
    """Loops through a list of systems, checks each, and reports status."""
    critical_count = 0
    for system in systems:
        reading = run_system_check(system)
        status = classify_reading(reading)
        print(f"{system:15} | reading: {reading:3} | status: {status}")
        if status == "CRITICAL":
            critical_count += 1
    return critical_count


# --- Main program ---
ship_systems = ["Oxygen", "Fuel Cell", "Navigation", "Hull Integrity", "Reactor"]

print("Running full diagnostics on", len(ship_systems), "systems...")
print("-" * 55)

number_of_critical = run_full_diagnostics(ship_systems)

print("-" * 55)
if number_of_critical == 0:
    print("All systems nominal. Mission cleared to proceed.")
else:
    print(f"WARNING: {number_of_critical} system(s) reporting critical status.")

# A while loop example: retry a connection until it succeeds (simulated)
attempts = 0
connected = False
while not connected and attempts < 5:
    attempts += 1
    signal_strength = random.randint(0, 100)
    print(f"Connection attempt {attempts}: signal strength {signal_strength}")
    if signal_strength > 50:
        connected = True

if connected:
    print(f"Connected to mission control after {attempts} attempt(s).")
else:
    print("Failed to connect after 5 attempts. Switching to backup relay.")
```

This program uses random values, so output will differ each run, but the structure (five system readings, then a connection retry loop) is always the same.

## Instructor-Guided Coding Walkthrough

1. Build the `for` loop example first with a plain `range(5)`, showing what `i` looks like at each pass by printing it.
2. Introduce the `while` countdown loop, and then deliberately remove the `countdown = countdown - 1` line to demonstrate an infinite loop — be ready to stop it with Ctrl+C, and discuss why this matters (a training loop for a real AI model that never terminates would run forever and never finish).
3. Build `classify_reading()` as a standalone function first, calling it manually with a few hardcoded numbers (`classify_reading(15)`, `classify_reading(75)`) so students see return values clearly before adding complexity.
4. Introduce `run_system_check()` next, explaining default parameter values (`min_value=0, max_value=100`).
5. Combine them inside `run_full_diagnostics()`, walking through the `for system in systems:` loop and explaining that `system` takes on each string in the list, one at a time.
6. Point out the f-string formatting (`f"{system:15}"`) briefly — explain it pads text to align columns, without going deep (this is covered more in Lesson 4).
7. Run the full script two or three times to show the readings and outcomes change due to `random`.

## Student Mission / Guided Challenge

**Explorer Challenge: Rover Patrol Loop**

Create `rover_patrol.py` that:

1. Defines a function `scan_sector(sector_number)` that uses `random.randint()` to simulate a "resource score" between 0 and 100 for that sector and returns it.
2. Defines a function `classify_sector(score)` that returns `"RICH DEPOSIT"` if score > 70, `"MODERATE"` if score > 30, or `"BARREN"` otherwise.
3. Uses a `for` loop to scan sectors numbered 1 through 10, calling both functions for each sector and printing a formatted report line.
4. Counts how many sectors were classified `"RICH DEPOSIT"` using a variable that accumulates across the loop, and prints the total at the end.
5. Imports the `math` module and uses `math.sqrt()` somewhere meaningful (e.g., converting the total rich-deposit count into a "search radius" value).

**Bonus objective:** Add a `while` loop after the `for` loop that keeps scanning random additional sectors until three consecutive `"BARREN"` results occur, then stops.

## Common Mistakes

- Forgetting to update the loop-control variable in a `while` loop, causing an infinite loop.
- Off-by-one errors with `range()` — forgetting that `range(5)` produces `0` through `4`, not `1` through `5`.
- Forgetting the `return` statement in a function, meaning it always returns `None` even though it appears to "do something."
- Trying to use a local variable outside the function where it was defined.
- Forgetting parentheses when calling a function, e.g. writing `greet_crew` instead of `greet_crew("Nova")`.
- Shadowing a built-in name by naming a variable `list`, `sum`, or `type`, which can cause confusing errors later.

## Check Your Understanding

1. What is the key difference between a `for` loop and a `while` loop?
2. Why is it dangerous to write a `while` loop without ensuring its condition eventually becomes false?
3. What is the difference between a parameter and an argument?
4. What does the `return` keyword do, and how is it different from `print()`?
5. What is variable scope, and why can't you access a function's local variables from outside it?
6. What does the `import` keyword let you do, and name one built-in module.

## Mini Quiz

1. What values does `range(3)` produce?
   a) `1, 2, 3`
   b) `0, 1, 2`
   c) `0, 1, 2, 3`
   d) `1, 2`

2. Which keyword defines a function in Python?
   a) `function`
   b) `func`
   c) `def`
   d) `method`

3. What happens if a `while` loop's condition never becomes `False`?
   a) Python automatically stops it after 100 iterations
   b) It becomes an infinite loop
   c) It raises a `SyntaxError`
   d) Nothing, it runs once and stops

4. Which statement correctly imports the math module?
   a) `include math`
   b) `import math`
   c) `using math`
   d) `require math`

5. True or False: A variable defined inside a function can be used anywhere in the program.

### Answer Key

1. A `for` loop repeats a set number of times (or once per item in a collection); a `while` loop repeats as long as a condition stays true, with no fixed count.
2. It creates an infinite loop, causing the program to run forever and never proceed to later code.
3. A parameter is the placeholder name defined in the function; an argument is the actual value supplied when the function is called.
4. `return` sends a value back to the caller so it can be stored or reused; `print()` only displays text and returns nothing useful.
5. Scope determines where a variable can be accessed; local variables only exist inside the function that created them and are destroyed once it finishes.
6. `import` brings in pre-written code from a module so you can reuse it; example: `math` or `random`.
7. b) `0, 1, 2`
8. c) `def`
9. b) It becomes an infinite loop
10. b) `import math`
11. False — local variables are confined to the function that created them.

## Lesson Recap

Today you learned to make your programs repeat work efficiently with `for` and `while` loops, and to organize code into clean, reusable **functions** with parameters and return values. You also learned about variable scope and how to bring in extra power using **modules** like `math` and `random`. These three tools — loops, functions, modules — are the backbone of every real Python program, including every machine learning script you'll write starting in Lesson 6, where training a model is literally a loop that repeatedly calls functions to improve predictions.

## Homework / Extension Mission

**Solo Mission: Fleet Status Report Generator**

Build `fleet_report.py` that:

1. Defines a function `check_ship(ship_name)` that uses `random.randint()` to generate a fuel percentage and a crew health percentage, returning both values (hint: a function can `return value1, value2`).
2. Defines a function `overall_status(fuel, health)` returning `"READY"`, `"NEEDS ATTENTION"`, or `"GROUNDED"` based on combined thresholds you decide.
3. Uses a `for` loop over a list of at least 5 ship names to check each one and print a formatted report row.
4. Tracks and prints, after the loop, how many ships are `"READY"` versus `"GROUNDED"`.
5. Uses the `math` module at least once (e.g., computing an average using `math.floor()`).

## Portfolio Connection

Loops and functions are the literal mechanics behind model training: in Lesson 6 and 7, a machine learning model "learns" by looping through training data repeatedly, and scikit-learn's `.fit()` function is, under the hood, exactly the kind of function you built today — it takes parameters (your data) and performs repeated internal work to produce a result. In the **Number Prediction Model** project, you will write functions like `load_data()`, `train_model()`, and `evaluate_model()` that mirror the `run_system_check()` and `classify_reading()` structure from today's lesson exactly.
