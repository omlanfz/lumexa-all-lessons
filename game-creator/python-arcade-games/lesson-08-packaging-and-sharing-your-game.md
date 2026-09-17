# Lesson 08: Packaging and Sharing Your Game

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 8 of 8
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.10+, Pygame 2.x, pip, PyInstaller

---

## Mission Brief

Cadet, mission accomplished — now it's time to transmit your work across the galaxy. A game that only runs on your computer, from inside VS Code, isn't finished; it's still in the hangar. Today's final lesson covers organizing your project professionally, writing a clear README, managing dependencies with `requirements.txt`, and packaging your game into a shareable form so a friend, a teacher, or a judge can run it without installing anything extra.

## Learning Objectives

1. Organize a Pygame project into a clean, professional folder structure (`src/`, `README.md`, `requirements.txt`).
2. Write a `requirements.txt` file and use it to install exact dependencies.
3. Write a complete, useful `README.md` for a game project.
4. Package a Pygame project into a standalone executable using PyInstaller.
5. Understand version control basics (Git) well enough to share source code online.

## What You'll Build

You will restructure one of your earlier lesson projects (or your favorite mini-game from this course) into a professional folder layout with a proper `README.md` and `requirements.txt`, then package it into a standalone executable that runs without Python installed, ready to share with friends or submit as a portfolio piece.

## Prerequisites

- Lessons 1–7 completed — ideally you have a working game with states, sprites, collisions, and sound.
- Comfortable using a terminal to run `pip install`.

## Key Concepts

- **Project structure** — separating `main.py`, class modules (`player.py`, `enemy.py`), constants (`settings.py`), and assets into clear folders.
- **`requirements.txt`** — a plain text list of exact package versions your project depends on, so anyone can recreate your environment with one command.
- **README documentation** — the first thing anyone sees; explains what the game is, how to install it, how to run it, and how to play.
- **PyInstaller** — a tool that bundles a Python script and all its dependencies (including Pygame) into a single standalone executable file.
- **Version control (Git basics)** — tracking changes and sharing code via a platform like GitHub.

## Concept Explanation

Right now, running your game requires: having Python installed, knowing to run `pip install pygame`, and typing `python main.py` in the correct folder. That's a fair number of steps for someone who just wants to click and play. Professional game projects solve this with three things:

1. **A clean structure.** Instead of one giant `main.py`, split code into focused modules: `settings.py` for constants, `player.py`/`enemy.py` for classes, `game.py` for the state machine and loop, and a thin `main.py` that just imports and runs everything. This mirrors exactly how the three portfolio projects in this course are organized.

2. **Reproducible dependencies.** A `requirements.txt` file lists exact package versions (e.g., `pygame==2.6.1`). Anyone — including a teacher's or judge's computer — can run `pip install -r requirements.txt` and get the *exact* same environment you tested with, avoiding "it works on my machine" problems.

3. **Packaging.** Tools like **PyInstaller** read your Python project and produce a single executable file containing a private Python interpreter, your code, and all dependencies bundled together. The person receiving it doesn't need Python or Pygame installed at all — they just run the file.

## Guided Coding

First, here's the professional folder structure you'll organize your game into (this exact shape is used by every project folder in this course):

```
my-game/
├── README.md
├── requirements.txt
└── src/
    ├── main.py
    ├── settings.py
    ├── player.py
    ├── enemy.py
    ├── game.py
    └── assets/
        └── sounds/
```

`requirements.txt`:

```
pygame==2.6.1
```

`src/settings.py` — constants extracted from earlier lessons into one file:

```python
"""Shared constants for the game - one place to tune the whole experience."""

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
FPS = 60

BLACK = (8, 8, 18)
CYAN = (80, 220, 255)
YELLOW = (255, 220, 80)
RED = (230, 70, 70)
WHITE = (255, 255, 255)

PLAYER_SPEED = 320
PLAYER_START_HEALTH = 100
```

`src/main.py` — a thin entry point, the pattern every project in this course follows:

```python
"""
Entry point: sets up Pygame and hands control to the Game class.
Run with: python src/main.py  (from the project's root folder)
"""

import pygame
import sys

from settings import SCREEN_WIDTH, SCREEN_HEIGHT, FPS
from game import Game


def main():
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Arcade")
    clock = pygame.time.Clock()

    game = Game(screen)
    running = True

    while running:
        dt = clock.tick(FPS) / 1000.0
        events = pygame.event.get()
        for event in events:
            if event.type == pygame.QUIT:
                running = False

        keys = pygame.key.get_pressed()
        running = game.handle_input(events, keys) and running
        game.update(dt, keys)
        game.draw()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

A minimal `README.md` template you should adapt for any game you package:

```markdown
# My Space Game

A short arcade shooter built with Python and Pygame for the Lumexa Game Creator Path.

## Requirements
- Python 3.10+
- pygame (see requirements.txt)

