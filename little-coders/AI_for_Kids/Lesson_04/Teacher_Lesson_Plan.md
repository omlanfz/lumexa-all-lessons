# Lesson 4: Building an AI Rock-Paper-Scissors Game

## Big Idea
You can build the entire game logic first, then add real AI recognition as the final upgrade!

## Learning Objectives
- Understand how a rock-paper-scissors game can be built fully in Scratch using only keyboard/mouse input as a stand-in for AI.
- Explain the win/lose/tie rules using operators and if/else blocks.
- Identify exactly which part of the finished game will later be replaced by a real AI gesture-recognition block.

## Teacher Preparation
- No new website account is needed for the main build day — this lesson uses the pre-built `Projects/Project_07_Rock_Paper_Scissors/project.sb3` file (already complete and playable).
- Optional (for later, once ML4Kids trained models exist): teachablemachine.withgoogle.com or machinelearningforkids.co.uk for the upgrade path described in the project's `AI_Training_Guide.md`.
- Open `project.sb3` yourself beforehand in the Scratch desktop app or scratch.mit.edu (upload) to preview it.

## Required Files
- `Projects/Project_07_Rock_Paper_Scissors/project.sb3`
- `Projects/Project_07_Rock_Paper_Scissors/README.md`, `Student_Guide.md`, `AI_Training_Guide.md`, `Scratch_Integration_Guide.md`

## Teaching Flow (approx. 55 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration (play the game) — 10 min
4. Guided Activity (explore the code) — 15 min
5. Mini Challenge — 10 min
6. Check for Understanding + Recap — 10 min

## Warm-up / Hook
🚀 **Coding Mission:** Play a quick real rock-paper-scissors round with a partner using hands. Ask: "How did you decide who won?" Introduce today's mission: build that exact decision-making logic inside Scratch.

## Concept Introduction
Explain the stand-in approach honestly: "Real AI gesture recognition (a webcam seeing your hand shape) takes real training on a website. Today we build the WHOLE GAME first — scorekeeping, win/lose/tie animations, computer's random choice — using number keys (1=Rock, 2=Paper, 3=Scissors) to stand in for your hand gesture. Later, in the AI Training Guide, we swap that keypress for the real trained AI block."

🧠 **Think Like a Coder:** "This is exactly how professional game/app developers work — build and test all your logic first, then plug in the AI last."

## Teacher Demonstration
1. Open `project.sb3` in the Scratch editor.
2. Click the green flag.
3. Press 1, 2, or 3 to make your choice; show the computer's random choice appearing and the winner being announced.
4. Play until the Score variable updates, showing a win, a loss, and a tie.
5. Open the code for the "Referee" sprite and show the class the `operators_equals`/if-else chain that decides the winner.

## Guided Activity
In pairs, students:
1. Play the game at least 5 rounds, noting the Score each time.
2. Open the Scratch code view and find: the broadcast used for "PlayerChose", the random computer-choice block, and the if/else-if chain comparing player vs computer choice.
3. Identify (write down) which exact key-press event block will later be replaced by an AI block, per the `Scratch_Integration_Guide.md`.

## Mini Challenge
🔍 **Debug Detective:** Ask students to find where the Tie condition is checked in the code, and explain in their own words why Rock beats Scissors but loses to Paper.

## Main Activity / Project
Students remix ONE small thing in the existing project (e.g., change the win sound, add a new backdrop, or change the winning message text) and playtest it.

## Fun Challenge
⭐ **Bonus Challenge:** Add a "Best of 5" round counter using a new variable.

## Check for Understanding
- "What key stands in for 'Rock' right now?" (key 1)
- "What will eventually replace that keypress?" (a real AI block from ML4Kids/Teachable Machine recognizing a hand gesture)
- "Is the AI part working in this file today?" (No — this file is complete GAME LOGIC only; AI comes later by following the AI Training Guide)

## Common Student Mistakes
- Assuming the shipped project already "sees" their hand — it does not; it currently reads keypresses only.
- Confusing which sprite holds the win/lose/tie logic.
- Forgetting that broadcasts must match spelling exactly between sender and receiver.

## Differentiation
- **Extra support:** Focus only on playing and reading the code with the teacher, skipping the remix step.
- **Extra challenge:** Read the `Scratch_Integration_Guide.md` and describe, in their own words, exactly what steps a student would follow to add a real webcam AI block.

## Recap
We built the FULL rock-paper-scissors game logic — random computer opponent, score, win/lose/tie animations — using key presses as our AI stand-in. Next, students who want the real AI upgrade follow the integration guide.

## Take-Home / Creative Challenge
Read through `AI_Training_Guide.md` for Project 07 at home and write down the 3 biggest steps you'd need to do to add real gesture recognition.

## Teacher Talking Points
- "This finished file is 100% real, playable Scratch code — nothing here is fake or pretend."
- "The only missing piece is the LIVE camera recognition step, and that's because it has to be trained by a real person (you!) using a real website."

**Fun elements used in this lesson:** 🚀 Coding Mission · 🤖 Robot Tip · 🧠 Think Like a Coder · ⭐ Bonus Challenge · 🎮 Game Challenge · 🔍 Debug Detective · 🎨 Make It Yours · 🏆 Victory Moment
