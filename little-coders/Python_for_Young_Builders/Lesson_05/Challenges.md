# 🔁 Lesson 5 Challenges

## 🎮 Mini Challenge: Count From 1

Change a `for` loop so it counts from 1 to 5 instead of 0 to 4:

```python
for i in range(1, 6):
    print("Blast off in", i)
```

---

## ⭐ Bonus Challenge: Keep Going Until "Quit"

Build a loop that keeps asking for words until the player types "quit":

```python
answer = ""
while answer != "quit":
    answer = input("Type a word (or 'quit' to stop): ")
    print(f"You typed: {answer}")
print("Goodbye!")
```

Can you make it count how many words the player typed before quitting?

```python
answer = ""
word_count = 0
while answer != "quit":
    answer = input("Type a word (or 'quit' to stop): ")
    if answer != "quit":
        word_count = word_count + 1
print(f"You typed {word_count} words before quitting!")
```

---

## 🏠 Take-Home Challenge: Print It 5 Times

Write a `for` loop that prints "I love coding!" five times:

```python
for i in range(5):
    print("I love coding!")
```

If you have a computer at home, run it and count the lines of output to make sure there are exactly 5. Try changing the number to print it 10 times instead!
