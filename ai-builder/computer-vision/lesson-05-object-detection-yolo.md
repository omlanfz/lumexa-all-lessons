# Lumexa Computer Vision Path

**Lesson:** 05 of 8
**Title:** Object Detection with YOLO
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3, Ultralytics YOLOv8 (`ultralytics`), OpenCV
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, mission control needs a full inventory scan of the cargo bay — not just "is there a face here" but "what *objects* are present, and where, across dozens of categories: crates, tools, vehicles, animals, people, and more?" Today you'll deploy **YOLO** ("You Only Look Once"), the most influential real-time object detection architecture in modern computer vision, using its current, actively maintained implementation: **Ultralytics YOLOv8**. Unlike the sliding-window cascade approach from Lesson 3, YOLO looks at the *entire image at once* and, in a single pass, predicts every object's location and class simultaneously — hence the name.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain the core idea behind YOLO's single-pass detection approach and how it differs from sliding-window methods.
2. Install and run a pre-trained YOLOv8 model using the `ultralytics` Python package.
3. Run object detection on both static images and video frames, interpreting bounding boxes, class labels, and confidence scores.
4. Filter detections by confidence threshold and class of interest.
5. Explain the limitations of a closed-vocabulary detector trained on a fixed dataset like COCO.

## Prerequisites

- Completion of Lessons 01–04.
- `pip install ultralytics opencv-python`.
- Internet access on first run (Ultralytics automatically downloads pre-trained weights the first time a model is used).

## Concept Explanation

### From cascades to single-pass detection

The Haar Cascade in Lesson 3 works by sliding a search window across many positions and scales, testing "is this a face?" at each one. That's effective for single-class detection but becomes very slow if you want to also ask "is this a dog, a car, a backpack, a bicycle...?" at every location.

YOLO takes a fundamentally different approach. It divides the image into a grid and, using a single deep convolutional neural network pass, simultaneously predicts for many candidate regions: whether an object is present, what its bounding box coordinates are, and which of many trained classes it most likely belongs to — all at once, hence "You Only Look Once." This is why YOLO models can run in real time on video (many frames per second) even while detecting dozens of object categories simultaneously.

### The COCO dataset and its 80 classes

Most pre-trained YOLO models, including the ones we use today, are trained on the **COCO** (Common Objects in Context) dataset, which contains roughly 80 everyday object categories: person, bicycle, car, dog, cat, backpack, bottle, chair, laptop, cell phone, and many more. This is a **closed vocabulary** — the model can only ever output labels from this fixed list of ~80 classes. If you point the camera at, say, a specific spacecraft part or a rare species of plant that isn't one of COCO's categories, the detector will either miss it entirely or (worse) mislabel it as the closest class it *does* know, with some reported confidence.

### Ultralytics YOLOv8

Ultralytics packaged the YOLO architecture into an easy pip-installable Python library:

```
pip install ultralytics
```

```python
from ultralytics import YOLO
model = YOLO("yolov8n.pt")  # "n" = nano, the smallest/fastest variant
```

The first time this runs, Ultralytics automatically downloads the pre-trained `yolov8n.pt` weights file (trained on COCO) from its official release servers — no manual URL or account needed. YOLOv8 ships in several sizes: `n` (nano), `s` (small), `m` (medium), `l` (large), `x` (extra-large) — smaller models run faster with somewhat lower accuracy; larger models are more accurate but slower. For real-time webcam work on a laptop, `yolov8n.pt` is the recommended starting point.

### Running detection

```python
results = model(image)  # or model(image_path), or model(frame) for a video frame
```

`results` is a list (one entry per input image) of `Results` objects. Each contains a `.boxes` attribute with, for every detected object: `.xyxy` (bounding box as `x1, y1, x2, y2` corner coordinates), `.conf` (confidence score, 0–1), and `.cls` (class index, mapped to a name via `model.names[class_index]`).

### Confidence thresholds and filtering

By default, YOLO only reports detections above roughly 0.25 confidence, but this is configurable via `model(image, conf=0.5)`. Raising the threshold reduces false positives (spurious low-confidence detections) but can cause the model to miss genuinely present but less-clear objects — the same false-positive/false-negative tradeoff you practiced tuning in Lesson 3, just in a different, more capable system.

### Responsible AI: what an object detector actually knows

- **It can only ever report classes it was trained on.** A YOLOv8 model trained on COCO's 80 classes has *no concept whatsoever* of anything outside that list — it isn't "uncertain" about an unfamiliar object so much as forced to either ignore it or misclassify it as the nearest known class.
- **Confidence scores are not guarantees.** A 0.91 confidence "dog" detection means the network's learned patterns for the detected region statistically resemble its "dog" training examples strongly — it does not mean there is a 91% chance a dog is *definitely* there in some absolute sense, and it can still be wrong, especially on unusual poses, lighting, occlusion, or objects that resemble multiple trained classes.
- **Detection quality varies by class representation in training data.** COCO, like most datasets, has far more labeled examples of some categories (person, car) than others (toaster, hair drier) — rarer classes are typically detected less reliably.
- **Bounding boxes describe location, not meaning or intent.** A detector reporting "person, 0.88 confidence" at a certain location says nothing about that person's identity, activity, or intentions — treating an object detector's output as more meaningful than "this region resembles this trained category" risks serious real-world misuse, particularly in surveillance-adjacent applications (a theme we'll return to directly in Lesson 6's security-camera project, where privacy and appropriate-use framing matter as much as the code).

