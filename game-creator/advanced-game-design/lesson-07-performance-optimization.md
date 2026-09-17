# Lesson 07 — Performance Optimization

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 7 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Python 3 + Pygame

---

## 🚀 Mission Brief

Your game plays great with 5 objects on screen. But what happens with 500 enemies, particles, or bullets? Today Mission Control assigns you to the **Engineering division**: performance optimization. You'll learn to measure (not guess) where your game is slow, avoid the classic per-frame performance traps, and implement **object pooling** — the single most impactful optimization technique in game development — so your capstone game stays smooth no matter how much action fills the screen.

## 🎯 Learning Objectives

1. Explain why "measure first, optimize second" is the golden rule of performance work.
2. Use Pygame's `Clock` and Python's `time`/`cProfile` to identify real bottlenecks.
3. Identify and fix common expensive per-frame mistakes (recreating objects, recomputing constants, inefficient collision checks).
4. Implement **object pooling** to eliminate the cost of constant object creation/destruction.
5. Apply a basic spatial partitioning idea to reduce unnecessary collision checks.

## 🛠️ What You'll Build

A **bullet-hell style stress test** comparing a naive (slow) implementation against an optimized one using object pooling and smarter collision checks — with an on-screen FPS counter so you can *see* the difference in real time, not just take it on faith.

## 📋 Prerequisites

- Lessons 1–6.
- Comfortable with Python lists, classes, and basic Big-O intuition (more objects = more work).

## 🔑 Key Concepts

- **Profiling** — measuring where your program actually spends its time, instead of guessing.
- **Frame Budget** — the total time available per frame (e.g., ~16.6ms at 60 FPS) that all your update/draw logic must fit inside.
- **Object Pooling** — pre-creating a reusable pool of objects and recycling them instead of constantly creating/destroying new ones.
- **Expensive Per-Frame Work** — operations that are fine once but costly when repeated every single frame for every object (e.g., loading a font, computing something constant).
- **Collision Check Complexity** — naive "check everything against everything" collision is O(n²) and gets slow fast as object counts grow.
- **Culling** — skipping update/draw work for objects that are off-screen or otherwise irrelevant this frame.

## 📖 Concept Explanation

### Measure First, Optimize Second

The single most important rule of performance work: **never optimize based on a guess**. Developers (even experienced ones) are frequently wrong about what's actually slow in their own code — a piece of code that "feels" expensive might cost almost nothing, while an innocent-looking line might be the real bottleneck. The fix is always the same: **measure**. Pygame's `clock.get_fps()` gives you a live frame rate. Python's built-in `time.perf_counter()` lets you time specific blocks of code precisely. Python's `cProfile` module gives a full breakdown of where time is spent across your entire program. Only after measuring do you know what to actually fix.

### The Frame Budget

At 60 FPS, your game has about **16.6 milliseconds** per frame to do *everything*: read input, update every entity, check every collision, and draw every pixel. If your logic takes longer than that, your frame rate drops below 60 and the game visibly stutters. Thinking in terms of a strict "budget" reframes performance work: it's not about being infinitely fast, it's about fitting comfortably inside that budget even during the busiest moments of your game (e.g., a screen full of enemies and bullets).

### The #1 Per-Frame Mistake: Creating and Destroying Objects Constantly

The most common performance killer in beginner games is this pattern: every time a bullet is fired, `bullets.append(Bullet())`; every time a bullet leaves the screen, `bullets.remove(bullet)`. Creating and destroying Python objects has real cost — memory allocation, garbage collection pressure — and in a bullet-hell game firing dozens of bullets per second, this adds up fast. Loading resources (fonts, images) inside the game loop instead of once at startup is the same mistake in a different disguise — always load/construct expensive things **once**, outside the loop, then reuse them.

### Object Pooling

