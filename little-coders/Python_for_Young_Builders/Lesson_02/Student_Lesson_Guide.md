# 📦 Lesson 2: Variables and User Input

## 👋 Welcome Back, Builder!

Remember in Scratch, when you made a variable called `score` to remember your points? Python has variables too — they're like little labeled boxes that hold information for us!

---

## 🧠 Think Like a Coder

> A **variable** is a box with a label. We write something on a card, put it in the box, and later we can look inside the box to see what's there.

---

## 💻 Step 1: Create Your First Variable

Type and run:

```python
name = "Captain Nova"
print(name)
```

Notice: no quotes around `name` in the `print()` line! That's because we want what's **inside** the box, not the word "name" itself.

---

## 🤖 Robot Tip

The `=` sign in Python means **"store this value in the box"** — not "these are equal" like in math class.

---

## 💻 Step 2: Ask the Player a Question

We can use `input()` to ask a question and save the answer in a variable:

```python
name = input("What is your name, space traveler? ")
print(name)
```

Run it — Python will wait for you to type an answer, then print it back!

---

## 💻 Step 3: Collect More Information

```python
name = input("What is your name, space traveler? ")
age = input("How old are you? ")
print(name)
print(age)
```

---

## 🎮 Mini Challenge

Add a variable for your favorite planet, and print it too:

```python
planet = input("What is your favorite planet? ")
print(planet)
```

---

## 🔍 Debug Detective

This code has a bug. Can you spot it?

```python
name = input("What is your name? ")
print(nam)
```

**Hint:** Look very closely at the spelling in the second line.

**The Fix:**
```python
name = input("What is your name? ")
print(name)
```
The variable was called `name`, but the `print()` line tried to use `nam` — a typo! Variable names must be spelled exactly the same every time.

---

## ⭐ Bonus Challenge

Variables can hold numbers too, and Python can do math with them:

```python
score = 10
bonus = 5
total = score + bonus
print(total)
```

Run it — what number do you see? Try changing the numbers and running it again.

---

## 🎨 Make It Yours

Build your own "Space Traveler ID Card" using variables and `input()` — check the `Activities.md` file for the full activity!

---

## 🏆 Victory Moment

You just wrote a program that ASKS a question and REMEMBERS the answer — that's real interactive coding!

## 🔮 Coming Up Next

In Lesson 3, we'll learn how to print messages in fancier ways using **string formatting** — so our programs can mix words and variables together smoothly.
