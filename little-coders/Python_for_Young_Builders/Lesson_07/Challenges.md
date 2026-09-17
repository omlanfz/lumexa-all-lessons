# 🧩 Lesson 7 Challenges

## 🎮 Mini Challenge: Tiered Feedback

Add a final message that changes based on the score out of 3:

```python
if score == 3:
    print("Perfect score! You're a quiz champion! 🏆")
elif score >= 1:
    print("Good try! Keep practicing!")
else:
    print("Don't worry, let's try again next time!")
```

---

## ⭐ Bonus Challenge: Personalized Quiz

Ask for the player's name first, and use it throughout the quiz:

```python
name = input("What is your name? ")
print(f"Alright {name}, let's begin the quiz!")

score = 0
q1 = input("What is 5 + 5? ")
if q1 == "10":
    print(f"Nice work, {name}!")
    score = score + 1
else:
    print(f"Not quite, {name} — the answer was 10.")

print(f"{name}, you scored {score} out of 1!")
```

---

## 🏠 Take-Home Challenge: Write Your Own Quiz Questions

Think of a topic you love — animals, sports, space, or video games. Write down 2 quiz questions and their correct answers, in this format:

```python
question = "What is the fastest land animal?"
correct_answer = "cheetah"
```

If you have a computer at home, try adding your questions into the 5-Question Challenge quiz and running the whole thing. Bring your questions to share next class!