**Object pooling** solves this directly: instead of creating and destroying objects, you pre-allocate a fixed pool of reusable objects up front. An "inactive" object simply has an `active = False` flag and is skipped during update/draw; when you need a new bullet, you find an inactive one in the pool, reset its position/velocity, and flip it to `active = True` — no new memory allocation happens during gameplay at all. This is one of the most important patterns in real game engines (and is literally how many professional engines implement particle systems and enemy spawners).

### Collision Check Complexity

Checking every object against every other object for collisions is called an **O(n²)** algorithm — if you have 10 objects, that's ~100 checks; 100 objects becomes ~10,000 checks; 1,000 objects becomes ~1,000,000 checks. This scales terribly. Two practical fixes for a capstone-scale game: (1) only check collisions between *relevant* groups (bullets vs. enemies, not bullets vs. bullets), using Pygame's built-in `pygame.sprite.spritecollide()` and `pygame.sprite.groupcollide()` which are optimized in C; and (2) **culling** — skip update/draw entirely for objects far off-screen, since they can't be visible or relevant anyway.

## 💻 Guided Coding

We'll build a stress-test comparison: a naive bullet system vs. a pooled one, with a visible FPS counter.

```python
"""
Performance Lab - Object Pooling Stress Test
Lumexa Game Creator Path - Course 03, Lesson 07
Press 1 for naive mode, 2 for pooled mode. Hold SPACE to fire bullets rapidly.
"""
import pygame
import sys
import cProfile
import pstats
import io

pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Performance Lab")
clock = pygame.time.Clock()
FPS = 60

BG = (10, 12, 28)
WHITE = (235, 235, 240)
BULLET_COLOR = (255, 210, 60)

# Load the font ONCE, outside the loop - a basic but critical optimization.
font = pygame.font.SysFont("consolas", 24)

POOL_SIZE = 800  # max bullets alive at once in pooled mode


class Bullet:
    __slots__ = ("x", "y", "vx", "vy", "active")  # slightly reduces memory overhead

    def __init__(self):
        self.x = 0.0
        self.y = 0.0
        self.vx = 0.0
        self.vy = -6.0
        self.active = False

    def fire(self, x, y):
        self.x, self.y = x, y
        self.active = True

    def update(self):
        self.y += self.vy
        if self.y < -10 or self.y > HEIGHT + 10:
            self.active = False

    def draw(self, surface):
        pygame.draw.circle(surface, BULLET_COLOR, (int(self.x), int(self.y)), 4)


class NaiveBulletSystem:
    """The 'bad' way: creates a brand-new object every shot, removes it on death.
    This causes constant memory allocation/deallocation - the classic mistake.
    """
    def __init__(self):
        self.bullets = []

    def fire(self, x, y):
        bullet = Bullet()  # <-- new allocation every single shot
        bullet.fire(x, y)
        self.bullets.append(bullet)

    def update(self):
        for bullet in self.bullets[:]:
            bullet.update()
            if not bullet.active:
                self.bullets.remove(bullet)  # <-- list removal, also not free

    def draw(self, surface):
        for bullet in self.bullets:
            bullet.draw(surface)

    def count_active(self):
        return len(self.bullets)


class PooledBulletSystem:
    """The optimized way: a fixed pool of pre-created bullets, reused forever.
    No allocation or deallocation happens during gameplay.
    """
    def __init__(self, size=POOL_SIZE):
        self.pool = [Bullet() for _ in range(size)]  # allocate once, up front

    def fire(self, x, y):
        for bullet in self.pool:
            if not bullet.active:
                bullet.fire(x, y)
                return  # reuse the first inactive bullet found

    def update(self):
        for bullet in self.pool:
            if bullet.active:
                bullet.update()

    def draw(self, surface):
        for bullet in self.pool:
            if bullet.active:
                bullet.draw(surface)

    def count_active(self):
        return sum(1 for b in self.pool if b.active)


def main():
    naive_system = NaiveBulletSystem()
    pooled_system = PooledBulletSystem()
    mode = "pooled"  # start in the good mode
    fire_cooldown = 0
    running = True

    while running:
        clock.tick(0)  # uncapped so we can see the true performance difference
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_1:
                    mode = "naive"
                    naive_system = NaiveBulletSystem()
                elif event.key == pygame.K_2:
                    mode = "pooled"
                    pooled_system = PooledBulletSystem()

        keys = pygame.key.get_pressed()
        if keys[pygame.K_SPACE] and fire_cooldown <= 0:
            x = WIDTH // 2
            y = HEIGHT - 40
            if mode == "naive":
                for _ in range(3):
                    naive_system.fire(x, y)
            else:
                for _ in range(3):
                    pooled_system.fire(x, y)
            fire_cooldown = 2
        fire_cooldown = max(0, fire_cooldown - 1)

        system = naive_system if mode == "naive" else pooled_system
        system.update()

        screen.fill(BG)
        system.draw(screen)

        fps = clock.get_fps()
        info_lines = [
            f"Mode: {mode.upper()}  (press 1=naive, 2=pooled)",
            f"Active bullets: {system.count_active()}",
            f"FPS: {fps:.1f}",
            "Hold SPACE to fire rapidly and watch the FPS counter.",
        ]
        for i, line in enumerate(info_lines):
            surface = font.render(line, True, WHITE)
            screen.blit(surface, (10, 10 + i * 26))

        pygame.display.flip()

    pygame.quit()
    sys.exit()


def profile_demo():
    """Run this function (instead of main()) to see a real cProfile report."""
    profiler = cProfile.Profile()
    profiler.enable()

    system = NaiveBulletSystem()
    for _ in range(500):
        system.fire(400, 500)
        system.update()

    profiler.disable()
    stream = io.StringIO()
    stats = pstats.Stats(profiler, stream=stream).sort_stats("cumulative")
    stats.print_stats(10)
    print(stream.getvalue())


if __name__ == "__main__":
    main()
    # Uncomment the next line instead of main() to see profiling output:
    # profile_demo()
```

