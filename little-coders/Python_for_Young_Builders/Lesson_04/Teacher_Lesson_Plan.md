# Lesson 4: If/Else Decisions — Teaching Our Program to Choose 🔀

## Big Idea
Programs can make decisions, just like a Scratch "if/else" block — they check something, and do one thing if it's true, or a different thing if it's not.

## Learning Objectives
- Explain that `if` and `else` let a program choose between paths, like a fork in the road.
- Write a Python `if`/`elif`/`else` block with correct indentation and colons.
- Use comparison operators (`==`, `>`, `<`, `>=`, `<=`) inside conditions.
- Identify and fix indentation errors and missing colons.

## Teacher Preparation
- Have Thonny ready.
- Prepare a simple physical or verbal "if/else" example (e.g., "IF it's raining, take an umbrella, ELSE wear sunglasses") to open the lesson.
- Review Lesson 3's f-strings and `int()` briefly, since they'll be used together with conditions today.

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
Ask: "In Scratch, did you ever use the 'if/else' block? What did it look like?" Let students describe it. Then say: "Today we write that exact same decision-making logic — but in text!"

🚀 **Coding Mission:** Today we teach our programs to make choices — just like a fork in the road!

## Concept Introduction
- "An `if` statement is like asking a yes/no question. If the answer is yes (True), the program does one thing. If no (False), it can do something else with `else`."
- "In Scratch, the 'if/else' block had puzzle pieces that snapped inside it. In Python, we show what's 'inside' the if by indenting — pressing Tab or a few spaces before the code."
- "We always need a colon `:` at the end of the `if` line and the `else` line — it's like Python's way of saying 'here comes the inside part.'"
- 🤖 **Robot Tip:** "Python is picky about indentation! Code under an `if` must be indented the same amount, or Python gets confused."

## Teacher Demonstration
1. Type and run:
   ```python
   age = int(input("How old are you? "))
   if age >= 10:
       print("You are old enough for the big kid missions!")
   else:
       print("You'll be ready for big missions soon!")
   ```
2. Break it on purpose — remove the colon after `if age >= 10` — show the `SyntaxError`. Fix it.
3. Break it again — un-indent the `print()` line under `if` — show the `IndentationError`. Fix it.
4. Introduce `elif` for a third option:
   ```python
   score = int(input("What score did you get (0-100)? "))
   if score >= 90:
       print("Amazing! You got an A!")
   elif score >= 70:
       print("Great job! You got a B!")
   else:
       print("Keep practicing, you'll get there!")
   ```

## Guided Coding
```python
age = int(input("How old are you? "))
if age >= 10:
    print("You are old enough for the big kid missions!")
else:
    print("You'll be ready for big missions soon!")
```

## Mini Challenge 🎮
Ask students to add a third condition using `elif` for age exactly 10:
```python
age = int(input("How old are you? "))
if age > 10:
    print("You are old enough for the big kid missions!")
elif age == 10:
    print("You just became old enough today!")
else:
    print("You'll be ready for big missions soon!")
```

## Main Activity / Project
Students complete "The Mood Ring Machine" in `Activities.md`: asks the player how they're feeling (a word), and uses `if`/`elif`/`else` to respond with a matching message.

## Fun Challenge (optional, harder) ⭐
Combine two conditions using `and`:
```python
age = int(input("How old are you? "))
has_permission = input("Do you have a permission slip? (yes/no) ")
if age >= 10 and has_permission == "yes":
    print("You can join the advanced mission!")
else:
    print("Sorry, you need to be 10+ AND have a permission slip.")
```

## Check for Understanding
- "What symbol must go at the end of an `if` line?"
- "What happens if the code under `if` isn't indented?"
- "What's the difference between `elif` and `else`?"
- "What does `==` mean, and how is it different from a single `=`?"

## Common Student Mistakes
- **Missing colon:** `if age >= 10` (no `:`). Fix: always add `:` at the end of `if`, `elif`, and `else` lines.
- **Wrong indentation:** code under `if` not indented, or indented inconsistently. Fix: use consistent spacing (Thonny usually auto-indents after a colon — trust it).
- **Using `=` instead of `==`:** `if age = 10:` causes a `SyntaxError`. Fix: use `==` to *compare*, and `=` only to *assign*.
- **Comparing text and numbers without converting:** comparing `input()` result directly to a number without `int()`. Fix: convert with `int()` first when comparing numbers.
- **Forgetting `elif` needs its own condition:** writing `else` when they meant to check another specific case. Fix: use `elif condition:` for additional specific checks, and plain `else:` only for "everything else."

## Differentiation
- **Struggling support:** Give a partially completed if/else template with blanks only for the condition and message text.
- **Standard:** Complete Guided Coding and the Mood Ring Machine.
- **Extension for advanced learners:** Try the Fun Challenge with `and`, or add a 4th `elif` mood option to the Mood Ring Machine.

## Recap
"Today our programs learned to make choices! We used `if`, `elif`, and `else` to respond differently depending on what the player told us — just like the if/else blocks in Scratch, but written with indentation and colons."

## Take-Home / Creative Challenge
Ask students to design an if/else "quiz gate" on paper: if the player answers a riddle correctly, print a "you may pass" message; otherwise print a "try again" message. Bring it to type up next class if possible.

## Teacher Talking Points
- "Remember your Scratch if/else block, with the puzzle pieces stacked inside? In Python, instead of snapping blocks together, we show 'this is inside the if' by indenting the code a little to the right."
- "Think of `if` like a fork in the road: if a condition is true, we walk down one path; if it's false, we walk down the other."
- "That colon at the end of the `if` line is like Python saying, 'okay, get ready — the instructions for this choice are coming next.'"
- "You're building programs that can respond differently to different people — that's exactly how quizzes, games, and apps make decisions!"

## 🏆 Victory Moment
Celebrate when a student's Mood Ring Machine correctly responds to at least three different moods — real decision-making code in action!
