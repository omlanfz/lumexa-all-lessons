# Lumexa Computer Vision Path

**Lesson:** 02 of 8
**Title:** OpenCV Setup and First Image Processing
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3, OpenCV (`opencv-python`)
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, your vision array is now calibrated — you understand that images are numbers. Today you get your hands on the actual instrument that mission control uses to process those numbers in the real world: **OpenCV** (Open Source Computer Vision Library). OpenCV has been the workhorse of computer vision since 1999, running everything from robotic rovers to industrial quality-control cameras to, yes, your future face detectors and object recognizers. Today's mission is to install it, load and save real images, and perform your first genuine image-processing operations: resizing, color conversion, blurring, and edge detection.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Install and import OpenCV in a Python environment.
2. Load, display, and save image files using OpenCV.
3. Resize and crop images, and convert between color spaces.
4. Apply blurring and edge-detection filters to an image.
5. Explain what a convolution/kernel operation does at a conceptual level.

## Prerequisites

- Completion of Lesson 01 (Pixels and Arrays), or equivalent understanding of image arrays.
- Python 3.9+ with pip.
- A sample image file (`.jpg` or `.png`) saved locally, or a webcam-capable machine.

## Concept Explanation

### Installing OpenCV

OpenCV's Python bindings are installed via pip:

```
pip install opencv-python numpy
```

Once installed, it's imported with `import cv2` — the module name `cv2` refers to "Computer Vision, version 2," a legacy name that stuck even as the library moved well past version 2.

### Loading and saving images

`cv2.imread(path)` reads an image file from disk into a NumPy array (in BGR order, as you learned in Lesson 01). `cv2.imwrite(path, image)` writes an array back out to a file. `cv2.imshow(window_name, image)` opens a window to display an image — but only works when you have a display (it will fail silently or error in a headless server environment).

### Color space conversions

`cv2.cvtColor(image, code)` converts an image between color representations. The most common conversions are:

- `cv2.COLOR_BGR2GRAY` — collapses 3 color channels into 1 grayscale channel, using a weighted formula that mimics human brightness perception (green contributes most, blue least).
- `cv2.COLOR_BGR2RGB` — reorders channels for correct display in libraries like matplotlib.
- `cv2.COLOR_BGR2HSV` — converts to Hue/Saturation/Value, which is often easier to use for color-based filtering (e.g., "find everything that's red") because hue isolates color identity from brightness.

### Resizing and cropping

`cv2.resize(image, (width, height))` scales an image up or down. Note the order: OpenCV's resize takes `(width, height)`, which is the *opposite* order from the array's own `.shape` attribute of `(height, width, channels)`. This inconsistency is a frequent source of confusion — call it out explicitly to students.

Cropping doesn't need a special function — it's just NumPy array slicing: `cropped = image[y1:y2, x1:x2]`.

### Kernels and convolution: the basis of filtering

Almost every image filter — blurring, sharpening, edge detection — works using the same underlying idea: a small grid of numbers called a **kernel** (or filter) slides across the image. At each position, the kernel's numbers are multiplied against the pixel values underneath it and summed up, producing one new pixel value. This sliding-window multiply-and-sum operation is called **convolution**.

- A **blur kernel** averages together a pixel's neighbors, smoothing out noise and fine detail (e.g., Gaussian blur uses a bell-curve-weighted average).
- An **edge-detection kernel** (like the Sobel or Canny algorithms) looks for sudden jumps in brightness between neighboring pixels, since edges in real photos usually correspond to sharp intensity changes.

Understanding convolution now will pay off enormously later: the "convolutional" in "Convolutional Neural Network" (which powers our emotion recognizer in Lesson 4 and YOLO in Lesson 5) is this exact same sliding-kernel operation, except the neural network *learns* what numbers should go in the kernel instead of a human hand-designing them.

### Gaussian blur

`cv2.GaussianBlur(image, (k, k), sigma)` applies a blur using a kernel of size `k × k` (must be odd, like 3, 5, 7...). Larger kernels blur more aggressively. Blurring is often used as a *preprocessing* step before edge detection or motion detection, because it reduces sensor noise that would otherwise be mistaken for real detail.

### Canny edge detection

`cv2.Canny(image, threshold1, threshold2)` is a multi-step algorithm (blur → gradient calculation → non-maximum suppression → hysteresis thresholding) that produces a clean black-and-white map of just the edges in an image. The two threshold values control sensitivity: pixels with gradient strength above `threshold2` are automatically kept as edges, pixels below `threshold1` are discarded, and pixels in between are kept only if connected to a strong edge.

## Key Vocabulary