## 🔍 Code Walkthrough

- **Font loaded once at module level**, not inside the loop — a small but real example of "don't do expensive setup every frame."
- **`NaiveBulletSystem.fire()`** allocates a new `Bullet()` every call; **`.update()`** calls `list.remove()` which is O(n) — both cost more as bullet counts grow.
- **`PooledBulletSystem.__init__`** allocates the entire pool **once**, up front; `fire()` finds and reuses an inactive slot; `update()`/`draw()` skip inactive bullets entirely — no allocation ever happens during play.
- **`clock.tick(0)`** removes the frame cap so you can observe raw performance differences; in your real game you'd use `clock.tick(60)` to cap normally.
- **`profile_demo()`** demonstrates real `cProfile` usage — running it prints exactly which function calls consumed the most cumulative time, replacing guesswork with data (echoing Lesson 6's "measure, don't guess" theme).
- **`__slots__`** on `Bullet` is a minor, real Python optimization: it prevents instances from having a dynamic `__dict__`, slightly reducing memory per object — worth knowing about, though pooling itself is the bigger win.

## 🏗️ Build Instructions

1. Save as `performance_lab.py`, run `python performance_lab.py`.
2. Press `1` for naive mode, hold SPACE, watch the FPS counter drop as bullet count grows.
3. Press `2` for pooled mode, hold SPACE the same way, and compare the FPS counter's stability.

## 🧭 Student Mission

Add an `Enemy` object-pool system, following the same pattern as `PooledBulletSystem`, and add pooled-bullet-vs-pooled-enemy collision checks using `pygame.Rect.colliderect` only between *active* bullets and *active* enemies (never bullet-vs-bullet). Confirm FPS stays stable with 50+ enemies and 500+ bullets on screen simultaneously.

## 🌌 Challenge

Implement basic **culling**: skip the `update()`/`draw()` call entirely for any bullet or enemy whose `y` position is more than 100 pixels outside the visible screen bounds, and measure (using `clock.get_fps()`) whether this meaningfully helps once pooling is already in place — explain in a comment why the answer might be "not much," since pooling already skips inactive objects.

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Change `POOL_SIZE` to 50 and 5000 and observe what happens when the pool is exhausted (new fire requests are silently ignored) — describe why a designer might want a "pool exhausted" fallback behavior.
- **Intermediate:** Add a timer that prints the average FPS over the last 60 frames to the console every second, giving a smoothed performance readout instead of one noisy per-frame number.
- **Advanced:** Run `profile_demo()`, read the `cProfile` output, and identify which function call takes the largest cumulative time — write one sentence explaining what it tells you.

## ⚠️ Common Mistakes

- **Optimizing before measuring** — spending an hour "optimizing" a function that was never actually slow, guided by a guess instead of data.
- **Loading images/fonts/sounds inside the game loop** instead of once at startup — this can single-handedly tank frame rate.
- **Checking every object against every other object for collisions** when only specific pairs matter (bullets vs. enemies, not enemies vs. enemies).
- **Pooling objects but forgetting to reset ALL their state on reuse** (e.g., forgetting to reset `vy` after a bullet type changes) — leads to subtle bugs where recycled objects behave like their previous use.

## 🐞 Debugging Tips

- If FPS still drops in "pooled" mode, confirm `count_active()` isn't secretly growing unbounded — a bug where bullets never get marked inactive would defeat pooling entirely.
- Use `clock.get_fps()` printed to the console (throttled to once per second) as your simplest, always-available performance readout.
- If `cProfile` output is overwhelming, sort by `"cumulative"` and only look at the top 5–10 entries — that's almost always where the real problem lives.

## ❓ Check Your Understanding

1. Why is "measure first" more reliable than guessing what's slow?
2. What is a "frame budget," and what happens when your logic exceeds it?
3. Explain object pooling in your own words. What problem does it solve?
4. Why is checking every object against every other object for collisions a scaling problem?
5. Give one example of "expensive per-frame work" that should be moved outside the loop.

## 📝 Mini Quiz (Answer Key at End)

1. The golden rule of performance optimization is:
   a) Optimize everything you can think of immediately
   b) Measure first, then optimize based on real data
   c) Never worry about performance until launch
   d) Always rewrite in a faster language

