# Lumexa Computer Vision Path

**Lesson:** 04 of 8
**Title:** Emotion Recognition and Classification
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3, OpenCV, TensorFlow/Keras (CNN)
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, you can now locate a crew member's face in any camera feed. Today's mission takes it one step further: mission control wants to build a system that can classify the *facial expression* visible in a detected face — is the crew member's expression more consistent with "happy," "neutral," "surprised," or "stressed"? This is genuinely useful for things like usability testing of a training simulator, accessibility tools, or gauging classroom engagement in an ed-tech tool. But it is also one of the most important lessons in this entire track for understanding AI's limits, because what we are about to build does **not** read minds or measure true feelings — it classifies facial *expression patterns*. Today you'll learn how a **Convolutional Neural Network (CNN)** performs this classification, and exactly what it can and cannot honestly claim to know.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain, at a conceptual level, how a Convolutional Neural Network processes an image.
2. Describe how a facial expression classification model is trained and what its output represents.
3. Load a Keras CNN model and use it to classify a cropped, preprocessed face image.
4. Combine face detection (Lesson 3) with emotion classification into one pipeline.
5. Articulate clearly why "emotion recognition" is more accurately described as "facial expression pattern classification," and why confidence scores are not guarantees of truth.

## Prerequisites

- Completion of Lessons 01–03.
- `tensorflow`, `opencv-python`, `numpy` installed (`pip install tensorflow opencv-python numpy`).
- Conceptual comfort with the idea of an array/kernel from Lesson 02.

## Concept Explanation

### From hand-designed features to learned features

In Lesson 3, Haar Cascades used features that a human researcher hand-designed (rectangular brightness comparisons). A **Convolutional Neural Network (CNN)** flips this approach: instead of a human designing what patterns to look for, the network *learns* the best patterns automatically from thousands of labeled example images, by adjusting millions of internal numbers (called **weights**) during a process called **training**.

### How a CNN processes an image, layer by layer

