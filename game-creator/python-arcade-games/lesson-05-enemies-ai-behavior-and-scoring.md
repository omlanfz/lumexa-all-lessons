# Lesson 05: Enemies, AI Behavior, and Scoring

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 5 of 8
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.10+, Pygame 2.x

---

## Mission Brief

Cadet, incoming hostiles! Random falling rocks are one thing — a squadron of enemies that move in formation, fire back, and get tougher wave after wave is another. Today you build the `Enemy` class with genuine (if simple) AI behavior, a wave-spawning system, and a proper scoring/difficulty progression system — the exact systems that will make **Project 04: Space Shooter** feel like a real arcade game.

## Learning Objectives

1. Design an `Enemy` class with distinct movement patterns (side-to-side, diving, tracking).
2. Implement a wave-based spawn system that increases in difficulty over time.
3. Give enemies simple "AI" decision logic using timers and conditionals (when to shoot, when to change direction).
4. Build a persistent score and difficulty-scaling system shared across waves.
5. Understand the difference between deterministic and randomized AI behavior, and when to use each.

## What You'll Build

A wave of enemy ships that move side-to-side and periodically fire bullets downward at the player, with a wave counter that increases enemy speed and spawn rate every 15 seconds, plus a scoring system that rewards faster kills.

## Prerequisites

- Lesson 4 completed (`Player`, `Bullet`, collision detection with groups).
- Comfortable using timers (`timer += dt`) for time-based events.

## Key Concepts

- **Game AI (in this context)** — not machine learning, but rule-based decision logic: "if timer exceeds X, do Y."
- **Movement patterns** — deterministic math (e.g., sine-wave side-to-side motion) vs. simple state changes (bounce at screen edges).
- **Wave/spawn systems** — spawning groups of enemies at increasing difficulty over time, rather than one at a time forever.
- **Difficulty scaling** — increasing speed, spawn rate, or enemy health as a function of elapsed time or wave number.
- **Score multipliers and combos** — rewarding skillful play (e.g., faster kills, no damage taken) with bonus points.

## Concept Explanation

"AI" in a 2D arcade game rarely means neural networks — it means small, readable rules that make enemies feel alive. An enemy that moves left until it hits a boundary, then moves right, feels purposeful even though the logic is just: `if self.rect.left <= 0 or self.rect.right >= SCREEN_WIDTH: self.direction *= -1`. Similarly, "deciding" to shoot is often just a per-enemy timer: `self.shoot_timer += dt; if self.shoot_timer > self.shoot_interval: shoot()`.

Difficulty scaling matters because a game that never gets harder gets boring, and a game that's always brutally hard is frustrating. The standard arcade solution is **waves**: track elapsed time or a wave counter, and every so often, spawn more enemies, make them faster, or shorten their firing intervals. This is exactly the same "increase a variable, check a condition" pattern from every previous lesson — just applied to overall game pacing instead of one object's behavior.

## Guided Coding

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 05
Enemy AI behavior, wave spawning, and scoring.
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
ORANGE = (255, 150, 60)
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
    """A shared bullet class; direction distinguishes player vs enemy shots."""

    def __init__(self, x, y, direction=-1, color=YELLOW, speed=500):
        super().__init__()
        self.image = pygame.Surface((4, 14))
        self.image.fill(color)
        self.rect = self.image.get_rect(center=(x, y))
        self.speed = speed
        self.direction = direction  # -1 = upward (player), 1 = downward (enemy)

    def update(self, dt, keys=None):
        self.rect.y += self.direction * self.speed * dt
        if self.rect.bottom < 0 or self.rect.top > SCREEN_HEIGHT:
            self.kill()


