# Lesson 02: Pygame Setup and the Game Loop

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 2 of 8
**Duration:** 60 minutes
**Difficulty:** Beginner
**Technology:** Python 3.10+, Pygame 2.x

---

## Mission Brief

Cadet, engines online! Today you install the flight computer that runs every game in this course: **Pygame**. Every mission from here forward — the Space Shooter, the Maze Runner, the Brick Breaker — runs inside the same core structure: a window, an event loop, and a clock that ticks 60 times per second. Get this loop right, and every future lesson builds on solid ground.

## Learning Objectives

1. Install Pygame and verify the installation.
2. Create a game window using `pygame.display.set_mode`.
3. Build a correct **game loop**: handle events, update state, draw, and control frame rate.
4. Use `pygame.time.Clock` to cap the frame rate and understand delta time.
5. Draw basic shapes and respond to keyboard input to move an object on screen.

## What You'll Build

A window with a small square "scout ship" that you can move using the arrow keys, running inside a properly structured Pygame game loop at a locked 60 FPS.

## Prerequisites

- Completion of Lesson 1 (variables, conditionals, loops, functions, classes).
- Python 3.10+ installed.
- Ability to install packages with `pip`.

## Key Concepts

- **Pygame** — a Python library for building 2D games: windows, graphics, input, sound.
- **The game loop** — the `while running:` loop that runs every frame: process events → update → draw → tick.
- **`pygame.event.get()`** — reads all pending input/window events since the last frame.
- **`pygame.key.get_pressed()`** — returns the current state of every key, for smooth continuous movement.
- **`pygame.time.Clock`** and **delta time** — capping frame rate and measuring elapsed time between frames.
- **Surfaces and `pygame.draw`** — the window is a `Surface`; you draw shapes onto it every frame.

## Concept Explanation

A game is really an illusion: we draw a picture, erase it, draw a slightly different picture, and repeat so fast (60 times per second) that it looks like smooth motion — like a flipbook. The code structure that makes this illusion work is called the **game loop**:

```
while the game is running:
    1. Handle input/events (did the player press a key, close the window?)
    2. Update game state (move the ship, check collisions, update score)
    3. Draw everything (clear the screen, draw the ship, draw the score, flip the display)
    4. Wait until it's time for the next frame (Clock.tick(60))
```

This structure never changes — from the simplest demo to a full commercial game. Every project in this course is a variation on this loop. `pygame.time.Clock().tick(60)` does two jobs: it pauses just enough to keep the loop from running faster than 60 frames per second, and it returns the number of milliseconds since the last call — useful for **delta time**, letting you move things at a consistent speed regardless of computer speed.

## Guided Coding

First, install Pygame:

```
pip install pygame
```

