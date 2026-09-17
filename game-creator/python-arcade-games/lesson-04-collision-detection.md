# Lesson 04: Collision Detection

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 4 of 8
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.10+, Pygame 2.x

---

## Mission Brief

Cadet, weapons systems online! A ship that can fly but never fires, and bullets that pass harmlessly through asteroids, aren't much of a mission. Today you install collision detection — the system that makes bullets destroy targets, obstacles hurt the player, and pickups get collected. Every "did two things touch?" moment in every game you'll ever build runs through the concepts in this lesson.

## Learning Objectives

1. Use `pygame.Rect.colliderect()` to detect overlap between two rectangles.
2. Use `pygame.sprite.spritecollide()` and `pygame.sprite.groupcollide()` to detect collisions between sprites and groups.
3. Implement shooting: spawning `Bullet` sprites and removing them safely with `self.kill()`.
4. Implement damage and destruction logic triggered by collisions.
5. Understand the difference between rect-based collision and pixel-perfect collision, and why rect-based is the right default.

## What You'll Build

A mini shooting-gallery scene: a player ship that fires bullets upward, drifting asteroid sprites that fall from the top, collision detection that destroys an asteroid and increases score when hit, and collision detection that reduces player health when an asteroid touches the ship.

## Prerequisites

- Lesson 3 completed (`Player` sprite class, `pygame.sprite.Group`).
- Understanding of `pygame.Rect` and `self.rect`.

## Key Concepts

- **`rect1.colliderect(rect2)`** — returns `True` if two rectangles overlap at all.
- **`pygame.sprite.spritecollide(sprite, group, dokill)`** — returns a list of every sprite in `group` colliding with `sprite`; `dokill=True` automatically removes matched group members.
- **`pygame.sprite.groupcollide(group1, group2, dokill1, dokill2)`** — checks every sprite in `group1` against every sprite in `group2`, returning a dictionary of matches; used for bullets-vs-enemies.
- **`sprite.kill()`** — removes a sprite from every group it belongs to, effectively deleting it from the game.
- **Rect-based vs. pixel-perfect collision** — rect collision checks bounding boxes (fast, simple, good enough for almost all 2D games); pixel-perfect collision checks actual drawn pixels (slower, rarely needed).

## Concept Explanation

Every `pygame.sprite.Sprite` already has a `self.rect`. Collision detection is fundamentally the question: "do these two rectangles overlap?" Pygame answers this with `rect1.colliderect(rect2)`, which returns `True`/`False`. That's the core of it — everything else (`spritecollide`, `groupcollide`) is convenient sugar built on top of that same idea, letting you check one sprite against a whole group, or one group against another group, without writing nested loops yourself.

The typical pattern in a real game: bullets live in a `bullets` group, enemies live in an `enemies` group. Every frame, we ask "which bullets hit which enemies?" with `pygame.sprite.groupcollide(bullets, enemies, True, True)`. The `True, True` means both the bullet *and* the enemy it hit are automatically removed (killed) from their groups. This one line replaces what would otherwise be a nested loop checking every bullet against every enemy manually.