## Installation
    pip install -r requirements.txt

## How to Run
    python src/main.py

## Controls
- Arrow keys / A-D: move
- SPACE: shoot
- ESC: quit
- R: restart after game over

## How It Works
A state machine (menu -> playing -> game over) manages the flow.
Player and Enemy sprites use pygame.sprite.Sprite with rect-based
collision detection. Sound is optional and safely skipped if audio
files are missing.
```

Finally, packaging into a standalone executable with PyInstaller (run these commands in your terminal, not as Python code):

```
pip install pyinstaller
cd my-game
pyinstaller --onefile --windowed --name MyGame src/main.py
```

After it finishes, your executable appears in a new `dist/` folder (e.g., `dist/MyGame.exe` on Windows, `dist/MyGame` on macOS/Linux) — a single file you can send to a friend that runs without them installing Python or Pygame.

## Code Walkthrough

- Splitting constants into `settings.py` means every module (`player.py`, `enemy.py`, `game.py`) can `from settings import SCREEN_WIDTH, ...` instead of redefining or hardcoding values — change one number in one place to retune the whole game.
- `src/main.py` importing `from game import Game` — this is why the folder structure matters: Python needs these files in the same directory (or a properly configured package) to resolve imports; running `python src/main.py` from the project root works because Python adds the script's own directory to its import path automatically.
- `requirements.txt` pinning `pygame==2.6.1` (a specific version) rather than leaving it unpinned — pinning guarantees the exact tested version installs, avoiding surprises from a future Pygame release changing behavior.
- The README's four sections — Requirements, Installation, How to Run, Controls — are the bare minimum any shared game needs; the guided code adds a fifth, "How It Works," which is valuable for portfolio/judged submissions where reviewers want to understand your architecture quickly.
- `pyinstaller --onefile --windowed --name MyGame src/main.py` — `--onefile` bundles everything into a single executable (versus a folder of files); `--windowed` prevents an extra terminal/console window from popping up alongside your game window (important for a polished feel); `--name` controls the output executable's name.
- PyInstaller inspects your imports (including `pygame` and your own `settings`/`player`/`enemy`/`game` modules) and bundles them all — this is why keeping imports clean and explicit (as in `src/main.py` above) matters: messy or dynamic imports can confuse PyInstaller's dependency detection.

## Build Instructions

1. Reorganize one of your existing lesson projects into the `src/` structure shown above — move class definitions into their own files and constants into `settings.py`.
2. Create `requirements.txt` with `pygame==2.6.1` (or whatever version `pip show pygame` reports you're using).
3. Test it still runs: `pip install -r requirements.txt` then `python src/main.py` from the project root.
4. Write a complete `README.md` following the template.
5. Install PyInstaller: `pip install pyinstaller`.
6. From the project root, run: `pyinstaller --onefile --windowed --name MyGame src/main.py`.
7. Locate the executable in the newly created `dist/` folder and test running it by double-clicking (no terminal needed).
8. Share the single executable file (or the whole project folder with instructions) with a friend or family member and get their feedback.

## Student Mission

Take your Lesson 7 state-machine game and fully reorganize it into the `src/` structure described here: `settings.py`, `player.py`, `enemy.py`, `game.py`, `main.py`. Confirm it still runs identically after the split.

## Challenge

Package your reorganized game with PyInstaller and successfully run the generated executable on your own machine without using `python` or VS Code at all — just double-click (or run from a plain terminal) the file in `dist/`.

## Experiment / Extension (Progressive)

1. **Change a value** — add a `VERSION = "1.0.0"` constant to `settings.py` and display it in small text in the corner of the menu screen.
2. **Observe** — delete your `dist/` and `build/` folders and re-run PyInstaller from scratch, noting how it regenerates everything from your source files (this is why those folders should never be your primary copy of the project).
3. **Modify a mechanic** — add a `--icon=my_icon.ico` flag to your PyInstaller command with a custom icon file for a more polished executable (Windows/macOS icon formats differ; check PyInstaller's docs for your platform).
4. **Combine mechanics** — write a second README section, "Extension Ideas," listing three specific features you'd add next (this is genuinely useful practice for real portfolio submissions).
5. **Build independently** — set up a Git repository (`git init`, `git add .`, `git commit -m "Initial release"`) for your project and, if you have a GitHub account, push it so it's shareable via a URL.

## Common Mistakes

- **Committing `dist/` and `build/` folders to version control** — these are generated output, not source code; they should be excluded (a `.gitignore` file listing `dist/`, `build/`, `*.spec`, `__pycache__/` keeps repositories clean).
- **Unpinned or missing `requirements.txt`** — "just `pip install pygame`" without a version can install a future, possibly incompatible version on someone else's machine.
- **Absolute file paths hardcoded to your own computer** (e.g., `"C:\Users\yourname\Desktop\sounds\shoot.wav"`) — always build paths relative to the script's own location using `os.path.dirname(__file__)`, as shown in Lesson 6, so the packaged game works on any computer.
- **Forgetting `--windowed`** on a GUI game — without it, an extra blank console window sits behind your game window, which looks unprofessional and can be confusing to players.
- **Not testing the packaged executable on a clean expectation** — always actually run the file from `dist/`, not just check that PyInstaller exited without errors; packaging issues (missing assets, wrong working directory assumptions) often only show up at runtime.

## Debugging Tips

- If PyInstaller's build fails, read the terminal output for `ModuleNotFoundError` — this usually means an import PyInstaller couldn't detect automatically; adding `--hidden-import=modulename` to the command can resolve it.
- If the packaged executable runs but can't find your sound/image assets, remember that a packaged app's "current directory" isn't your project folder — use `os.path.dirname(os.path.abspath(__file__))`-based paths (as in Lesson 6's `SoundManager`) rather than assuming a specific working directory, and consider PyInstaller's `--add-data` flag to bundle asset folders explicitly.
- If `python src/main.py` fails with `ModuleNotFoundError: No module named 'settings'`, verify you are running it from the project root (not from inside `src/`) or that `src/` is correctly recognized as the script's own directory by Python's import resolution.
- Keep a working, un-packaged copy of your game at all times; treat the `dist/` executable as a build output you can always regenerate, never as your only copy of the game.

## Check Your Understanding

- Why is `requirements.txt` important even though you already have Pygame installed on your own machine?
- What is the difference between your project's source code and the `dist/` folder PyInstaller produces?
- Why does hardcoding a file path like `"C:\Users\me\sounds\shoot.wav"` break the game on someone else's computer?

## Mini Quiz

1. What is the purpose of `requirements.txt`?
   a) It stores the game's save file  b) It lists exact package versions so others can recreate your environment  c) It's required for Pygame to run at all  d) It stores high scores

2. What does the `--onefile` flag do in a PyInstaller command?
   a) Limits the game to one level  b) Bundles the entire program and its dependencies into a single executable file  c) Deletes all but one Python file  d) Compresses image assets only

3. Predict: if `settings.py` defines `SCREEN_WIDTH = 800` and `player.py` does `from settings import SCREEN_WIDTH`, what happens if you later change `SCREEN_WIDTH` to `1024` in `settings.py` only?
   a) Nothing changes anywhere  b) `player.py` automatically uses the new value of 1024 the next time the program runs  c) It causes an ImportError  d) You must manually update every file that uses `SCREEN_WIDTH`

4. Why should `dist/` and `build/` folders typically be excluded from version control (e.g., via `.gitignore`)?
   a) They are required for the game to run and must never be deleted  b) They are regenerated build output, not source code, and don't need to be tracked  c) Git cannot store binary files  d) PyInstaller requires them to be excluded or it won't run

5. What is the main risk of hardcoding an absolute file path specific to your own computer?
   a) None, absolute paths always work everywhere  b) The game will fail to find that file on any other computer with a different folder structure  c) It makes the game run faster  d) It's required for PyInstaller to work

6. (Code reading) In the guided `src/main.py`, what does `from game import Game` require to succeed?
   a) Nothing special, it always works  b) `game.py` must exist in the same directory as `main.py` (or otherwise be importable) and define a class named `Game`  c) `pygame` must be uninstalled first  d) `Game` must be defined inside `main.py` itself

### Answer Key

1. b — lists exact package versions for reproducible environments
2. b — bundles everything into a single executable file
3. b — `player.py` automatically picks up the new value at next run, since it imports the shared constant
4. b — they are regenerated build output, not source
5. b — the game fails to find that file on any other computer
6. b — `game.py` must exist alongside `main.py` and define a `Game` class

## Lesson Recap

You took a working game and gave it the final polish that separates a "school exercise" from a shareable, professional project: a clean `src/` module structure, a pinned `requirements.txt`, a genuinely useful `README.md`, and a standalone executable built with PyInstaller that runs on a friend's computer with zero setup. Every project in this course — and every game you build after it — should follow this exact packaging pattern before you consider it "done."

## Homework / Practice Mission

Fully package and share one complete game from this course (ideally your Lesson 7 project or one of the portfolio projects) with at least one other person outside this class. Ask them to follow only your README instructions, with no help from you, and note anywhere they got stuck — then fix your README or code based on that real feedback. This "cold install test" is exactly what professional teams do before any public release.

## Portfolio Project Connection

The exact `src/` folder structure, `requirements.txt`, and README template from this lesson are what you'll find already set up in **Project 04: Space Shooter**, **Project 05: Maze Runner Timer**, and **Project 06: Brick Breaker Clone** — go open any of their project folders and you'll recognize `main.py`, `settings.py`, and a full README following this same shape. Completing this lesson means you're now equipped to package and share not just those three projects, but any future game you build on the Lumexa Game Creator Path.
