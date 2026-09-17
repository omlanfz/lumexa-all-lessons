# Lumexa Mission Log: Python AI Foundations

**Course/Path:** Python & AI Foundations
**Lesson:** 01 of 08
**Title:** Python Setup and First Program
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3.11+, VS Code (or any text editor), Terminal/Command Line

---

## Mission Brief

Welcome aboard the Lumexa Explorer Program, Cadet. Every mission to deep space starts with checking your equipment before launch. Today's mission is not about flying a ship — it's about assembling the tools you'll use for every mission from here on: your Python environment. By the end of this session, you will have Python installed and verified on your machine, you will understand what a programming language actually is, and you will have written and executed your very first program. This is the ignition sequence. Nothing in later missions (building prediction models, training AI, classifying images) works without this foundation being solid.

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain what Python is and why it is used for AI and data science.
2. Install Python and confirm the installation using the terminal.
3. Open and use a code editor (VS Code recommended) to write Python files.
4. Write, save, and run a `.py` file from the command line.
5. Use the `print()` function to display output and add comments to code.
6. Identify and fix a basic syntax error.

## Prerequisites

- No prior programming experience required.
- A laptop or desktop computer (Windows, macOS, or Linux) with administrator access to install software.
- Basic familiarity with using a file explorer/finder and opening applications.

## Concept Explanation

### What is a programming language?

A computer only understands electrical signals — ultimately just 1s and 0s. A programming language is a bridge between how humans think and how computers operate. Python is one of the most popular programming languages in the world, and it is the primary language used in artificial intelligence, machine learning, data science, robotics, and space mission software (NASA and SpaceX both use Python for parts of their tooling). Python was designed to be readable — it looks almost like structured English, which is part of why it's the best starting point for both new coders and professional AI engineers.

### Why Python for AI?

Every project in this course — the Number Prediction Model, the Simple Image Classifier, and the Data Pattern Finder — is built in Python because:

- It has powerful, free libraries built for AI: NumPy (math), pandas (data), scikit-learn (machine learning), TensorFlow/Keras (deep learning).
- It's beginner-friendly: no semicolons, minimal punctuation, clear structure.
- It's the industry standard: professional AI engineers at companies like Google, OpenAI, and NASA JPL use Python daily.

### Interpreters, scripts, and the terminal

Python code is not compiled into a standalone app the way some languages are. Instead, an **interpreter** reads your code line by line and executes it immediately. This means you can write a single line of Python and see a result instantly — perfect for experimentation, which is exactly what data science and AI work require.

A **script** is simply a text file with a `.py` extension containing Python instructions. To run it, you type a command in the **terminal** (also called the command line or shell) — the text-based interface where you type instructions directly to your computer's operating system instead of clicking icons.

### Installing Python

1. Go to python.org/downloads and download the latest stable Python 3 release (3.11 or newer).
2. Run the installer. **On Windows, make sure to check the box "Add Python to PATH"** before clicking Install — this is the single most common setup mistake.
3. On macOS, the installer handles this automatically, but you can also install via Homebrew: `brew install python3`.
4. On Linux, Python 3 is usually pre-installed; verify with the version check below, or install via your package manager, e.g. `sudo apt install python3`.

### Verifying your installation

Open your terminal application (Terminal on macOS/Linux, Command Prompt or PowerShell on Windows) and type:

```bash
python3 --version
```

You should see something like `Python 3.11.6`. On some Windows setups, the command is `python` instead of `python3` — try both if one doesn't work.

### Setting up your editor

We recommend **Visual Studio Code (VS Code)**, a free code editor from Microsoft. Download it from code.visualstudio.com, install it, then install the official "Python" extension from the Extensions panel (the icon that looks like four squares in the left sidebar). This extension gives you syntax highlighting (color-coded code), auto-completion, and a built-in way to run Python files.

### Your first program

Every programmer's first program is traditionally a "Hello, World!" program — it simply displays text on the screen. In Python, this uses the `print()` function.

## Key Vocabulary

| Term | Definition |
|---|---|
| **Python** | A high-level, readable programming language widely used in AI and data science. |
| **Interpreter** | A program that reads and executes Python code line by line. |
| **Terminal / Command Line** | A text-based interface for typing commands directly to your computer. |
| **Script** | A saved text file containing code, typically ending in `.py`. |
| **IDE / Code Editor** | Software (like VS Code) designed for writing and running code. |
| **Syntax** | The rules that define how code must be written to be valid. |
| **Comment** | A note in code, ignored by the interpreter, used to explain what the code does. |
| **Function** | A named, reusable block of code that performs a task, e.g. `print()`. |

## Code Example: Your First Mission Script

Create a new file named `mission_control.py` and enter the following:

```python
# mission_control.py
# Lumexa Mission Control - First Launch Sequence
# This program introduces the crew and begins the countdown.

# print() displays text on the screen. Text must be wrapped in quotes.
print("Lumexa Mission Control: Systems check initiated.")

# You can print multiple lines by calling print() more than once.
print("Life support: ONLINE")
print("Navigation: ONLINE")
print("Engines: ONLINE")

# A blank print() adds an empty line for readability.
print()

# You can store text in a variable (we'll dive deeper into this next lesson).
crew_name = "Cadet"
print("Welcome aboard, " + crew_name + ". All systems are go for launch.")

# Comments (lines starting with #) are notes for humans only.
# The interpreter skips them completely - they never affect the program.
```

Save the file, then in your terminal, navigate to the folder where you saved it using the `cd` (change directory) command, and run:

```bash
python3 mission_control.py
```

