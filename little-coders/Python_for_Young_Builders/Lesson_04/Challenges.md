# 🔀 Lesson 4 Challenges

## 🎮 Mini Challenge: Exactly 10

Add a third case for when the age is exactly 10:

```python
age = int(input("How old are you? "))
if age > 10:
    print("You are old enough for the big kid missions!")
elif age == 10:
    print("You just became old enough today!")
else:
    print("You'll be ready for big missions soon!")
```

---

## ⭐ Bonus Challenge: Two Conditions at Once

Use `and` to check two things are both true:

```python
age = int(input("How old are you? "))
has_permission = input("Do you have a permission slip? (yes/no) ")
if age >= 10 and has_permission == "yes":
    print("You can join the advanced mission!")
else:
    print("Sorry, you need to be 10+ AND have a permission slip.")
```

Can you add an `or` version that lets someone in if they are 10+ OR have special teacher approval?

```python
teacher_approved = input("Does your teacher approve? (yes/no) ")
if age >= 10 or teacher_approved == "yes":
    print("You're in!")
```

---

## 🏠 Take-Home Challenge: The Riddle Gate

Design a riddle gate: ask a riddle question, and if the player's answer matches, let them "pass"; otherwise, they must "try again."

```python
answer = input("Riddle: I have keys but no locks. I have space but no room. What am I? ")
if answer == "keyboard":
    print("Correct! You may pass!")
else:
    print("Not quite — try again!")
```

If you have a computer at home, try running it and see if a family member can solve your riddle!
