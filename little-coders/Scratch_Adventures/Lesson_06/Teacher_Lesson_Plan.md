# Lesson 6: If This, Then That — Conditionals and Decisions 🚀

## Big Idea
Conditionals let our sprites make DECISIONS — "if this is true, do this; otherwise, do that" — just like the choices we make every day.

## Learning Objectives
- Understand what a "condition" is (a question with a yes/no, true/false answer).
- Use "if <condition> then" blocks to run code only when something is true.
- Use "if <condition> then / else" to choose between two different actions.
- Use sensing blocks (touching, key pressed, mouse down) as conditions.
- Combine conditionals with loops (from Lesson 5) to check something repeatedly.

## Teacher Preparation
- Prepare a simple "if/else" real-life example to demonstrate physically (e.g., "If it's raining, take an umbrella; else, wear sunglasses").
- Remind students: "Remember our loops from last time? Today we'll teach our sprites to make DECISIONS while they loop — like 'if you're touching the edge, turn around!'"

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`

## Teaching Flow (approx. 45 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 10 min
3. Teacher Demonstration — 8 min
4. Guided Coding — 10 min
5. Mini Challenge — 5 min
6. Main Activity — 5 min
7. Check for Understanding + Recap — 2 min

## Warm-up / Hook (5 min)
🚀 **Coding Mission:** Ask: "If it's raining outside, what do you do? If it's sunny, what do you do differently?" Let a few kids answer. Say: "You just used a CONDITIONAL in real life — 'if it's raining, THEN take an umbrella, ELSE wear sunglasses.' Today, our sprites are going to learn to make decisions just like that!"

## Concept Introduction (10 min)
- **Condition** — a question with only a TRUE or FALSE answer, like "is the sprite touching the edge?" or "is the space key pressed?"
- 🧠 **Think Like a Coder:** "A condition is like a yes-or-no question. If the answer is YES (true), the code inside the 'if' runs. If the answer is NO (false), it's skipped."
- **"if <condition> then"** — only runs the blocks inside if the condition is true; otherwise, does nothing there and moves on.
- **"if <condition> then / else"** — runs ONE set of blocks if true, and a DIFFERENT set if false. Analogy: "Two paths on a hiking trail — if the sign says left, you go left; else, you go right. You always pick exactly one path."
- Preview sensing blocks that work as conditions: "touching [mouse-pointer/edge/sprite]?", "key [space] pressed?", "mouse down?"

## Teacher Demonstration (8 min)
1. Build: "when green flag clicked" → "forever" → "if <touching edge?> then" → "turn right 15 degrees" — otherwise the sprite just keeps moving. Combine with a "move 10 steps" outside/before the if, inside the forever loop, to show a sprite bouncing off the edges intelligently.
2. Build an if/else: "if <key [space] pressed?> then / else" → say "Flying!" in the "then" branch, say "Walking..." in the "else" branch, inside a forever loop. Press and release space to show it switching live.
3. Emphasize: "Notice the sprite is CHECKING the condition over and over inside the loop — that's how it can react instantly."

## Guided Coding (10 min)
Students build alongside teacher:
1. "when green flag clicked" → "forever" containing: "move 10 steps," then "if <touching edge?> then" containing "turn right 180 degrees" (or "ifonedgebounce" equivalent taught as: if touching edge, turn around).
2. Separately, build: "forever" containing "if <key [space] pressed?> then / else" → then-branch: "say [Jumping!] for 1 second"; else-branch: "say [Waiting...] for 1 second."

## Mini Challenge (5 min)
Change the condition in your if/else script from "key space pressed" to "touching mouse-pointer?" and test moving your mouse near/onto the sprite.

## Main Activity / Project (5 min)
**"Bouncing Explorer":** Combine loop + conditional so a sprite continuously moves and turns around whenever it touches the edge of the stage, forever, creating a simple bouncing pattern. Add a sound that plays only when the "touching edge" condition is true.

## Fun Challenge (optional, harder) ⭐
Add a SECOND condition using "if <touching [another sprite]?> then" so your sprite reacts differently (says something, changes costume, plays a sound) when it touches a different sprite versus just the edge.

## Check for Understanding
- "What is a condition? Give an example of a real-life one."
- "What's the difference between 'if/then' and 'if/then/else'?"
- "Why do we usually put an 'if' block inside a loop instead of by itself?"
- "What sensing blocks can we use as conditions?"

## Common Student Mistakes (with fixes)
- **Mistake:** Putting the "if" block OUTSIDE the loop, so it only checks the condition ONE time instead of continuously. **Fix:** Show them: put the "if" INSIDE the forever loop so it checks again and again.
- **Mistake:** Forgetting to put a condition (leaving the hexagon slot empty) — Scratch treats an empty condition as false, so the "if" never runs. **Fix:** Drag a sensing block (like "touching edge?") into the hexagon-shaped slot.
- **Mistake:** Mixing up "then" and "else" branches (celebration message plays when something is FALSE instead of TRUE). **Fix:** Read it out loud together: "IF this is true, do the TOP branch; ELSE (if false), do the BOTTOM branch."
- 🔍 **Debug Detective:** "A student's sprite is supposed to say 'Ouch!' when it touches the edge, but it says 'Ouch!' the WHOLE time, non-stop, even when clearly not touching the edge. What might be wrong?" (Answer: check whether the "if" condition is actually connected — an empty hexagon defaults strangely, or perhaps they used "forever: say Ouch" without any "if" at all. Walk through re-checking the condition slot.)

## Differentiation
- **Struggling support:** Provide the "bouncing" script pre-built except for the condition slot, and have them just drag in "touching edge?" themselves.
- **Standard:** Full lesson as planned.
- **Extension for advanced learners:** Have them combine TWO conditions using the "and"/"or" operator blocks inside a single "if" (e.g., "if touching edge AND key space pressed") and predict what combination makes it true.

## Recap
"Today, your sprites learned to make DECISIONS — checking a yes/no question and choosing what to do next. That's exactly how real games decide when you've won, lost, or bumped into something!"

## Take-Home / Creative Challenge
Write down 3 "if/else" decisions from your own day (e.g., "if it's bedtime, then brush teeth, else keep playing"). Share one with the class next lesson.

## Teacher Talking Points
- "Conditionals are basically the sprite's brain — this is how it decides what to do next, just like you do all day long."
- "It's totally normal to mix up 'then' and 'else' at first — just read the block out loud like a sentence and it'll click."
- "You're building the exact same logic that real video games use to decide when a character wins or loses!"

## 🏆 Victory Moment
Have students demo their "Bouncing Explorer" to a partner and try to make it touch something to trigger their bonus reaction. Celebrate: "Your sprite just made its OWN decisions — you're basically giving it a brain now!"
