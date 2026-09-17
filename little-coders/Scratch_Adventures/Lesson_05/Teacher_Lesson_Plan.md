# Lesson 5: Round and Round — Loops and Repetition 🚀

## Big Idea
Loops let us repeat instructions without writing them over and over again — coders are always looking for ways to avoid repeating themselves!

## Learning Objectives
- Understand what a loop is and why coders use loops instead of copy-pasting blocks.
- Use "repeat [10]" to run a set number of times.
- Use "forever" to run a script endlessly (and understand when that's useful vs. risky).
- Use "repeat until" to loop until a condition becomes true (light introduction, deepened in Lesson 6).
- Combine loops with motion/looks/sound to build simple animations.

## Teacher Preparation
- Prepare a short physical demonstration: clapping a rhythm 4 times, or bouncing a ball 5 times, to show "repeating the same action."
- Remind students: "Remember when your sprite could react to key presses and broadcasts? Today, we'll make it repeat actions automatically, instead of clicking a block over and over!"

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`

## Teaching Flow (approx. 45 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 8 min
3. Teacher Demonstration — 8 min
4. Guided Coding — 10 min
5. Mini Challenge — 5 min
6. Main Activity — 7 min
7. Check for Understanding + Recap — 2 min

## Warm-up / Hook (5 min)
🚀 **Coding Mission:** Ask the whole class to clap 5 times, count out loud together. Then ask: "What if I asked you to clap 100 times? Would you want to write out 'clap' one hundred separate times on paper, or would you rather just say 'clap 100 times'?" Introduce: "That second way — saying it once but doing it many times — is exactly what a LOOP does in coding!"

## Concept Introduction (8 min)
- **Loop** — a block that repeats the instructions inside it, so you don't have to copy-paste them over and over.
- **"repeat [10]"** — like saying "do this 10 times, then stop."
- **"forever"** — like saying "keep doing this forever, until I stop the whole program." Explain: forever loops never end on their own — good for things like a background animation that should always be happening, but risky if you forget you're inside one (nothing after a forever loop will ever run!).
- **"repeat until <condition>"** — a sneak peek: "This one repeats until something becomes TRUE, like 'repeat until touching the edge.' We'll dig deeper into conditions next lesson."
- 🧠 **Think Like a Coder:** "Coders are a little lazy in a GOOD way — if we're about to write the same instruction many times, we stop and ask: can a loop do this for me instead?"

## Teacher Demonstration (8 min)
1. Build without a loop: 5 separate "move 10 steps" blocks stacked. Run it, point out how long that took to build.
2. Delete those, build "repeat [5]" containing ONE "move 10 steps" block inside. Run it — same result, way less building!
3. Show "forever" containing "next costume" and "wait 0.2 seconds" — sprite animates continuously. Click the red stop sign to show how to stop it.
4. Give a light preview of "repeat until <touching edge>" containing "move 10 steps" — show the sprite moving until it reaches the edge, then stopping automatically.

## Guided Coding (10 min)
Students build alongside teacher:
1. "when green flag clicked" → "repeat [4]" containing "move 50 steps" and "turn right 90 degrees." Run it — the sprite walks in a SQUARE!
2. Add a "forever" loop containing "next costume" and "wait [0.3] seconds" on a separate hat block ("when green flag clicked") to make a simple continuous animation. Click the stop sign to end it.

## Mini Challenge (5 min)
Change the repeat count in your square-walking script to different numbers (try 3, then 6) and predict/observe what shape appears each time.

## Main Activity / Project (7 min)
**"Loopy Dance":** Students build a "repeat [8]" loop containing a move + turn + costume change, creating a fun spinning/dancing motion. Add a "play sound until done" inside the loop for a rhythmic beat.

## Fun Challenge (optional, harder) ⭐
Nest a small repeat loop INSIDE a bigger repeat loop (e.g., repeat 3 times: {repeat 4 times: {move + turn}}) and predict what shape or pattern results before testing it.

## Check for Understanding
- "What is a loop, and why do coders like using them?"
- "What's the difference between 'repeat 10' and 'forever'?"
- "What could go wrong if you put important blocks AFTER a forever loop?"
- "How did the repeat count change the shape your sprite walked in?"

## Common Student Mistakes (with fixes)
- **Mistake:** Putting blocks OUTSIDE the repeat/forever "mouth" of the loop instead of inside it. **Fix:** Show them the loop block has a C-shape — drag new blocks so they snap INSIDE the C, not underneath the whole loop.
- **Mistake:** Using "forever" when they meant "repeat 10," and getting confused why the sprite never stops. **Fix:** Ask: "Do you want this forever, or a certain number of times?" and swap blocks accordingly.
- **Mistake:** Expecting code AFTER a forever loop to run. **Fix:** Explain forever never finishes on its own, so nothing below it will ever run — if they need something to happen after, it shouldn't go after a forever loop.
- 🔍 **Debug Detective:** "A student's sprite was supposed to draw a square by repeating 'move + turn' four times, but instead it drew a triangle-ish shape or didn't close up! What could be wrong?" (Answer: check the turn angle — should be 90 degrees for a square repeated 4 times; also check the repeat count matches the number of sides wanted.)

## Differentiation
- **Struggling support:** Provide the "repeat 4 / move / turn 90" square script pre-built and have them just change the repeat number and observe the new shape.
- **Standard:** Full lesson as planned.
- **Extension for advanced learners:** Challenge them to figure out the turn angle needed for a triangle (120 degrees, repeated 3 times) or a hexagon (60 degrees, repeated 6 times) using math reasoning (360 ÷ number of sides).

## Recap
"Today you learned to use loops so you never have to repeat the same blocks over and over by hand. You made squares, dances, and endless animations — all with loops!"

## Take-Home / Creative Challenge
Look around your house or neighborhood for something that repeats a pattern (tiles on a floor, a fence, wallpaper). Draw it, and think about how you'd describe that pattern as a loop (e.g., "repeat 6 times: draw one tile").

## Teacher Talking Points
- "Loops are one of the most powerful ideas in ALL of coding — you just learned something real coders use every single day."
- "If your forever loop won't stop, don't panic — just click the red stop sign. That's totally normal!"
- "I love that you're already thinking in patterns — that's exactly the mindset of a real programmer."

## 🏆 Victory Moment
Have students share their "Loopy Dance" with a partner. Celebrate: "Look at all that repeating movement — and you only had to build it ONCE!"
