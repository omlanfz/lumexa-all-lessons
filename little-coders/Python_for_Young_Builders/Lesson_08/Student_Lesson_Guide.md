# 🎮 Lesson 8: Final Project — Our Own Mini Game!

## 👋 Welcome to Your Final Mission!

You've learned SO much: variables, input, if/else decisions, loops, and lists. Now it's time to use ALL of it to build your very own original mini game!

---

## 🧠 Think Like a Coder

> Great game designers PLAN before they code. Before typing anything, think: what does the player do? What are the rules? How do they win or lose?

---

## 💻 Step 1: A Model Game — The Lucky Number Game

Let's look at an example game together:

```python
import random

secret_number = random.randint(1, 5)
guess = int(input("Guess a number between 1 and 5: "))

if guess == secret_number:
    print("🎉 You guessed it! You win!")
else:
    print(f"Not quite! The number was {secret_number}. Try again next time!")
```

Run it a few times. Sometimes you'll win, sometimes you won't — that's the randomness at work!

---

## 🤖 Robot Tip

`random.randint(1, 5)` picks a random WHOLE number between 1 and 5, including both 1 and 5. Remember to `import random` at the very top of your file!

---

## 🎮 Mini Challenge

Add a replay loop so the player can guess again and again:

```python
import random

play_again = "yes"
while play_again == "yes":
    secret_number = random.randint(1, 5)
    guess = int(input("Guess a number between 1 and 5: "))
    if guess == secret_number:
        print("🎉 You guessed it! You win!")
    else:
        print(f"Not quite! The number was {secret_number}.")
    play_again = input("Play again? (yes/no) ")

print("Thanks for playing!")
```

---

## 🎨 Make It Yours: Design Your Own Game!

Use the Game Planning Sheet in `Activities.md` to design your OWN original game. Here are your building blocks:

- **Variables** — to store information
- **input()** — to ask the player questions
- **if/elif/else** — to make decisions
- **while / for loops** — to repeat things
- **lists + random** — to add variety and surprises

Some ideas to inspire you:
- A rock-paper-scissors game against the computer
- A "would you rather" question game
- A treasure hunt where the player guesses a hidden location
- Your very own version of the Word Guessing Game from Project 06!

---

## ⭐ Bonus Challenge

Add a scoring system or limited number of attempts to your game, like this:

```python
import random

secret_number = random.randint(1, 5)
attempts = 3
guess_count = 0
won = False

while guess_count < attempts and not won:
    guess = int(input(f"Guess a number 1-5 (attempt {guess_count + 1} of {attempts}): "))
    guess_count = guess_count + 1
    if guess == secret_number:
        print("🎉 You guessed it! You win!")
        won = True
    else:
        print("Not quite, try again!")

if not won:
    print(f"Out of attempts! The number was {secret_number}.")
```

---

## 🔍 Debug Detective

Even the best coders make mistakes! When you test your own game:
1. Run it at least 3 times.
2. Try answers that should win AND answers that should lose.
3. If you see a red error message, read it carefully — it usually tells you exactly which line has the problem.
4. Fix one thing at a time, then run it again.

---

## 🏆 Victory Moment

You just built your very own game — completely from your own ideas — using everything you learned in this whole course. You are officially a Python game builder!

## 🔮 What's Next?

You've completed all 8 lessons! Now it's time to build your 3 big portfolio projects: a Personal Quiz App, a Simple Calculator, and a Word Guessing Game. Check them out in the `Projects` folder — you're ready for all of them!
