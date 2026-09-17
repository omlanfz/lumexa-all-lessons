# 🎨 Lesson 3: Printing and String Formatting

## 👋 Welcome Back!

Last time you learned about variables — little boxes that hold information. Today we learn a cool trick: mixing plain words together with variables in ONE smooth sentence, using something called an **f-string**.

---

## 🧠 Think Like a Coder

> An f-string is like a fill-in-the-blank sentence. The letter `f` before the quotes tells Python "get ready — some blanks need to be filled in with variable values!"

---

## 💻 Step 1: Your First F-String

Type and run:

```python
name = "Nova"
age = 10
print(f"Hello, {name}! You are {age} years old.")
```

You should see: `Hello, Nova! You are 10 years old.`

---

## 🤖 Robot Tip

If you forget the `f` before the quotes, Python will show the curly braces exactly as typed, like `Hello, {name}!` — not what we want!

---

## 💻 Step 2: Turning Input Into Real Numbers

`input()` always gives us text — even if it looks like a number! To do math with it, we use `int()`:

```python
age_text = input("How old are you? ")
age_number = int(age_text)
print(f"Next year you will be {age_number + 1}!")
```

---

## 🔍 Debug Detective

This code has a bug. Can you find it?

```python
age = input("How old are you? ")
print(f"Next year you will be {age + 1}!")
```

**Hint:** what does `input()` always give us — text or numbers?

**The Fix:**
```python
age = input("How old are you? ")
age = int(age)
print(f"Next year you will be {age + 1}!")
```
`input()` gives us text, so we must convert it with `int()` before adding numbers to it — otherwise Python gets confused and shows a `TypeError`.

---

## 💻 Step 3: Put It All Together

```python
name = input("What is your name? ")
age = input("How old are you? ")
age = int(age)
print(f"Hello, {name}! You are {age} years old.")
print(f"Next year you will be {age + 1}!")
```

---

## 🎮 Mini Challenge

Add a hobby question and mix it into a sentence:

```python
hobby = input("What is your favorite hobby? ")
print(f"{name} loves {hobby}!")
```

---

## ⭐ Bonus Challenge

Try math right inside the curly braces:

```python
print(f"In 10 years you will be {age + 10} years old!")
```

---

## 🎨 Make It Yours

Build "The Birthday Message Machine" — check `Activities.md` for the full steps!

---

## 🏆 Victory Moment

You just built a program that mixes real conversation with real math — that's a skill used in real apps every day!

## 🔮 Coming Up Next

In Lesson 4, we'll teach our programs to make **decisions** using `if` and `else` — so they can respond differently depending on what the player says!
