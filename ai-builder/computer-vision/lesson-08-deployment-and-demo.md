# Lumexa Computer Vision Path

**Lesson:** 08 of 8
**Title:** Deploying and Demoing Your Project
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3, `requirements.txt`, virtual environments, Git
**Path:** Computer Vision Mission Track

---

## Mission Brief

Cadet, this is your final mission briefing for the Computer Vision Track. You've built the sensors, the perception systems, and the full application architecture. Today, mission control needs your project ready to hand off: packaged so anyone else can install and run it, documented so anyone else can understand it, and prepared for a live demonstration in front of a real audience — instructors, family, or fellow cadets at a Lumexa showcase. Deployment and demoing aren't just "extra polish" — they're the difference between a project that only works on your machine and a project that is genuinely finished.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Package a Python computer vision project with a proper `requirements.txt` and virtual environment.
2. Write clear setup/installation instructions that another person can follow successfully.
3. Prepare a live demo that handles common failure points (camera issues, missing files) gracefully in front of an audience.
4. Structure a project README covering overview, features, installation, usage, and limitations.
5. Present a computer vision project's capabilities and limitations honestly and confidently to a non-technical audience.

## Prerequisites

- Completion of Lessons 01–07.
- A working project from this track (ideally one of the three portfolio projects, or your own Lesson 7 architecture).
- Basic command-line familiarity (`cd`, running Python scripts).

## Concept Explanation

### Why packaging matters

Code that "works on my machine" is not the same as code that works anywhere. Different computers have different Python versions, different installed libraries (or none at all), and different available hardware (a webcam vs. none). Packaging means giving your project everything another person needs to reliably reproduce your working environment.

### Virtual environments

A **virtual environment** is an isolated Python installation just for one project, so its library versions don't conflict with other projects on the same machine.

```
python -m venv venv
```

Activating it (Linux/macOS: `source venv/bin/activate`; Windows: `venv\Scripts\activate`) makes any `pip install` commands install into that isolated environment instead of system-wide. This is standard professional practice — always recommend it in your project's README.

### `requirements.txt`

A `requirements.txt` file lists every third-party library your project depends on, so a new user can install them all in one command:

```
pip install -r requirements.txt
```

Each of the three Lumexa portfolio projects ships with exactly this file, listing precisely the libraries that project's code imports — nothing more, nothing less. Keeping this file accurate (adding a line whenever you add a new `import` of a third-party package) is a simple habit that saves enormous frustration for anyone trying to run your code.

### Writing a great README

A README is often the *first and only* thing another person reads before deciding whether (and how) to run your project. A strong computer vision project README typically includes:

- **Overview** — one or two sentences on what the project does.
- **Learning objectives** — what someone builds/learns by studying it (especially valuable in an educational context like Lumexa).
- **Features** — a bullet list of concrete capabilities.
- **Project structure** — a short file/folder map.
- **Requirements** — hardware (webcam?) and software (Python version, libraries) needed.
- **Installation** — exact, copy-pasteable commands, in order.
- **Configuration** — any settings a user might want to change, and where to find them.
- **Running** — the exact command to start the project.
- **How it works** — a short technical explanation of the pipeline, connecting back to concepts learned across the lessons.
- **Common problems** — a troubleshooting section for the most likely failure points (camera not found, missing model file, library version mismatch).
- **Extensions** — ideas for how a curious user could build on the project further.
- **Responsible-use / limitations section** — an honest, specific statement of what the system does and doesn't know, matching the vocabulary practiced across Lessons 3–6.

### Preparing a live demo

Live demos are unpredictable — projectors misbehave, webcams get claimed by another app, WiFi (needed for that first-time YOLO weight download!) drops out. Professional presenters plan around this:

1. **Test the exact demo, on the exact machine, beforehand** — never assume "it worked yesterday" is enough.
2. **Pre-download any required files** (like YOLO weights) well before the demo, since a live download depending on conference/classroom WiFi is a common failure point.
3. **Have a backup**: a pre-recorded screen capture video of the working project, in case live hardware fails during the actual demo.
4. **Know your fallback talking points**: if the live demo genuinely breaks, be ready to explain what *should* be happening and why, using your own understanding of the pipeline (this is also just excellent practice for genuinely understanding your own project).
5. **Practice the "why," not just the "what."** A great demo doesn't just show boxes appearing on a video feed — it explains, briefly, what's actually happening computationally (e.g., "this is a convolutional neural network classifying facial expression patterns, evaluated fresh on every video frame").

### Presenting honestly and confidently

The single most important habit from this entire track carries into your final presentation: describe your project's real capabilities *and* real limitations, in the same breath, without either overselling ("this reads your true emotions!") or being needlessly self-deprecating about genuinely solid, real, working AI systems. A confident, honest one-liner like: *"This app classifies facial expression patterns using a convolutional neural network — it's a real, working AI pipeline, and like all such systems, its confidence scores reflect pattern similarity to training data, not certainty about someone's true internal feelings,"* demonstrates exactly the technical maturity Lumexa is trying to build in you.

