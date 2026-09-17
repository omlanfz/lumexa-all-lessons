# Lumexa Computer Vision Path

**Lesson:** 01 of 8
**Title:** How Computers See — Pixels and Arrays
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3, NumPy, Matplotlib
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, welcome to Deep Space Vision Lab. Before any spacecraft can dock automatically, chase down an asteroid, or recognize a fellow astronaut through a helmet visor, it needs a way to *see*. Human eyes send light through a lens onto a retina, which converts it into nerve signals. A computer has no retina — instead, it has a camera sensor that converts light into **numbers**. Every image you have ever seen on a screen, from a mission-control dashboard to a photo of Jupiter's rings, is secretly a giant grid of numbers.

Today's mission: learn to think like a computer sees. By the end of this lesson, you will be able to load an image in Python, inspect its raw numeric structure, and manipulate individual pixels — the same foundational skill that powers every face detector, emotion classifier, and object recognizer you will build later in this track.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain what a pixel is and how images are represented as numerical arrays.
2. Describe the difference between grayscale and color (RGB/BGR) images.
3. Use NumPy to inspect the shape, data type, and values of an image array.
4. Read, display, and manipulate an image using Python.
5. Explain why the resolution and bit depth of an image affect its size and detail.

## Prerequisites

- Basic Python: variables, loops, lists, functions.
- Basic understanding of coordinate systems (rows/columns or x/y).
- Python 3.9+ installed with `numpy`, `matplotlib`, and `opencv-python` available (`pip install numpy matplotlib opencv-python`).
- No prior computer vision experience required.

## Concept Explanation

### Pixels: the atoms of an image

A digital image is a rectangular grid of tiny colored squares called **pixels** (short for "picture elements"). Each pixel stores a numeric value that represents its brightness and, for color images, its color.

Think of a spacecraft's hull camera feed as graph paper. Every little square on the graph paper gets colored in with a number. Zoom in far enough on any digital photo and you will see this graph-paper structure — the smooth photograph dissolves into a mosaic of colored squares.

### Grayscale images: one number per pixel

The simplest image type is **grayscale**. Every pixel is a single number between 0 and 255:

- `0` = pure black
- `255` = pure white
- Everything in between is a shade of gray

Why 0–255? Because computers store this value using 8 bits, and 8 bits can represent 2⁸ = 256 distinct values (0 through 255). This is called **8-bit depth**, and it is the most common bit depth for everyday images.

A grayscale image with a height of 480 pixels and a width of 640 pixels is stored as a 2D array of shape `(480, 640)` — 480 rows, 640 columns, one number per cell.

### Color images: three numbers per pixel

A color image adds two more numbers per pixel. Each pixel becomes a small list of three values representing the intensity of **Red**, **Green**, and **Blue** light — the same three colors of light your monitor mixes to create every color you see. This is called the **RGB color model**.

A color image with height 480 and width 640 is stored as a 3D array of shape `(480, 640, 3)`. The `3` is called a **channel** dimension — one channel for red, one for green, one for blue.

Important mission detail: **OpenCV**, the computer vision library we will use starting in Lesson 2, loads color images in **BGR order** (Blue, Green, Red) instead of RGB, for historical reasons dating back to early camera hardware standards. This trips up almost every beginner at least once — we will practice converting between the two.

### Resolution and array shape

The **resolution** of an image is its width × height in pixels. A 1920×1080 ("Full HD") image has over 2 million pixels. For a color image, that means over 6 million individual numbers describing just one single frame. A real-time video feed showing 30 frames per second is therefore processing over 180 million numbers *every second* — which is exactly why efficient array libraries like NumPy (and, under the hood, OpenCV written in optimized C++) matter so much in computer vision.

### Coordinate systems: rows and columns vs. x and y

In image arrays, the first index is usually the **row** (vertical position, top to bottom) and the second index is the **column** (horizontal position, left to right). This is the opposite order from the (x, y) coordinates you may be used to from math class, where x is horizontal and comes first. Getting this backwards is one of the most common bugs in computer vision code — we will call this out explicitly with a Common Mistake below.

### Why this matters for AI vision systems

Every technique later in this track — face detection, emotion recognition, object detection with YOLO — ultimately works by performing mathematical operations on these pixel arrays. A face detector doesn't "see a face" the way you do; it scans grids of numbers looking for patterns of light and dark that statistically correlate with what human faces tend to look like. Keeping this in mind early will help you understand, later in the track, why these systems can make mistakes: they are pattern-matching on numbers, not truly perceiving the world.

## Key Vocabulary

