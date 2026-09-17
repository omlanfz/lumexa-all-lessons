# 📦 Lesson 2 Activities: Variables and User Input

## Activity 1: My First Variable Box

**Steps:**
1. Open Thonny.
2. Type:
   ```python
   name = "Captain Nova"
   print(name)
   ```
3. Run it and confirm "Captain Nova" appears.
4. Change the text inside the quotes to your own name, and run it again.

---

## Activity 2: Ask and Remember

**Steps:**
1. Type:
   ```python
   name = input("What is your name, space traveler? ")
   print(name)
   ```
2. Run it. When Thonny asks, type your name and press Enter.
3. Confirm your name is printed back to you.

---

## Activity 3: Space Traveler ID Card (Main Activity)

**Goal:** Collect three pieces of information using `input()` and display them nicely.

**Steps:**
1. Type the following program:
   ```python
   name = input("What is your name, space traveler? ")
   age = input("How old are you? ")
   planet = input("What is your favorite planet? ")

   print("=== SPACE TRAVELER ID CARD ===")
   print(name)
   print(age)
   print(planet)
   ```
2. Run the program and answer each question.
3. Check that your ID card shows all three pieces of information.
4. Share your ID card with a partner — whose favorite planet is different from yours?

---

## Activity 4: Fix the Typo

**Steps:**
1. Type this broken code:
   ```python
   name = input("What is your name? ")
   print(nam)
   ```
2. Run it and read the red error message (`NameError`).
3. Find the typo and fix it so both spellings of `name` match exactly.
4. Run it again to confirm the error is gone.

---

## Activity 5: Math With Variables (Extension)

**Steps:**
1. Type:
   ```python
   score = 10
   bonus = 5
   total = score + bonus
   print(total)
   ```
2. Run it and check the answer is 15.
3. Change `score` and `bonus` to different numbers and predict the answer before running.
