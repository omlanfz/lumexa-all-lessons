# Lesson 6: Lists and Simple Data — Our Whole Crew in One Box 📋

## Big Idea
A list is like one big box that can hold many pieces of information together, in order, so we don't need a separate variable for each one.

## Learning Objectives
- Explain what a list is and why it's useful compared to many separate variables.
- Create a list of strings or numbers using square brackets `[ ]`.
- Access an item in a list using its index, and understand indexes start at 0.
- Use a `for` loop to go through every item in a list.
- Use the `random` module to pick a random item from a list.

## Teacher Preparation
- Have Thonny ready.
- Bring a real small set of index cards or objects (e.g., 5 different colored blocks in a row) to demonstrate "position/index" physically.
- Review Lesson 5's `for` loops briefly, since lists and loops work closely together.

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`
- Thonny IDE or Repl.it

## Teaching Flow (approx. 45–50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 8 min
3. Teacher Demonstration — 8 min
4. Guided Coding — 10 min
5. Mini Challenge — 5 min
6. Main Activity — 10 min
7. Check for Understanding + Recap — 5 min

## Warm-up / Hook (5 min)
Line up 5 students (or 5 objects) in a row. Point to the first one and say "this is position 0" (not 1!). Ask: "If computers start counting at 0, what position is the 3rd student in line?" (Answer: position 2.) Say: "Today we learn about lists — Python's way of keeping many things in order, just like this line!"

🚀 **Coding Mission:** Today we build a whole crew list and learn to call on any crew member by their position in line!

## Concept Introduction
- "A list is like a row of labeled boxes, all connected together, holding many pieces of information in one variable."
- "We create a list with square brackets: `crew = ["Nova", "Max", "Zara"]`."
- "Every item in a list has a position called an index, and — just like `range()` — indexes start counting at 0! So `crew[0]` is "Nova," not "Max.""
- "In Scratch, this is like a 'list' variable where you could add items and check item #1, #2, etc. — except Scratch starts counting at 1, and Python starts at 0, so we have to be extra careful!"
- 🤖 **Robot Tip:** "If a list has 3 items, the valid indexes are 0, 1, and 2 — NOT 3! Asking for index 3 gives an error."

## Teacher Demonstration
1. Type and run:
   ```python
   crew = ["Nova", "Max", "Zara"]
   print(crew[0])
   print(crew[1])
   print(crew[2])
   ```
2. Break it on purpose — ask for `crew[3]` — show the `IndexError`. Explain: only 3 items means indexes 0, 1, 2.
3. Show looping through a list:
   ```python
   for member in crew:
       print(f"Crew member: {member}")
   ```
4. Introduce `random.choice()`:
   ```python
   import random
   crew = ["Nova", "Max", "Zara"]
   captain = random.choice(crew)
   print(f"Today's captain is {captain}!")
   ```

## Guided Coding
```python
crew = ["Nova", "Max", "Zara"]
for member in crew:
    print(f"Crew member: {member}")
```

## Mini Challenge 🎮
Ask students to add a 4th crew member to the list and re-run the loop to see it appear automatically:
```python
crew = ["Nova", "Max", "Zara", "Kai"]
for member in crew:
    print(f"Crew member: {member}")
```

## Main Activity / Project
Students complete "The Random Mission Picker" in `Activities.md`: build a list of at least 5 fun space missions, then use `random.choice()` to pick one each time the program runs.

## Fun Challenge (optional, harder) ⭐
Count how many items are in a list using `len()`, and use it inside a `for i in range(len(list))` loop:
```python
crew = ["Nova", "Max", "Zara"]
print(f"There are {len(crew)} crew members.")
for i in range(len(crew)):
    print(f"Position {i}: {crew[i]}")
```

## Check for Understanding
- "What symbol do we use to create a list?"
- "What index number does the FIRST item in a list have?"
- "What happens if we ask for an index that doesn't exist?"
- "What does `random.choice()` do?"

## Common Student Mistakes
- **Off-by-one indexing:** assuming the first item is index 1 instead of 0. Fix: remind students Python (and `range()`) always starts counting at 0.
- **IndexError from asking for one index too many:** e.g., `crew[3]` on a 3-item list. Fix: the last valid index is always `len(list) - 1`.
- **Forgetting to `import random`:** trying to use `random.choice()` without importing the module first. Fix: always add `import random` at the very top of the file.
- **Missing commas between list items:** `["Nova" "Max"]` instead of `["Nova", "Max"]`. Fix: separate every item with a comma.
- **Forgetting quotes around list of strings:** `[Nova, Max]` instead of `["Nova", "Max"]`. Fix: text items always need quotes, just like in `print()`.

## Differentiation
- **Struggling support:** Provide a pre-made list variable and only have students write the `for` loop or the `random.choice()` line.
- **Standard:** Complete Guided Coding and the Random Mission Picker.
- **Extension for advanced learners:** Try the Fun Challenge with `len()` and index-based loops, or build a list of numbers and use a loop to add them all up.

## Recap
"Today we learned that a list can hold many pieces of information in one variable, like a whole crew in one box. We learned that indexes start at 0, we can loop through every item with `for`, and we can pick a random item using `random.choice()`."

## Take-Home / Creative Challenge
Ask students to write a list of their 5 favorite foods (on paper or at home) and, if possible, use `random.choice()` to have Python pick what's for dinner!

## Teacher Talking Points
- "Remember Scratch's list blocks, where you could add items and check item #1? Python lists work the same way, except Python starts counting from 0 instead of 1 — a fun little twist to remember!"
- "Think of a list like a train with numbered train cars — car 0 is the front car, not car 1!"
- "The `random` module is like a magic hat — `random.choice()` reaches in and pulls out one item for us, and we never know which one until we run it!"
- "Lists let us handle LOTS of information with just one variable name — instead of `crew1`, `crew2`, `crew3`, we just use one list called `crew`!"

## 🏆 Victory Moment
Celebrate when a student's Random Mission Picker successfully picks a different mission each time it's run — real randomness in their own program!