class Enemy(pygame.sprite.Sprite):
    """An enemy with simple rule-based AI: side-to-side patrol + timed shooting."""

    def __init__(self, x, y, wave):
        super().__init__()
        self.image = pygame.Surface((36, 28), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, RED, [(0, 0), (36, 0), (18, 28)])
        self.rect = self.image.get_rect(center=(x, y))

        # Difficulty scaling: later waves move and shoot faster
        self.direction = random.choice([-1, 1])
        self.horizontal_speed = 60 + wave * 10
        self.shoot_timer = random.uniform(0, 2.0)
        self.shoot_interval = max(0.8, 2.5 - wave * 0.15)

    def update(self, dt, keys=None):
        self.rect.x += self.direction * self.horizontal_speed * dt
        if self.rect.left <= 0 or self.rect.right >= SCREEN_WIDTH:
            self.direction *= -1  # bounce off screen edges

        self.shoot_timer += dt
        if self.shoot_timer >= self.shoot_interval:
            self.shoot_timer = 0.0
            self.want_to_shoot = True
        else:
            self.want_to_shoot = False


def spawn_wave(wave_number, enemies, all_sprites):
    """Spawn a formation of enemies; more enemies appear in later waves."""
    enemy_count = min(4 + wave_number, 12)
    cols = min(enemy_count, 6)
    for i in range(enemy_count):
        row = i // cols
        col = i % cols
        x = 80 + col * 110
        y = 50 + row * 60
        enemy = Enemy(x, y, wave_number)
        enemies.add(enemy)
        all_sprites.add(enemy)


