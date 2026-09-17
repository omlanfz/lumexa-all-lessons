# 📋 Lesson 6: Lists and Simple Data

## 👋 Welcome Back!

Remember Scratch's list variable, where you could keep adding items and check item #1, #2, #3? Python has lists too — and today we learn how to use them!

---

## 🧠 Think Like a Coder

> A list is like a row of connected boxes, all sharing one label, holding many pieces of information in order.

---

## 💻 Step 1: Create Your First List

Type and run:

```python
crew = ["Nova", "Max", "Zara"]
print(crew[0])
print(crew[1])
print(crew[2])
```

---

## 🤖 Robot Tip

Lists start counting at **0**, not 1! So `crew[0]` is the FIRST item ("Nova"), not the second.

---

## 🔍 Debug Detective

This code has a bug. Can you find it?

```python
crew = ["Nova", "Max", "Zara"]
print(crew[3])
```

**Hint:** how many items are in the list, and what are their valid index numbers?

**The Fix:**
```python
crew = ["Nova", "Max", "Zara"]
print(crew[2])
```
The list only has 3 items, so the valid indexes are 0, 1, and 2. Index 3 doesn't exist — that caused an `IndexError`!

---

## 💻 Step 2: Loop Through a List

```python
crew = ["Nova", "Max", "Zara"]
for member in crew:
    print(f"Crew member: {member}")
```

This prints every single item in the list, one at a time, without us needing to know how many there are!

---

## 💻 Step 3: Pick a Random Item

```python
import random

crew = ["Nova", "Max", "Zara"]
captain = random.choice(crew)
print(f"Today's captain is {captain}!")
```

Run it a few times — the captain changes randomly each time!

---

## 🎮 Mini Challenge

Add a 4th crew member and run the loop again to see it appear automatically:

```python
crew = ["Nova", "Max", "Zara", "Kai"]
for member in crew:
    print(f"Crew member: {member}")
```

---

## ⭐ Bonus Challenge

Use `len()` to find out how many items are in a list, and combine it with `range()`:

```python
crew = ["Nova", "Max", "Zara"]
print(f"There are {len(crew)} crew members.")
for i in range(len(crew)):
    print(f"Position {i}: {crew[i]}")
```

---

## 🎨 Make It Yours

Build "The Random Mission Picker" — check `Activities.md` for full steps!

---

## 🏆 Victory Moment

You just built a program that stores a whole group of information AND picks something random from it — that's how games choose random events and rewards!

## 🔮 Coming Up Next

In Lesson 7, we'll combine everything we've learned — variables, input, if/else, and more — to build a real **mini quiz app**!