| Term | Definition |
|---|---|
| Pixel | The smallest unit of a digital image; stores a numeric brightness/color value. |
| Grayscale image | An image where each pixel is a single intensity value from 0 (black) to 255 (white). |
| RGB | A color model representing color as a mix of Red, Green, and Blue intensities. |
| BGR | The reversed channel order (Blue, Green, Red) used by default in OpenCV. |
| Channel | One layer of a color image's array (e.g., the red channel). |
| Resolution | The width × height of an image, measured in pixels. |
| Bit depth | The number of bits used to store each pixel value (8-bit = 0–255). |
| Array shape | The dimensions of a NumPy array, e.g., `(height, width, channels)`. |
| NumPy | A Python library for fast numerical array operations, used under the hood by OpenCV. |

## Real Runnable Code

```python
"""
Lesson 01 — Pixels and Arrays
Mission: Deep Space Vision Lab
This script demonstrates how an image is really just a grid of numbers.
Requires: numpy, matplotlib
"""

import numpy as np
import matplotlib.pyplot as plt

# --- 1. Build a tiny grayscale image by hand ---
# 5 rows x 5 columns, values from 0 (black) to 255 (white)
tiny_grayscale = np.array([
    [0,   50,  100, 150, 200],
    [50,  100, 150, 200, 255],
    [100, 150, 200, 255, 200],
    [150, 200, 255, 200, 150],
    [200, 255, 200, 150, 100]
], dtype=np.uint8)  # uint8 = unsigned 8-bit integer, range 0-255

print("Shape of our tiny grayscale image:", tiny_grayscale.shape)
print("Data type:", tiny_grayscale.dtype)
print("Pixel at row 2, column 3:", tiny_grayscale[2, 3])

# Display it — matplotlib will stretch each cell into a visible square
plt.figure(figsize=(4, 4))
plt.imshow(tiny_grayscale, cmap="gray", vmin=0, vmax=255)
plt.title("A 5x5 Grayscale Image (Zoomed In)")
plt.colorbar(label="Pixel Intensity (0=black, 255=white)")
plt.savefig("tiny_grayscale.png")
plt.close()

# --- 2. Build a tiny color (RGB) image by hand ---
# Shape (height, width, channels) = (3, 3, 3)
tiny_color = np.array([
    [[255, 0, 0], [0, 255, 0], [0, 0, 255]],       # red, green, blue
    [[255, 255, 0], [0, 255, 255], [255, 0, 255]], # yellow, cyan, magenta
    [[0, 0, 0], [128, 128, 128], [255, 255, 255]]  # black, gray, white
], dtype=np.uint8)

print("\nShape of our tiny color image:", tiny_color.shape)
print("Pixel at row 0, col 0 (should be pure red RGB):", tiny_color[0, 0])

plt.figure(figsize=(4, 4))
plt.imshow(tiny_color)
plt.title("A 3x3 Color Image (Zoomed In)")
plt.savefig("tiny_color.png")
plt.close()

# --- 3. Manipulate pixels directly ---
# Make a copy so we don't damage the original array
modified = tiny_grayscale.copy()

# Invert every pixel: black becomes white and vice versa
modified = 255 - modified
print("\nOriginal top-left pixel:", tiny_grayscale[0, 0])
print("Inverted top-left pixel:", modified[0, 0])

# Zero out (blacken) the top row only — a simple region edit
modified[0, :] = 0
print("Top row after blacking it out:", modified[0])

# --- 4. Load a real photo and inspect it (requires a sample image file) ---
# Uncomment these lines once you have a real .jpg or .png in this folder:
#
# import cv2
# photo = cv2.imread("sample.jpg")           # loads as BGR by default
# print("Real photo shape:", photo.shape)     # (height, width, 3)
# print("Total pixels:", photo.shape[0] * photo.shape[1])
# print("Total numbers stored:", photo.size)  # height * width * channels
#
# rgb_photo = cv2.cvtColor(photo, cv2.COLOR_BGR2RGB)  # convert BGR -> RGB
# plt.imshow(rgb_photo)
# plt.title("Real Photo (correctly color-converted)")
# plt.show()
```

## Instructor-Guided Coding

Walk through this live with the class, typing each step together:

1. Open a Python REPL or notebook and create the `tiny_grayscale` array together. Ask students to predict what `tiny_grayscale.shape` will print *before* running it.
2. Have students individually change one pixel value (e.g., `tiny_grayscale[0, 0] = 255`) and predict how the displayed image will change before re-running `plt.imshow`.
3. Introduce the color array. Ask: "If I want the pixel at row 1, column 2 to be pure blue, what three numbers do I need?" Have a student answer, then verify together.
4. Demonstrate the inversion operation (`255 - modified`) and ask students to explain in their own words why this flips black and white.
5. If a webcam or sample photo is available, load a real photo with `cv2.imread` and project `photo.shape` on screen. Emphasize the (rows, columns, channels) ordering versus the (x, y) ordering students may expect.

## Student Mission / Guided Challenge

**Explorer Challenge: Build Your Own Nebula**

