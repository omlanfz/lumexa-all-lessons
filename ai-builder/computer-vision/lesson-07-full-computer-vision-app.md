# Lumexa Computer Vision Path

**Lesson:** 07 of 8
**Title:** Building a Full Computer Vision App
**Duration:** 60 minutes
**Difficulty:** Intermediate–Advanced
**Technology:** Python 3, OpenCV, TensorFlow/Keras, Ultralytics YOLOv8
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, you now command every subsystem needed for a complete vision mission: pixel manipulation, filtering, face detection, expression classification, object detection, and real-time video processing. Today's mission is architectural — mission control needs these subsystems assembled into one coherent, well-organized, configurable **application**, not a pile of standalone scripts. You'll learn how real computer vision software is structured: separated into modules, configured cleanly, logging its own activity, and handling errors gracefully instead of crashing at the first unexpected input. This is the difference between a classroom demo and something you could actually hand to another user.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Structure a computer vision project into logical, reusable modules (capture, detection, processing, display).
2. Build a configuration system (e.g., a config file or class) instead of hardcoding values throughout the code.
3. Implement basic logging and error handling for a real-time vision pipeline.
4. Integrate two or more vision subsystems (e.g., detection + classification) into a single cohesive application.
5. Apply a "responsible AI checklist" to a finished application before considering it complete.

## Prerequisites

- Completion of Lessons 01–06.
- Familiarity with Python functions, classes, and basic file organization.
- All libraries from prior lessons installed (`opencv-python`, `tensorflow`, `ultralytics`).

## Concept Explanation

### Why structure matters

A single 200-line script that mixes camera setup, model loading, detection logic, drawing code, and the main loop all together is hard to test, hard to extend, and hard for a teammate (or future you) to understand. Professional computer vision applications separate concerns into distinct pieces, commonly:

- **Configuration** — all tunable values (thresholds, model paths, camera index) in one place, not scattered through the code.
- **Capture layer** — responsible only for getting frames from a camera or file.
- **Processing/inference layer** — takes a frame in, returns structured detection results out; knows nothing about how results will be displayed.
- **Rendering/display layer** — takes frame + results, draws annotations, shows or saves output; knows nothing about how detections were computed.
- **Application/orchestration layer** — the `main()` function that wires the above pieces together and runs the loop.

This is the same architectural pattern used in `projects/04-real-time-emotion-detector`, `projects/05-object-recognition`, and `projects/06-motion-activated-security-cam` — each has a `src/` folder with separated modules rather than one giant file.

### Configuration objects

Instead of hardcoding `scaleFactor=1.1` or `conf=0.4` inline throughout your code, collect all tunable parameters into one configuration class or dictionary at the top of your program (or in a separate `config.py` file). This makes it trivial to tune your application's behavior in one place, and makes the rest of the code read like a description of *what* happens rather than a jumble of magic numbers.

```python
class AppConfig:
    CAMERA_INDEX = 0
    FACE_CASCADE_PATH = None  # filled in at runtime via cv2.data.haarcascades
    FACE_SCALE_FACTOR = 1.1
    FACE_MIN_NEIGHBORS = 5
    EMOTION_MODEL_PATH = "models/emotion_model.h5"
    EMOTION_CONFIDENCE_THRESHOLD = 0.4
    DISPLAY_FPS_OVERLAY = True
```

### Logging instead of scattered print statements

Python's built-in `logging` module gives you timestamped, severity-leveled output (`INFO`, `WARNING`, `ERROR`) that can be redirected to a file, filtered, and formatted consistently — far more useful for a real application than scattered `print()` calls, especially once the app runs unattended for a while.

```python
import logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")
logger = logging.getLogger("lumexa_vision")
logger.info("Camera initialized successfully.")
logger.warning("No face detected in this frame.")
logger.error("Failed to load emotion model file.")
```

### Graceful error handling

A production-quality vision app should anticipate and handle common failure modes rather than crashing:

- Camera not found or already in use by another program.
- Model file missing or corrupted.
- No objects/faces detected in a given frame (this is a normal, expected outcome — not an error!).
- User interrupts the program (Ctrl+C) — clean up resources before exiting.

```python
try:
    cap = cv2.VideoCapture(config.CAMERA_INDEX)
    if not cap.isOpened():
        raise RuntimeError(f"Could not open camera index {config.CAMERA_INDEX}")
    # ... main loop ...
except KeyboardInterrupt:
    logger.info("Shutdown requested by user (Ctrl+C).")
except Exception as e:
    logger.error(f"Unexpected error: {e}")
finally:
    cap.release()
    cv2.destroyAllWindows()
    logger.info("Resources cleaned up. Application exited.")
```