Expected output:

```
Lumexa Mission Control: Systems check initiated.
Life support: ONLINE
Navigation: ONLINE
Engines: ONLINE

Welcome aboard, Cadet. All systems are go for launch.
```

## Instructor-Guided Coding Walkthrough

Walk through this live with students, typing along step by step:

1. **Open the terminal together.** Show students how to open it on their specific OS. Run `python3 --version` as a group and troubleshoot any "command not found" errors immediately (usually a PATH issue on Windows — reinstall with the PATH box checked).
2. **Create a project folder.** Have everyone create a folder called `lumexa-python` on their Desktop, and open that folder in VS Code (File > Open Folder).
3. **Create the file.** In VS Code, create `mission_control.py` inside that folder.
4. **Type the code together, line by line**, explaining each `print()` call before running it.
5. **Run it three ways** so students see the options:
   - From the terminal: `python3 mission_control.py`
   - From VS Code's built-in "Run" button (the triangle/play icon top-right)
   - From VS Code's integrated terminal (Terminal > New Terminal)
6. **Break it on purpose.** Delete a closing quote mark from one of the `print()` lines and run it again. Read the error message together: `SyntaxError: unterminated string literal`. This teaches students that error messages, while intimidating at first, are actually helpful clues, not something to fear.
7. **Fix it and re-run** to confirm the output returns to normal.

## Student Mission / Guided Challenge

**Explorer Challenge: Build Your Own Crew Log**

Create a new file called `crew_log.py`. Your mission:

1. Print a welcome message identifying your ship (make up a name, e.g. "Aurora-7").
2. Create a variable holding your own name and print a personalized greeting using it.
3. Print at least three "system status" lines (e.g., "Oxygen: 98%", "Fuel: 87%", "Hull integrity: 100%").
4. Add at least two comments explaining what different parts of your code do.
5. Run the file from the terminal and confirm the output matches what you expect.

**Bonus objective:** Add a line that prints a fun fact about space using `print()`.

## Common Mistakes

- **Forgetting quotation marks** around text passed to `print()`, causing a `SyntaxError`.
- **Mismatched quotes**, e.g. starting with `"` and ending with `'`.
- **Running the wrong file** because the terminal is in the wrong folder — always confirm with the `pwd` (macOS/Linux) or `cd` with no arguments (Windows) command, or list files with `ls` (macOS/Linux) or `dir` (Windows).
- **Typing `python` when the system only recognizes `python3`**, or vice versa.
- **Saving the file with the wrong extension** (e.g. `.txt` instead of `.py`), which prevents it from running as Python code.
- **Indentation added accidentally** — Python is sensitive to spacing, and even a single unexpected space before `print` can cause an `IndentationError`. We'll cover indentation rules in depth in Lesson 3.

## Check Your Understanding

1. What is the difference between a programming language and the Python interpreter?
2. Why do professional AI engineers use Python instead of writing directly in 1s and 0s?
3. What terminal command checks your installed Python version?
4. What does the `print()` function do?
5. What happens if you forget a closing quotation mark in a `print()` statement?
6. What is the purpose of a comment, and how does the interpreter treat it?

## Mini Quiz

1. Which file extension is used for Python scripts?
   a) `.py`
   b) `.python`
   c) `.pt`
   d) `.exe`

2. What symbol starts a comment in Python?
   a) `//`
   b) `#`
   c) `--`
   d) `<!--`

3. Which command runs a Python file named `launch.py` from the terminal?
   a) `run launch.py`
   b) `python3 launch.py`
   c) `open launch.py`
   d) `execute launch.py`

4. What is the most common installation mistake on Windows?
   a) Forgetting to restart the computer
   b) Not checking "Add Python to PATH"
   c) Installing the 32-bit version
   d) Choosing the wrong download folder

5. True or False: Comments are executed by the Python interpreter.

### Answer Key

1. a) `.py`
2. b) `#`
3. b) `python3 launch.py`
4. b) Not checking "Add Python to PATH"
5. False — comments are ignored by the interpreter; they exist only for humans reading the code.

## Lesson Recap

Today you completed pre-flight checks for your entire coding journey. You installed Python, verified it through the terminal, set up VS Code as your mission control editor, and wrote, saved, and ran your first real program. You learned that Python is the language of choice for AI because it's readable and backed by powerful libraries you'll meet soon. You also learned that error messages aren't scary — they're diagnostic readouts telling you exactly what to fix. Every lesson from here builds directly on these skills: writing files, running them from the terminal, and reading output and errors carefully.

## Homework / Extension Mission

**Solo Mission: System Diagnostics Report**

At home, create a file called `diagnostics_report.py` that:

1. Prints a title banner (e.g., `"===== LUMEXA DIAGNOSTICS REPORT ====="`).
2. Uses at least five `print()` statements simulating different ship system checks.
3. Includes a comment block at the top of the file with your name, the date, and a one-sentence description of what the program does.
4. Intentionally introduces one syntax error, runs the file to see the resulting error message, writes down (as a comment) what the error message said, then fixes it.

Submit a screenshot or copy of your terminal output showing the successful run.

## Portfolio Connection

Every project you'll build in this course — the **Number Prediction Model**, the **Simple Image Classifier**, and the **Data Pattern Finder** — begins exactly the way today's lesson did: setting up a working Python environment and confirming code runs correctly before adding complexity. The habit of running small pieces of code often, and reading error messages carefully, is the single most important professional skill in AI engineering. You'll rely on the exact terminal commands from today (`python3 <filename>.py`, navigating folders) every time you launch a training script in your future projects.
