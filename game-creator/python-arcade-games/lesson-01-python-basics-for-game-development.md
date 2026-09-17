# Lesson 01: Python Basics for Game Development

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 1 of 8
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3.10+

---

## Mission Brief

Cadet, welcome aboard the Lumexa Arcade Division! Before any starship can fly, its engineers must understand the raw materials it's built from — bolts, wiring, fuel lines. Before you can build a space shooter, a maze runner, or a brick-breaking cannon, you need the raw materials of every game: **variables, loops, conditionals, functions, and classes**. Today's mission is a systems check on your Python engineering fundamentals, refactored specifically for game logic. By the end of this hour, you'll have written the exact building blocks that will power your ship, your enemies, and your score counter in every project this course.

## Learning Objectives

By the end of this lesson, you will be able to:

1. Declare and update variables that represent game state (position, health, score).
2. Use `if`/`elif`/`else` conditionals to make decisions based on game state.
3. Use `for` and `while` loops to repeat actions — including the concept behind a "game loop."
4. Write functions with parameters and return values to organize game logic.
5. Define a simple Python class with an `__init__` method and instance methods, in preparation for `Player` and `Enemy` classes later in the course.

## What You'll Build

A terminal-based **"Ship Status Simulator"** — a text program that tracks a spaceship's position, fuel, and health across a simulated flight, using variables, loops, conditionals, functions, and a `Ship` class. This is not a graphical game yet (that begins in Lesson 2), but every concept here maps directly onto real Pygame code you'll write next week.

## Prerequisites

- Python 3.10+ installed on your machine.
- A code editor (VS Code recommended) with the Python extension.
- No prior Pygame knowledge required.
- Comfortable typing and running a `.py` file from a terminal.

## Key Concepts

- **Variables** — named containers for data that changes over time (`x`, `y`, `health`, `score`).
- **Data types** — `int`, `float`, `str`, `bool`, and `tuple`/`list` for grouping values like `(x, y)` coordinates.
- **Conditionals** — `if`/`elif`/`else` for branching logic ("if health <= 0, game over").
- **Loops** — `for` (fixed number of repetitions) and `while` (repeat until a condition changes) — the ancestor of the game loop.
- **Functions** — reusable blocks of code with parameters and `return` values.
- **Classes and objects** — blueprints (`class Ship:`) and instances (`my_ship = Ship()`) that bundle data and behavior together.

## Concept Explanation

Every game, no matter how flashy, is really just a program that keeps track of numbers and updates them over and over, many times per second. A player's x and y position are variables. Health is a variable. Score is a variable. The game "AI" of an enemy is just a function that changes an enemy's variables based on conditions.

Think of a spaceship's dashboard: fuel gauge, hull integrity, coordinates. Each of those is a **variable** in Python:

```python
fuel = 100
hull_integrity = 100
x_position = 0
y_position = 0
```

A **conditional** lets the ship's computer make decisions: "if fuel is less than 10, sound the alarm." A **loop** lets the simulation run for many "ticks" of time — in a real game, this loop runs 60 times per second and is called the **game loop**, which you'll build for real in Lesson 2. A **function** lets us package up a behavior, like `burn_fuel(amount)`, so we don't repeat code. And a **class** lets us bundle all of a ship's variables and behaviors into one clean object, `Ship`, instead of juggling a dozen separate variables — this is exactly the pattern we will use for the `Player` and `Enemy` classes starting in Lesson 3.

## Guided Coding

