# 🎮 Lesson 8 Activities: Final Project — Our Own Mini Game

## Activity 1: Play the Model Game

**Steps:**
1. Type and run:
   ```python
   import random

   secret_number = random.randint(1, 5)
   guess = int(input("Guess a number between 1 and 5: "))

   if guess == secret_number:
       print("🎉 You guessed it! You win!")
   else:
       print(f"Not quite! The number was {secret_number}. Try again next time!")
   ```
2. Run it 3 times, and count how many times you won versus lost.

---

## Activity 2: Add a Replay Loop

**Steps:**
1. Add a `while` loop so the game repeats until the player says "no":
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
2. Play at least twice, then type "no" and confirm the game ends politely.

---

## Activity 3: Game Planning Sheet (Main Activity)

**Goal:** Plan your own original mini game on paper (or in a text file) BEFORE coding it.

**Fill in the blanks:**
1. **Game name:** ______________________
2. **What does the player do?** (guess a number, answer a question, pick rock/paper/scissors, etc.) ______________________
3. **What information do we need from the player?** (use `input()`) ______________________
4. **What are the rules for winning?** (use `if`/`elif`/`else`) ______________________
5. **Will the game repeat?** (use a `while` loop) yes / no
6. **Will anything be random?** (use `random` and maybe a list) yes / no

---

## Activity 4: Build Your Game

**Steps:**
1. Open a new file in Thonny.
2. Using your planning sheet from Activity 3, write your game step by step:
   - Start with variables and any lists you need.
   - Add your `input()` questions.
   - Add your `if`/`elif`/`else` rules.
   - Add a loop if your game repeats.
3. Run your game and test it at least 3 times, trying different answers (including ones that should win AND ones that should lose).
4. If you find a bug, read the error message, fix one thing, and run it again.

---

## Activity 5: Share and Celebrate

**Steps:**
1. Show your finished game to a partner or the teacher.
2. Ask your partner to play it and give you one thing they liked and one idea for what to add next.
3. Celebrate — you just built your first original Python game! 🏆
