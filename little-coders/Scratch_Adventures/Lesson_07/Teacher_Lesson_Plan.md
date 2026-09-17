# Lesson 7: Lights, Camera, Code! Building an Interactive Story 🚀

## Big Idea
We can combine EVERYTHING we've learned — motion, looks, sound, events, broadcasting, loops, and conditionals — to tell a story that reacts to the player.

## Learning Objectives
- Plan a simple story with a beginning, middle, and end using a storyboard.
- Use multiple backdrops (or costume changes) to represent different "scenes."
- Use broadcasting to move the story from one scene to the next.
- Use say/think blocks to write dialogue for characters.
- Add at least one moment where the player's action (a key press or click) changes what happens next.

## Teacher Preparation
- Prepare a simple storyboard template (3 boxes: Beginning / Middle / End) on paper or the whiteboard.
- Remind students: "Remember broadcasting from Lesson 4? Today it becomes our secret tool for moving between scenes in a story, like turning the page in a book!"
- Have the class's example animal-story project (Project 01 in this course) ready to show as inspiration, without giving away every detail.

## Required Files
- `Student_Lesson_Guide.md`
- `Activities.md`
- `Challenges.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 8 min
3. Teacher Demonstration — 10 min
4. Guided Coding — 15 min
5. Mini Challenge — 5 min
6. Check for Understanding + Recap — 5 min
7. Wrap toward take-home planning — 2 min

## Warm-up / Hook (5 min)
🚀 **Coding Mission:** Ask: "What's your favorite story? Does it have a beginning, middle, and end?" Let a few students share. Say: "Today, YOU are going to be the author AND the coder of your own interactive story — one that can even change depending on what the player does!"

## Concept Introduction (8 min)
- **Storyboard** — a simple plan (like a comic strip) showing what happens in each part of your story before you start coding.
- **Scenes** — different "chapters" of your story, shown using different backdrops or costume changes.
- **Broadcasting to change scenes** — Remind them: "Remember our walkie-talkie broadcasts? We'll use a broadcast like 'next page' to move our whole story from Scene 1 to Scene 2."
- **Dialogue** — using "say" and "think" blocks to give characters personality and move the plot forward.
- **Interactive moment** — a spot in the story where the player's choice (a key press, a click, an answer) changes what happens, using an "if/else" (Lesson 6 connection).
- 🧠 **Think Like a Coder:** "A good interactive story isn't just a movie that plays by itself — it listens to the player, just like our sprites listened to key presses back in Lesson 4."

## Teacher Demonstration (10 min)
1. Sketch a 3-box storyboard on the board: Scene 1 (character wakes up), Scene 2 (character finds a problem), Scene 3 (character solves it, happy ending).
2. Build live: Sprite "when green flag clicked" → say something → wait 2 seconds → "broadcast [scene 2]."
3. On the backdrop (Stage): "when I receive [scene 2]" → "switch backdrop to [backdrop2]" (or, if using costume-based scenes on a single sprite, "switch costume to...").
4. Add one interactive choice: "when green flag clicked" → after some dialogue → "if <key [space] pressed?> then / else" → then: say "You found the treasure!"; else: say "Keep looking..." Show it live, pressing/not pressing space.

## Guided Coding (15 min)
Students plan first (storyboard, 5 min), then build (10 min):
1. Add a "when green flag clicked" script to their main character: opening dialogue with 2–3 say blocks (with waits between using "wait 1 second" or "say for 2 seconds").
2. End the opening script with "broadcast [scene 2]."
3. On the Stage or another sprite: "when I receive [scene 2]" → change backdrop/costume + new dialogue.
4. Add ONE interactive moment somewhere using a key press or click condition with if/else, giving two different story outcomes.

## Mini Challenge (5 min)
Add a THIRD scene using another broadcast (like "scene 3") that wraps up the story with an ending message.

## Main Activity / Project
(This lesson's guided coding + mini challenge together form the main activity — a complete 2–3 scene interactive story draft, which students will polish and expand in Lesson 8's final project work and in Portfolio Project 01.)

## Fun Challenge (optional, harder) ⭐
Add a second character sprite who also reacts to the same scene broadcasts, so more than one character is "on stage" and talking during your story.

## Check for Understanding
- "What are the three main parts of a story, and how did you plan them before coding?"
- "How did you move your story from one scene to the next?"
- "Where in your story did the player's choice change what happened?"
- "Why is planning with a storyboard helpful before you start coding?"

## Common Student Mistakes (with fixes)
- **Mistake:** Diving straight into coding without planning, leading to a confusing story with no clear scenes. **Fix:** Require a quick storyboard sketch (even 3 stick-figure boxes) before opening Scratch.
- **Mistake:** Using "say" blocks without any "wait," so dialogue flashes by too fast to read. **Fix:** Use "say [...] for 2 seconds" instead of a bare "say" block so there's built-in time to read it.
- **Mistake:** Forgetting that ALL sprites/stage need "when I receive [scene X]" to actually react — silence in later scenes. **Fix:** Check each sprite that should do something in Scene 2 has its own "when I receive [scene 2]" block.
- 🔍 **Debug Detective:** "A student's Scene 2 dialogue never appears, even though they added a 'broadcast [scene 2]' block in Scene 1. What could be wrong?" (Answer: check whether the receiving sprite has a matching 'when I receive' block, picked from the dropdown, spelled and cased exactly the same as the broadcast being sent.)

## Differentiation
- **Struggling support:** Limit to 2 scenes and one piece of dialogue per scene; provide a pre-made simple storyboard template to fill in.
- **Standard:** Full 3-scene interactive story as planned.
- **Extension for advanced learners:** Add a second interactive choice point, or a "Score" variable (preview of Project 02) that changes based on the player's choices throughout the story.

## Recap
"Today, you became a real interactive storyteller — planning a story, coding scenes with broadcasts, giving characters dialogue, and letting the player's choices change what happens!"

## Take-Home / Creative Challenge
Think of an ending TWIST for your story — something surprising that could happen in a Scene 3 or Scene 4 you haven't built yet. Sketch it out to bring to the next lesson.

## Teacher Talking Points
- "You're not just coding anymore — you're an author AND a programmer. That's a huge combination of skills!"
- "It's totally fine if your first story is short — real authors write many drafts too."
- "I love seeing everyone's stories look so different — that's the best part of being a creative coder."

## 🏆 Victory Moment
Have a few volunteers perform their interactive story live for the class, including the choice moment. Celebrate: "You just built something that reacts to a real player — that's exactly how video games work!"
