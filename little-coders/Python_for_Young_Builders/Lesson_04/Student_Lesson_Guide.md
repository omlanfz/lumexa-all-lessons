# 🔀 Lesson 4: If/Else Decisions

## 👋 Welcome Back!

Remember the "if/else" block in Scratch? Today we teach Python to make decisions too — choosing between two (or more!) paths depending on what's true.

---

## 🧠 Think Like a Coder

> `if` is like a fork in the road. If something is TRUE, we go one way. If it's FALSE, `else` sends us the other way.

---

## 💻 Step 1: Your First Decision

Type and run:

```python
age = int(input("How old are you? "))
if age >= 10:
    print("You are old enough for the big kid missions!")
else:
    print("You'll be ready for big missions soon!")
```

Notice two important things:
1. There's a **colon `:`** at the end of the `if` and `else` lines.
2. The `print()` lines underneath are **indented** (pushed in a little).

---

## 🤖 Robot Tip

Python cares A LOT about indentation. Everything "inside" an `if` must line up with the same amount of space. Thonny usually indents automatically after you type a colon and press Enter!

---

## 💻 Step 2: Adding a Third Option With `elif`

```python
score = int(input("What score did you get (0-100)? "))
if score >= 90:
    print("Amazing! You got an A!")
elif score >= 70:
    print("Great job! You got a B!")
else:
    print("Keep practicing, you'll get there!")
```

`elif` means "else, if this other thing is true" — it lets us check more than two options.

---

## 🔍 Debug Detective

This code has TWO bugs. Can you find both?

```python
age = int(input("How old are you? "))
if age >= 10
print("You are old enough!")
else:
print("Not yet!")
```

**Hint:** check every `if`/`else` line ending, and check the lines underneath them.

**The Fix:**
```python
age = int(input("How old are you? "))
if age >= 10:
    print("You are old enough!")
else:
    print("Not yet!")
```
Bug 1: the `if` line was missing its colon `:`. Bug 2: the `print()` lines under `if` and `else` weren't indented.

---

## 🎮 Mini Challenge

Add a third case using `elif` for when the age is EXACTLY 10:

```python
age = int(input("How old are you? "))
if age > 10:
    print("You are old enough for the big kid missions!")
elif age == 10:
    print("You just became old enough today!")
else:
    print("You'll be ready for big missions soon!")
```

Notice: `==` (two equal signs) checks if two things are equal — it's different from `=`, which stores a value!

---

## ⭐ Bonus Challenge

Combine two conditions using `and`:

```python
age = int(input("How old are you? "))
has_permission = input("Do you have a permission slip? (yes/no) ")
if age >= 10 and has_permission == "yes":
    print("You can join the advanced mission!")
else:
    print("Sorry, you need to be 10+ AND have a permission slip.")
```

---

## 🎨 Make It Yours

Build "The Mood Ring Machine" — check `Activities.md` for full steps!

---

## 🏆 Victory Moment

You just gave your program the power to make decisions — that's the same skill behind every quiz app, game, and website that responds to what you do!

## 🔮 Coming Up Next

In Lesson 5, we'll learn about **loops** — how to make a program repeat actions over and over without retyping the same code again and again!
