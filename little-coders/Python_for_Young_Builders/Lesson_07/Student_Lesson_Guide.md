# 🧩 Lesson 7: Building a Mini Quiz App

## 👋 Welcome Back!

Today is a big day — we combine EVERYTHING we've learned so far (variables, input, if/else, and counting) to build a real quiz app that keeps score!

---

## 🧠 Think Like a Coder

> A quiz app is just three tools working together: ask a question (`input()`), check the answer (`if`/`else`), and keep score (a counting variable).

---

## 💻 Step 1: A 2-Question Quiz

Type and run:

```python
score = 0

answer1 = input("What color do you get when you mix blue and yellow? ")
if answer1 == "green":
    print("Correct!")
    score = score + 1
else:
    print("Oops, the answer was green.")

answer2 = input("How many legs does a spider have? ")
if answer2 == "8":
    print("Correct!")
    score = score + 1
else:
    print("Oops, the answer was 8.")

print(f"You scored {score} out of 2!")
```

Try running it once answering both correctly, and once answering both wrong. Watch the score change!

---

## 🤖 Robot Tip

`score = score + 1` means "take whatever is in the score box right now, add 1 to it, and put the new total back in the box." It's like a scoreboard ticking up one point at a time.

---

## 🎮 Mini Challenge

Add tiered final feedback based on the score out of 3 (after adding your own 3rd question):

```python
if score == 3:
    print("Perfect score! You're a quiz champion! 🏆")
elif score >= 1:
    print("Good try! Keep practicing!")
else:
    print("Don't worry, let's try again next time!")
```

---

## 🔍 Debug Detective

This quiz code has a bug. Can you find it?

```python
answer2 = input("How many legs does a spider have? ")
if answer2 == 8:
    print("Correct!")
    score = score + 1
```

**Hint:** what type of value does `input()` always give — text or a number?

**The Fix:**
```python
answer2 = input("How many legs does a spider have? ")
if answer2 == "8":
    print("Correct!")
    score = score + 1
```
`input()` always returns text, so we must compare it to `"8"` (in quotes), not the number `8`.

---

## ⭐ Bonus Challenge

Ask for the player's name at the start, and use it in the final message:

```python
name = input("What is your name? ")
print(f"Great job, {name}! You scored {score} out of 5!")
```

---

## 🎨 Make It Yours

Build "The 5-Question Challenge" — check `Activities.md` for full steps! This is great practice for your Project 04: Personal Quiz App coming up soon.

---

## 🏆 Victory Moment

You just built a real, working quiz app using everything you've learned — variables, input, decisions, and counting, all working together!

## 🔮 Coming Up Next

In Lesson 8, our final lesson, you'll design and build your very own mini game from scratch, using everything you've learned in this whole course!
