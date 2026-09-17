# Lesson 4: React and Message — Events and Broadcasting 🚀

## Big Idea
Events let sprites REACT to things (like a key press or a click), and broadcasting lets sprites send secret messages to tell each other when to do something.

## Learning Objectives
- Understand that "hat blocks" (like "when green flag clicked") start a script based on an event.
- Use "when key pressed" and "when this sprite clicked" to make sprites react to the player.
- Understand broadcasting as a way for one sprite to "shout a message" that other sprites can hear and react to.
- Use "broadcast" and "when I receive" to coordinate two or more sprites/scenes.

## Teacher Preparation
- Prepare a simple two-sprite demo project ahead of time (or build live): Sprite A broadcasts "jump," Sprite B receives "jump" and reacts.
- Remind students: "Remember when your sprite could move, look different, and make sound? Today, your sprite is going to REACT to YOU pressing keys — and even talk to OTHER sprites!"

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`

## Teaching Flow (approx. 45 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 10 min
3. Teacher Demonstration — 10 min
4. Guided Coding — 10 min
5. Mini Challenge — 5 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook (5 min)
🚀 **Coding Mission:** Play "Clap Response": Teacher claps once, and everyone must jump. Teacher claps twice, and everyone must spin. Explain: "You all just REACTED to an event — my clap! In Scratch, sprites can react to events too, like a key being pressed or the green flag being clicked."

## Concept Introduction (10 min)
- **Hat blocks** — special blocks with a rounded top that START a script when something happens. "When green flag clicked" starts when you press the green flag. "When key pressed" starts when a specific key is pressed. "When this sprite clicked" starts when you click directly on the sprite.
- 🧠 **Think Like a Coder:** "A hat block is like a doorbell. Nothing happens until someone rings it (the event happens) — then the whole script underneath runs."
- **Broadcasting** — Explain with an analogy: "Imagine you're on a playground with walkie-talkies. If you shout 'GO!' into your walkie-talkie, everyone with a walkie-talkie tuned to that channel hears it and can react, even if they're far away. In Scratch, 'broadcast' shouts a message, and 'when I receive' is like a sprite listening on that channel."
- Explain broadcast vs broadcast-and-wait: broadcast just shouts and moves on immediately; broadcast-and-wait shouts, then waits for everyone who heard it to finish reacting before continuing.

## Teacher Demonstration (10 min)
1. Build a sprite with "when key pressed [space]" → "say [Jump!] for 1 second." Press spacebar, show it works.
2. Build a sprite with "when this sprite clicked" → "play sound [pop] until done." Click the sprite directly.
3. Build a TWO-sprite broadcast demo: Sprite A has "when green flag clicked" → "broadcast [next scene]." Sprite B has "when I receive [next scene]" → "switch costume to [costume2]" and/or "say [Scene 2!]". Click green flag, show both sprites react even though only Sprite A had the broadcast block.
4. Emphasize: "Sprite A didn't tell Sprite B directly what to do — it just shouted a message, and Sprite B decided to listen and react on its own."

## Guided Coding (10 min)
Students build, step by step:
1. On their main sprite: "when key pressed [space]" → "move 20 steps."
2. On the same sprite: "when this sprite clicked" → "say [You clicked me!] for 2 seconds."
3. Add a second sprite (or use one already in their project). On the first sprite: "when green flag clicked" → "broadcast [wave hello]." On the second sprite: "when I receive [wave hello]" → "say [Hi there!] for 2 seconds."
4. Test with the green flag and by pressing keys/clicking.

## Mini Challenge (5 min)
Add a second key-press event to your sprite (like the up arrow) that does something different from your first one (like the down arrow). Test both.

## Main Activity / Project
(Folded into Guided Coding above for this lesson, given the shorter block focus — the two-sprite broadcast is the main hands-on build.)

## Fun Challenge (optional, harder) ⭐
Create a chain of THREE broadcasts: Sprite A broadcasts "scene 2," which Sprite B receives and reacts to AND itself broadcasts "scene 3," which Sprite C receives and reacts to. This creates a simple relay!

## Check for Understanding
- "What is a hat block, and why do we call it that?"
- "What's the difference between clicking a sprite and pressing a key, as far as events go?"
- "If Sprite A broadcasts 'jump,' does Sprite A have to tell Sprite B directly what to do?"
- "What's the difference between 'broadcast' and 'broadcast and wait'?"

## Common Student Mistakes (with fixes)
- **Mistake:** Typing a NEW broadcast message name on the receiving sprite that doesn't exactly match the one being sent (like "Jump" vs "jump 2"). **Fix:** Show students how to click the dropdown on "when I receive" and pick the EXACT existing broadcast from the list instead of typing a new one by accident.
- **Mistake:** Forgetting broadcasts are heard by ALL sprites listening, not just one. **Fix:** Demonstrate with three sprites all listening to the same broadcast — show they ALL react at once.
- **Mistake:** Using "when key pressed" but pressing the wrong key because the dropdown wasn't changed from the default. **Fix:** Double-check the dropdown says the intended key.
- 🔍 **Debug Detective:** "A student built 'when I receive [go]' on Sprite B, and 'broadcast [Go]' on Sprite A — capital G. They click the green flag and Sprite B never reacts! What's wrong?" (Answer: In Scratch, if you pick from the dropdown menu it always matches, but if two DIFFERENT broadcast messages were created by typing new names separately, they're actually different messages even if they look similar. Fix: always select the same broadcast from the dropdown, never retype it.)

## Differentiation
- **Struggling support:** Focus only on one key-press event and one click event; skip broadcasting or do it as a guided pair activity with the teacher.
- **Standard:** Full lesson as planned.
- **Extension for advanced learners:** Build the three-broadcast relay chain from the Fun Challenge, and add a "broadcast and wait" somewhere to see how it changes timing versus a normal "broadcast."

## Recap
"Today your sprites learned to REACT — to key presses, to clicks, and even to secret messages from other sprites called broadcasts. This is how we'll build interactive stories and games!"

## Take-Home / Creative Challenge
Think of two friends passing a note in class as a "broadcast" — one friend writes and passes it (broadcasts), the other reads and reacts (receives). Come up with your own funny "broadcast message" idea for a future Scratch project (like "surprise!" or "dance time!").

## Teacher Talking Points
- "Broadcasting is one of the coolest things in Scratch — it's how sprites who don't even know about each other can still work together."
- "It's totally normal for broadcast names to trip us up at first — just remember to always pick from the list, never retype."
- "You're building real interactive projects now — this is exactly how big games make characters react to each other!"

## 🏆 Victory Moment
Have students demo their two-sprite broadcast to a partner. Celebrate: "You just built a mini conversation between two sprites using nothing but code! That's a huge coder milestone."
