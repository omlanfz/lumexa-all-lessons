# 📋 Lesson 6 Activities: Lists and Simple Data

## Activity 1: Meet the Crew

**Steps:**
1. Type and run:
   ```python
   crew = ["Nova", "Max", "Zara"]
   print(crew[0])
   print(crew[1])
   print(crew[2])
   ```
2. Check: does `crew[0]` print "Nova," the first item? Confirm indexes start at 0.

---

## Activity 2: Loop Through the Crew

**Steps:**
1. Type and run:
   ```python
   crew = ["Nova", "Max", "Zara"]
   for member in crew:
       print(f"Crew member: {member}")
   ```
2. Add a 4th name to the list and run it again — did it print automatically without changing the loop?

---

## Activity 3: The Random Mission Picker (Main Activity)

**Goal:** Build a program with a list of at least 5 missions, and randomly pick one each time it runs.

**Steps:**
1. Type:
   ```python
   import random

   missions = [
       "Explore the Moon crater",
       "Repair the space station",
       "Collect a rock sample from Mars",
       "Photograph a comet",
       "Test the new rocket engine"
   ]

   chosen_mission = random.choice(missions)
   print("🚀 Today's mission is:")
   print(chosen_mission)
   ```
2. Run it 5 times. Do you get a different mission each time? (You might see repeats sometimes — that's normal with randomness!)
3. Add 2 more missions of your own to the list.

---

## Activity 4: Fix the Index Error

**Steps:**
1. Type this broken code:
   ```python
   crew = ["Nova", "Max", "Zara"]
   print(crew[3])
   ```
2. Run it and read the `IndexError`.
3. Fix it by using a valid index (0, 1, or 2):
   ```python
   crew = ["Nova", "Max", "Zara"]
   print(crew[2])
   ```
4. Run it again to confirm the error is gone.

---

## Activity 5: Count and Position (Extension)

**Steps:**
1. Type:
   ```python
   crew = ["Nova", "Max", "Zara"]
   print(f"There are {len(crew)} crew members.")
   for i in range(len(crew)):
       print(f"Position {i}: {crew[i]}")
   ```
2. Run it and confirm the positions shown match the order of the names in the list.
