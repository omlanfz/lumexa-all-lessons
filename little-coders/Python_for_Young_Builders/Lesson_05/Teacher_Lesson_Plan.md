# Lesson 5: While Loops and For Loops — Repeat, Repeat, Repeat! 🔁

## Big Idea
A loop lets our program repeat instructions again and again without us typing the same code over and over, just like the "repeat" block in Scratch.

## Learning Objectives
- Explain what a loop does using the "repeat block" analogy from Scratch.
- Write a `for` loop using `range()` to repeat an action a set number of times.
- Write a `while` loop that repeats until a condition becomes false.
- Identify and fix an off-by-one mistake in a `range()` loop and an infinite loop mistake.

## Teacher Preparation
- Have Thonny ready.
- Prepare a physical demonstration: clap 5 times while counting out loud, to model "repeating an action a set number of times."
- Have a way to stop a program quickly in Thonny (the red Stop/Restart button) in case a student accidentally makes an infinite loop.

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
Clap 5 times out loud while counting "1, 2, 3, 4, 5." Ask: "In Scratch, what block would make my sprite do something 5 times without me stacking 5 separate blocks?" (Answer: the "repeat" block.) Say: "Python has that too — it's called a `for` loop!"

🚀 **Coding Mission:** Today we teach our program to repeat actions without getting tired — just like a robot on repeat!

## Concept Introduction
- "A `for` loop repeats something a specific number of times. We use `range()` to say how many times."
- "`range(5)` actually counts 0, 1, 2, 3, 4 — five numbers total, starting at 0! This trips up a lot of coders at first, so we'll practice it carefully."
- "A `while` loop repeats as long as something stays true — like Scratch's 'repeat until' block, but flipped: it keeps going WHILE the condition is true, and stops once it becomes false."
- 🤖 **Robot Tip:** "Loops are powerful, but if we never change the condition inside a `while` loop, it can run forever! We call that an infinite loop, and we always need a way to stop it (like a variable that changes each time)."

## Teacher Demonstration
1. Type and run:
   ```python
   for i in range(5):
       print("Blast off in", i)
   ```
2. Point out the output is 0,1,2,3,4 — five lines total, ask why it doesn't show 5.
3. Show a countdown using `range` differently:
   ```python
   for i in range(5, 0, -1):
       print(i)
   print("Liftoff!")
   ```
4. Introduce `while`:
   ```python
   count = 1
   while count <= 3:
       print(f"Lap {count}")
       count = count + 1
   ```
5. Show an infinite loop mistake on purpose (WARN students, keep the Stop button ready):
   ```python
   count = 1
   while count <= 3:
       print(f"Lap {count}")
   ```
   Point out it never stops because `count` never changes — press Stop, then fix it by adding `count = count + 1` back in.

## Guided Coding
```python
for i in range(5):
    print("Blast off in", i)
```
Then:
```python
count = 1
while count <= 3:
    print(f"Lap {count}")
    count = count + 1
```

## Mini Challenge 🎮
Ask students to change the `for` loop to count from 1 to 5 instead of 0 to 4:
```python
for i in range(1, 6):
    print("Blast off in", i)
```

## Main Activity / Project
Students complete "The Rocket Countdown" in `Activities.md`: build a countdown from a number the player types down to 1, then print "Liftoff!" using a `while` loop.

## Fun Challenge (optional, harder) ⭐
Combine a `while` loop with `input()` to repeat until the player types "quit":
```python
answer = ""
while answer != "quit":
    answer = input("Type a word (or 'quit' to stop): ")
    print(f"You typed: {answer}")
print("Goodbye!")
```

## Check for Understanding
- "What does `range(5)` actually count — and how many numbers is that?"
- "What's the difference between a `for` loop and a `while` loop?"
- "What could cause a `while` loop to run forever?"
- "How do we stop a program that's stuck in an infinite loop in Thonny?"

## Common Student Mistakes
- **Off-by-one with `range()`:** expecting `range(5)` to count 1-5, but it actually counts 0-4. Fix: use `range(1, 6)` to count 1 through 5.
- **Infinite loops:** forgetting to update the loop variable inside a `while` loop. Fix: always change the variable being checked (like `count = count + 1`) inside the loop body.
- **Wrong indentation inside the loop:** code that should repeat isn't indented under the `for`/`while` line. Fix: indent everything that should repeat.
- **Missing colon:** `for i in range(5)` without a `:`. Fix: always add the colon.
- **Confusing `while` and `for`:** using `while` when a `for` loop with a known count would be simpler, or vice versa. Fix: use `for` when you know exactly how many times to repeat, and `while` when it depends on a changing condition.

## Differentiation
- **Struggling support:** Give a pre-written `for` loop template with only the range numbers left blank to fill in.
- **Standard:** Complete Guided Coding and the Rocket Countdown.
- **Extension for advanced learners:** Try the Fun Challenge with `while` + `input()`, or nest a small `for` loop inside another (e.g. print stars in rows).

## Recap
"Today our programs learned to repeat things without us retyping code — `for` loops repeat a set number of times, and `while` loops repeat until something changes. Just like the repeat blocks in Scratch, but written as text!"

## Take-Home / Creative Challenge
Ask students to write a `for` loop (on paper or at home) that prints "I love coding!" five times, and if possible run it to check.

## Teacher Talking Points
- "Remember stacking a 'repeat 10' block in Scratch around other blocks? A Python `for` loop does that same job — we just tell it how many times with `range()`."
- "Here's a fun quirk: `range(5)` actually starts counting at 0, not 1! It still gives us 5 numbers total — 0, 1, 2, 3, 4 — Python just likes to start counting from zero."
- "A `while` loop is like saying 'keep going while this is still true' — the moment it becomes false, the loop politely stops."
- "Be careful with `while` loops — if we forget to change what we're checking, the loop could run forever! Always make sure something changes each time around."

## 🏆 Victory Moment
Celebrate when a student successfully builds a working countdown from any starting number down to "Liftoff!" — real repeating logic in action!
