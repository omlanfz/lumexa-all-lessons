# Lesson 03: Sprites, Images, and Movement

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 3 of 8
**Duration:** 60 minutes
**Difficulty:** Beginner-Intermediate
**Technology:** Python 3.10+, Pygame 2.x

---

## Mission Brief

Cadet, your scout ship needs a proper hull! Today we upgrade from a plain square drawn by hand each frame to a real `pygame.sprite.Sprite` — the object type professional Pygame developers use for every player, enemy, bullet, and obstacle. You'll build the actual `Player` class that will appear, almost unchanged, in every remaining project of this course.

## Learning Objectives

1. Explain what `pygame.sprite.Sprite` is and why it's used instead of plain variables.
2. Create a `Player` class that inherits from `pygame.sprite.Sprite`.
3. Use `pygame.sprite.Group` to manage and draw multiple sprites at once.
4. Draw sprite visuals procedurally with `pygame.draw` (no external image files required).
5. Implement smooth, bounded movement using `self.rect` and delta time.

## What You'll Build

A flyable player ship built as a proper `Sprite` subclass, managed inside a `pygame.sprite.Group`, alongside several decorative "star" sprites that drift across the background — introducing multi-sprite management.

## Prerequisites

- Lessons 1 and 2 completed (classes, the game loop, delta time).
- Comfort running `python main.py` from a terminal.

## Key Concepts

- **`pygame.sprite.Sprite`** — the base class for anything drawn and updated in a game: it expects `self.image` (a `Surface`) and `self.rect` (a `pygame.Rect`).
- **`pygame.sprite.Group`** — a container that can `update()` and `draw()` every sprite inside it in one call.
- **`self.image` / `self.rect`** — the two required attributes: what to draw, and where/how big.
- **Procedural drawing** — building a sprite's visual with `pygame.draw` calls instead of loading an image file, avoiding missing-asset errors.
- **Inheritance** — `class Player(pygame.sprite.Sprite):` means `Player` gets all of `Sprite`'s built-in behavior for free.

## Concept Explanation

In Lesson 2, we tracked `ship_x` and `ship_y` as separate variables and drew a rectangle by hand every frame. That works for one object, but real games have dozens of moving things: the player, waves of enemies, bullets, power-ups. Pygame gives us `pygame.sprite.Sprite` as the standard base class for any of these. A sprite needs exactly two things:

- `self.image` — a `Surface` (a picture, even a simple colored rectangle drawn with `pygame.draw`) representing what the object looks like.
- `self.rect` — a `pygame.Rect` representing its position and size, used both for drawing and later for collision detection.

Once objects are proper sprites, we can put many of them into a `pygame.sprite.Group` — a container object. Calling `all_sprites.update(dt)` calls `update()` on every sprite in the group, and `all_sprites.draw(screen)` draws every one of them, in one line each, instead of managing separate lists and loops by hand. This is the exact pattern the entire rest of this course depends on: `Player`, `Enemy`, `Bullet`, and `Star` will all be `Sprite` subclasses living inside `Group`s.

## Guided Coding

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 03
Sprites, Groups, and procedurally-drawn ships.
"""

import pygame
import random
import sys

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
FPS = 60

BLACK = (8, 8, 18)
CYAN = (80, 220, 255)
WHITE = (255, 255, 255)
YELLOW = (255, 220, 80)


class Player(pygame.sprite.Sprite):
    """The player's ship. This exact class shape reappears in Project 04."""

    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((44, 34), pygame.SRCALPHA)
        # Draw a simple triangular ship procedurally - no image file needed.
        pygame.draw.polygon(
            self.image, CYAN,
            [(22, 0), (0, 34), (44, 34)]
        )
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = 320  # pixels per second

    def update(self, dt, keys):
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            self.rect.x -= self.speed * dt
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            self.rect.x += self.speed * dt
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            self.rect.y -= self.speed * dt
        if keys[pygame.K_DOWN] or keys[pygame.K_s]:
            self.rect.y += self.speed * dt

        # Keep the ship inside the screen bounds
        self.rect.left = max(0, self.rect.left)
        self.rect.right = min(SCREEN_WIDTH, self.rect.right)
        self.rect.top = max(0, self.rect.top)
        self.rect.bottom = min(SCREEN_HEIGHT, self.rect.bottom)