Using only NumPy arrays (no image files), construct a 10×10 grayscale image that looks like a simple gradient nebula: values should smoothly increase from 0 in the top-left corner to 255 in the bottom-right corner. Then:

1. Print the shape and data type of your array.
2. Display it using `plt.imshow` with a grayscale colormap.
3. Modify your array so the center 2×2 block of pixels becomes pure white (255), representing a "star" at the nebula's core.
4. Print the average pixel value of your entire image using `np.mean()`.
5. **Bonus:** Convert your grayscale nebula into a color image (shape `(10, 10, 3)`) where the "star" glows red instead of white.

Students should submit their script plus the saved PNG of their nebula image.

## Common Mistakes

- **Confusing (row, column) with (x, y).** Remember: `image[row, column]` in NumPy means `image[y, x]` in everyday (x, y) terms, not `image[x, y]`. This backwards-feeling order trips up nearly everyone at first.
- **Assuming color channels are always RGB.** OpenCV loads and saves images in BGR order by default. Forgetting to convert with `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` before displaying with matplotlib produces images with swapped red and blue colors — a classic "why is the sky orange" bug.
- **Using the wrong data type.** Pixel arithmetic on `uint8` arrays wraps around instead of clipping — `np.uint8(250) + np.uint8(10)` equals `4`, not `260`, because it overflows. Always be careful with data types during pixel math, and convert to a larger type (like `int16` or `float32`) before doing arithmetic that might exceed 255.
- **Forgetting images are copied by reference.** Writing `modified = tiny_grayscale` (without `.copy()`) means changes to `modified` will also silently change `tiny_grayscale`, since both variables point to the same array in memory.

## Check Your Understanding

1. What is a pixel, and what does the number stored in it represent?
2. Why does an 8-bit grayscale image only use values from 0 to 255?
3. What is the shape of a color image that is 100 pixels tall and 200 pixels wide?
4. What does "BGR" stand for, and which library uses it by default?
5. If you add 20 to every pixel in a `uint8` image, what problem might occur near white pixels, and why?

## Mini Quiz

1. A grayscale image has shape `(300, 400)`. How many total pixels does it contain?
   a) 300  b) 400  c) 120,000  d) 700

2. Which value represents pure black in an 8-bit grayscale image?
   a) 0  b) 1  c) 128  d) 255

3. A color image has shape `(480, 640, 3)`. What does the `3` represent?
   a) Three copies of the image  b) Three color channels  c) Three seconds of video  d) Three cameras

4. In NumPy, `image[row, column]` corresponds to which everyday coordinate order?
   a) (x, y)  b) (y, x)  c) They are always the same  d) Neither

5. What data type is most commonly used to store 8-bit pixel values in NumPy?
   a) `int32`  b) `float64`  c) `uint8`  d) `bool`

### Answer Key

1. c) 120,000 (300 × 400)
2. a) 0
3. b) Three color channels (Red, Green, Blue — or Blue, Green, Red in OpenCV)
4. b) (y, x) — row corresponds to the y (vertical) position, column to x (horizontal)
5. c) `uint8`

## Lesson Recap

Today you learned that every digital image a computer processes is fundamentally a grid of numbers stored in an array. Grayscale images use one number per pixel (0–255); color images use three (Red, Green, Blue channels), and OpenCV specifically stores them in reversed BGR order. You practiced building, inspecting, and modifying these arrays directly with NumPy, and you learned to recognize the (row, column) versus (x, y) ordering trap that catches many beginners. This numeric foundation underlies everything from here forward — face detection, emotion recognition, and object detection all ultimately reduce to mathematical operations on these same pixel grids.

## Homework / Extension Mission

**Mission: Pixel Detective**

1. Find any digital photo (from your own device, with permission, or a royalty-free source). Write a Python script using OpenCV to load it and print its shape, data type, and total pixel count.
2. Split the image into its three separate channels (red, green, blue) and display each channel as its own grayscale image. Which channel is brightest in your photo, and why do you think that is?
3. Write a function `darken(image, amount)` that safely subtracts `amount` from every pixel without wrapping around below 0 (hint: convert to a signed type first, clip the values, then convert back to `uint8`).
4. In two or three sentences, explain in your own words the difference between an image's *resolution* and its *bit depth*.

## Portfolio Connection

The pixel-array skills from this lesson are the bedrock of every project in this track:

- **Real-Time Emotion Detector:** every video frame you process will be a live-updating NumPy array that you crop, resize, and feed into a neural network.
- **Object Recognition App:** bounding boxes you draw around detected objects are just coordinates into this same pixel grid.
- **Motion-Activated Security Cam:** motion detection works by comparing pixel arrays between frames and measuring how much they've changed.

Keep today's nebula-building script — you'll recognize this exact array-manipulation pattern reappearing in every later lesson.