Now create `main.py`:

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 02
A scout ship you can fly around the screen using arrow keys.
"""

import pygame
import sys

# ---------------------------------------------------------
# Constants (this pattern becomes a full settings.py later)
# ---------------------------------------------------------
SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
FPS = 60
SHIP_SIZE = 40
SHIP_SPEED = 300  # pixels per second

BLACK = (10, 10, 20)
CYAN = (80, 220, 255)
WHITE = (255, 255, 255)


def main():
    # -------------------- SETUP (runs once) --------------------
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Scout Ship - Lesson 02")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 28)

    ship_x = SCREEN_WIDTH // 2 - SHIP_SIZE // 2
    ship_y = SCREEN_HEIGHT // 2 - SHIP_SIZE // 2

    running = True

    # -------------------- GAME LOOP (runs every frame) --------------------
    while running:
        # delta time in seconds since the previous frame
        dt = clock.tick(FPS) / 1000.0

        # 1. HANDLE EVENTS
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    running = False

        # 2. UPDATE STATE
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            ship_x -= SHIP_SPEED * dt
        if keys[pygame.K_RIGHT]:
            ship_x += SHIP_SPEED * dt
        if keys[pygame.K_UP]:
            ship_y -= SHIP_SPEED * dt
        if keys[pygame.K_DOWN]:
            ship_y += SHIP_SPEED * dt

        # keep the ship on screen
        ship_x = max(0, min(SCREEN_WIDTH - SHIP_SIZE, ship_x))
        ship_y = max(0, min(SCREEN_HEIGHT - SHIP_SIZE, ship_y))

        # 3. DRAW
        screen.fill(BLACK)
        ship_rect = pygame.Rect(int(ship_x), int(ship_y), SHIP_SIZE, SHIP_SIZE)
        pygame.draw.rect(screen, CYAN, ship_rect)

        hint = font.render("Arrow keys to fly. ESC to quit.", True, WHITE)
        screen.blit(hint, (10, 10))

        pygame.display.flip()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## Code Walkthrough

- `pygame.init()` — initializes all Pygame subsystems (display, font, mixer, etc.). Always call this first.
- `pygame.display.set_mode((width, height))` — creates the game window and returns a `Surface` you draw on, conventionally called `screen`.
- `pygame.display.set_caption(...)` — sets the window's title bar text.
- `pygame.time.Clock()` — an object that manages frame timing.
- `clock.tick(FPS)` — pauses just long enough to cap the loop at `FPS` frames per second, and returns milliseconds elapsed; dividing by `1000.0` converts it to **delta time (`dt`)** in seconds.
- `pygame.event.get()` — drains the event queue: window close clicks, key presses/releases, mouse clicks. You must call this every frame or the OS will think your program is frozen.
- `event.type == pygame.QUIT` — fired when the player clicks the window's close button.
- `pygame.key.get_pressed()` — returns a list-like object of booleans for every key, `True` if currently held down. This is how we get smooth, continuous movement (as opposed to `KEYDOWN`, which fires once per press).
- `ship_x -= SHIP_SPEED * dt` — this is why we use delta time: moving `SHIP_SPEED * dt` pixels means the ship moves the same real-world speed (300 pixels per **second**) no matter how fast or slow the computer's frame rate is.
- `max(0, min(SCREEN_WIDTH - SHIP_SIZE, ship_x))` — the "clamp" pattern from Lesson 1, now keeping the ship inside the screen bounds.
- `screen.fill(BLACK)` — clears the previous frame by painting the whole screen one color; skipping this causes "trails" of old frames.
- `pygame.draw.rect(screen, CYAN, ship_rect)` — draws a filled rectangle using a `pygame.Rect`, the object that represents position + size and will later power collision detection.
- `font.render(text, True, color)` — turns text into a `Surface` (the `True` enables anti-aliasing); `screen.blit(surface, position)` stamps it onto the screen.
- `pygame.display.flip()` — swaps the finished frame onto the actual monitor. Nothing you draw is visible until you call this.
- `pygame.quit()` / `sys.exit()` — cleanly shuts down Pygame and the Python process when the loop ends.

## Build Instructions

1. Confirm Python 3.10+ is installed: `python --version`.
2. Install Pygame: `pip install pygame`.
3. In VS Code, create a folder, add `main.py`, paste the code above.
4. Run it from the integrated terminal: `python main.py`.
5. A window should open. Use arrow keys to move the cyan square; press ESC or click the close button to quit.
6. If the window opens then instantly closes, you're likely running it by double-clicking the file instead of from a terminal — always run Pygame programs from a terminal so you can see error messages.

## Student Mission

Add WASD as an alternative control scheme alongside the arrow keys (both should work at the same time). Then change `SHIP_SPEED` to `500` and observe how movement feels faster but still smooth and consistent, thanks to delta time.

## Challenge

Add a second square, a stationary "space station," at a fixed position. Use `ship_rect.colliderect(station_rect)` (a preview of Lesson 4) to change the ship's color to yellow whenever it overlaps the station.

## Experiment / Extension (Progressive)

1. **Change a value** — change `FPS` from `60` to `15` and observe how choppy movement becomes even though `dt`-based movement keeps the *speed* correct.
2. **Observe** — comment out `screen.fill(BLACK)` and rerun; notice the "smearing" trail effect, which explains why clearing the screen every frame matters.
3. **Modify a mechanic** — make the ship's color change based on which key is held (e.g., cyan by default, orange while boosting with SHIFT held).
4. **Combine mechanics** — add a `boost` mechanic: holding SHIFT doubles `SHIP_SPEED` for that frame only.
5. **Build independently** — add a simple HUD in the corner showing the ship's current `(x, y)` position, updated live each frame using `font.render`.

## Common Mistakes

- **Forgetting `pygame.display.flip()`** — the window will appear blank or frozen because nothing drawn is actually shown.
- **Not calling `pygame.event.get()`** every frame — the OS reports the window as "Not Responding."
- **Moving without delta time** (`ship_x -= 5` instead of `ship_x -= SHIP_SPEED * dt`) — movement speed then depends on the computer's frame rate, which is inconsistent across machines.
- **Placing `clock.tick(FPS)` in the wrong spot** — it should be called once per loop iteration, typically at the very top, so `dt` reflects the previous frame's duration.
- **Using `KEYDOWN` for continuous movement** — `KEYDOWN` only fires once per key press, causing "stuttery," one-step-at-a-time movement instead of smooth motion. Use `pygame.key.get_pressed()` instead.

## Debugging Tips

- If nothing appears, check that `screen.fill()`, your `pygame.draw` calls, and `pygame.display.flip()` all happen **inside** the loop, in that order, every frame.
- If the ship moves at wildly different speeds on different runs, verify you're multiplying by `dt`, not a hardcoded number.
- Print `dt` occasionally to confirm it's a small number like `0.016` (roughly 1/60th of a second) — if it's `0` or huge, your clock/tick setup is likely wrong.
- Use `print(ship_x, ship_y)` inside the loop temporarily to trace movement bugs, then remove the print once fixed.

## Check Your Understanding

- In your own words, what are the four steps of a game loop, in order?
- Why is delta time necessary, when Lesson 1's simulation didn't need it?
- What's the difference between `pygame.event.get()` and `pygame.key.get_pressed()`, and when would you use each?

## Mini Quiz

1. What does `pygame.display.flip()` do?
   a) Rotates the screen  b) Displays the frame you just drew onto the actual monitor  c) Clears the screen  d) Closes the game

2. Why do we multiply movement by `dt`?
   a) To make the game slower  b) To keep movement speed consistent regardless of frame rate  c) It's required syntax with no functional purpose  d) To convert pixels to inches

3. Predict: if `FPS = 60` and `clock.tick(FPS)` returns roughly `16` (milliseconds), what is `dt` in seconds?
   a) 16.0  b) 1.6  c) 0.16  d) 0.016

4. Which event type fires when the player clicks the window's close (X) button?
   a) `pygame.KEYDOWN`  b) `pygame.QUIT`  c) `pygame.CLOSE`  d) `pygame.EXIT`

5. What would happen if you removed `screen.fill(BLACK)` from the loop?
   a) The game would crash  b) Old frames would visually "smear" since nothing clears them  c) Nothing would change  d) The FPS would double

6. (Code reading) In the guided code, what stops the ship from moving off the left edge of the screen?
   a) `pygame.QUIT`  b) `clock.tick(FPS)`  c) `max(0, min(SCREEN_WIDTH - SHIP_SIZE, ship_x))`  d) `font.render(...)`

### Answer Key

1. Handle events → update state → draw → tick the clock (repeat)
2. b — keeps movement speed consistent regardless of frame rate
3. d — 0.016
4. b — `pygame.QUIT`
5. b — old frames would smear/trail
6. c — the clamp expression

## Lesson Recap

You installed Pygame and built the single most important piece of structure in this entire course: the game loop. You now know how to open a window, handle events without freezing the OS, read continuous keyboard input, move something with correct delta-time-based speed, draw shapes and text, and present the final frame. Every remaining lesson adds features *inside* this same loop — nothing about its core shape changes.

## Homework / Practice Mission

Extend `main.py` so the ship leaves behind a fading trail: keep a list of the last 10 positions and draw progressively dimmer squares at each of those positions before drawing the ship itself. This previews sprite-list management, which you'll formalize with `pygame.sprite.Group` in Lesson 3.

## Portfolio Project Connection

This exact loop structure — event handling, `pygame.key.get_pressed()`, delta-time movement, clamped screen bounds, `clock.tick(FPS)` — is the beating heart of `game.py` in **Project 04 (Space Shooter)**, `game.py` in **Project 05 (Maze Runner Timer)**, and `game.py` in **Project 06 (Brick Breaker Clone)**. In every one of those projects, look for this same four-step loop; only the update and draw logic grows more elaborate as we add sprites, collisions, enemies, and sound in the lessons ahead.
