# 🧩 Lesson 7 Activities: Building a Mini Quiz App

## Activity 1: The 2-Question Warm-Up Quiz

**Steps:**
1. Type and run:
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
2. Run it once, answering both correctly. Confirm the score is 2.
3. Run it again, answering both incorrectly. Confirm the score is 0.

---

## Activity 2: The 5-Question Challenge (Main Activity)

**Goal:** Build a full quiz with 5 questions, a running score, and a tiered final message.

**Steps:**
1. Type the following complete quiz program:
   ```python
   score = 0

   q1 = input("What color do you get when you mix blue and yellow? ")
   if q1 == "green":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was green.")

   q2 = input("How many legs does a spider have? ")
   if q2 == "8":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was 8.")

   q3 = input("What planet do we live on? ")
   if q3 == "Earth":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was Earth.")

   q4 = input("What is 2 + 2? ")
   if q4 == "4":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was 4.")

   q5 = input("What do bees make? ")
   if q5 == "honey":
       print("Correct!")
       score = score + 1
   else:
       print("Oops, the answer was honey.")

   print(f"You scored {score} out of 5!")

   if score == 5:
       print("Perfect score! You're a quiz champion! 🏆")
   elif score >= 3:
       print("Great job! You really know your stuff!")
   else:
       print("Nice try! Practice makes perfect!")
   ```
2. Run it and answer all 5 questions correctly. Confirm you see the "Perfect score!" message.
3. Run it again, answering only 2 correctly, and confirm you see the "Nice try!" message.

---

## Activity 3: Fix the Comparison Bug

**Steps:**
1. Type this broken code:
   ```python
   score = 0
   answer2 = input("How many legs does a spider have? ")
   if answer2 == 8:
       print("Correct!")
       score = score + 1
   print(f"Score: {score}")
   ```
2. Run it and type "8" when asked — notice the score stays 0, even though the answer was right!
3. Fix the bug by comparing to `"8"` (in quotes) instead of `8`.
4. Run it again to confirm the score updates correctly.

---

## Activity 4: Personalize It (Extension)

**Steps:**
1. Add a name question at the very top of your 5-Question Challenge:
   ```python
   name = input("What is your name? ")
   ```
2. Change the final print line to use the player's name:
   ```python
   print(f"Great job, {name}! You scored {score} out of 5!")
   ```
3. Run the full quiz from start to finish to confirm it works.