## Guided Coding

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 04
Collision detection: shooting, damage, and destruction.
"""

import pygame
import random
import sys

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
FPS = 60

BLACK = (8, 8, 18)
CYAN = (80, 220, 255)
YELLOW = (255, 220, 80)
RED = (230, 70, 70)
WHITE = (255, 255, 255)


class Player(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((44, 34), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, CYAN, [(22, 0), (0, 34), (44, 34)])
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = 320
        self.health = 100

    def update(self, dt, keys):
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            self.rect.x -= self.speed * dt
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            self.rect.x += self.speed * dt
        self.rect.left = max(0, self.rect.left)
        self.rect.right = min(SCREEN_WIDTH, self.rect.right)

    def take_damage(self, amount):
        self.health = max(0, self.health - amount)


class Bullet(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((4, 14))
        self.image.fill(YELLOW)
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = 500

    def update(self, dt, keys=None):
        self.rect.y -= self.speed * dt
        if self.rect.bottom < 0:
            self.kill()  # removes this sprite from ALL groups it belongs to


class Asteroid(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        size = random.randint(20, 40)
        self.image = pygame.Surface((size, size), pygame.SRCALPHA)
        pygame.draw.circle(self.image, RED, (size // 2, size // 2), size // 2)
        self.rect = self.image.get_rect(
            x=random.randint(0, SCREEN_WIDTH - size), y=-size
        )
        self.speed = random.uniform(80, 180)

    def update(self, dt, keys=None):
        self.rect.y += self.speed * dt
        if self.rect.top > SCREEN_HEIGHT:
            self.kill()


def main():
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Collisions - Lesson 04")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 28)

    player = Player(SCREEN_WIDTH // 2, SCREEN_HEIGHT - 80)
    all_sprites = pygame.sprite.Group(player)
    bullets = pygame.sprite.Group()
    asteroids = pygame.sprite.Group()

    score = 0
    spawn_timer = 0.0
    spawn_interval = 0.8

    running = True
    while running:
        dt = clock.tick(FPS) / 1000.0

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_ESCAPE:
                    running = False
                elif event.key == pygame.K_SPACE:
                    bullet = Bullet(player.rect.centerx, player.rect.top)
                    bullets.add(bullet)
                    all_sprites.add(bullet)

        keys = pygame.key.get_pressed()
        player.update(dt, keys)
        bullets.update(dt)
        asteroids.update(dt)

        # Spawn new asteroids over time
        spawn_timer += dt
        if spawn_timer >= spawn_interval:
            spawn_timer = 0.0
            asteroid = Asteroid()
            asteroids.add(asteroid)
            all_sprites.add(asteroid)

        # --- COLLISION 1: bullets vs asteroids (both destroyed, score awarded) ---
        hits = pygame.sprite.groupcollide(bullets, asteroids, True, True)
        for bullet, hit_asteroids in hits.items():
            score += 10 * len(hit_asteroids)

        # --- COLLISION 2: player vs asteroids (player takes damage) ---
        hit_list = pygame.sprite.spritecollide(player, asteroids, True)
        for asteroid in hit_list:
            player.take_damage(20)

        screen.fill(BLACK)
        all_sprites.draw(screen)
        # all_sprites doesn't auto-include newly added bullets/asteroids drawing
        # order issues are avoided since we add to all_sprites at creation time.

        hud = font.render(
            f"Score: {score}   Health: {player.health}", True, WHITE
        )
        screen.blit(hud, (10, 10))

        if player.health <= 0:
            over_text = font.render("SHIP DESTROYED - Press ESC to quit", True, RED)
            screen.blit(over_text, (SCREEN_WIDTH // 2 - 160, SCREEN_HEIGHT // 2))

        pygame.display.flip()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## Code Walkthrough

- `self.health = 100` on `Player`, and `take_damage(self, amount)` — the same clamped-subtraction pattern from Lesson 1's `Ship` class, now driving real gameplay.
- `class Bullet` — a new sprite that moves upward (`self.rect.y -= self.speed * dt`) and calls `self.kill()` once off-screen, so bullets don't accumulate forever in memory.
- `pygame.K_SPACE` inside a `KEYDOWN` check — deliberately using `KEYDOWN` (fires once) rather than `get_pressed()` (continuous) so holding space doesn't fire hundreds of bullets per second; a `shoot cooldown timer` could refine this further (see Challenge).
- `pygame.sprite.groupcollide(bullets, asteroids, True, True)` — checks every bullet against every asteroid; wherever they overlap, **both** are removed (`True, True`) and the pairing is returned as a dictionary `{bullet: [asteroid, asteroid, ...]}`.
- `score += 10 * len(hit_asteroids)` — a single bullet could theoretically hit multiple overlapping asteroids in one frame, so we award points per asteroid destroyed, not per bullet.
- `pygame.sprite.spritecollide(player, asteroids, True)` — checks the single `player` sprite against every sprite in `asteroids`, returning a plain list of colliding asteroids, and removes them (`True`) since they should be destroyed when they hit the ship.
- `player.take_damage(20)` for every colliding asteroid — if two asteroids hit the ship in the same frame, both damage instances apply.
- `if player.health <= 0:` — a simple conditional producing a game-over message, foreshadowing the full state machine you'll build in Lesson 7.

## Build Instructions

1. `pip install pygame` if not already installed.
2. Save as `main.py`, run with `python main.py`.
3. Move with arrow keys/A/D, fire with SPACE. Destroy red asteroids before they reach and damage your ship.
4. Watch the HUD score and health update live; when health hits 0, a game-over message appears (ESC still quits).

## Student Mission

Add a shoot cooldown: track a `shoot_timer` that increases by `dt` every frame, and only allow firing a new bullet when `shoot_timer` exceeds `0.25` seconds (then reset it to `0`). This prevents machine-gun-speed spam even if you later switch to holding SPACE with `get_pressed()`.

## Challenge

Add a `Shield` power-up sprite (drawn as a small green circle) that spawns occasionally like an asteroid. When the player collides with it (`spritecollide`), instead of taking damage, restore `20` health (clamped at 100) and remove the power-up.

## Experiment / Extension (Progressive)

1. **Change a value** — change asteroid damage from `20` to `5` and notice how much longer a run survives.
2. **Observe** — change `spawn_interval` from `0.8` to `0.3` and watch difficulty spike.
3. **Modify a mechanic** — make bigger asteroids (`size > 30`) require two bullet hits to destroy by giving `Asteroid` a `health` attribute instead of instantly dying on the first `groupcollide` hit (hint: you'll need `dokill=False` on the asteroid side and manually call `.kill()` when its health reaches 0).
4. **Combine mechanics** — award bonus score equal to the asteroid's `size` attribute, rewarding players more for destroying bigger asteroids.
5. **Build independently** — add an explosion effect: when an asteroid is destroyed, spawn a short-lived `Explosion` sprite (a growing, fading circle) at its position for a few frames before it removes itself.

## Common Mistakes

- **Using `dokill=True` on both sides of `groupcollide` for a "damage" scenario** where you actually want the surviving side (e.g., a tough enemy) to stay — check your `True`/`False` arguments carefully; they control which side(s) get automatically removed.
- **Modifying a group while iterating over it** without using the built-in collision functions — writing a manual `for` loop that calls `.kill()` mid-iteration can skip sprites or raise errors; prefer `spritecollide`/`groupcollide`, which are written to handle this safely.
- **Forgetting to add new sprites to *every* relevant group** — a `Bullet` must be added to both `bullets` (for collision checks) and `all_sprites` (for drawing/updating), or it won't behave correctly.
- **Holding SPACE with no cooldown and no `KEYDOWN` guard** — using `keys[pygame.K_SPACE]` directly in the update loop fires a new bullet every single frame (60 times a second), instantly overwhelming the game.

## Debugging Tips

- If bullets pass through asteroids without effect, print `len(bullets)` and `len(asteroids)` each frame to confirm both groups actually contain sprites at the time of collision.
- If score jumps by unexpected amounts, print `hits` (the dictionary from `groupcollide`) to see exactly which bullets matched which asteroids.
- If sprites seem to "ghost" (still visible after being killed), verify `.kill()` is actually being called — check you passed `True` for the correct side of `spritecollide`/`groupcollide`.
- Temporarily draw a red outline (`pygame.draw.rect(screen, RED, rect, 1)`) around a sprite's `rect` to double check its true collision bounds versus its visible art (especially with polygons, where `rect` is the bounding box, not the exact triangle).

## Check Your Understanding

- What is the difference between `spritecollide` and `groupcollide`?
- Why do we use `KEYDOWN` for shooting instead of `get_pressed()`?
- What does `sprite.kill()` actually do, and why is it safer than manually removing a sprite from a list?

## Mini Quiz

1. What does `pygame.sprite.groupcollide(bullets, asteroids, True, True)` return?
   a) A single boolean  b) A dictionary mapping colliding bullets to lists of asteroids they hit  c) Nothing, it only has side effects  d) The total number of collisions

2. What does calling `.kill()` on a sprite do?
   a) Deletes the Python object from memory immediately  b) Removes it from every group it belongs to  c) Sets its health to zero  d) Pauses its updates

3. Predict: if `dokill1=False, dokill2=True` in `groupcollide(group1, group2, dokill1, dokill2)`, which sprites are removed after a collision?
   a) Only sprites in `group1`  b) Only sprites in `group2`  c) Both  d) Neither

4. Why does the guided code check for `pygame.K_SPACE` inside the `KEYDOWN` event branch rather than `keys[pygame.K_SPACE]` in the continuous input section?
   a) `KEYDOWN` is required syntax  b) It prevents firing a new bullet every single frame while held  c) `get_pressed()` doesn't support the space key  d) There's no real difference

5. What is rect-based collision checking, fundamentally?
   a) Comparing exact pixel colors between two images  b) Checking whether two bounding rectangles overlap  c) Checking distance between two center points only  d) A machine-learning prediction

6. (Code reading) In the guided code, what happens to the player's health if two asteroids collide with the ship in the same frame?
   a) Only one hit registers, so `20` is subtracted total  b) Both hits register, so `40` is subtracted total  c) The game crashes  d) Health resets to 100

### Answer Key

1. b — a dict mapping bullets to the asteroids they hit
2. b — removes it from every group it belongs to
3. b — only sprites in `group2` are removed
4. b — prevents firing every single frame while held
5. b — checking whether two bounding rectangles overlap
6. b — both hits register, 40 total subtracted

## Lesson Recap

Today you built real gameplay consequence: bullets that can destroy targets, and hazards that can damage the player, all through Pygame's rect-based collision tools — `colliderect`, `spritecollide`, and `groupcollide`. You also practiced the shoot-and-cooldown pattern and safe sprite removal with `.kill()`. This is the mechanical core of nearly every 2D game genre: shooters, platformers, and even brick-breakers all reduce to "did these rects overlap, and what should happen if so?"

## Homework / Practice Mission

Add a `lives` system to the player: instead of ending the run the instant `health` reaches 0, give the player 3 lives. When health hits 0, decrement `lives`, reset `health` to 100, and briefly make the player invulnerable (skip damage) for 2 seconds (track an `invulnerable_timer`). When `lives` reaches 0, show the "SHIP DESTROYED" message permanently.

## Portfolio Project Connection

The `Bullet`, `Asteroid`(→`Enemy`), and collision-handling code in this lesson map directly onto `src/bullet.py`, `src/enemy.py`, and the collision section of `src/game.py` in **Project 04: Space Shooter**. The same `spritecollide`/`groupcollide` techniques reappear in **Project 06: Brick Breaker Clone** to detect ball-vs-paddle and ball-vs-brick collisions, and in **Project 05: Maze Runner Timer** to detect player-vs-hazard and player-vs-exit collisions.
