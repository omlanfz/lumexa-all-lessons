# Lesson 3: Printing and String Formatting — Mixing Words and Boxes 🎨

## Big Idea
We can mix plain words together with the values stored in our variable boxes to build friendly, personalized messages using f-strings.

## Learning Objectives
- Combine text and variables in a single `print()` statement using an f-string.
- Convert text input into a whole number using `int()` for simple math.
- Explain why `int()` conversion is needed when doing math on `input()` results.
- Identify and fix a `TypeError` caused by mixing text and numbers incorrectly.

## Teacher Preparation
- Review Lesson 2's variables and `input()` briefly.
- Have Thonny ready with a blank file.
- Prepare one broken code example on the board/screen to debug live (see Teacher Demonstration).

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
Ask: "If I told you my name is Nova and I am 10 years old, how could I say that in ONE sentence instead of two separate ones?" Let students suggest phrasing, then say: "Python has a neat trick called an f-string that lets us build one sentence out of words AND variables mixed together!"

🚀 **Coding Mission:** Today we become message-mixing wizards, blending plain text and variable boxes into one smooth sentence!

## Concept Introduction
- "Last lesson we printed variables by themselves, like `print(name)`. Today we'll mix them into full sentences."
- "An f-string looks like this: `f"Hello, {name}!"`. The letter `f` right before the quote tells Python 'get ready to mix in some variables.' Curly braces `{}` show where a variable's value goes."
- "Just like in Scratch, when you joined the 'say' block with a variable reporter (the oval puzzle piece) to build a sentence like 'Score: ' + score, f-strings do that same job, but written as text."
- 🤖 **Robot Tip:** "Forget the `f` before the quote, and Python will just show `{name}` as literal text instead of your value!"

## Teacher Demonstration
1. Type and run:
   ```python
   name = "Nova"
   age = 10
   print(f"Hello, {name}! You are {age} years old.")
   ```
2. Remove the `f` on purpose and run again — show the curly braces print literally. Add the `f` back.
3. Introduce number conversion:
   ```python
   age_text = input("How old are you? ")
   age_number = int(age_text)
   print(f"Next year you will be {age_number + 1}!")
   ```
4. Show the mistake on purpose — try `age_text + 1` without converting — and show the `TypeError`. Explain that `input()` always gives text, so we must convert it to a number with `int()` before doing math.

## Guided Coding
```python
name = input("What is your name? ")
age = input("How old are you? ")
age = int(age)
print(f"Hello, {name}! You are {age} years old.")
print(f"Next year you will be {age + 1}!")
```

## Mini Challenge 🎮
Ask students to add a favorite hobby and include it in the f-string:
```python
hobby = input("What is your favorite hobby? ")
print(f"{name} loves {hobby}!")
```

## Main Activity / Project
Students complete "The Birthday Message Machine" in `Activities.md`: it asks for name and current age, converts age to a number, and prints a friendly personalized message including their age next year and in 10 years.

## Fun Challenge (optional, harder) ⭐
Add simple math inside the f-string directly:
```python
print(f"In 10 years you will be {age + 10} years old!")
```
Discuss: Python can do the math right inside the curly braces!

## Check for Understanding
- "What letter do we put before the quotes to make an f-string?"
- "What symbols hold a variable's place inside an f-string?"
- "Why do we need to use `int()` on something from `input()` before adding numbers to it?"
- "What happens if we forget the `f`?"

## Common Student Mistakes
- **Forgetting the `f` before the quotes:** `print("Hello, {name}!")` shows literal curly braces. Fix: add `f` right before the opening quote.
- **Forgetting `int()` conversion:** `age + 1` when `age` is still text causes a `TypeError: can only concatenate str`. Fix: convert with `age = int(age)` first.
- **Mismatched curly braces:** forgetting a closing `}`. Fix: always pair every `{` with a `}`.
- **Quotes inside quotes without care:** using double quotes inside an f-string that's already using double quotes. Fix: use single quotes inside if the outer string uses double quotes, or vice versa.
- **Off-by-one thinking:** assuming "next year" means adding 0 instead of 1. Fix: remind students "next year" = current age + 1.

## Differentiation
- **Struggling support:** Give a fill-in-the-blank f-string template where only the variable names need to be typed inside the braces.
- **Standard:** Complete Guided Coding and the Birthday Message Machine.
- **Extension for advanced learners:** Add math directly inside the f-string (Fun Challenge) or build a 3-line message with 3 different variables mixed in.

## Recap
"Today we learned to mix plain words and variables into one smooth sentence using f-strings, and we learned that `input()` always gives us text, so we use `int()` to turn it into a real number for math."

## Take-Home / Creative Challenge
Ask students to write an f-string birthday message for a family member, including their name and a fun fact, and if possible, run it at home.

## Teacher Talking Points
- "Remember how in Scratch you'd snap together the 'say' block with a variable oval to build a sentence? An f-string is Python's way of doing that same trick with typed code."
- "That little letter `f` right before the quotes is like a magic switch — it tells Python 'hey, get ready, there are variables hiding inside these curly braces!'"
- "Numbers that come from `input()` are sneaky — they LOOK like numbers, but Python actually treats them like words until we tell it otherwise with `int()`."
- "You're building programs that talk back to people in a personal, friendly way — that's exactly how real apps and games are made!"

## 🏆 Victory Moment
Celebrate the first time a student successfully builds a personalized birthday message combining input, int() conversion, and an f-string — a big jump in coding skill!
