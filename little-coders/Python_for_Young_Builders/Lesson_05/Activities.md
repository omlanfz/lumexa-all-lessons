# 🔁 Lesson 5 Activities: While Loops and For Loops

## Activity 1: Counting With a For Loop

**Steps:**
1. Type and run:
   ```python
   for i in range(5):
       print("Blast off in", i)
   ```
2. Count how many lines of output you see. Is it 5? Notice it starts at 0.

---

## Activity 2: Counting Down

**Steps:**
1. Type and run:
   ```python
   for i in range(5, 0, -1):
       print(i)
   print("Liftoff!")
   ```
2. Confirm it prints 5, 4, 3, 2, 1, then "Liftoff!"

---

## Activity 3: The Rocket Countdown (Main Activity)

**Goal:** Build a countdown that starts from a number the player types.

**Steps:**
1. Type:
   ```python
   start = int(input("Countdown from what number? "))
   count = start
   while count > 0:
       print(count)
       count = count - 1
   print("🚀 Liftoff!")
   ```
2. Run it and test with the number 5, then test again with the number 3.
3. Confirm the countdown always ends with "🚀 Liftoff!"

---

## Activity 4: Fix the Infinite Loop

**Steps:**
1. Type this broken code (be ready to click the red Stop button in Thonny!):
   ```python
   count = 1
   while count <= 3:
       print(f"Lap {count}")
   ```
2. Run it, notice it keeps printing "Lap 1" forever, and click Stop.
3. Add the missing update line so the loop actually finishes:
   ```python
   count = 1
   while count <= 3:
       print(f"Lap {count}")
       count = count + 1
   ```
4. Run the fixed version and confirm it stops after 3 laps.

---

## Activity 5: Quit When You Say So (Extension)

**Steps:**
1. Type:
   ```python
   answer = ""
   while answer != "quit":
       answer = input("Type a word (or 'quit' to stop): ")
       print(f"You typed: {answer}")
   print("Goodbye!")
   ```
2. Run it, type a few different words, then type "quit" to confirm the loop stops.
