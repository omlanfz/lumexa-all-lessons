# 📋 Lesson 6 Challenges

## 🎮 Mini Challenge: Grow the Crew

Add a 4th crew member to the list and confirm the loop shows them automatically:

```python
crew = ["Nova", "Max", "Zara", "Kai"]
for member in crew:
    print(f"Crew member: {member}")
```

---

## ⭐ Bonus Challenge: Count and Show Positions

Use `len()` and `range()` together to show every crew member's position number:

```python
crew = ["Nova", "Max", "Zara"]
print(f"There are {len(crew)} crew members.")
for i in range(len(crew)):
    print(f"Position {i}: {crew[i]}")
```

Can you make it print only the crew member at the LAST position, using `crew[len(crew) - 1]`?

```python
print(f"The last crew member is {crew[len(crew) - 1]}")
```

---

## 🏠 Take-Home Challenge: What's for Dinner?

Write a list of your 5 favorite foods, then let Python pick one randomly:

```python
import random

foods = ["pizza", "tacos", "sushi", "pasta", "burgers"]
choice = random.choice(foods)
print(f"Tonight's dinner is... {choice}!")
```

If you have a computer at home, run it a few times and see what Python picks! Bring your favorite foods list to share next class.
