# Lesson 7: Facial Expression Recognition and Virtual Pets

## Big Idea
A computer can learn to tell a happy face from a sad face, and use that to make a virtual pet react with real feeling!

## Learning Objectives
- Understand how an image-recognition model can be trained on facial expressions (happy, sad, surprised) instead of objects.
- Explore the pre-built emotion-recognition virtual pet project (`Project_09_Emotion_Pet`), using keyboard keys as a stand-in for facial expression recognition.
- Explain how a Happiness variable/meter changes based on the pet's detected "mood."

## Teacher Preparation
- No new website account needed for the main build day — this lesson uses the pre-built `Projects/Project_09_Emotion_Pet/project.sb3` (complete and playable).
- Optional (for the AI upgrade path): teachablemachine.withgoogle.com or machinelearningforkids.co.uk, reusing prior lessons' training skills with faces instead of objects.
- Preview `project.sb3` yourself beforehand; note the H/S/W key mapping.

## Required Files
- `Projects/Project_09_Emotion_Pet/project.sb3`, `README.md`, `Student_Guide.md`, `AI_Training_Guide.md`, `Scratch_Integration_Guide.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration — 10 min
4. Guided Activity — 15 min
5. Mini Challenge — 10 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook
🚀 **Coding Mission:** Make three faces yourself (happy, sad, surprised) and ask the class to name each one instantly. "You just did facial expression recognition — with your own brain!"

## Concept Introduction
"Just like Teachable Machine can be trained on photos of hands or objects, it can also be trained on photos of FACES showing different expressions. The model studies patterns like the shape of your mouth and eyes. Today's pet project uses keyboard keys (H = Happy, S = Sad, W = Surprised/Wow) as a stand-in for that — later, real trained expression-recognition can replace those key presses."

🧠 **Think Like a Coder:** "Notice the pet has a Happiness variable, just like the variables you made in earlier courses — the AI's job is just to decide how that variable changes."

## Teacher Demonstration
1. Open `project.sb3`, click the green flag.
2. Press H — show the pet's happy costume/animation/sound and the Happiness meter rising.
3. Press S — show the pet's sad costume/animation and Happiness falling.
4. Press W — show the surprised reaction.
5. Open the pet sprite's code and show the `sensing_keypressed` blocks feeding into changes to the Happiness variable and `switchcostumeto`.

## Guided Activity
Students play with the pet for several minutes:
1. Try all three keys and record what happens to Happiness each time.
2. Let Happiness fall low (sad) and then try to raise it back up.
3. Open the code and trace exactly which block changes the Happiness variable for each key.

## Mini Challenge
🎮 **Game Challenge:** Can students get Happiness to its maximum value and see if anything special happens (e.g., a "Victory Moment" sound/costume)?

## Main Activity / Project
Students describe (in words or a quick sketch) what a NEW mood costume (e.g., "sleepy") could look like and what key might trigger it.

## Fun Challenge
⭐ **Bonus Challenge:** Find where Happiness naturally decreases over time (if implemented) and explain why a virtual pet might need care over time, not just one-time reactions.

## Check for Understanding
- "What key press stands in for a happy face right now?" (H)
- "What variable tracks the pet's mood?" (Happiness)
- "Is the shipped project already reading real facial expressions?" (No — it uses key presses; real recognition is added later via the AI Training Guide)

## Common Student Mistakes
- Expecting the webcam to already work in the shipped file.
- Mixing up which key maps to which mood.
- Not noticing the Happiness variable displayed on stage (the monitor checkbox).

## Differentiation
- **Extra support:** Focus on trying each key and describing what happens, without exploring the code.
- **Extra challenge:** Read `Scratch_Integration_Guide.md` and explain exactly which broadcasts would replace which key press.

## Recap
We explored a complete, playable virtual pet game where key presses stand in for facial-expression recognition, changing a Happiness variable and triggering costume/sound reactions — the same Examples → Training → Model → Prediction → Action idea, applied to faces.

## Take-Home / Creative Challenge
Design one new pet mood (name it, describe its costume, and what should happen to Happiness) that isn't in the project yet.

## Teacher Talking Points
- "A virtual pet like this is a great first step toward truly emotion-aware apps and games."
- "Next lesson is our Showcase — you'll get to present one of your 3 AI projects to the class or your family!"

**Fun elements used in this lesson:** 🚀 Coding Mission · 🤖 Robot Tip · 🧠 Think Like a Coder · ⭐ Bonus Challenge · 🎮 Game Challenge · 🔍 Debug Detective · 🎨 Make It Yours · 🏆 Victory Moment