| Term | Definition |
|---|---|
| `cv2.imread` / `cv2.imwrite` | Functions to load/save an image file as/from a NumPy array. |
| Color space | A system for representing color numerically (BGR, RGB, HSV, grayscale). |
| Kernel | A small matrix of numbers used to transform an image via convolution. |
| Convolution | Sliding a kernel across an image, multiplying and summing values at each position. |
| Gaussian blur | A smoothing filter that averages nearby pixels with bell-curve weighting. |
| Canny edge detection | An algorithm that highlights sharp intensity transitions ("edges") in an image. |
| Cropping | Extracting a rectangular sub-region of an image via array slicing. |
| Threshold | A cutoff value used to decide between two outcomes (e.g., edge vs. no edge). |

## Real Runnable Code

```python
"""
Lesson 02 — OpenCV Setup and First Image Processing
Mission: Deep Space Vision Lab
Requires: opencv-python, numpy
Run: python lesson02_opencv_basics.py
"""

import cv2
import numpy as np

# --- 1. Create a sample image if no real photo is available ---
# (In class, replace this with cv2.imread("your_photo.jpg") if you have one.)
def make_sample_image():
    """Generates a synthetic 400x600 test image with shapes, for demo purposes."""
    img = np.full((400, 600, 3), 30, dtype=np.uint8)  # dark background
    cv2.rectangle(img, (50, 50), (250, 200), (0, 140, 255), -1)   # filled orange rectangle
    cv2.circle(img, (450, 150), 80, (255, 100, 0), -1)             # filled blue circle
    cv2.putText(img, "LUMEXA", (150, 320), cv2.FONT_HERSHEY_SIMPLEX,
                2, (255, 255, 255), 3, cv2.LINE_AA)
    return img

image = make_sample_image()
cv2.imwrite("sample_original.png", image)
print("Original image shape:", image.shape)

# --- 2. Color space conversions ---
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
hsv = cv2.cvtColor(image, cv2.COLOR_BGR2HSV)
cv2.imwrite("sample_gray.png", gray)
cv2.imwrite("sample_hsv.png", hsv)
print("Grayscale shape:", gray.shape)   # note: no third dimension
print("HSV shape:", hsv.shape)

# --- 3. Resizing ---
# cv2.resize takes (width, height) -- opposite order from .shape!
resized_up = cv2.resize(image, (900, 600))
resized_down = cv2.resize(image, (150, 100))
# Resize by scale factor instead of exact pixels:
resized_half = cv2.resize(image, None, fx=0.5, fy=0.5, interpolation=cv2.INTER_AREA)
cv2.imwrite("sample_resized_half.png", resized_half)
print("Resized (half) shape:", resized_half.shape)

# --- 4. Cropping via NumPy slicing: image[y1:y2, x1:x2] ---
cropped = image[50:200, 50:250]  # crop out just the orange rectangle region
cv2.imwrite("sample_cropped.png", cropped)
print("Cropped shape:", cropped.shape)

# --- 5. Gaussian blur ---
blurred_light = cv2.GaussianBlur(image, (5, 5), 0)
blurred_heavy = cv2.GaussianBlur(image, (25, 25), 0)
cv2.imwrite("sample_blur_light.png", blurred_light)
cv2.imwrite("sample_blur_heavy.png", blurred_heavy)

# --- 6. Canny edge detection ---
# Edge detection typically works best on a blurred grayscale image
gray_blurred = cv2.GaussianBlur(gray, (5, 5), 0)
edges = cv2.Canny(gray_blurred, threshold1=50, threshold2=150)
cv2.imwrite("sample_edges.png", edges)
print("Edge map shape:", edges.shape, "| unique values:", np.unique(edges))

# --- 7. Drawing shapes and text (useful for annotating detections later) ---
annotated = image.copy()
cv2.rectangle(annotated, (300, 250), (550, 380), (0, 255, 0), 2)  # bounding-box style rect
cv2.putText(annotated, "Confidence: 0.94", (300, 240),
            cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 255, 0), 2, cv2.LINE_AA)
cv2.imwrite("sample_annotated.png", annotated)

print("\nAll processed images saved to disk. Mission complete.")
```

## Instructor-Guided Coding

1. Live-install OpenCV together (`pip install opencv-python`) and confirm the import works with `import cv2; print(cv2.__version__)`.
2. Run the `make_sample_image()` function together and open `sample_original.png` to view it.
3. Convert to grayscale and HSV. Ask students to predict `gray.shape` before printing it — reinforce that grayscale drops the channel dimension entirely.
4. Demonstrate resizing with both exact pixel targets and scale factors (`fx`/`fy`). Discuss why `cv2.INTER_AREA` is generally best for shrinking and `cv2.INTER_LINEAR`/`cv2.INTER_CUBIC` for enlarging.
5. Crop a region using slicing and connect this back to Lesson 01's array-indexing skills.
6. Apply light vs. heavy Gaussian blur side by side and ask students to describe the visual difference in their own words.
7. Run Canny edge detection and experiment live with different `threshold1`/`threshold2` values, observing how the edge map changes.

## Student Mission / Guided Challenge

**Explorer Challenge: Sensor Calibration Station**

Using your own synthetic image (or a real photo, if available):

