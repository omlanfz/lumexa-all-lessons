# Lesson 2: Training Your First Image Recognition Model

## Big Idea
The more good examples you show a model, the smarter and more confident its guesses become!

## Learning Objectives
- Create a free project on Teachable Machine (teachablemachine.withgoogle.com).
- Collect webcam image examples for at least two classes (categories).
- Train a real image classification model and test live predictions with the webcam.
- Explain why training example QUALITY and QUANTITY both matter.

## Teacher Preparation
- Needs: teachablemachine.withgoogle.com (free, no login required to use the Image Project tool) and a working internet connection.
- Test that classroom devices have a working webcam and that the school network allows access to teachablemachine.withgoogle.com before class.
- Have two simple, safe "classes" ready to demo (e.g., "Thumbs Up" vs "Thumbs Down", or "Empty Background" vs "Holding a Book").
- Consider pairing students on devices if there aren't enough webcams.

## Required Files
- `Student_Lesson_Guide.md` (step-by-step Teachable Machine walkthrough for kids)
- `Activities.md`, `Challenges.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction (recap + new: "confidence") — 5 min
3. Teacher Demonstration on Teachable Machine — 10 min
4. Guided Activity (everyone trains a model) — 20 min
5. Mini Challenge — 5 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook
🚀 **Coding Mission:** "Yesterday you sorted PAPER cards. Today YOU get to be the teacher of a real computer! You'll show it examples with your own webcam and watch it learn live."

## Concept Introduction
Recap Examples → Training → Model → Prediction → Action from Lesson 1. New idea: **confidence**. When a trained model predicts, it doesn't just say "yes" or "no" — it gives a percentage, like "87% Thumbs Up." Explain: "The more clear, varied examples you gave it, the higher and more reliable that confidence number gets."

🧠 **Think Like a Coder:** "Remember — this is NOT like your Course 10/11 code where YOU typed the exact if/then rule. Here, the pattern is discovered by the computer itself from your examples."

## Teacher Demonstration (live, numbered steps on Teachable Machine)
1. Open a browser and go to **teachablemachine.withgoogle.com**.
2. Click **Get Started**.
3. Click **Image Project**, then choose **Standard image model**.
4. You will see two default classes: rename "Class 1" to something like "Thumbs Up" and "Class 2" to "Thumbs Down" (click the pencil icon to rename).
5. Under "Thumbs Up," click **Webcam**, then **Hold to Record** and record ~20-30 seconds while showing a thumbs-up in different angles/lighting/distances.
6. Repeat for "Thumbs Down" with the opposite gesture.
7. Click the big **Train Model** button in the middle column and wait for it to finish (usually under a minute).
8. In the **Preview** panel on the right, show your webcam a live thumbs-up/thumbs-down and watch the confidence bars update in real time.

🤖 **Robot Tip:** "Watch my confidence bars — if I'm unsure, it means my examples weren't clear or varied enough yet!"

## Guided Activity
Students (in pairs if needed) repeat the teacher's steps on their own device:
1. Create their own Image Project with 2 classes of their choice (keep it simple and safe: hand gestures, holding vs. not holding an object, or facial expressions).
2. Record at least 25-30 examples PER class, varying angle, distance, and background a little each time.
3. Train the model.
4. Test with the live webcam preview and note the confidence percentage for at least 3 tries per class.

## Mini Challenge
🔍 **Debug Detective:** If the model keeps confusing the two classes, have students add MORE examples with different angles/lighting and retrain. Ask: "What did you change, and did the confidence get better?"

## Main Activity / Project
Each student trains a 2-class model to at least 80% confidence on both classes when tested live, and writes down (or says) which class had more confident predictions and why they think that happened.

## Fun Challenge
⭐ **Bonus Challenge:** Try adding a THIRD class (e.g., "Nothing" / empty background) so the model can tell the difference between "doing a gesture" and "doing nothing."

## Check for Understanding
- "What does the confidence percentage tell us?" (how sure the model is)
- "What can you do if the model keeps getting confused?" (add more/better examples and retrain)
- "Did you write the rule, or did the computer find it?" (the computer found it from examples)

## Common Student Mistakes
- Too few training examples (fewer than ~20 per class) — the model will guess poorly.
- Training examples too similar (same exact pose/angle every time) — the model won't generalize to new angles.
- Background or lighting differences between classes accidentally becoming "the pattern" instead of the actual gesture (e.g., if "Thumbs Up" was always recorded near a window and "Thumbs Down" in a darker corner, the model may learn to recognize the LIGHTING, not the gesture!).
- Forgetting to click Train Model after recording new examples.

## Differentiation
- **Extra support:** Provide a printed numbered checklist of the exact Teachable Machine steps to follow at their own pace; pair with a buddy.
- **Extra challenge:** Add a third or fourth class and test which classes the model confuses most, then explain why.

## Recap
Today you were the TEACHER of a real AI model — you gave it examples with your own webcam, it trained itself, and then it made live predictions with a confidence score. More good, varied examples = a smarter model!

## Take-Home / Creative Challenge
At home (or next free computer time), retrain your model but try to make it MORE confident by recording 10 extra examples in different lighting or angles. Compare your new confidence score to the old one.

## Teacher Talking Points
- "You just trained a real machine learning model — the same basic idea used in real apps you use every day!"
- "If the computer seems confused, it's not broken — it just needs BETTER examples, exactly like you needed more practice to learn something new."
- "Next lesson, we'll connect a model like this into Scratch so it can control a whole game!"
