# 🎮 Lesson 8 Challenges

## 🎮 Mini Challenge: Replay Loop

Add a `while` loop so your Lucky Number Game can be played again and again:

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

## ⭐ Bonus Challenge: Limited Attempts

Give the player only 3 tries to guess correctly:

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

## 🏠 Take-Home Challenge: Show Your Game

Finish and polish your own original mini game at home. Show it to a family member and ask them to play it. Write down one thing they suggested you add or change, and bring your game AND their feedback to share next class!
