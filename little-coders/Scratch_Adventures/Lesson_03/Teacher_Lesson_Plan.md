# Lesson 3: Move It, Look It, Sound It! 🎨

## Big Idea
Motion, Looks, and Sound blocks are the three basic superpowers coders use to bring any sprite to life.

## Learning Objectives
- Use motion blocks (move, turn, go to x/y, glide-like changes) to move a sprite around the stage.
- Use looks blocks (say, think, switch costume, change size, show/hide) to change how a sprite appears.
- Use sound blocks (play sound, play sound until done, change volume) to add sound effects.
- Combine all three block types into one working script.
- Understand the x/y coordinate grid as "an address" for where a sprite stands on stage.

## Teacher Preparation
- Review x/y coordinates: x is left/right (negative = left, positive = right), y is up/down (negative = down, positive = up), center of stage is (0,0).
- Prepare a large printed or drawn grid (or draw one on the whiteboard) to demonstrate x/y visually before going on the computer.
- Remind students: "Remember Lesson 2, when we made our sprite move and talk for the first time? Today we go MUCH further with all three superpowers together."

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
🚀 **Coding Mission:** Play a quick "Simon Says" style game: "Simon says take one step right! Simon says turn around! Simon says freeze and shout 'Hooray!'" Connect it: "That's exactly what motion, looks, and sound blocks do for our sprites — just like your body just moved, turned, and made a sound!"

## Concept Introduction (10 min)
- **Motion blocks (blue):** move the sprite around — think of them as "sprite feet." Introduce the x/y grid: "Imagine the stage is a giant map. x tells you left or right, y tells you up or down. The very middle of the stage is (0,0)."
- **Looks blocks (purple):** change how the sprite appears or what it says — think of them as "sprite face and clothes." Costumes are like different outfits or poses.
- **Sound blocks (pink/magenta):** make the sprite make noise — think of them as "sprite voice."
- 🧠 **Think Like a Coder:** "A sprite with only motion blocks can move, but it can't tell you how it feels. A sprite with only looks blocks can talk, but it can't go anywhere. Real characters need ALL of these together — just like you use your legs, face, and voice together every day!"

## Teacher Demonstration (8 min)
1. Show "move 10 steps" vs "go to x: 0 y: 0" — explain the difference (move is relative/step-by-step, goto is an exact teleport to a spot).
2. Show "turn right 15 degrees" a few times in a row, and how the sprite spins.
3. Show "switch costume to [costume2]" and "next costume," pointing out the Costumes tab where new looks are drawn/chosen.
4. Show "change size by 10" a few times, then "set size to 100" to reset.
5. Show "play sound [meow] until done" — explain the difference between "play sound" (keeps going) and "play sound until done" (waits for it to finish before the next block runs).

## Guided Coding (10 min)
Students build, step by step, alongside the teacher:
1. "go to x: -100 y: 0" (start position, like home base)
2. "move 10 steps" repeated by clicking, OR a single "move 50 steps"
3. "next costume"
4. "play sound [pop] until done"
5. "say [Wheee!] for 2 seconds"
Run the full stack and watch it all happen together.

## Mini Challenge (5 min)
Rearrange the ORDER of your blocks (say first, then move, then sound) and predict what changes before running it. Discuss: does order matter? (Yes! Code runs top to bottom.)

## Main Activity / Project (5 min)
**"Sprite Show":** Students create a short 4–6 block sequence combining at least one motion block, one looks block, and one sound block, telling a tiny "moment" (like a sprite waking up, stretching, and yawning with a sound).

## Fun Challenge (optional, harder) ⭐
Use "glide"-style repeated small moves plus a costume change on each step to make your sprite look like it's really walking (move a little, switch costume, move a little, switch costume again).

## Check for Understanding
- "What's the difference between 'move' and 'go to x/y'?"
- "What block would you use to make a sprite look surprised or happy?"
- "What happens if you use 'play sound' instead of 'play sound until done' right before a 'say' block?"
- "Does the order of blocks in a stack matter? Why?"

## Common Student Mistakes (with fixes)
- **Mistake:** Confusing "move" (relative) with "go to x/y" (absolute position). **Fix:** Show side by side: click move 3 times in a row (sprite keeps going) vs go-to-x-y 3 times with the same numbers (sprite doesn't move after the first click, because it's already there!).
- **Mistake:** Sprite "disappears" off the edge of the stage. **Fix:** Use "go to x:0 y:0" to bring it back, and discuss the stage boundaries (roughly x: -240 to 240, y: -180 to 180).
- **Mistake:** Using "play sound" (not "until done") before a say block, so the say block runs immediately and cuts the sound off in perception. **Fix:** Show them "play sound until done" so blocks below wait their turn.
- 🔍 **Debug Detective:** "A student's sprite is supposed to grow bigger every time it's clicked, but it just keeps getting bigger and bigger forever, off the screen! What happened?" (Answer: they used "change size by" repeatedly without ever resetting with "set size to 100" — walk through adding a reset step.)

## Differentiation
- **Struggling support:** Provide a pre-built partial stack (move + say) and have them add just ONE sound block.
- **Standard:** Full guided + main activity as planned.
- **Extension for advanced learners:** Challenge them to make a sprite "walk in a square" using four move + turn combinations (move, turn right 90, move, turn right 90, move, turn right 90, move, turn right 90) and predict the shape before running it.

## Recap
"Today, your sprites got THREE new superpowers: moving with motion blocks, changing looks with looks blocks, and adding sound with sound blocks. You even discovered that the ORDER of blocks changes what happens!"

## Take-Home / Creative Challenge
At home (no computer needed), act out a tiny 3-part "show" using your own body: one movement, one facial expression or costume change (like putting on a hat), and one sound effect you make with your voice. Perform it for a family member!

## Teacher Talking Points
- "You just made your sprite do three things at once — moving, looking different, AND making sound. That's a real animated character!"
- "It's totally fine if your sprite flies off the stage — that just means we learned something about the x/y map together."
- "I noticed some of you are already predicting what a block will do BEFORE clicking it. That's exactly how real coders think!"

## 🏆 Victory Moment
Have a few volunteers show their "Sprite Show" to the class. Celebrate every single one: "Look at that — three superpowers, one awesome sprite moment!"
