# 🎨 Lesson 3 Activities: Printing and String Formatting

## Activity 1: My First F-String

**Steps:**
1. Type and run:
   ```python
   name = "Nova"
   age = 10
   print(f"Hello, {name}! You are {age} years old.")
   ```
2. Change `name` and `age` to your own information and run it again.

---

## Activity 2: Forget the F on Purpose

**Steps:**
1. Type:
   ```python
   name = "Nova"
   print("Hello, {name}!")
   ```
2. Run it — notice the curly braces show up as plain text, not the name.
3. Add the `f` right before the quote and run again to see the fix:
   ```python
   print(f"Hello, {name}!")
   ```

---

## Activity 3: The Birthday Message Machine (Main Activity)

**Goal:** Build a program that asks for a name and age, converts age to a number, and prints a friendly personalized message.

**Steps:**
1. Type:
   ```python
   name = input("What is your name? ")
   age = input("How old are you? ")
   age = int(age)

   print(f"Happy almost-birthday, {name}!")
   print(f"You are {age} years old right now.")
   print(f"Next year you will be {age + 1}!")
   print(f"In 10 years you will be {age + 10}!")
   ```
2. Run the program and answer the questions.
3. Check that the math for "next year" and "in 10 years" is correct.
4. Try running it again with a different age to double-check the math changes correctly.

---

## Activity 4: Fix the Math Bug

**Steps:**
1. Type this broken code:
   ```python
   age = input("How old are you? ")
   print(f"Next year you will be {age + 1}!")
   ```
2. Run it and read the `TypeError` message.
3. Add the missing conversion line:
   ```python
   age = int(age)
   ```
4. Run it again to confirm the error is gone and the math works.

---

## Activity 5: Hobby Mix-In (Extension)

**Steps:**
1. Add a hobby question to your Birthday Message Machine:
   ```python
   hobby = input("What is your favorite hobby? ")
   print(f"{name} loves {hobby}!")
   ```
2. Run the full program from start to finish and check every line makes sense.