## Key Vocabulary

| Term | Definition |
|---|---|
| Virtual environment | An isolated Python installation for one project's dependencies. |
| `requirements.txt` | A file listing a project's required third-party libraries. |
| Deployment | Preparing and packaging a project so it can run reliably outside your own machine. |
| README | The primary documentation file explaining a project's purpose, setup, and usage. |
| Fallback plan | A backup demo strategy (e.g., a recorded video) in case a live demo fails. |
| Reproducibility | The property that another person, following your instructions, gets the same working result. |

## Real Runnable Code

```python
"""
Lesson 08 — Deployment and Demoing Your Project
Mission: Deep Space Vision Lab
This script is a small, runnable "pre-flight check" utility that any
Lumexa cadet can run before a live demo to catch common failure points
early, rather than discovering them in front of an audience.

Requires: opencv-python (ultralytics/tensorflow optional, checked if present)
Run: python lesson08_preflight_check.py
"""

import importlib
import os
import sys

import cv2


def check_python_version(min_major=3, min_minor=9):
    ok = sys.version_info >= (min_major, min_minor)
    status = "OK" if ok else "FAIL"
    print(f"[{status}] Python version: {sys.version.split()[0]} "
          f"(need >= {min_major}.{min_minor})")
    return ok


def check_library(name):
    try:
        module = importlib.import_module(name)
        version = getattr(module, "__version__", "unknown")
        print(f"[OK] Library '{name}' is installed (version: {version})")
        return True
    except ImportError:
        print(f"[MISSING] Library '{name}' is not installed.")
        return False


def check_camera(camera_index=0):
    cap = cv2.VideoCapture(camera_index)
    opened = cap.isOpened()
    if opened:
        ret, frame = cap.read()
        cap.release()
        if ret and frame is not None:
            print(f"[OK] Camera index {camera_index} opened and returned a frame "
                  f"of shape {frame.shape}.")
            return True
        else:
            print(f"[WARN] Camera index {camera_index} opened but returned no frame.")
            return False
    else:
        print(f"[MISSING] Could not open camera index {camera_index}. "
              f"Live demos needing a webcam will not work -- have a backup video ready.")
        return False


def check_model_file(path, description):
    if os.path.isfile(path):
        size_kb = os.path.getsize(path) / 1024
        print(f"[OK] {description} found at '{path}' ({size_kb:.1f} KB).")
        return True
    else:
        print(f"[MISSING] {description} not found at '{path}'. "
              f"See the project README for how to obtain/train this file.")
        return False


def run_preflight_check():
    print("=" * 60)
    print("LUMEXA COMPUTER VISION PRE-FLIGHT CHECK")
    print("=" * 60)

    results = []
    results.append(check_python_version())

    print("\n--- Library Check ---")
    for lib in ["cv2", "numpy"]:
        results.append(check_library(lib))
    # Optional libraries depending on which project you're demoing:
    for lib in ["tensorflow", "ultralytics"]:
        importlib.import_module  # (no-op reference to avoid unused import warnings)
        try:
            importlib.import_module(lib)
            check_library(lib)
        except ImportError:
            print(f"[INFO] Optional library '{lib}' not installed "
                  f"(only needed for the emotion/object-detection projects).")

    print("\n--- Camera Check ---")
    results.append(check_camera())

    print("\n--- Model File Check (edit paths for your specific project) ---")
    check_model_file("models/emotion_model.h5", "Emotion classification model")

    print("\n" + "=" * 60)
    if all(results):
        print("PRE-FLIGHT CHECK: ALL CRITICAL SYSTEMS GO. Ready for demo.")
    else:
        print("PRE-FLIGHT CHECK: ONE OR MORE ISSUES FOUND. Review warnings above "
              "before your live demo, and prepare a backup plan (recorded video).")
    print("=" * 60)


if __name__ == "__main__":
    run_preflight_check()
```

## Instructor-Guided Coding

1. Have students create a fresh virtual environment together (`python -m venv venv`, activate it) and install a project's `requirements.txt` from a clean environment, experiencing firsthand what a new user would encounter.
2. Run the pre-flight check script together on classroom machines before any live demo, discussing any `[MISSING]` or `[WARN]` results as a class.
3. Review one of the three portfolio project READMEs section by section, mapping each section back to the README structure taught in this lesson.
4. Role-play a "demo failure" scenario: intentionally disconnect a webcam or rename a model file, run the project, and practice the instructor's calm fallback explanation ("Here's what should be happening right now...").
5. Have each student (or pair) do a 60–90 second practice pitch of their own project to a partner, including one sentence of honest limitation, then swap feedback.