### Integrating multiple subsystems

A full application often chains multiple vision subsystems together into a **pipeline**: capture → detect faces → classify expressions → detect objects in the same frame → render all annotations together → display/log/save. Each stage should be a clearly named function or method taking well-defined inputs and producing well-defined outputs, so stages can be tested, replaced, or reordered independently.

### The responsible-AI checklist

Before considering any computer vision application "done," professionals run through a checklist like this one. Use it as a template for your own future projects:

1. **Accurate labeling:** does the app's UI/output describe what the model actually does (e.g., "expression pattern classifier," not "emotion reader")?
2. **Visible confidence:** are confidence scores shown alongside labels, not hidden?
3. **Uncertainty handling:** does low-confidence output degrade gracefully (e.g., "Uncertain") rather than forcing a falsely confident label?
4. **Known limitations documented:** does a README or in-app message disclose the model's closed vocabulary, training data source, and known accuracy gaps?
5. **Transparency to those observed:** if the system watches real people, is its presence and purpose visible to them?
6. **Data handling:** if frames or clips are saved, is that clearly disclosed, and is retention/deletion considered?
7. **No high-stakes automated decisions:** is the system's output kept advisory, with a human able to review and override, rather than triggering irreversible consequences on its own?

## Key Vocabulary

| Term | Definition |
|---|---|
| Module | A self-contained unit of code (often a Python file or class) with a clear responsibility. |
| Configuration object | A centralized collection of tunable parameters used throughout an application. |
| Logging | Structured, leveled, timestamped output for tracking an application's behavior. |
| Pipeline | A sequence of processing stages, each taking the previous stage's output as input. |
| Graceful error handling | Anticipating and cleanly responding to failure cases instead of crashing. |
| Responsible-AI checklist | A structured review of an AI application's honesty, transparency, and appropriate use before deployment. |

## Real Runnable Code