## Key Vocabulary

| Term | Definition |
|---|---|
| YOLO | "You Only Look Once" — a single-pass object detection architecture. |
| COCO dataset | A large labeled image dataset with ~80 common object categories, used to pre-train YOLO. |
| Closed vocabulary | A model's fixed, limited set of classes it can ever output. |
| Bounding box | Rectangle coordinates marking a detected object's location, here as `(x1, y1, x2, y2)`. |
| Confidence threshold | The minimum score required for a detection to be reported. |
| `ultralytics` | The Python package providing an easy interface to YOLO models. |
| Class index | An integer identifying which trained category a detection belongs to. |

## Real Runnable Code

```python
"""
Lesson 05 — Object Detection with YOLOv8 (Ultralytics)
Mission: Deep Space Vision Lab
Requires: ultralytics, opencv-python
Run: python lesson05_yolo_detection.py

First run will automatically download yolov8n.pt (pre-trained on COCO)
from Ultralytics' official servers -- requires internet access once.
"""

import cv2
from ultralytics import YOLO

# --- 1. Load the pre-trained YOLOv8 nano model ---
# "n" = nano variant: smallest and fastest, ideal for real-time / laptop use.
model = YOLO("yolov8n.pt")

print("Model loaded. It recognizes these classes (COCO's ~80 categories):")
print(list(model.names.values()))

# --- 2. Run detection on a static image ---
# Replace "cargo_bay.jpg" with any real photo containing everyday objects.
image_path = "cargo_bay.jpg"
image = cv2.imread(image_path)

if image is None:
    print(f"\nNo {image_path} found -- generating a synthetic placeholder image.")
    import numpy as np
    image = np.full((480, 640, 3), 220, dtype=np.uint8)
    cv2.rectangle(image, (100, 150), (300, 400), (60, 60, 200), -1)  # placeholder shape
    cv2.putText(image, "No real photo provided", (60, 40),
                cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 0, 0), 2, cv2.LINE_AA)

results = model(image, conf=0.4)  # only report detections with confidence >= 0.4

# --- 3. Parse and draw detection results manually ---
annotated = image.copy()
detection_count = 0

for result in results:                     # one Results object per input image
    boxes = result.boxes                   # Boxes object holding all detections
    for box in boxes:
        x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())  # corner coordinates
        confidence = float(box.conf[0])
        class_id = int(box.cls[0])
        class_name = model.names[class_id]

        detection_count += 1
        label = f"{class_name} {confidence:.2f}"

        cv2.rectangle(annotated, (x1, y1), (x2, y2), (0, 200, 0), 2)
        cv2.putText(annotated, label, (x1, max(20, y1 - 8)),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 200, 0), 2, cv2.LINE_AA)

        print(f"Detected: {class_name} | confidence={confidence:.2f} | "
              f"box=({x1},{y1})-({x2},{y2})")

print(f"\nTotal objects detected: {detection_count}")
cv2.imwrite("cargo_bay_detected.png", annotated)
print("Annotated image saved as cargo_bay_detected.png")

# --- 4. Using Ultralytics' own built-in plotting (a convenient shortcut) ---
# result.plot() returns a ready-annotated BGR numpy array -- handy for quick demos.
for result in results:
    quick_annotated = result.plot()
    cv2.imwrite("cargo_bay_quickplot.png", quick_annotated)

# --- 5. Filtering detections by class of interest ---
TARGET_CLASSES = {"person", "backpack", "bottle", "laptop"}

interesting = [
    (model.names[int(box.cls[0])], float(box.conf[0]))
    for result in results
    for box in result.boxes
    if model.names[int(box.cls[0])] in TARGET_CLASSES
]
print(f"\nDetections matching target classes {TARGET_CLASSES}:")
for name, conf in interesting:
    print(f"  - {name} ({conf:.2f})")

print("\nResponsible-use reminder: this model can ONLY report the ~80 COCO classes "
      "it was trained on, and confidence scores reflect pattern similarity to "
      "training data -- not a guarantee of what is truly present.")
```

## Instructor-Guided Coding

1. Install `ultralytics` together and load `YOLO("yolov8n.pt")`, watching the automatic weight download happen live. Print `model.names` and have students scan the list — ask them to name three everyday objects that are *not* in this list (a great segue into the closed-vocabulary limitation).
2. Run detection on a class-provided photo or the synthetic placeholder and view the annotated result.
3. Compare `conf=0.25` (default) vs. `conf=0.7` on the same image, discussing how many detections disappear and why.
4. Walk through the manual box-parsing loop line by line, connecting `.xyxy`, `.conf`, `.cls` back to the bounding-box concepts from Lesson 3.
5. Demonstrate `result.plot()` as a fast built-in alternative to manual drawing, but emphasize that understanding the manual version matters for building custom applications later.
6. Discuss as a class: "This model was trained on photos mostly from Western, English-language internet sources circa 2014–2017. What kinds of objects, environments, or cultural contexts might be underrepresented, and what might that mean for detection accuracy in different parts of the world?"