Create a file called `ship_status_simulator.py` and build it up step by step as described below. Here is the complete, final version:

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 01
Ship Status Simulator: Python fundamentals for game development.
"""

import random

# ---------------------------------------------------------
# STEP 1: Variables representing game state
# ---------------------------------------------------------
MAX_FUEL = 100
MAX_HULL = 100


# ---------------------------------------------------------
# STEP 2: A function that makes a decision (conditional)
# ---------------------------------------------------------
def check_status(fuel, hull_integrity):
    """Return a short status message based on current ship stats."""
    if hull_integrity <= 0:
        return "CRITICAL: Hull breached! Ship destroyed."
    elif hull_integrity < 30:
        return "WARNING: Hull integrity low!"
    elif fuel <= 0:
        return "CRITICAL: Out of fuel, drifting in space."
    elif fuel < 20:
        return "WARNING: Fuel running low."
    else:
        return "Status nominal. All systems green."


# ---------------------------------------------------------
# STEP 3: A class that bundles data + behavior together
# ---------------------------------------------------------
class Ship:
    """Represents a single spaceship and its changing state."""

    def __init__(self, name):
        self.name = name
        self.fuel = MAX_FUEL
        self.hull_integrity = MAX_HULL
        self.x = 0
        self.y = 0
        self.score = 0

    def move(self, dx, dy):
        """Move the ship and burn fuel proportional to distance traveled."""
        distance = abs(dx) + abs(dy)
        self.burn_fuel(distance)
        self.x += dx
        self.y += dy

    def burn_fuel(self, amount):
        """Reduce fuel, never going below zero."""
        self.fuel = max(0, self.fuel - amount)

    def take_damage(self, amount):
        """Reduce hull integrity, never going below zero."""
        self.hull_integrity = max(0, self.hull_integrity - amount)

    def add_score(self, points):
        self.score += points

    def is_alive(self):
        return self.hull_integrity > 0

    def __str__(self):
        return (f"[{self.name}] pos=({self.x},{self.y}) "
                f"fuel={self.fuel} hull={self.hull_integrity} score={self.score}")


# ---------------------------------------------------------
# STEP 4: A loop that simulates many "ticks" of gameplay
# ---------------------------------------------------------
def run_simulation(ticks=10):
    ship = Ship("Lumexa Voyager")
    print(f"Launching {ship.name}!\n")

    for tick in range(1, ticks + 1):
        if not ship.is_alive():
            print(f"Tick {tick}: Ship destroyed. Simulation ended early.")
            break

        # Simulate random movement and events each tick
        dx = random.choice([-2, -1, 0, 1, 2])
        dy = random.choice([-2, -1, 0, 1, 2])
        ship.move(dx, dy)

        # Random chance of an asteroid hit
        if random.random() < 0.2:
            damage = random.randint(5, 15)
            ship.take_damage(damage)
            print(f"Tick {tick}: Asteroid impact! -{damage} hull.")
        else:
            ship.add_score(10)

        print(f"Tick {tick}: {ship} -> {check_status(ship.fuel, ship.hull_integrity)}")

    print("\n--- Final Report ---")
    print(ship)
    print("Mission complete!" if ship.is_alive() else "Mission failed.")


if __name__ == "__main__":
    run_simulation(ticks=12)
```

## Code Walkthrough

- `import random` — the `random` module lets us simulate unpredictable events (asteroid hits), the same way randomness will drive enemy spawn timing and movement patterns in later lessons.
- `MAX_FUEL = 100` — constants written in ALL_CAPS by convention. In Pygame projects, this pattern becomes `settings.py`, a dedicated file of constants like `SCREEN_WIDTH` and `PLAYER_SPEED`.
- `check_status(fuel, hull_integrity)` — a **function with parameters**. It takes inputs and `return`s a string, rather than printing directly, which makes it reusable and testable.
- `class Ship:` — the **class definition**. `__init__(self, name)` is the **constructor**, run automatically when you write `Ship("Lumexa Voyager")`. `self` refers to "this particular ship" — every method needs it as the first parameter.
- `self.fuel`, `self.hull_integrity`, etc. — **instance attributes**, unique to each `Ship` object. This is precisely how `Player` and `Enemy` will store `self.rect`, `self.speed`, and `self.health` in Pygame.
- `def move(self, dx, dy):` — a **method**, a function that belongs to the class and can read/modify `self`'s attributes.
- `max(0, self.fuel - amount)` — a defensive pattern that prevents fuel from going negative; you'll use the identical pattern to clamp player health.
- `for tick in range(1, ticks + 1):` — a **for loop** simulating repeated "frames" of gameplay, foreshadowing the real game loop in Lesson 2, which uses `while True:` instead of a fixed range.
- `if not ship.is_alive(): break` — combining a conditional with `break` to exit a loop early, exactly how you'll exit the game loop when the player dies.
- `if __name__ == "__main__":` — ensures `run_simulation()` only runs when the file is executed directly, not when imported elsewhere — a pattern every `main.py` in this course will use.

## Build Instructions

1. Install Python 3.10 or later from python.org if you haven't already.
2. Open VS Code, install the official "Python" extension from Microsoft if prompted.
3. Create a new folder for this lesson and open it in VS Code.
4. Create `ship_status_simulator.py` and paste in the code above.
5. Open a terminal in VS Code (``Ctrl+` `` or ``Cmd+` ``) and run:
   ```
   python ship_status_simulator.py
   ```
6. You should see 12 lines of simulated flight followed by a final report. Run it several times — since it uses `random`, the output changes each time.

No external libraries are needed for this lesson — pure Python only. Pygame installation begins in Lesson 2.

## Student Mission

Modify `run_simulation` so that:
1. The simulation runs for 20 ticks instead of 12.
2. Every 5th tick, the ship automatically refuels by 15 (simulating passing a space station), clamped at `MAX_FUEL`.
3. Print a special message when the ship's score crosses 100 for the first time.

## Challenge

Add a second `Ship` object representing a rival racer. Run both ships through the same number of ticks (you can call `run_simulation`-style logic twice, or refactor it to accept a `Ship` object as a parameter instead of creating one internally). At the end, print which ship has the higher score.

## Experiment / Extension (Progressive)

1. **Change a value** — set `MAX_FUEL = 50` and rerun. Notice ships run out of fuel much faster.
2. **Observe** — change the asteroid probability from `0.2` to `0.5` and note how much more damage accumulates.
3. **Modify a mechanic** — add a `shield` attribute to `Ship` that absorbs the first 5 points of damage each hit before hull integrity is touched.
4. **Combine mechanics** — make `take_damage` reduce `fuel` slightly too (a hit damages engines), combining two systems in one method.
5. **Build independently** — design your own `PowerCore` class with its own attributes and a method that interacts with `Ship` (e.g., `power_core.recharge(ship)`).

## Common Mistakes

- **Forgetting `self`** in method definitions (`def move(dx, dy):` instead of `def move(self, dx, dy):`) — Python will raise a `TypeError` about missing arguments.
- **Confusing `=` and `==`** — `=` assigns a value, `==` compares two values. Using `=` inside an `if` statement is a syntax error in Python (which actually helps you catch this mistake early).
- **Modifying a variable without `self.`** inside a method, e.g. writing `fuel = fuel - 1` instead of `self.fuel = self.fuel - 1` — this creates a local variable that disappears instead of updating the object.
- **Off-by-one errors in `range()`** — `range(1, ticks + 1)` is deliberate so tick numbers start at 1 and include the final tick; `range(1, ticks)` would stop one short.

## Debugging Tips

- Add temporary `print()` statements inside functions to see intermediate values — this is the single most useful beginner debugging technique.
- Read Python error messages from the **bottom up** — the last line tells you the actual error type and message.
- If you get `AttributeError: 'Ship' object has no attribute 'x'`, check that `__init__` actually sets `self.x` before any method tries to use it.
- Use VS Code's built-in debugger (the "Run and Debug" panel) to set a breakpoint and step through `run_simulation` one line at a time.

## Check Your Understanding

- Can you explain, in your own words, the difference between a variable and an attribute (`self.something`)?
- Why does `check_status` return a string instead of printing it directly? What advantage does that give us?
- What would happen if `move()` didn't call `burn_fuel()`?

## Mini Quiz

1. What keyword defines a class in Python?
   a) `def`  b) `class`  c) `struct`  d) `object`

2. In `def move(self, dx, dy):`, what does `self` represent?
   a) A global variable  b) The specific object the method is called on  c) A required Python keyword unrelated to the object  d) The class name

3. Predict the output: given `fuel = 5` and calling `burn_fuel(10)` where `burn_fuel` does `self.fuel = max(0, self.fuel - amount)`, what is `self.fuel` afterward?
   a) -5  b) 0  c) 5  d) 10

4. Which loop type is best suited for "repeat until the player's health reaches zero," where you don't know in advance how many repetitions will occur?
   a) `for i in range(10):`  b) `while` loop  c) A function with no loop  d) `if` statement

5. What does `if __name__ == "__main__":` accomplish?
   a) It defines the main class  b) It only runs the indented code when the file is executed directly, not when imported  c) It is required in every Python file or it will not run  d) It names the program "main"

6. (Code reading) What will `check_status(fuel=15, hull_integrity=80)` return, given the function body shown in this lesson?
   a) "Status nominal. All systems green."  b) "WARNING: Fuel running low."  c) "CRITICAL: Out of fuel, drifting in space."  d) "WARNING: Hull integrity low!"

### Answer Key

1. b — `class`
2. b — the specific object the method is called on
3. b — `0` (since `5 - 10 = -5`, clamped to `0` by `max(0, ...)`)
4. b — `while` loop
5. b — guards code so it only runs when the file is executed directly
6. b — "WARNING: Fuel running low." (fuel `15` is below `20`, and hull `80` doesn't trigger any hull warning first, since fuel check comes after hull checks in the `elif` chain but hull is fine so it falls through to fuel)

## Lesson Recap

Today you engineered the fundamental components every game needs: variables to hold changing state, conditionals to make decisions, loops to repeat actions over time, functions to organize logic, and a class to bundle it all into a clean, reusable `Ship` blueprint. This `Ship` class is a direct preview of the `Player` class you'll build in Lesson 3 — same idea, same structure, just with a graphical `pygame.Rect` instead of plain `x`/`y` numbers, and `pygame.sprite.Sprite` instead of a plain Python class.

## Homework / Practice Mission

Extend `ship_status_simulator.py` with a `Cargo` class representing collectible resources. Give it a `value` attribute and a method `collect(self, ship)` that adds its value to `ship.score` and removes the cargo from play (you can use a `collected` boolean flag). Create a list of 5 `Cargo` objects and simulate the ship "encountering" one every few ticks, adding to its score when it does.

## Portfolio Project Connection

The `Ship` class you built today is the direct ancestor of the `Player` class in **Project 04: Space Shooter**, where `self.health`, `self.x`/`self.y` (as a `pygame.Rect`), and methods like `take_damage()` and `move()` reappear nearly unchanged, now driven by real keyboard input and rendered on screen. The scoring pattern (`self.score`, `add_score()`) is reused identically across all three portfolio projects: the Space Shooter, the Maze Runner Timer, and the Brick Breaker Clone. Mastering these fundamentals today means you'll recognize this exact code shape in every project for the rest of the course.