1. **Input layer:** the network receives a small, standardized grayscale image — commonly 48×48 pixels for facial expression datasets like FER2013.
2. **Convolutional layers:** each layer slides many small learned kernels (recall Lesson 2's convolution!) across the image, each kernel producing a "feature map" highlighting where a particular pattern occurs — early layers might learn to detect simple edges and corners; deeper layers combine those into more complex patterns like "curved mouth corners" or "raised eyebrow shape."
3. **Pooling layers:** these shrink the feature maps down (e.g., taking the maximum value in each small block), reducing computation and making the network less sensitive to a pattern's exact pixel position.
4. **Fully connected layers:** near the end of the network, the shrunk-down feature maps are flattened into a long list of numbers and passed through dense layers that combine everything learned so far into a final decision.
5. **Output layer:** produces one number per possible class (e.g., angry, disgust, fear, happy, neutral, sad, surprise), each between 0 and 1, that sum to 1 — this is done using a **softmax** activation function. Each number represents the model's estimated probability that the input image belongs to that class.

### What training actually does

During training, the network is repeatedly shown labeled images (e.g., "this 48×48 face image is labeled 'happy'"), makes a prediction, and is corrected based on how wrong it was, adjusting its internal weights slightly via an algorithm called **backpropagation** combined with **gradient descent**. After many thousands of repetitions across many thousands of images, the weights settle into values that produce reasonably accurate classifications on new, unseen images.

### Preprocessing for classification

Before an image can be classified, it must be prepared to match exactly what the network was trained on:

1. Detect the face (Lesson 3's Haar Cascade).
2. Crop just the face region.
3. Convert to grayscale.
4. Resize to the model's expected input size (commonly 48×48 for FER2013-style models).
5. Normalize pixel values from the 0–255 range down to 0–1 (dividing by 255.0), since neural networks train and predict more reliably on small, consistent numeric ranges.
6. Reshape into the exact tensor shape the model expects, typically `(1, 48, 48, 1)` — batch size 1, height 48, width 48, 1 channel.

### Reading the output correctly

`model.predict(face_image)` returns an array of probabilities, one per emotion class. `np.argmax()` finds the index of the highest probability, and that index maps to a class label (e.g., index 3 → "happy"). The value itself (e.g., 0.82) is often reported as a "confidence score."

### Responsible AI: what this system actually does (and does not do)

This is the most important concept in this lesson, so state it plainly to students, more than once, in different ways:

- **The model classifies visual patterns in facial muscle configuration, not internal feelings.** A person can produce a "happy" facial expression while feeling anxious, bored, or in pain, and can feel genuinely joyful while their face is neutral or unreadable. The model has no access to what a person is actually experiencing — only to the pixels of their face.
- **"Confidence score" is not the same as "certainty of truth."** A softmax output of 0.82 for "happy" means the model's learned pattern-matching most strongly resembles training examples labeled "happy" — it is a statement about pattern similarity to the training data, not a guarantee about the real world.
- **Training data bias transfers directly into the model.** Datasets like FER2013 were labeled by human annotators who may disagree with each other on ambiguous expressions, and datasets are known to be imbalanced across expressions, lighting conditions, ages, and cultural backgrounds. A model trained on such data will inherit and can amplify those same imbalances and blind spots — for example, performing less accurately on facial structures, expressions norms, or lighting conditions underrepresented in training.
- **Only the classes it was trained on exist to it.** If a model is trained on seven categories (angry, disgust, fear, happy, neutral, sad, surprise), it will force every face into one of those seven boxes even if the real expression doesn't fit neatly into any of them — it cannot say "I don't know" or "this doesn't match any category well" unless specifically designed to.
- **Appropriate framing for any product built on this:** describe it to users as "facial expression classifier" or "estimated expression," never as "emotion reader" or "mind reader," and always disclose that results can be wrong, especially for underrepresented groups, and should never be used for high-stakes decisions about a real person (e.g., hiring, discipline, medical diagnosis) without qualified human review.

## Key Vocabulary

| Term | Definition |
|---|---|
| Convolutional Neural Network (CNN) | A neural network architecture specialized for processing grid-like data such as images. |
| Weight | An internal adjustable number in a neural network, tuned during training. |
| Training | The process of adjusting a model's weights using labeled example data. |
| Feature map | The output of applying one learned kernel across an image, highlighting a pattern. |
| Pooling | A downsampling operation that shrinks feature maps while keeping key information. |
| Softmax | An activation function converting raw scores into probabilities that sum to 1. |
| Confidence score | The model's estimated probability for a predicted class; not a guarantee of truth. |
| Facial expression classification | The precise, honest term for what this system does — classifying visible facial patterns, not reading true emotions. |

## Real Runnable Code

```python
"""
Lesson 04 — Emotion (Facial Expression) Recognition with a CNN
Mission: Deep Space Vision Lab
Requires: tensorflow, opencv-python, numpy
Run: python lesson04_emotion_classifier.py

This script builds and trains a SMALL demonstration CNN on synthetic
placeholder data so the full pipeline runs end-to-end without external
downloads. In a real deployment, you would train on a genuine labeled
dataset (such as FER2013) and load that trained model instead --
see the project README in projects/04-real-time-emotion-detector
for the full production training script and dataset structure.
"""

import numpy as np
import cv2
import tensorflow as tf
from tensorflow.keras import layers, models

EMOTION_LABELS = ["Angry", "Disgust", "Fear", "Happy", "Neutral", "Sad", "Surprise"]
IMG_SIZE = 48  # standard input size for FER2013-style facial expression models

# --- 1. Define a small CNN architecture for facial expression classification ---
def build_emotion_model(num_classes=7):
    model = models.Sequential([
        layers.Input(shape=(IMG_SIZE, IMG_SIZE, 1)),
        layers.Conv2D(32, (3, 3), activation="relu", padding="same"),
        layers.BatchNormalization(),
        layers.MaxPooling2D((2, 2)),

        layers.Conv2D(64, (3, 3), activation="relu", padding="same"),
        layers.BatchNormalization(),
        layers.MaxPooling2D((2, 2)),

        layers.Conv2D(128, (3, 3), activation="relu", padding="same"),
        layers.BatchNormalization(),
        layers.MaxPooling2D((2, 2)),

        layers.Flatten(),
        layers.Dense(256, activation="relu"),
        layers.Dropout(0.5),
        layers.Dense(num_classes, activation="softmax"),
    ])
    model.compile(optimizer="adam",
                  loss="categorical_crossentropy",
                  metrics=["accuracy"])
    return model

model = build_emotion_model(num_classes=len(EMOTION_LABELS))
model.summary()

# --- 2. Demonstration-only synthetic training data ---
# In production you would load a real dataset (e.g. FER2013 CSV/images).
# Here we generate random labeled examples purely to demonstrate that the
# training loop and prediction pipeline run correctly end-to-end.
def make_demo_dataset(num_samples=200):
    x = np.random.rand(num_samples, IMG_SIZE, IMG_SIZE, 1).astype("float32")
    y_indices = np.random.randint(0, len(EMOTION_LABELS), size=num_samples)
    y = tf.keras.utils.to_categorical(y_indices, num_classes=len(EMOTION_LABELS))
    return x, y

x_train, y_train = make_demo_dataset(200)
x_val, y_val = make_demo_dataset(40)

print("\nTraining a DEMO model on synthetic data (for pipeline demonstration only)...")
model.fit(x_train, y_train, validation_data=(x_val, y_val),
          epochs=3, batch_size=16, verbose=1)

# Save the trained model in Keras format -- this is the exact file format
# the real-time webcam project expects at models/emotion_model.h5
model.save("emotion_model_demo.h5")
print("Demo model saved as emotion_model_demo.h5")

# --- 3. Preprocessing a face crop for classification ---
def preprocess_face(face_bgr_image):
    """
    Converts a cropped BGR face image into the exact tensor shape
    the emotion model expects: (1, 48, 48, 1), normalized to [0, 1].
    """
    gray = cv2.cvtColor(face_bgr_image, cv2.COLOR_BGR2GRAY)
    resized = cv2.resize(gray, (IMG_SIZE, IMG_SIZE), interpolation=cv2.INTER_AREA)
    normalized = resized.astype("float32") / 255.0
    tensor = normalized.reshape(1, IMG_SIZE, IMG_SIZE, 1)
    return tensor

# --- 4. Combine face detection (Lesson 3) with emotion classification ---
face_cascade = cv2.CascadeClassifier(
    cv2.data.haarcascades + "haarcascade_frontalface_default.xml"
)

def classify_faces_in_image(image_bgr, emotion_model):
    gray = cv2.cvtColor(image_bgr, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

    annotated = image_bgr.copy()
    results = []
    for (x, y, w, h) in faces:
        face_crop = image_bgr[y:y + h, x:x + w]
        face_tensor = preprocess_face(face_crop)

        predictions = emotion_model.predict(face_tensor, verbose=0)[0]
        best_index = int(np.argmax(predictions))
        label = EMOTION_LABELS[best_index]
        confidence = float(predictions[best_index])

        results.append({"box": (x, y, w, h), "label": label, "confidence": confidence})

        cv2.rectangle(annotated, (x, y), (x + w, y + h), (0, 255, 0), 2)
        caption = f"{label} ({confidence * 100:.1f}%)"
        cv2.putText(annotated, caption, (x, y - 10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 255, 0), 2, cv2.LINE_AA)

    return annotated, results

# --- 5. Run the full pipeline on a test image ---
test_image = cv2.imread("crew_photo.jpg")
if test_image is None:
    print("\nNo crew_photo.jpg found -- skipping live classification demo.")
    print("Provide a real photo with faces to see the full pipeline in action.")
else:
    annotated_image, detections = classify_faces_in_image(test_image, model)
    cv2.imwrite("emotion_classified.png", annotated_image)
    for i, detection in enumerate(detections):
        print(f"Face {i}: predicted expression = {detection['label']} "
              f"(confidence {detection['confidence']*100:.1f}%)")
    print("\nResponsible-use reminder: this is a CLASSIFICATION OF VISIBLE FACIAL "
          "PATTERNS, not a measurement of true internal feelings. Confidence scores "
          "reflect pattern similarity to training data, not certainty about reality.")
```

## Instructor-Guided Coding

1. Build the CNN architecture together layer by layer, pausing at each `Conv2D` / `MaxPooling2D` pair to ask: "What do you think shrinks here, and why?"
2. Run `model.summary()` and examine the parameter counts together — point out how quickly the number of trainable weights grows, motivating why training needs lots of data and computing power.
3. Train the demo model on synthetic random data live, and be explicit that this demo model has learned nothing meaningful (its labels are random) — it exists only to prove the *pipeline* (build → train → save → load → predict) works end-to-end.
4. Walk through `preprocess_face()` step by step, connecting each line back to Lessons 1–2 (grayscale conversion, resizing, normalization).
5. Combine detection and classification into the full pipeline and run it on a real class photo (with consent) or a synthetic image, discussing the printed confidence scores as a class.
6. Lead a short discussion: "If this model said someone was 68% 'Sad' but they told you they felt fine, whose account should you trust — and why?" Anchor the responsible-AI framing in this concrete example.

## Student Mission / Guided Challenge

**Explorer Challenge: Expression Log**

1. Using the provided pipeline, run classification on at least three face images (or reuse `crew_photo.jpg` variations, or the synthetic demo model).
2. For each detected face, print a "mission log" line in this exact format: `"MISSION LOG: Crew member expression classified as {label} with {confidence}% pattern-match confidence."`
3. Modify `classify_faces_in_image` so that any prediction with confidence below 40% is labeled `"Uncertain"` instead of the top predicted class — this teaches an important responsible-AI pattern: don't force a confident-sounding label when the model itself is unsure.
4. Write a 3–4 sentence reflection: why is "Uncertain" a more honest output than forcing every face into the most likely of seven categories, even when that category only barely won?
5. **Bonus:** Add a rolling log that tracks how many times each emotion label was predicted across all processed images, and print a summary table at the end.

## Common Mistakes

- **Feeding the raw face crop directly into the model without resizing/normalizing it**, causing a shape-mismatch error or garbage predictions.
- **Forgetting to divide pixel values by 255.0**, which can cause the network to behave erratically since it was trained expecting small input values.
- **Treating the model's top prediction as certain fact.** Always report the confidence score alongside the label, and consider a minimum-confidence threshold before displaying any label at all.
- **Calling this system an "emotion detector" without qualification** in any public-facing description — always clarify it classifies facial expression patterns, not true feelings.
- **Using a model trained on one demographic's expressions and assuming it generalizes perfectly to everyone.** Real production models should be evaluated across diverse test sets before being trusted.

## Check Your Understanding

1. What is the difference between a hand-designed feature (Haar Cascade, Lesson 3) and a learned feature (CNN, this lesson)?
2. What does the softmax output layer actually represent?
3. Why must a face crop be resized and normalized before being classified?
4. Why is "facial expression classification" a more accurate term than "emotion recognition"?
5. Give one concrete example of how training-data bias could cause an emotion classifier to behave unfairly.

## Mini Quiz

1. What shape of input does a typical FER2013-style CNN expect for one face image?
   a) `(1, 224, 224, 3)`  b) `(1, 48, 48, 1)`  c) `(48, 48)`  d) `(1, 7)`

