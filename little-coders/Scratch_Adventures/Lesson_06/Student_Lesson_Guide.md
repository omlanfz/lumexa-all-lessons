# 🚀 Lesson 6: If This, Then That — Sprites That Decide!

## Hi, coder! 👋

If it's raining, you take an umbrella. If it's sunny, you wear sunglasses. You just made a decision using a **condition**! Today, your sprites are going to learn to make decisions too.

## 🧠 Think Like a Coder: What's a Condition?

A condition is a yes-or-no question, like:
- "Is the sprite touching the edge?"
- "Is the space key pressed?"
- "Is the mouse button down?"

If the answer is YES (true), the code inside the "if" runs. If the answer is NO (false), it's skipped.

## Two Kinds of Decisions

- **"if <condition> then"** — only does something when the condition is true. Otherwise, nothing happens there.
- **"if <condition> then / else"** — picks between TWO paths, like a fork in a hiking trail. If true, go one way. If false, go the other way. You always pick exactly one.

## Try This: A Sprite That Bounces

1. Add **"when green flag clicked"** → **"forever."**
2. Inside forever, add **"move 10 steps."**
3. Right after that (still inside forever), add **"if <touching edge?> then"** with **"turn right 180 degrees"** inside it.
4. Click the green flag — your sprite moves and turns around whenever it hits the edge!

## Try This: If/Else Reaction

1. Add **"forever"** with **"if <key [space] pressed?> then / else"** inside.
2. In the TOP (then) branch: **"say [Jumping!] for 1 second."**
3. In the BOTTOM (else) branch: **"say [Waiting...] for 1 second."**
4. Click the green flag, then press and release the spacebar — watch your sprite's message change!

## ⭐ Bonus Challenge

Add a SECOND condition, like "if touching [another sprite] then," so your sprite reacts differently when it touches a friend sprite versus just the edge of the stage.

## 🔍 Debug Detective

Uh-oh! Someone's sprite is supposed to say "Ouch!" only when it touches the edge, but it says "Ouch!" ALL the time, even in the middle of the stage! What could be wrong? (Hint: check the hexagon-shaped condition slot inside the "if" block — is "touching edge?" actually plugged in there?)

## 🏆 Victory Moment

You just gave your sprite a BRAIN that makes decisions! Next time, we'll use everything you've learned — motion, looks, sound, events, loops, and conditionals — to build a real interactive story. See you in Lesson 7! 🚀