1. Load or generate an image and print its shape.
2. Create four processed versions: grayscale, a heavily blurred version, a Canny edge map, and a version resized to exactly 320×240.
3. Save all five images (original + 4 processed) to a folder called `output/`.
4. Draw a bounding-box rectangle and a confidence-score label onto a copy of your original image, mimicking what an object detector's output might look like.
5. **Bonus:** Write a function `pixelate(image, block_size)` that pixelates an image by shrinking it down (e.g., to 10% size) and then resizing it back up to the original size using `cv2.INTER_NEAREST` interpolation — this is a real, classic "mosaic censor" technique.

## Common Mistakes

- **Mixing up `cv2.resize`'s (width, height) order with the array's (height, width) shape.** `cv2.resize(img, (100, 200))` produces an image 100 pixels *wide* and 200 pixels *tall* — the reverse of what `.shape` would report.
- **Displaying BGR images directly in matplotlib without converting to RGB first**, resulting in swapped red/blue colors.
- **Using even-sized blur kernels.** `cv2.GaussianBlur` requires an odd kernel size like `(3,3)`, `(5,5)`, `(7,7)` — an even size like `(4,4)` raises an error, because a kernel needs a well-defined center pixel.
- **Running Canny edge detection on noisy, unblurred images**, producing a "static-y" edge map full of false edges from sensor noise rather than real object boundaries.
- **Calling `cv2.imshow` on a headless server** (no display attached), which either does nothing or throws an error — always fall back to `cv2.imwrite` to inspect results in such environments.

## Check Your Understanding

1. What Python import statement do you use after installing `opencv-python`?
2. Why does a grayscale image's `.shape` have only two dimensions instead of three?
3. What is a kernel, and how does convolution use it to process an image?
4. Why might you apply Gaussian blur before running Canny edge detection?
5. What is the argument order difference between `cv2.resize()` and an image array's `.shape`?

## Mini Quiz

1. What does `cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)` return?
   a) The same image with reversed channels  b) A single-channel grayscale image  c) An HSV image  d) A resized image

2. In `cv2.resize(image, (300, 200))`, what does 300 represent?
   a) Height in pixels  b) Width in pixels  c) Number of channels  d) Frame rate

3. Which OpenCV function detects sharp intensity changes as "edges"?
   a) `cv2.GaussianBlur`  b) `cv2.resize`  c) `cv2.Canny`  d) `cv2.imread`

4. Why must a Gaussian blur kernel size be an odd number?
   a) Even numbers are slower  b) So there's a well-defined center pixel  c) OpenCV doesn't support even numbers at all, by convention only  d) It doesn't matter

5. What does cropping an image with `image[50:200, 100:300]` actually do under the hood?
   a) Calls a special crop() function  b) Slices the underlying NumPy array  c) Resizes the image  d) Rotates the image

### Answer Key

1. `import cv2`
2. Because grayscale stores one intensity value per pixel instead of three color channels, so there's no channel axis at all.
3. A kernel is a small matrix of numbers; convolution slides it across the image, multiplying and summing overlapping values to produce each output pixel.
4. Blurring reduces sensor noise so Canny doesn't mistake random noise fluctuations for real edges.
5. `cv2.resize` takes (width, height); `.shape` reports (height, width, channels) — reversed order.

Mini Quiz answers: 1-b, 2-b, 3-c, 4-b, 5-b

## Lesson Recap

You installed OpenCV and performed your first real image-processing pipeline: loading and saving files, converting between color spaces, resizing and cropping, and applying blur and edge-detection filters. Crucially, you connected these operations back to the kernel/convolution concept — the same sliding-window mathematics that will reappear, learned automatically rather than hand-designed, inside the convolutional neural networks powering emotion recognition and object detection later in this track.

## Homework / Extension Mission

**Mission: Filter Bank**

1. Take any photo and apply at least three different Gaussian blur kernel sizes (e.g., 3×3, 11×11, 31×31). Save each result and write one sentence describing the tradeoff you observe between noise reduction and loss of detail.
2. Experiment with at least four different `(threshold1, threshold2)` pairs for Canny edge detection on the same photo. Save each result in a labeled file name.
3. Write a function `sepia_tone(image)` that manually applies a sepia color transform using a custom color-conversion matrix multiplied against each pixel (research the standard sepia matrix online, then implement it using `cv2.transform` or manual NumPy matrix multiplication).
4. In 2–3 sentences, explain why the Canny algorithm is more useful than simple thresholding for finding object outlines.

## Portfolio Connection

The image-processing pipeline skills from today are used directly in every upcoming project:

- **Real-Time Emotion Detector:** you'll resize detected face regions to a fixed size (e.g., 48×48) before feeding them into the emotion-classification neural network — exactly the resize skill practiced today.
- **Object Recognition App:** the bounding-box drawing and confidence-label annotation code you wrote today is the exact same technique used to visualize YOLO's detections.
- **Motion-Activated Security Cam:** Gaussian blur is a standard preprocessing step before motion detection, reducing false triggers from camera sensor noise.
