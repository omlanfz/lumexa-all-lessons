# Lesson 3: Connecting an AI Model to Scratch with ML4Kids

## Big Idea
Your trained AI model can send its answers straight into Scratch, turning a smart guess into a real game action!

## Learning Objectives
- Create a free ML4Kids (machinelearningforkids.co.uk) project and understand its Train → Learn & Test → Make workflow.
- Add a custom AI recognition block into a Scratch project opened through ML4Kids.
- Explain how an AI prediction becomes a Scratch event (using an if/else block or a broadcast).

## Teacher Preparation
- Needs: a free teacher/class account at **machinelearningforkids.co.uk**, set up in advance (teachers create a class and student logins ahead of time — this can take 10-15 minutes the first time).
- Needs a working internet connection for every device.
- Decide in advance what simple project the class will train together (recommended: an image project with 2-3 classes, like "Happy Face" vs "Sad Face," reusable in Lesson 7).
- Make sure each student has their ML4Kids login ready before class starts.

## Required Files
- `Student_Lesson_Guide.md`, `Activities.md`, `Challenges.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration on ML4Kids — 15 min
4. Guided Activity — 15 min
5. Mini Challenge — 5 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook
🚀 **Coding Mission:** "You've already trained a model on Teachable Machine. But how does that smart guess get INSIDE a Scratch game? Today we build the bridge!"

## Concept Introduction
Explain: ML4Kids has 3 tabs — **Train** (add examples, just like Teachable Machine), **Learn & Test** (train the model and test it), and **Make** (opens a special Scratch editor that has NEW custom blocks made just for YOUR trained model, like "recognise image" or "recognise text"). Emphasize: "This new Scratch block isn't a normal Scratch block — it's a MAGIC KEY that only works because YOU trained your own model first."

🧠 **Think Like a Coder:** "In Course 11 you used broadcasts to make sprites talk to each other. Today, we'll use a broadcast to make the AI's guess talk to the rest of the game!"

## Teacher Demonstration (numbered live steps)
1. Go to **machinelearningforkids.co.uk** and log in to the class account.
2. Click **+ Add new project**, name it, and choose recognition type **Images** (or Text/Numbers for other ideas).
3. Click into the project, then the **Train** tab. Add 2 buckets/labels (e.g., "Happy" and "Sad").
4. Add example images to each bucket (webcam capture or upload) — at least 20-25 per bucket.
5. Click **Learn & Test**, then click **Train new machine learning model** and wait for training to complete.
6. Test live in the **Test** box with the webcam.
7. Click **Make**, then choose **Scratch 3**. This opens Scratch with new custom blocks (they will appear in their own section at the bottom of the blocks palette).
8. Drag out the new "recognise image" reporter block and drop it inside an `if <recognise image> = [Happy]` block to show the class how a prediction becomes a Scratch decision.

## Guided Activity
Students (in pairs is fine) train their own small model in ML4Kids (2 labels), then open Make → Scratch 3 and build ONE simple test script:
1. When green flag clicked → forever → if `(recognise image) = (Happy)` then sprite says "You look happy!" else sprite says "Show me a happy face!"

## Mini Challenge
🎮 **Game Challenge:** Make the sprite change costume or color depending on which label the model recognizes.

## Main Activity / Project
Students test their AI+Scratch connection with a partner in front of the webcam, checking that different faces/gestures correctly trigger different sprite reactions.

## Fun Challenge
⭐ **Bonus Challenge:** Add a broadcast message ("HappyDetected") that fires when the AI recognizes "Happy," instead of only using an if/else block, and have a second sprite react to that broadcast.

## Check for Understanding
- "What are the three ML4Kids tabs, in order?" (Train, Learn & Test, Make)
- "How does an AI prediction become something a Scratch sprite can react to?" (an if/else block checking the AI reporter block, or a broadcast)

## Common Student Mistakes
- Forgetting to click "Train new machine learning model" after adding examples (the model won't update).
- Too few or too-similar training examples (same as prior lessons) causing low accuracy.
- Trying to drag the AI block into a project NOT opened through the ML4Kids "Make" button (the custom block only exists in that special project).

## Differentiation
- **Extra support:** Provide a printed numbered ML4Kids checklist; use a pre-made 2-label project so pairs focus only on the Scratch connection step.
- **Extra challenge:** Add a 3rd label and a 3-way if/else-if chain in Scratch.

## Recap
Today you connected a REAL trained AI model into a REAL Scratch project using ML4Kids' custom blocks, and made the AI's guess control what a sprite says and does.

## Take-Home / Creative Challenge
Think of one more idea for an AI-powered Scratch game using your own trained model (e.g., recognizing thumbs up/down to control a quiz game) and describe it in a sentence or two.

## Teacher Talking Points
- "Every custom AI block you see was created just for the model YOU trained — nobody else's project has that exact block."
- "This bridge between AI and Scratch is exactly how real apps combine machine learning with regular app logic."
- "Next lesson, we use this exact bridge to build a full rock-paper-scissors game!"

**Fun elements used in this lesson:** 🚀 Coding Mission · 🤖 Robot Tip · 🧠 Think Like a Coder · ⭐ Bonus Challenge · 🎮 Game Challenge · 🔍 Debug Detective · 🎨 Make It Yours · 🏆 Victory Moment