```python
"""
Lesson 07 — Building a Full Computer Vision App
Mission: Deep Space Vision Lab
Requires: opencv-python, tensorflow, ultralytics
Run: python lesson07_full_app.py

This demonstrates a small but complete, well-structured application that
combines face detection + expression classification, following the same
architecture used in projects/04-real-time-emotion-detector.
"""

import logging
import time
import numpy as np
import cv2

# --- Configuration layer ---
class AppConfig:
    CAMERA_INDEX = 0
    FACE_SCALE_FACTOR = 1.1
    FACE_MIN_NEIGHBORS = 5
    FACE_MIN_SIZE = (40, 40)
    EMOTION_INPUT_SIZE = 48
    EMOTION_CONFIDENCE_THRESHOLD = 0.35
    PROCESS_EVERY_N_FRAMES = 2
    SHOW_FPS = True


# --- Logging setup ---
logging.basicConfig(level=logging.INFO, format="%(asctime)s [%(levelname)s] %(message)s")
logger = logging.getLogger("lumexa_vision_app")

EMOTION_LABELS = ["Angry", "Disgust", "Fear", "Happy", "Neutral", "Sad", "Surprise"]


# --- Detection layer: knows only about finding faces ---
class FaceDetector:
    def __init__(self, config: AppConfig):
        self.config = config
        self.cascade = cv2.CascadeClassifier(
            cv2.data.haarcascades + "haarcascade_frontalface_default.xml"
        )
        if self.cascade.empty():
            raise RuntimeError("Failed to load Haar Cascade face detector.")

    def detect(self, frame_bgr):
        gray = cv2.cvtColor(frame_bgr, cv2.COLOR_BGR2GRAY)
        faces = self.cascade.detectMultiScale(
            gray,
            scaleFactor=self.config.FACE_SCALE_FACTOR,
            minNeighbors=self.config.FACE_MIN_NEIGHBORS,
            minSize=self.config.FACE_MIN_SIZE,
        )
        return faces  # list of (x, y, w, h)


# --- Processing layer: knows only about classifying a face crop ---
class ExpressionClassifier:
    def __init__(self, config: AppConfig, model=None):
        self.config = config
        # In production, load a real trained model here, e.g.:
        # from tensorflow.keras.models import load_model
        # self.model = load_model("models/emotion_model.h5")
        # For this lesson demo, an untrained placeholder model may be passed in.
        self.model = model

    def classify(self, face_crop_bgr):
        if self.model is None:
            return "Unavailable", 0.0

        gray = cv2.cvtColor(face_crop_bgr, cv2.COLOR_BGR2GRAY)
        resized = cv2.resize(gray, (self.config.EMOTION_INPUT_SIZE, self.config.EMOTION_INPUT_SIZE))
        normalized = resized.astype("float32") / 255.0
        tensor = normalized.reshape(1, self.config.EMOTION_INPUT_SIZE, self.config.EMOTION_INPUT_SIZE, 1)

        predictions = self.model.predict(tensor, verbose=0)[0]
        best_index = int(np.argmax(predictions))
        confidence = float(predictions[best_index])

        if confidence < self.config.EMOTION_CONFIDENCE_THRESHOLD:
            return "Uncertain", confidence
        return EMOTION_LABELS[best_index], confidence


# --- Rendering layer: knows only about drawing results onto a frame ---
class ResultRenderer:
    @staticmethod
    def draw(frame, faces_with_labels, fps=None):
        for (x, y, w, h), label, confidence in faces_with_labels:
            cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
            caption = f"{label} ({confidence * 100:.0f}%)" if label != "Unavailable" else label
            cv2.putText(frame, caption, (x, max(20, y - 10)),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 255, 0), 2, cv2.LINE_AA)

        cv2.putText(frame, "LIVE ANALYSIS ACTIVE", (10, 25),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 200, 255), 2, cv2.LINE_AA)

        if fps is not None:
            cv2.putText(frame, f"FPS: {fps:.1f}", (10, frame.shape[0] - 15),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255, 255, 0), 2, cv2.LINE_AA)
        return frame


# --- Application/orchestration layer ---
class LumexaVisionApp:
    def __init__(self, config: AppConfig):
        self.config = config
        self.detector = FaceDetector(config)
        self.classifier = ExpressionClassifier(config, model=None)  # plug in a real model in production
        self.renderer = ResultRenderer()

    def run(self):
        cap = cv2.VideoCapture(self.config.CAMERA_INDEX)
        if not cap.isOpened():
            logger.error(f"Could not open camera index {self.config.CAMERA_INDEX}")
            return

        logger.info("Lumexa Vision App started. Press 'q' to quit.")
        frame_count = 0
        prev_time = time.time()

        try:
            while True:
                ret, frame = cap.read()
                if not ret:
                    logger.warning("Frame capture failed; ending session.")
                    break

                faces = self.detector.detect(frame)
                if len(faces) == 0:
                    logger.info("No faces detected in this frame (this is normal).")

                results = []
                for (x, y, w, h) in faces:
                    face_crop = frame[y:y + h, x:x + w]
                    label, confidence = self.classifier.classify(face_crop)
                    results.append(((x, y, w, h), label, confidence))

                current_time = time.time()
                fps = 1.0 / (current_time - prev_time) if current_time != prev_time else 0
                prev_time = current_time

                frame = self.renderer.draw(frame, results, fps if self.config.SHOW_FPS else None)
                cv2.imshow("Lumexa Vision App", frame)

                frame_count += 1
                if cv2.waitKey(1) & 0xFF == ord('q'):
                    logger.info("Quit key pressed by user.")
                    break

        except KeyboardInterrupt:
            logger.info("Shutdown requested via Ctrl+C.")
        except Exception as exc:
            logger.error(f"Unexpected error: {exc}")
        finally:
            cap.release()
            cv2.destroyAllWindows()
            logger.info(f"Session ended. Processed {frame_count} frames total.")


if __name__ == "__main__":
    app = LumexaVisionApp(AppConfig())
    app.run()
```

## Instructor-Guided Coding

1. Walk through the four-layer structure (config, detection, processing, rendering, orchestration) as a whiteboard diagram before showing any code, then map each class in the script back to a box on the diagram.
2. Point out that `ExpressionClassifier.model` is `None` by default in this lesson's demo — discuss what would need to change to plug in a real trained `.h5` model (loading it in `__init__`), connecting directly to Lesson 4 and the emotion-detector project.
3. Run the app with the `try/except/finally` block intact, then deliberately trigger `KeyboardInterrupt` (Ctrl+C) to show clean shutdown logging in action.
4. Discuss the "Uncertain" fallback in `classify()` as a direct, code-level implementation of the responsible-AI principle from Lesson 4.
5. Go through the seven-item responsible-AI checklist as a class, checking off which items this lesson's demo app already satisfies (visible "LIVE ANALYSIS ACTIVE" label, uncertainty handling, logging) and which would need more work for a real deployment (documented limitations, data handling policy).

## Student Mission / Guided Challenge

**Explorer Challenge: Mission-Ready Console**