def main():
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Enemy Waves - Lesson 05")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 28)

    player = Player(SCREEN_WIDTH // 2, SCREEN_HEIGHT - 80)
    all_sprites = pygame.sprite.Group(player)
    player_bullets = pygame.sprite.Group()
    enemy_bullets = pygame.sprite.Group()
    enemies = pygame.sprite.Group()

    score = 0
    wave_number = 1
    wave_timer = 0.0
    wave_interval = 15.0  # seconds between new waves
    spawn_wave(wave_number, enemies, all_sprites)

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
                    bullet = Bullet(player.rect.centerx, player.rect.top,
                                     direction=-1, color=YELLOW, speed=520)
                    player_bullets.add(bullet)
                    all_sprites.add(bullet)

        keys = pygame.key.get_pressed()
        player.update(dt, keys)
        enemies.update(dt)
        player_bullets.update(dt)
        enemy_bullets.update(dt)

        # Enemy "AI decision": if an enemy wants to shoot, spawn a bullet
        for enemy in enemies:
            if getattr(enemy, "want_to_shoot", False):
                bullet = Bullet(enemy.rect.centerx, enemy.rect.bottom,
                                 direction=1, color=ORANGE, speed=280)
                enemy_bullets.add(bullet)
                all_sprites.add(bullet)

        # Collisions: player bullets vs enemies (score)
        hits = pygame.sprite.groupcollide(player_bullets, enemies, True, True)
        for bullet, hit_enemies in hits.items():
            score += 25 * len(hit_enemies)

        # Collisions: enemy bullets vs player (damage)
        hit_list = pygame.sprite.spritecollide(player, enemy_bullets, True)
        for _ in hit_list:
            player.take_damage(10)

        # Collisions: enemy ships ramming the player
        rammed = pygame.sprite.spritecollide(player, enemies, True)
        for _ in rammed:
            player.take_damage(25)

        # Wave progression: increase difficulty over time
        wave_timer += dt
        if len(enemies) == 0 or wave_timer >= wave_interval:
            wave_timer = 0.0
            wave_number += 1
            spawn_wave(wave_number, enemies, all_sprites)

        screen.fill(BLACK)
        all_sprites.draw(screen)
        hud = font.render(
            f"Score: {score}   Health: {player.health}   Wave: {wave_number}",
            True, WHITE,
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

- `class Bullet` now accepts `direction` and `color` parameters, letting a single class represent both player bullets (moving up, yellow) and enemy bullets (moving down, orange) — avoiding duplicate near-identical classes.
- `Enemy.__init__(self, x, y, wave)` — the `wave` parameter drives **difficulty scaling**: `horizontal_speed = 60 + wave * 10` and `shoot_interval = max(0.8, 2.5 - wave * 0.15)` both get harder as `wave` increases, with `max(0.8, ...)` preventing the interval from becoming unfairly close to zero.
- `if self.rect.left <= 0 or self.rect.right >= SCREEN_WIDTH: self.direction *= -1` — the classic "bounce" AI rule: reverse direction at screen edges, producing believable patrol behavior with almost no code.
- `self.shoot_timer += dt` / `if self.shoot_timer >= self.shoot_interval:` — the same timer-based decision pattern used for wave spawning, now driving individual enemy shooting "decisions" — this is what "AI" means at this level: a rule evaluated every frame.
- `self.want_to_shoot = True` — rather than having `Enemy.update()` directly create a `Bullet` (which would require passing bullet groups into every enemy), the enemy just raises a flag; the main loop checks that flag and performs the actual spawning — a clean separation of "deciding" from "acting."
- `getattr(enemy, "want_to_shoot", False)` — a defensive way to read an attribute that might not exist yet on the very first frame before `update()` has run once.
- `spawn_wave(wave_number, enemies, all_sprites)` — a standalone function (not a method) that builds a whole formation at once, using `enemy_count = min(4 + wave_number, 12)` to cap the maximum number of on-screen enemies so waves scale but don't spiral out of control.
- `if len(enemies) == 0 or wave_timer >= wave_interval:` — a new wave begins either when the player clears the current wave early, or when the timer runs out regardless — keeping the pace of the game consistent.
- Three separate collision checks (`player_bullets` vs `enemies`, `enemy_bullets` vs `player`, `enemies` vs `player`) show how a single frame can involve multiple independent collision rules, each with its own consequence.

## Build Instructions

1. `pip install pygame` (already installed if you've been following the course).
2. Save as `main.py`, run `python main.py`.
3. Move with arrow keys/A/D, shoot with SPACE. Clear waves of red enemy ships that patrol and shoot orange bullets back at you. Watch the wave counter and difficulty increase.

## Student Mission

Add a `score_multiplier` that increases by `0.1` for every wave survived without taking damage, and resets to `1.0` the moment the player takes any damage. Apply it when awarding points: `score += int(25 * len(hit_enemies) * score_multiplier)`.

## Challenge

Introduce a second enemy type, `DivingEnemy`, that instead of patrolling side-to-side, periodically picks a target y-position below its current one and accelerates toward the player's x position over a short window (a simple "dive" pattern), then returns to patrol behavior. Spawn a mix of `Enemy` and `DivingEnemy` starting at wave 3.

## Experiment / Extension (Progressive)

1. **Change a value** — change `wave_interval` from `15.0` to `8.0` and observe how much more frequently new waves arrive.
2. **Observe** — change `horizontal_speed = 60 + wave * 10` to `60 + wave * 30` and notice how quickly enemy movement becomes overwhelming.
3. **Modify a mechanic** — make enemies shoot in bursts of 2 bullets (fire, wait 0.15s, fire again) instead of a single bullet per interval.
4. **Combine mechanics** — give enemies a `health` of 2 for waves 5+, requiring two hits (use `dokill2=False` in `groupcollide` and manually track/reduce enemy health, killing manually at 0).
5. **Build independently** — design a `Boss` enemy that appears every 5th wave: bigger, more health, and a unique shooting pattern (e.g., three bullets fanned out at once using slightly different `direction` vectors, which requires extending `Bullet` to support angled movement with both x and y velocity).

## Common Mistakes

- **Coupling AI decisions directly to object creation** (having `Enemy.update()` try to create `Bullet` objects itself) — this tightly couples classes together and makes testing harder; the flag-based (`want_to_shoot`) pattern used here keeps `Enemy` independent of bullet/group management.
- **Forgetting `max()`/`min()` clamps on scaling formulas** — unclamped difficulty scaling (`shoot_interval = 2.5 - wave * 0.15`) can go negative or absurdly low at high wave numbers, breaking the game.
- **Spawning a new wave every frame** by checking `len(enemies) == 0` without also tracking whether a wave was *just* spawned — since `spawn_wave` runs synchronously in the same frame it's triggered, this is safe here, but if you delay spawning, guard against spawning multiple waves in a row.
- **Not resetting `wave_timer`** after spawning a new wave — this causes waves to trigger far more often than intended, stacking spawns.

## Debugging Tips

- Print `wave_number` and `len(enemies)` once per second (using a separate accumulating timer) to sanity-check pacing without flooding your terminal every frame.
- If enemies never shoot, verify `self.shoot_timer` is actually initialized in `__init__` and incremented in `update()` — a common bug is initializing it in `__init__` but never doing `+= dt` in `update`.
- If difficulty feels wrong, temporarily print the computed `horizontal_speed` and `shoot_interval` for each new enemy to confirm your scaling formula behaves as expected across waves 1 through 10.
- Use a debugger breakpoint inside the `if getattr(enemy, "want_to_shoot", False):` block to confirm bullets are created with sensible starting positions.

## Check Your Understanding

- Why does `Enemy` use a `want_to_shoot` flag instead of directly creating a `Bullet`?
- What two things does the `wave` parameter influence in `Enemy.__init__`, and why does that count as "difficulty scaling"?
- How does the bounce-at-edges pattern work, line by line?

## Mini Quiz

1. What triggers an enemy's "decision" to shoot in this lesson's code?
   a) Random chance every frame  b) A per-enemy timer reaching `shoot_interval`  c) The player pressing a key  d) Colliding with the player