class Star(pygame.sprite.Sprite):
    """A decorative background star drifting downward for a parallax feel."""

    def __init__(self):
        super().__init__()
        size = random.randint(2, 4)
        self.image = pygame.Surface((size, size))
        self.image.fill(WHITE)
        self.rect = self.image.get_rect(
            x=random.randint(0, SCREEN_WIDTH),
            y=random.randint(0, SCREEN_HEIGHT),
        )
        self.speed = random.uniform(30, 90)

    def update(self, dt, keys=None):
        self.rect.y += self.speed * dt
        if self.rect.top > SCREEN_HEIGHT:
            self.rect.y = -5
            self.rect.x = random.randint(0, SCREEN_WIDTH)


def main():
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Sprites - Lesson 03")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 28)

    player = Player(SCREEN_WIDTH // 2, SCREEN_HEIGHT - 80)

    all_sprites = pygame.sprite.Group()
    all_sprites.add(player)
    for _ in range(60):
        all_sprites.add(Star())

    running = True
    while running:
        dt = clock.tick(FPS) / 1000.0

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE:
                running = False

        keys = pygame.key.get_pressed()
        all_sprites.update(dt, keys)

        screen.fill(BLACK)
        all_sprites.draw(screen)
        hint = font.render("WASD/Arrows to fly. ESC to quit.", True, YELLOW)
        screen.blit(hint, (10, 10))
        pygame.display.flip()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## Code Walkthrough

- `class Player(pygame.sprite.Sprite):` — **inheritance**: `Player` automatically gains sprite behavior (like group membership tracking) by extending `pygame.sprite.Sprite`.
- `super().__init__()` — calls the parent class's constructor first, which is required for `Sprite` to set itself up correctly before we add our own attributes.
- `pygame.Surface((44, 34), pygame.SRCALPHA)` — creates a blank transparent canvas of size 44×34; `SRCALPHA` allows transparency so the ship isn't a solid rectangle box.
- `pygame.draw.polygon(self.image, CYAN, [...])` — draws a triangle directly onto the sprite's own `Surface`, our "procedural art" instead of loading a PNG file — this is deliberate so the game never crashes from a missing image file.
- `self.image.get_rect(center=(x, y))` — a convenient way to get a `Rect` matching the image's size, positioned by its center point rather than top-left corner.
- `def update(self, dt, keys):` — Pygame's `Group.update()` calls this method automatically on every sprite; any extra arguments you pass to `group.update(dt, keys)` are forwarded here.
- `self.rect.x -= self.speed * dt` — identical delta-time movement pattern from Lesson 2, now living inside the sprite itself instead of loose variables.
- `self.rect.left = max(0, self.rect.left)` — `pygame.Rect` exposes convenient named edges (`left`, `right`, `top`, `bottom`, `center`) that update the whole rect consistently; this is far less error-prone than manipulating raw `x`/`y`.
- `class Star(pygame.sprite.Sprite):` — a second, independent sprite type, showing that many different classes can share one `Group`.
- `pygame.sprite.Group()` — the container; `.add(player)` and the loop `.add(Star())` populate it.
- `all_sprites.update(dt, keys)` — calls `update(dt, keys)` on *every* sprite in the group in one line — both `Player.update` and `Star.update` get called, which is why `Star.update` accepts (and ignores) `keys` too, keeping a matching method signature.
- `all_sprites.draw(screen)` — draws every sprite's `self.image` at `self.rect`'s position, in one line, regardless of how many sprites are in the group.

## Build Instructions

1. Ensure Pygame is installed: `pip install pygame`.
2. Save the code as `main.py` in your lesson folder.
3. Run with `python main.py`.
4. Fly the cyan ship with WASD or arrow keys while white stars drift downward behind it.
5. In VS Code, use the "Run Python File" play button, or the integrated terminal — both work identically.

## Student Mission

Add a new sprite class, `PowerCore`, drawn as a small yellow circle (`pygame.draw.circle`) that sits at a fixed position. It doesn't need to move. Add several `PowerCore` instances to `all_sprites` at random positions.

## Challenge

Give `Player` a `boost` mechanic: while `SHIFT` is held, `self.speed` should temporarily become `600`; otherwise it returns to `320`. Implement this cleanly inside `update()` without duplicating the movement code four times.

## Experiment / Extension (Progressive)

1. **Change a value** — change the star count from `60` to `200` and observe performance and visual density.
2. **Observe** — change `Star`'s speed range to `(200, 400)` to make it feel like the ship is flying much faster.
3. **Modify a mechanic** — make `Player`'s ship visually "tilt" by drawing a slightly different polygon shape when moving left vs. right (store an `image_left`/`image_right`/`image_straight` and swap `self.image` based on input).
4. **Combine mechanics** — make stars twinkle: randomly change their brightness (color value) every few frames using `random.randint`.
5. **Build independently** — create an `Asteroid` sprite class that drifts diagonally and wraps around all four screen edges (not just top-to-bottom like `Star`).

## Common Mistakes

- **Forgetting `super().__init__()`** — leads to confusing errors because the sprite's internal group-tracking machinery was never set up.
- **Setting `self.rect` before `self.image` exists**, or using `self.image.get_rect()` before `self.image` is assigned — always create `self.image` first.
- **Mismatched `update()` signatures** across sprite classes in the same `Group` — if `Player.update(self, dt, keys)` requires two arguments but `Star.update(self, dt)` only accepts one, calling `group.update(dt, keys)` will crash on `Star`. Keep the signatures compatible, as shown with `keys=None` in `Star`.
- **Drawing sprites manually with `screen.blit()` in a loop** after already calling `group.draw(screen)` — this either duplicates the drawing or does nothing useful; pick one approach.

## Debugging Tips

- If a sprite doesn't appear, print `sprite.rect` to check its position is actually on screen (not far outside `SCREEN_WIDTH`/`SCREEN_HEIGHT`).
- `AttributeError: 'X' object has no attribute 'rect'` almost always means `__init__` never finished setting `self.rect`, often because an earlier line crashed silently or `super().__init__()` was omitted.
- If `group.update()` throws a `TypeError` about arguments, check every sprite class in that group has a compatible `update()` signature.
- Use `pygame.draw.rect(screen, (255,0,0), sprite.rect, 1)` temporarily to draw an outline around a sprite's rect and visually confirm its exact bounds.

## Check Your Understanding

- What two attributes must every sprite have, and what does each represent?
- Why is it useful to put many different sprite types into the same `Group`?
- Why did we draw the ship with `pygame.draw.polygon` instead of loading an image file?

## Mini Quiz

1. What two attributes does `pygame.sprite.Sprite` expect a subclass to define?
   a) `x` and `y`  b) `image` and `rect`  c) `width` and `height`  d) `speed` and `position`

