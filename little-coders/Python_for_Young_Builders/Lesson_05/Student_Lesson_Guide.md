# 🔁 Lesson 5: While Loops and For Loops

## 👋 Welcome Back!

Remember Scratch's "repeat" block, that let your sprite do something over and over without stacking the same block 10 times? Python has that superpower too — it's called a **loop**!

---

## 🧠 Think Like a Coder

> A loop is like telling a robot "do this again and again" — either a set number of times (`for`), or until something changes (`while`).

---

## 💻 Step 1: Your First For Loop

Type and run:

```python
for i in range(5):
    print("Blast off in", i)
```

Look closely at the output — it counts 0, 1, 2, 3, 4. That's still 5 numbers total — Python just starts counting at 0!

---

## 🤖 Robot Tip

`range(5)` means "5 numbers, starting at 0" — so it stops at 4, not 5! If you want to count 1 through 5, use `range(1, 6)` instead.

---

## 💻 Step 2: A Countdown

```python
for i in range(5, 0, -1):
    print(i)
print("Liftoff!")
```

This counts DOWN from 5 to 1, then prints "Liftoff!"

---

## 💻 Step 3: Your First While Loop

```python
count = 1
while count <= 3:
    print(f"Lap {count}")
    count = count + 1
```

A `while` loop keeps going as long as its condition is true. Here, it repeats while `count` is 3 or less.

---

## 🔍 Debug Detective

This code never stops running! Can you figure out why?

```python
count = 1
while count <= 3:
    print(f"Lap {count}")
```

**Hint:** does anything inside the loop ever change?

**The Fix:**
```python
count = 1
while count <= 3:
    print(f"Lap {count}")
    count = count + 1
```
The original code never changed `count`, so `count <= 3` was ALWAYS true — an infinite loop! We must update the variable being checked, or the loop never stops. (If this ever happens by accident, press the red Stop button in Thonny.)

---

## 🎮 Mini Challenge

Change the `for` loop to count from 1 to 5 instead of 0 to 4:

```python
for i in range(1, 6):
    print("Blast off in", i)
```

---

## ⭐ Bonus Challenge

Combine a `while` loop with `input()` to keep going until the player types "quit":

```python
answer = ""
while answer != "quit":
    answer = input("Type a word (or 'quit' to stop): ")
    print(f"You typed: {answer}")
print("Goodbye!")
```

---

## 🎨 Make It Yours

Build "The Rocket Countdown" — check `Activities.md` for full steps!

---

## 🏆 Victory Moment

You just taught your program to repeat itself without writing the same line over and over — a huge coding superpower!

## 🔮 Coming Up Next

In Lesson 6, we'll learn about **lists** — a way to store lots of information (like a whole crew of astronauts) in one single variable!
