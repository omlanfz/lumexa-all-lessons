# Lesson 7: Building a Mini Quiz App — Putting It All Together! 🧩

## Big Idea
We can combine everything we've learned so far — variables, input, if/else, and loops — to build one complete, real program: a quiz app that keeps score.

## Learning Objectives
- Combine variables, `input()`, `if`/`elif`/`else`, and a counting variable into one working program.
- Track a running score using a variable that increases when an answer is correct.
- Give a final message based on the player's score using tiered `if`/`elif`/`else` logic.
- Debug a multi-part program by reading error messages and checking indentation/spelling.

## Teacher Preparation
- Have Thonny ready.
- Review briefly: variables (Lesson 2), f-strings and int() (Lesson 3), if/else (Lesson 4), loops (Lesson 5).
- This lesson previews Project 04 (Personal Quiz App), so let students know they'll build a bigger version of this later.

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`
- Thonny IDE or Repl.it

## Teaching Flow (approx. 50–55 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration — 10 min
4. Guided Coding — 12 min
5. Mini Challenge — 5 min
6. Main Activity — 12 min
7. Check for Understanding + Recap — 5 min

## Warm-up / Hook (5 min)
Ask: "Have you ever played a trivia game show, or a quiz on a tablet, where it keeps track of how many you got right?" Say: "Today we build our very own quiz app that keeps score, just like that!"

🚀 **Coding Mission:** Today we combine every tool in our coder toolbox — variables, input, if/else, and counting — to build a real quiz game!

## Concept Introduction
- "A quiz app needs to: ask a question, check the answer, and keep a running score. We already know how to do every one of these things separately — today we put them together!"
- "We'll use a variable called `score` that starts at 0, and every time an answer is correct, we add 1 to it: `score = score + 1`."
- "At the very end, we'll use `if`/`elif`/`else` to give a different message depending on the final score — just like the grading machine from Lesson 4!"

## Teacher Demonstration
1. Type and run this simple 2-question quiz live:
   ```python
   score = 0

   answer1 = input("What color do you get when you mix blue and yellow? ")
   if answer1 == "green":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was green.")

   answer2 = input("How many legs does a spider have? ")
   if answer2 == "8":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was 8.")

   print(f"You scored {score} out of 2!")
   ```
2. Run it once answering both correctly, and once answering both wrong, to show the score changes.
3. Point out how `score = score + 1` is the same "counter" pattern from the loops lesson.

## Guided Coding
Have students type and run the same 2-question quiz above, then add a 3rd question of their own with a different topic.

## Mini Challenge 🎮
Ask students to add tiered final feedback using if/elif/else based on score out of 3:
```python
if score == 3:
    print("Perfect score! You're a quiz champion! 🏆")
elif score >= 1:
    print("Good try! Keep practicing!")
else:
    print("Don't worry, let's try again next time!")
```

## Main Activity / Project
Students complete "The 5-Question Challenge" in `Activities.md`: build a full 5-question quiz with a running score and a tiered final message. This directly previews Project 04.

## Fun Challenge (optional, harder) ⭐
Ask the player's name at the start and include it in the final message using an f-string:
```python
name = input("What is your name? ")
print(f"Great job, {name}! You scored {score} out of 5!")
```

## Check for Understanding
- "What does `score = score + 1` actually do?"
- "Why do we set `score = 0` at the very beginning?"
- "How can we give different final messages depending on the score?"
- "What tools from earlier lessons did we use today?"

## Common Student Mistakes
- **Forgetting to initialize `score = 0` first:** causes a `NameError` when trying to add to it. Fix: always create the score variable before the quiz starts.
- **Comparing input to a number without quotes:** `if answer2 == 8` when `answer2` is text from `input()`. Fix: compare to the text version `"8"`, or convert with `int(answer2) == 8`.
- **Indentation mismatches inside if/else blocks with multiple lines.** Fix: make sure every line meant to run inside the `if` is indented the same amount.
- **Case sensitivity:** typing "Green" when checking for "green" fails to match. Fix: mention that Python is case-sensitive, or use `.lower()` as an advanced tip if the class is ready.
- **Losing track of which variable stores what** in a longer program. Fix: use clear, descriptive variable names (`answer1`, `answer2`, `score`) and comments.

## Differentiation
- **Struggling support:** Provide a 3-question quiz template with only the questions/answers to fill in.
- **Standard:** Complete the 5-Question Challenge as described.
- **Extension for advanced learners:** Add the Fun Challenge personalization, or add a 6th question worth "bonus" points that adds 2 to the score instead of 1.

## Recap
"Today we built a real quiz app by combining everything we've learned: variables to store answers and score, input() to ask questions, if/else to check answers, and tiered feedback at the end. This is exactly the kind of program real quiz apps use!"

## Take-Home / Creative Challenge
Ask students to write down (or type at home) 2 new quiz questions on a topic they love — like animals, sports, or video games — to add to their quiz next class.

## Teacher Talking Points
- "See how every tool we learned — boxes for information, decisions, and counting — all work together to build one real program? That's what coding really is: combining small building blocks into something bigger!"
- "Think of `score = score + 1` like a scoreboard at a game — every time something good happens, we add one point to the board."
- "You're basically building the same kind of quiz you'd find in a real app on a tablet or phone — that's a big deal!"
- "Don't worry about it being perfect — real programmers test, find bugs, and fix them constantly. That's exactly what you're doing right now."

## 🏆 Victory Moment
Celebrate when a student successfully runs their full 5-question quiz from start to finish, with a working score and final message — this is essentially Project 04 in miniature!
