# Lesson 5: Voice Commands — Training a Sound Recognizer

## Big Idea
Just like you trained a model to recognize pictures, you can train a model to recognize SOUNDS — even your own voice!

## Learning Objectives
- Train a sound-recognition model using ML4Kids' Sounds project type.
- Record multiple clear voice-command examples per label.
- Explain how background noise and inconsistent volume can confuse a sound model.

## Teacher Preparation
- Needs: a free teacher/class account at **machinelearningforkids.co.uk** (reuse the account set up in Lesson 3) and a working internet connection.
- Needs a working microphone on each device (built-in laptop mics are fine).
- Choose 2-3 simple, clear spoken words for the class demo (e.g., "go," "stop").
- Try to find a reasonably quiet space/time for recording — background noise is the #1 sound-training problem.

## Required Files
- `Student_Lesson_Guide.md`, `Activities.md`, `Challenges.md`

## Teaching Flow (approx. 50 minutes)
1. Warm-up / Hook — 5 min
2. Concept Introduction — 5 min
3. Teacher Demonstration — 10 min
4. Guided Activity — 20 min
5. Mini Challenge — 5 min
6. Check for Understanding + Recap — 5 min

## Warm-up / Hook
🚀 **Coding Mission:** Ask: "How does a smart speaker know you said 'stop' and not 'go'?" Explain today we train our own version of that.

## Concept Introduction
"Sound recognition works just like image recognition — Examples → Training → Model → Prediction → Action — except the examples are short audio clips instead of pictures. The computer studies the SHAPE of the sound wave (how loud, how the pitch changes) to find patterns."

🤖 **Robot Tip:** "Background noise is tricky for me — a barking dog or loud fan in only ONE class's recordings can confuse my patterns!"

## Teacher Demonstration
1. Go to machinelearningforkids.co.uk, open (or create) a project, choose recognition type **Sounds**.
2. In **Train**, add 2 labels (e.g., "go" and "stop").
3. Click the microphone icon under a label and record yourself saying the word clearly, 3-5 times per click (repeat until you have ~15-20 examples per label).
4. Also add a "background noise" label with quiet clips of the room's normal sound — this helps the model tell "someone spoke" apart from "silence/noise."
5. Go to **Learn & Test**, train the model, and test by speaking into the mic live.

## Guided Activity
Students (pairs are fine given shared mics) train their own 2-3 label sound model with a partner:
1. Choose two clear, different-sounding words.
2. Record 15-20 examples per label, plus a background-noise label.
3. Train and test — try saying each word from different distances from the mic.

## Mini Challenge
🔍 **Debug Detective:** If the model confuses "go" and "stop," check: are the recordings too quiet? Is there background chatter from other groups in some recordings but not others?

## Main Activity / Project
Each pair gets their sound model to correctly recognize both words at least 4 out of 5 tries when tested live.

## Fun Challenge
⭐ **Bonus Challenge:** Add a third spoken command and re-test recognition accuracy across all three.

## Check for Understanding
- "What does a sound model actually study in your voice clip?" (the pattern/shape of the sound wave)
- "Why do we add a 'background noise' label?" (so the model can tell speech apart from silence/room noise)

## Common Student Mistakes
- Too few sound examples per label (fewer than ~15).
- Recording examples too similar (exact same tone/volume every time) instead of natural variation.
- Loud background noise present in only some recordings, confusing the pattern.
- Whispering or mumbling instead of speaking clearly and consistently.

## Differentiation
- **Extra support:** Use just 2 very different-sounding words (like "yes" and "banana") to make the difference obvious.
- **Extra challenge:** Try training with 4 labels including two similar-sounding words, and discuss why accuracy drops.

## Recap
We learned that sound recognition follows the same Examples → Training → Model → Prediction pattern as images, but pays attention to sound waves instead of pixels, and that background noise and volume consistency really matter.

## Take-Home / Creative Challenge
At home, say your two trained words out loud 5 times each in a different room and guess whether your model (if you had it there) would still recognize them. Why or why not?

## Teacher Talking Points
- "The same AI idea — learn from examples — works for sound, pictures, and lots of other kinds of data."
- "Next lesson, we'll use a voice-command model just like this one to control an entire branching story in Scratch!"

**Fun elements used in this lesson:** 🚀 Coding Mission · 🤖 Robot Tip · 🧠 Think Like a Coder · ⭐ Bonus Challenge · 🎮 Game Challenge · 🔍 Debug Detective · 🎨 Make It Yours · 🏆 Victory Moment