2. Why is `shoot_interval` computed with `max(0.8, 2.5 - wave * 0.15)` instead of just `2.5 - wave * 0.15`?
   a) `max()` is required Python syntax  b) It prevents the interval from dropping below a reasonable floor as waves increase  c) It has no real effect  d) It converts seconds to milliseconds

3. Predict: at `wave = 1`, what is `horizontal_speed` given `60 + wave * 10`?
   a) 60  b) 70  c) 100  d) 610

4. What does `pygame.sprite.groupcollide(player_bullets, enemies, True, True)` do differently from `spritecollide`?
   a) It checks one sprite against a group  b) It checks every sprite in one group against every sprite in another group  c) It only works with two sprites at once  d) It is identical to `spritecollide`

5. Why is a `Bullet` class parameterized with `direction` and `color` instead of writing separate `PlayerBullet` and `EnemyBullet` classes?
   a) Because Python forbids multiple similar classes  b) To avoid duplicating nearly identical code for two behaviors that differ only by a couple of values  c) Because `pygame.sprite.Sprite` requires exactly one bullet class  d) It has no benefit either way

6. (Code reading) What happens when `len(enemies) == 0` in the main loop, regardless of `wave_timer`?
   a) The game ends  b) A new wave spawns immediately  c) The player's health resets  d) Nothing happens until `wave_timer` also expires

### Answer Key

1. b — a per-enemy timer reaching `shoot_interval`
2. b — prevents the interval from dropping unreasonably low at high waves
3. b — 70 (60 + 1×10)
4. b — checks every sprite in one group against every sprite in another
5. b — avoids duplicating nearly identical code
6. b — a new wave spawns immediately

## Lesson Recap

You built rule-based "AI" — patrol-and-bounce movement, timer-driven shooting decisions — and a wave/difficulty-scaling system that keeps a game interesting over time. You also practiced separating "deciding" (setting a flag) from "acting" (the main loop reading that flag and spawning a bullet), a design habit that keeps sprite classes independent and easier to extend. Scoring is now tied to meaningful play (enemies destroyed) rather than arbitrary ticks.

## Homework / Practice Mission

Add a simple "combo" system: track `combo_count`, incrementing it each time an enemy is destroyed without the player taking damage in between, and reset it to 0 the instant the player takes damage. Award bonus score equal to `combo_count * 5` per kill once `combo_count` exceeds 3. Display the current combo in the HUD when it's active.

## Portfolio Project Connection

This lesson's `Enemy` class, `spawn_wave` function, and difficulty-scaling formulas become `src/enemy.py` and the wave-management logic inside `src/game.py` in **Project 04: Space Shooter**, essentially unchanged. The general pattern of "timer-driven decision flags read by the main loop" also appears in **Project 05: Maze Runner Timer**'s moving hazard, which uses the same timer-and-bounce logic to patrol a corridor.