## Student Mission / Guided Challenge

**Explorer Challenge: Cargo Bay Inventory Scanner**

1. Run YOLOv8n detection on at least two different real or synthetic images containing multiple everyday objects.
2. Build a "mission inventory report": count how many objects of each class were detected across both images and print a summary table, e.g., `person: 3, backpack: 1, bottle: 2`.
3. Modify the confidence threshold to `0.6` and compare your inventory report — which detections disappeared?
4. Write a function `highlight_class(results, model, target_class, color)` that draws bounding boxes only for one specific class of interest, in a custom color, ignoring all other detected objects.
5. **Bonus:** Save a cropped image of just the single highest-confidence detected object from your image (hint: use the box coordinates to slice the original image array, exactly as practiced in Lesson 1/2).

## Common Mistakes

- **Expecting the model to detect objects outside COCO's ~80 classes.** If it isn't in `model.names`, the detector cannot output it as a label — it will either miss it or mislabel it as the closest known class.
- **Forgetting `int()` conversions on box coordinates.** `box.xyxy` returns floating-point tensor values; passing floats directly to `cv2.rectangle` raises an error — always convert to integers first.
- **Setting the confidence threshold too low**, flooding the output with low-quality, likely-spurious detections; or too high, silently losing real objects.
- **Assuming a high confidence score means the detection is certainly correct.** It reflects pattern-matching similarity to training data, not absolute certainty.
- **Running detection on every single video frame at full resolution without considering performance**, which we'll address directly with practical strategies in Lesson 6's real-time webcam lesson.

## Check Your Understanding

1. What does the acronym YOLO stand for, and what does it capture about the algorithm's approach?
2. How does YOLO's approach differ from the Haar Cascade sliding-window approach from Lesson 3?
3. What does `model.names` represent, and why does its length matter for what the model can detect?
4. What are `.xyxy`, `.conf`, and `.cls` on a detection box, respectively?
5. Why can't a YOLOv8n model trained on COCO ever report a class like "spacecraft thruster nozzle"?

## Mini Quiz

1. What dataset are the pre-trained YOLOv8 weights used in this lesson trained on?
   a) ImageNet  b) COCO  c) FER2013  d) MNIST

2. Roughly how many object classes does the standard COCO-trained YOLOv8 model recognize?
   a) 10  b) 80  c) 1,000  d) Unlimited

3. What does raising the `conf` threshold from 0.25 to 0.7 typically do?
   a) Detects more objects overall  b) Reduces reported detections, possibly missing real but lower-confidence objects  c) Makes the model faster only, with no effect on results  d) Changes the model's trained classes

4. Which YOLOv8 variant is smallest and fastest, best suited for real-time laptop use?
   a) yolov8x  b) yolov8l  c) yolov8n  d) yolov8m

5. What is the most accurate way to describe a YOLO detection's confidence score?
   a) A guarantee the object is present  b) An estimated probability based on similarity to training data  c) A random number  d) The object's exact size in pixels

### Answer Key

Mini Quiz: 1-b, 2-b, 3-c, 4-c, 5-b

## Lesson Recap

Today you deployed a modern, real-time-capable object detector, YOLOv8, understanding its single-pass grid-based architecture as a contrast to Lesson 3's sliding-window cascade. You practiced loading a pre-trained model, running detection on images, parsing bounding boxes/confidence/class results, and filtering by class and confidence threshold. You also reinforced the responsible-AI thread of this track: closed-vocabulary limitations, confidence scores as pattern-similarity estimates rather than guarantees, and training-data representation gaps that can produce uneven real-world accuracy.

## Homework / Extension Mission

**Mission: Class Coverage Audit**

1. Print the full list of all ~80 COCO classes `model.names` supports. Identify five real-world objects you interact with daily that are *not* on this list.
2. Run detection on at least three of your own real or synthetic photos and record, for each, the objects detected and their confidence scores in a simple table.
3. Experiment with the `yolov8s.pt` (small) variant instead of `yolov8n.pt` on the same images (it will auto-download on first use) — compare detection count, confidence scores, and (informally, by timing your script) speed.
4. Write a 4–6 sentence "appropriate use" statement for an object-recognition app built on this technology, describing at least one situation where its output should not be treated as a final, standalone decision-maker (e.g., safety-critical systems, automated access control).

## Portfolio Connection

Today's entire pipeline — model loading, detection, box/label/confidence parsing, and class filtering — is the direct technical foundation of the **Object Recognition App** portfolio project (`projects/05-object-recognition`), which extends this exact code to work on both static images and a live webcam feed, and documents the same COCO closed-vocabulary and confidence-score limitations you learned today in its README.