## Student Mission / Guided Challenge

**Explorer Challenge: Launch Readiness Review**

1. Choose one project you've built during this track (or one of the three Lumexa portfolio projects). Write or refine its `requirements.txt` so it lists exactly the libraries actually imported by the code — no more, no less.
2. Run the pre-flight check script against your chosen project's expected model files and camera requirements, fixing any `[MISSING]` results you can.
3. Write or revise that project's README following the structure from this lesson (Overview, Features, Structure, Requirements, Installation, Configuration, Running, How It Works, Common Problems, Extensions, Limitations/Responsible Use).
4. Prepare a 90-second spoken demo script (written out) that you could read/deliver live, including at least one honest limitation stated confidently, not apologetically.
5. **Bonus:** Record a 1–2 minute screen-capture backup video of your project running successfully, to use as a fallback if live hardware fails during an actual presentation.

## Common Mistakes

- **Shipping a `requirements.txt` that's missing a library** the code actually imports, causing a fresh install to crash immediately.
- **Never testing the installation instructions on a truly clean environment** — "it works on my machine" is not the same as "it works."
- **Relying entirely on live internet access during a demo** (e.g., first-time YOLO weight downloads) without a pre-downloaded fallback.
- **Overselling capabilities in a demo** ("this literally reads your emotions") instead of using the precise, honest vocabulary practiced throughout this track.
- **Having no backup plan at all** if the live camera or hardware fails mid-demo, leading to an awkward, project-ending silence instead of a graceful recovery.

## Check Your Understanding

1. What problem does a virtual environment solve?
2. Why should `requirements.txt` be kept accurate as a project evolves?
3. List three sections a strong computer vision project README should include.
4. Name two concrete steps to prepare for the possibility of a live demo failing.
5. Give an example of an honest, precise one-sentence description of an emotion-classification project's limitations, suitable for a live audience.

## Mini Quiz

1. What command creates a new Python virtual environment?
   a) `pip install venv`  b) `python -m venv venv`  c) `cv2.venv()`  d) `git venv`

2. What is the purpose of `requirements.txt`?
   a) It stores camera settings  b) It lists the third-party libraries a project needs, for easy installation  c) It's a log file  d) It stores model weights

3. Which of the following is a recommended live-demo preparation step?
   a) Rely on live WiFi to download model weights during the demo  b) Pre-download required files and have a backup recorded video  c) Skip testing since the code "worked yesterday"  d) Avoid writing a README to save time

4. Which statement best reflects the responsible presentation habit taught in this lesson?
   a) Always claim the highest possible accuracy without caveats  b) State both real capabilities and real limitations honestly and confidently  c) Avoid mentioning limitations in a demo to seem more impressive  d) Only technical audiences need to hear about limitations

5. What should a project README's "Common Problems" section contain?
   a) A list of every possible Python error message  b) Troubleshooting guidance for the most likely real failure points (camera, missing files, versions)  c) Marketing copy  d) The full source code

### Answer Key

Mini Quiz: 1-b, 2-b, 3-b, 4-b, 5-b

## Lesson Recap

In this final lesson of the Computer Vision Track, you learned to package a project properly with virtual environments and an accurate `requirements.txt`, write a complete and genuinely useful README, prepare for the realities of live demoing (including planning for failure), and present your work with the same honest, precise vocabulary about AI capabilities and limitations that has run through this entire track since Lesson 3. This closes the loop from Lesson 1's "pixels are just numbers" through to a fully deployed, demo-ready, responsibly-framed AI application — you are now equipped to build, document, and present original computer vision projects beyond this track.

## Homework / Extension Mission

**Mission: Mission Control Handoff**

1. Package one full project (your own, or one of the three Lumexa portfolio projects) completely: verified `requirements.txt`, a complete README following this lesson's structure, and a working pre-flight check.
2. Have a family member, friend, or classmate who did *not* build the project try to install and run it using only your README, with no other help from you. Note every point where they got confused or stuck, and revise your README to fix each one.
3. Write a final one-paragraph "responsible use" statement for your project, written for a general (non-technical) audience, honestly describing what the system does, what it doesn't know, and appropriate contexts for using it.
4. Deliver your 90-second demo pitch (from the Explorer Challenge) to at least one real listener, and note what question they asked that you hadn't anticipated.

## Portfolio Connection

This lesson directly completes all three Lumexa portfolio projects: `projects/04-real-time-emotion-detector`, `projects/05-object-recognition`, and `projects/06-motion-activated-security-cam` each already ship with a full README following exactly this lesson's structure, an accurate `requirements.txt`, and documented limitations/responsible-use sections. Applying this lesson's practices means you are now able to take any of them — or a new project of your own design — from "working on my machine" to genuinely finished, deployable, and demo-ready, the true mark of a completed Lumexa Computer Vision cadet.
