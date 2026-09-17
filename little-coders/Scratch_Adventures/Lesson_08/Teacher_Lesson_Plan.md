# Lesson 8: Game On! Final Project — Our Own Game 🚀

## Big Idea
Now it's time to use everything you've learned to design and build your very own real, playable Scratch game.

## Learning Objectives
- Independently plan a simple game (goal, controls, win/lose condition).
- Combine motion, looks, sound, events, broadcasting, loops, and conditionals into one working project.
- Use a Score variable to track progress or points.
- Test and debug their own project, fixing at least one real bug.
- Present their finished game to classmates.

## Teacher Preparation
- Review all previous lesson concepts briefly (motion/looks/sound, events/broadcast, loops, conditionals) as a quick "toolbox recap" poster or verbal list.
- Prepare simple game-idea cards in case some students are stuck for ideas (e.g., "catch the falling stars," "avoid the moving obstacle," "collect 5 coins").
- Set expectations: this is a full project lesson (or two sessions) with less direct instruction and more independent/guided building time.

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`

## Teaching Flow (approx. 50–60 minutes, can extend across two sessions)
1. Warm-up / Hook — 5 min
2. Concept Introduction (Toolbox Recap) — 8 min
3. Teacher Demonstration (mini example game) — 10 min
4. Guided Planning — 5 min
5. Main Activity: Build Your Game — 20 min
6. Fun Challenge / Polish time — 5 min
7. Check for Understanding + Recap + Sharing — 7 min

## Warm-up / Hook (5 min)
🚀 **Coding Mission:** Ask: "What is your all-time favorite simple game? What do you DO in it — move, collect things, avoid something?" Let a few students answer. Say: "Today, YOU get to design and build a real game like that, using every coding tool you've collected so far!"

## Concept Introduction — Toolbox Recap (8 min)
Quickly review, out loud, as a class "toolbox":
- 🔵 Motion — move your character
- 🟣 Looks — change appearance, say things
- 🟪 Sound — add effects and music
- 🟡 Events — react to key presses, clicks, and the green flag
- 🟠 Broadcasting — send messages between sprites/scenes
- 🔁 Loops — repeat actions
- 💎 Conditionals — make decisions
- 🧮 Variables — remember a Score or count (new/reinforced today)
🧠 **Think Like a Coder:** "A game is really just a story that keeps checking: did the player win? Did they lose? Should something happen now? That's loops + conditionals + variables working together."

## Teacher Demonstration (10 min)
Build a tiny example game live: a sprite that moves with arrow keys (or left/right), a "Score" variable that goes up when a key is pressed or a sprite is clicked, and an ending message when Score reaches a target number (using "if <Score = 5> then" inside a forever loop, followed by "stop all").
1. Create variable "Score," set to 0 on green flag.
2. "when this sprite clicked" → "change Score by 1" → "play sound until done."
3. In a forever loop: "if <Score = 5> then" → "say [You win!] for 2 seconds" → "stop [all]."
4. Run it, click the sprite 5 times, show the win message and the game stopping.

## Guided Planning (5 min)
Each student (or pair) fills out a simple plan:
- **My game's goal:** (What does the player try to do?)
- **Controls:** (What keys/clicks does the player use?)
- **Win condition:** (What happens when they succeed?)
- **At least one sprite, one sound, one loop, one conditional, and one variable.**

## Main Activity: Build Your Game (20 min)
Students build their own game independently or in pairs, using the toolbox. Teacher circulates to help debug and encourage. Remind them to test often — click the green flag after every few new blocks, not just at the very end.

## Fun Challenge / Polish Time (5 min) ⭐
Add a sound effect for winning, a second sprite (an obstacle or a friend), or a background backdrop that fits the game's theme.

## Check for Understanding (spoken, during share-out)
- "What is your game's goal, and how does the player win?"
- "Where did you use a loop? Where did you use a conditional?"
- "What was one bug you found, and how did you fix it?"

## Common Student Mistakes (with fixes)
- **Mistake:** Forgetting to reset the Score variable to 0 at the start (green flag), so it keeps climbing from the last test. **Fix:** Add "set Score to 0" right after "when green flag clicked."
- **Mistake:** Win condition check placed outside any loop, so it's only checked once and never notices when Score actually reaches the target. **Fix:** Put the "if Score = [target]" check inside a "forever" loop so it's always watching.
- **Mistake:** Too big a game idea for the time available, leading to frustration. **Fix:** Help them scale down to ONE clear goal and ONE win condition first; extra features become the "Fun Challenge."
- 🔍 **Debug Detective:** "A student's Score variable never changes, even though they keep clicking their sprite and there's a 'change Score by 1' block. What could be wrong?" (Answer: check that the 'change Score by 1' block is actually connected to a 'when this sprite clicked' hat block, and that the SHOW VARIABLE checkbox is checked so they can even see the score updating.)

## Differentiation
- **Struggling support:** Offer a game-idea card with a pre-planned goal/controls/win-condition, and pair them with a peer helper; focus on getting ONE full loop + ONE conditional + ONE variable working.
- **Standard:** Full independent build as planned.
- **Extension for advanced learners:** Add a Lives variable in addition to Score, and a "lose" condition (e.g., if Lives reaches 0, say "Game Over" and stop all), creating both a win AND a lose path.

## Recap
"Today you built a REAL game from scratch — pun intended! — using motion, looks, sound, events, broadcasting, loops, conditionals, and variables, ALL together. You are officially Scratch Adventurers!"

## Take-Home / Creative Challenge
Show your finished game to a family member and watch them play it. Ask them for one piece of feedback — what did they enjoy, and what's one thing you could add next time?

## Teacher Talking Points
- "Look at everything you just combined — that's eight lessons' worth of coding skills, all working together in one project!"
- "Bugs are not failures — every single game you'll ever play, even famous ones, had bugs the coders had to fix. You're doing exactly what real developers do."
- "I am so proud of the games in this room today — every single one is different, and that's exactly what makes them special."

## 🏆 Victory Moment
Host a mini "Game Arcade" — let each student demo their game for 30 seconds to the class or in small groups. Celebrate: "You are now official Scratch Adventurers — you built a real game, from an idea in your head to a project you can share with anyone!"