2. What does the softmax function guarantee about its output values?
   a) They are all equal  b) They sum to 1 and represent probabilities  c) They are always above 0.5  d) They are integers

3. What is a "confidence score" most accurately described as?
   a) A guarantee of correctness  b) The model's estimated probability based on pattern similarity to training data  c) A measurement of the person's true feelings  d) A random number

4. Why should you preprocess (grayscale, resize, normalize) a face crop before classification?
   a) It's optional and only for speed  b) The model expects input matching its training format exactly  c) It changes the model's weights  d) It removes the need for face detection

5. Which statement best reflects responsible AI practice for this lesson's system?
   a) Always show the top label with no caveat  b) Describe it as reading true emotions to build user trust  c) Disclose that it classifies visible expression patterns and can be wrong, especially for underrepresented groups  d) Assume 99% accuracy for all users

### Answer Key

Mini Quiz: 1-b, 2-b, 3-b, 4-b, 5-c

## Lesson Recap

Today you learned how a Convolutional Neural Network processes an image through convolutional, pooling, and dense layers to produce a probability distribution over expression classes, and you built and ran a complete detection-plus-classification pipeline combining Lesson 3's face detector with a CNN emotion classifier. Most importantly, you practiced the precise, honest vocabulary this technology demands: these systems classify facial expression *patterns*, not true internal feelings, confidence scores reflect pattern similarity rather than certainty, and biased training data produces biased, unevenly accurate models. This responsible-AI framing will continue through object detection in Lesson 5 and the real-time system in Lesson 6.

## Homework / Extension Mission

**Mission: Confidence Audit**

1. Research (briefly, in your own words, 1 paragraph) what the FER2013 dataset is, how large it is, and what its seven expression categories are.
2. Using the demo pipeline, generate at least 10 face-classification results (real or synthetic) and calculate the average confidence score across all of them.
3. Write a short "model card" (4–6 bullet points) describing this emotion classifier honestly: what it does, what data it would ideally be trained on, its known limitations, and appropriate/inappropriate uses — this is a real practice used by responsible AI teams before deploying any model.
4. **Bonus:** Modify the CNN architecture to add one additional convolutional layer, retrain the demo model, and compare the new `model.summary()` parameter count to the original.

## Portfolio Connection

This lesson *is* the technical core of the **Real-Time Emotion Detector** portfolio project: the CNN architecture, preprocessing function, and combined detection-plus-classification pipeline you built today are used directly (extended for live video) in `projects/04-real-time-emotion-detector`. The responsible-use framing you practiced here also directly informs that project's README disclaimer, which every Lumexa cadet must include before demoing an emotion-classification system to any real audience.
