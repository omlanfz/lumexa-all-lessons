# 🔀 Lesson 4 Activities: If/Else Decisions

## Activity 1: Big Kid Missions Check

**Steps:**
1. Type and run:
   ```python
   age = int(input("How old are you? "))
   if age >= 10:
       print("You are old enough for the big kid missions!")
   else:
       print("You'll be ready for big missions soon!")
   ```
2. Run it once with an age of 12, then run it again with an age of 6. Confirm you get different messages.

---

## Activity 2: Grading Machine

**Steps:**
1. Type and run:
   ```python
   score = int(input("What score did you get (0-100)? "))
   if score >= 90:
       print("Amazing! You got an A!")
   elif score >= 70:
       print("Great job! You got a B!")
   else:
       print("Keep practicing, you'll get there!")
   ```
2. Test it with scores 95, 80, and 50. Confirm each gives the right message.

---

## Activity 3: The Mood Ring Machine (Main Activity)

**Goal:** Build a program that responds differently based on the player's mood.

**Steps:**
1. Type:
   ```python
   mood = input("How are you feeling today? (happy/sad/excited/tired) ")

   if mood == "happy":
       print("Yay! Keep spreading that sunshine! ☀️")
   elif mood == "sad":
       print("Aw, sending you a virtual hug! 🤗")
   elif mood == "excited":
       print("Woohoo! Let's channel that energy into coding! 🚀")
   elif mood == "tired":
       print("Maybe it's time for a quick stretch break! 🧘")
   else:
       print("Thanks for sharing how you feel!")
   ```
2. Run it and test all four moods, plus one word not on the list (to check the `else` message shows).
3. Add a fifth mood of your own choosing with its own `elif` and message.

---

## Activity 4: Fix the Broken Decision

**Steps:**
1. Type this broken code:
   ```python
   age = int(input("How old are you? "))
   if age >= 10
   print("You are old enough!")
   else:
   print("Not yet!")
   ```
2. Run it and read the error.
3. Fix both bugs: add the missing colon after `if age >= 10`, and indent both `print()` lines.
4. Run it again to confirm it works.

---

## Activity 5: Permission Slip Check (Extension)

**Steps:**
1. Type:
   ```python
   age = int(input("How old are you? "))
   has_permission = input("Do you have a permission slip? (yes/no) ")
   if age >= 10 and has_permission == "yes":
       print("You can join the advanced mission!")
   else:
       print("Sorry, you need to be 10+ AND have a permission slip.")
   ```
2. Test all four combinations: (age 12, yes), (age 12, no), (age 8, yes), (age 8, no).
