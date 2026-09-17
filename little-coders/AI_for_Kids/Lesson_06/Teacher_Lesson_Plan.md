# Lesson 6: Creating a Voice-Activated Story in Scratch

## Big Idea
Words spoken out loud can steer a story's path — turning YOUR voice into the story's control stick!

## Learning Objectives
- Explain how a branching story uses choices to change what happens next.
- Explore the pre-built voice-activated story project (`Project_08_Voice_Story`) built with number-key/click stand-ins for voice commands.
- Identify exactly which stand-in inputs will be replaced by real ML4Kids sound-recognition broadcasts.

## Teacher Preparation
- No new website account needed for the main build day — this lesson uses the pre-built `Projects/Project_08_Voice_Story/project.sb3` (complete and playable).
- Optional: machinelearningforkids.co.uk (from Lesson 5) if extending into the real voice AI upgrade.
- Preview `project.sb3` yourself beforehand.

## Required Files
- `Projects/Project_08_Voice_Story/project.sb3`, `README.md`, `Student_Guide.md`, `AI_Training_Guide.md`, `Scratch_Integration_Guide.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration — 10 min
4. Guided Activity — 15 min
5. Mini Challenge — 10 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook
🚀 **Coding Mission:** Ask: "Have you ever read a 'choose your own adventure' book?" Explain today's story works like that, but in Scratch, and later can be controlled by REAL spoken words.

## Concept Introduction
"A branching story has SCENES. Each scene ends with a choice. In this project, each scene broadcasts a message like 'Scene2Start' to move the story forward, and the player currently picks a choice by pressing a number key or clicking a sprite — this stands in for the moment when a REAL trained voice model would recognize a spoken word like 'left' or 'right' and send that broadcast instead."

🧠 **Think Like a Coder:** "This is the exact same broadcast idea from Course 11 — sprites/scenes talking to each other with messages — just now the 'sender' of the choice will eventually be an AI, not a key press."

## Teacher Demonstration
1. Open `project.sb3`, click the green flag.
2. Play through the story, showing at least one branching choice point (e.g., press 1 to go left, press 2 to go right).
3. Show both branches by replaying and choosing differently the second time.
4. Open the code for the Narrator/Scene sprite and show the broadcast chain moving from scene to scene.

## Guided Activity
Students play through the whole story twice, choosing differently each time, and note:
1. How many total scenes/broadcasts they can find in the project.
2. Which exact key press or click stands in for a spoken word, and what word it represents (see `Scratch_Integration_Guide.md`).

## Mini Challenge
🔍 **Debug Detective:** Find the broadcast name used to move from the branch point to each of the two next scenes. Are the names spelled exactly the same in the sender and receiver scripts?

## Main Activity / Project
Students write one new line of story dialogue they wish existed in one scene, and share with a partner.

## Fun Challenge
⭐ **Bonus Challenge:** Sketch a story map (boxes and arrows) showing every scene and choice in the project.

## Check for Understanding
- "What moves the story from one scene to the next?" (a broadcast message)
- "What currently stands in for a spoken command?" (a key press or sprite click)
- "Does this file already recognize real spoken words?" (No — that comes from following the AI Training Guide with ML4Kids)

## Common Student Mistakes
- Assuming the microphone is already active in the shipped project — it is not.
- Mixing up which key/click corresponds to which story branch.
- Misreading broadcast names as identical when spelling actually differs.

## Differentiation
- **Extra support:** Focus on playing through both branches with teacher support; skip the story map bonus.
- **Extra challenge:** Read `Scratch_Integration_Guide.md` and explain, step by step, how they would replace one keypress with a real ML4Kids sound broadcast.

## Recap
We explored a full branching Scratch story controlled today by key presses/clicks, understanding that these are stand-ins for real spoken voice commands that a trained ML4Kids sound model will provide later.

## Take-Home / Creative Challenge
Write one new possible branch/ending for the story (a few sentences) that could be added later.

## Teacher Talking Points
- "Every scene, every line of dialogue, and every choice in this file is real and already works — the only missing piece is swapping the keypress for a live voice broadcast."
- "Next lesson we move to virtual pets and facial expressions, using this exact same stand-in strategy!"

**Fun elements used in this lesson:** 🚀 Coding Mission · 🤖 Robot Tip · 🧠 Think Like a Coder · ⭐ Bonus Challenge · 🎮 Game Challenge · 🔍 Debug Detective · 🎨 Make It Yours · 🏆 Victory Moment