2. Object pooling primarily solves the cost of:
   a) Drawing shapes
   b) Constantly creating and destroying objects
   c) Reading keyboard input
   d) Loading levels

3. Checking every object against every other object for collisions is called:
   a) O(1) - constant time
   b) O(n²) - quadratic time, scales poorly
   c) Object pooling
   d) Culling

4. Loading a font inside the game loop every frame instead of once at startup is:
   a) A best practice
   b) An expensive per-frame mistake
   c) Required by Pygame
   d) Necessary for object pooling

5. `cProfile` is used to:
   a) Draw the game
   b) Measure exactly where a program spends its time
   c) Load levels from a file
   d) Handle keyboard input

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

## 🔁 Lesson Recap

You learned to measure performance instead of guessing, understood the concept of a per-frame **budget**, identified classic expensive mistakes (constant object creation, per-frame resource loading, O(n²) collision checks), and implemented **object pooling** — one of the most important optimization patterns in all of game development. You saw, with your own eyes via the FPS counter, the real difference pooling makes under load.

## 🏠 Homework / Practice Mission

Take any prototype from Lessons 1–5 that spawns objects repeatedly (orbs, asteroids, gems) and convert it to use object pooling, following today's `PooledBulletSystem` pattern. Measure and record the FPS before and after your change using `clock.get_fps()`, with at least 200 objects active simultaneously.

## 🗂️ Portfolio Project Connection

Object pooling and careful collision-check scoping are directly required in **Project 08** (physics objects) and **Project 09** (collectibles, moving platforms, enemies) to keep those games smooth. Project 09 is explicitly graded on "performance-conscious code per Lesson 7" — the pooling pattern from today is the primary technique graders will look for in its `entities/` modules.
