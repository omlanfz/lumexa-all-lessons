# Lesson 2: Variables — Little Boxes That Remember Things 📦

## Big Idea
A variable is a little labeled box that holds information for us, so the computer can remember it and use it again later.

## Learning Objectives
- Explain what a variable is using the "labeled box" analogy.
- Create variables to store text (strings) and numbers.
- Use `input()` to ask the user a question and store their answer in a variable.
- Print a variable's value using `print()`.
- Recognize and fix the mistake of forgetting to define a variable before using it.

## Teacher Preparation
- Have Thonny/Repl.it ready and working from Lesson 1.
- Bring a real small box or container with a label (e.g., "Snacks") to use as a physical prop for the variable analogy.
- Review Lesson 1's `print()` command briefly as a warm-up bridge.

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
Hold up a labeled box (e.g., a box labeled "name"). Ask: "If I write 'Sam' on a card and put it in this box labeled 'name,' what's inside the box?" Then say: "In Python, we use variables just like this box — a name that holds a piece of information."

🚀 **Coding Mission:** Today we build boxes (variables) that can hold names, ages, and more — and we'll even ask the PLAYER to tell us what to put inside!

## Concept Introduction
- "A variable is a little box with a label. The label is the variable's name, and what's inside is the variable's value."
- "In Scratch, you used the 'Variables' category to create things like `score` or `lives`. Python variables work the exact same way — we just type them instead of clicking a button to create them."
- "We create a variable like this: `name = "Sam"`. The `=` sign doesn't mean 'equals' like in math — it means 'put this value into the box.'"
- 🤖 **Robot Tip:** "Variable names can't have spaces or start with a number. `player_name` works, but `2ndPlace` does not!"

## Teacher Demonstration
1. Type and run:
   ```python
   name = "Captain Nova"
   print(name)
   ```
2. Point out: no quotes around `name` in the `print()` line — because we want the box's contents, not the word "name" itself.
3. Show the mistake on purpose:
   ```python
   print(nam)
   ```
   Run it, show the `NameError`, then fix the typo.
4. Introduce `input()`:
   ```python
   name = input("What is your name, space traveler? ")
   print(name)
   ```
5. Run it, type an answer when prompted, and show it gets stored and printed.

## Guided Coding
Have students type and run:
```python
name = input("What is your name, space traveler? ")
age = input("How old are you? ")
print(name)
print(age)
```
Point out both `name` and `age` are stored as text (strings) even though age looks like a number — we'll deal with real math later in Lesson 3/5.

## Mini Challenge 🎮
Ask students to add one more variable for their favorite planet and print it too:
```python
planet = input("What is your favorite planet? ")
print(planet)
```

## Main Activity / Project
Students complete "My Space Traveler ID Card" in `Activities.md`: collect name, age, and favorite planet using `input()`, store each in a variable, and print all three in a friendly format.

## Fun Challenge (optional, harder) ⭐
Introduce number variables and simple math:
```python
score = 10
bonus = 5
total = score + bonus
print(total)
```
Explain: since `score` and `bonus` are numbers (not in quotes), Python can add them together.

## Check for Understanding
- "What is a variable, in your own words?"
- "What symbol do we use to put something into a variable?"
- "Why doesn't `print(name)` need quotes around `name`?"
- "What happens if we try to print a variable that was never created?"

## Common Student Mistakes
- **Using a variable before creating it:** `print(score)` with no `score = ...` above → `NameError`. Fix: always create (define) the variable first.
- **Typos in variable names:** creating `plyer_name` but printing `player_name`. Fix: check spelling matches exactly, including underscores and capitalization.
- **Putting quotes around a variable name in print:** `print("name")` prints the literal word "name" instead of the value. Fix: remove the quotes to print the variable's value: `print(name)`.
- **Forgetting `input()` returns text:** trying to do math directly on `age` from `input()` without converting first (we'll fully cover `int()` conversion in Lesson 3 math and Lesson 5 loops) — for now just note that input is text.
- **Missing the `=` sign:** writing `name "Sam"` instead of `name = "Sam"`. Fix: always include the equals sign to assign a value.

## Differentiation
- **Struggling support:** Give a fill-in-the-blank template with the variable name already written; students just add the input prompt text.
- **Standard:** Complete Guided Coding and Main Activity fully.
- **Extension for advanced learners:** Try the Fun Challenge with number variables and simple addition, or create 5 different variables about their pet or favorite game character.

## Recap
"Today we learned that variables are labeled boxes that hold information. We used `input()` to ask a question and store the answer, and `print()` to show what's inside the box. Just like Scratch's variable blocks, but typed!"

## Take-Home / Creative Challenge
Have students write down (on paper or at home) three variables describing their dream pet — name, type of animal, and superpower — and, if possible, type and run the code at home.

## Teacher Talking Points
- "Remember how in Scratch you could make a variable called 'score' and it would remember a number for you? Python variables do the exact same job!"
- "Think of a variable like a labeled lunchbox — you can put something in it, take a peek at what's inside, or even swap it out for something new later."
- "The equals sign in Python is a little different from math class — here it means 'store this value,' not 'these two things are the same.'"
- "You're now able to make programs that listen to people and remember what they say — that's a big coder superpower!"

## 🏆 Victory Moment
Celebrate when a student's program successfully asks for their name and prints it back — this is the first "interactive" program many of them will ever write!