2. What does `all_sprites.draw(screen)` do?
   a) Updates every sprite's logic  b) Draws every sprite in the group onto `screen` at its rect position  c) Deletes all sprites  d) Only draws the first sprite added

3. Predict: if `Player.speed = 320` and `dt = 0.05`, how far (in pixels) does the ship move in one frame when only the right key is held?
   a) 320  b) 16  c) 6.4  d) 0.05

4. Why must `super().__init__()` be called in a subclass of `pygame.sprite.Sprite`?
   a) It's optional style preference  b) It sets up internal sprite/group tracking required for the object to work correctly  c) It draws the sprite  d) It sets the frame rate

5. What is the purpose of `pygame.SRCALPHA` when creating a `Surface`?
   a) It makes the surface faster to draw  b) It enables per-pixel transparency so non-drawn areas stay see-through  c) It converts the surface to grayscale  d) It locks the surface so it can't be modified

6. (Code reading) In `Star.update`, what happens when `self.rect.top > SCREEN_HEIGHT`?
   a) The star is removed from the group  b) The game ends  c) The star resets to the top of the screen at a new random x position  d) The star's speed doubles

### Answer Key

1. b — `image` and `rect`
2. b — draws every sprite in the group at its rect position
3. b — 16 pixels (320 × 0.05 = 16)
4. b — sets up required internal Sprite/Group bookkeeping
5. b — enables per-pixel transparency
6. c — resets to the top at a new random x position

## Lesson Recap

You converted a hand-managed square into a real `pygame.sprite.Sprite` subclass with `image` and `rect`, learned to manage many objects at once with `pygame.sprite.Group`, and practiced procedural drawing so your games never depend on missing image files. The `Player` class you wrote today is, structurally, the same `Player` class that will appear in every project going forward.

## Homework / Practice Mission

Build a `Bullet` sprite class: a small yellow rectangle that moves straight upward at a constant speed and removes itself from all groups (`self.kill()`) once it travels off the top of the screen. Add a `SPACE` keydown handler in the main loop that creates a new `Bullet` at the player's position and adds it to `all_sprites` (and a separate `bullets` group). This is a direct preview of shooting mechanics in Lesson 4 and Project 04.

## Portfolio Project Connection

The `Player` and background-drift patterns from this lesson become `src/player.py` in **Project 04: Space Shooter** nearly verbatim, and the `Group`-based architecture (`all_sprites`, plus dedicated groups like `bullets` and `enemies`) is exactly how **all three portfolio projects** organize their moving objects. The `Star` class previews the parallax starfield background used in the Space Shooter project's menu and gameplay screens.