1. Extend `LumexaVisionApp` with a second processing subsystem: add an `ObjectDetector` class wrapping Lesson 5's YOLOv8 pipeline, and render its detections alongside the face/expression results in the same frame.
2. Add a `save_snapshot()` method to the app that, when the user presses 's', saves the current annotated frame to a timestamped file in a `snapshots/` folder, and logs an INFO message confirming the save.
3. Move all magic numbers you can find in the script into `AppConfig` (if any remain), and add at least one new configurable option of your own (e.g., `SNAPSHOT_DIR`, `BOX_COLOR`).
4. Write your own responsible-AI checklist markdown file (reuse the 7-point structure from this lesson) evaluating your extended application honestly, including at least one limitation you have NOT yet fixed.
5. **Bonus:** Add a rotating log file handler (`logging.handlers.RotatingFileHandler`) so the app's logs are saved to disk across sessions instead of only appearing in the console.

## Common Mistakes

- **Mixing responsibilities across layers**, e.g., having the rendering class also compute detections — this makes the code harder to test and reuse; keep each class's job narrow.
- **Hardcoding values inline instead of using the config object**, making the app harder to tune consistently later.
- **Swallowing all exceptions silently** (`except: pass`) instead of logging them — this hides real bugs and makes debugging painful.
- **Forgetting that "no detections" is a normal, expected result**, not an error — logging it at `ERROR` level (instead of `INFO`) creates alarming, misleading logs.
- **Treating the responsible-AI checklist as a one-time afterthought** rather than something to revisit every time the application's capabilities change.

## Check Your Understanding

1. Name the four (or five) architectural layers described in this lesson and one responsibility of each.
2. Why is a configuration object preferable to hardcoded values scattered through the code?
3. What is the difference between `logger.info()`, `logger.warning()`, and `logger.error()`, and when would you use each?
4. Why does the `finally` block matter in the app's `run()` method?
5. Name two items from the responsible-AI checklist and explain, in your own words, why each matters.

## Mini Quiz

1. Which layer in this lesson's architecture is responsible for drawing bounding boxes and labels?
   a) Configuration  b) Detection  c) Rendering  d) Capture

2. What is the main benefit of a centralized `AppConfig` class?
   a) It makes the code run faster  b) It centralizes tunable parameters instead of scattering magic numbers  c) It replaces the need for a camera  d) It automatically fixes bugs

3. What does `logger.warning("No face detected in this frame.")` demonstrate a mistake of, in most real applications?
   a) Nothing — this is generally fine as INFO-level, not necessarily WARNING  b) It should always crash the app  c) It should never be logged  d) It's required to be ERROR level

4. Why should a model like a Haar Cascade or CNN be instantiated in `__init__` rather than inside the per-frame loop?
   a) It doesn't matter  b) So the (potentially expensive) loading only happens once, not every frame  c) Because Python requires it  d) To hide it from the user

5. What is the purpose of the responsible-AI checklist described in this lesson?
   a) To speed up the code  b) To ensure an application's claims, transparency, and limitations are honestly addressed before deployment  c) To reduce the number of files in the project  d) To satisfy a legal requirement only

### Answer Key

Mini Quiz: 1-c, 2-b, 3-a, 4-b, 5-b

## Lesson Recap

Today you moved from individual vision techniques to genuine software architecture: separating configuration, detection, processing, rendering, and orchestration into distinct, well-defined layers; using proper logging instead of scattered print statements; handling errors and cleanup gracefully; and integrating multiple subsystems (face detection + expression classification, extensible to object detection) into one cohesive application. You also formalized the track's responsible-AI thread into a concrete, reusable seven-point checklist you can apply to any future vision project.

## Homework / Extension Mission

**Mission: Architecture Review**

1. Take one of your Lesson 3–6 scripts (face detection, emotion classification, or object detection) and refactor it into the layered structure from this lesson (config class, detector/classifier class, renderer class, app class).
2. Add logging throughout your refactored app, using appropriate levels (`INFO` for normal events, `WARNING` for recoverable issues, `ERROR` for real failures).
3. Write a short (1 page) "architecture diagram" description — in words or as a simple text-based diagram — showing how data flows from camera capture through to displayed output in your refactored app.
4. Run your responsible-AI checklist against your refactored app and note any changes you'd want to make before you'd consider showing it to a real audience.

## Portfolio Connection

The layered architecture you built today (config, detection, processing, rendering, orchestration, logging, error handling) is exactly the structure used across `src/` in all three portfolio projects: `projects/04-real-time-emotion-detector`, `projects/05-object-recognition`, and `projects/06-motion-activated-security-cam`. Understanding this lesson deeply means you can read, extend, and confidently explain the "how it works" section of any of those project READMEs, and you're now equipped to build a fourth original vision app of your own design using the same professional structure.
