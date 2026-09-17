# Activities — Lesson 3: Connecting AI to Scratch

## Activity 1: Create Your ML4Kids Project (5 min)
1. Log in, click + Add new project, choose Images.

## Activity 2: Train Two Labels (15 min)
1. Add "Label A" and "Label B" buckets.
2. Collect 20-25 webcam examples per label.
3. Go to Learn & Test → Train new machine learning model.
4. Test with the webcam and note accuracy.

## Activity 3: Build the Bridge in Scratch (15 min)
1. Click Make → Scratch 3.
2. Find the new custom block at the bottom of the blocks palette.
3. Build: when flag clicked → forever → if (recognise image)=(Label A) then say "Label A!" else say "Label B!"
4. Test it with your webcam live.

## Activity 4: Add a Broadcast (10 min)
1. Instead of just "say", add a broadcast block that fires a message like "GotLabelA" when recognized.
2. Create a second sprite that reacts (changes costume) when it receives that broadcast.
