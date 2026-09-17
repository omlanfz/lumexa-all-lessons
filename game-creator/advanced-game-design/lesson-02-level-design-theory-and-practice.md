# Lesson 02 — Level Design Theory and Practice

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 2 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Python 3 + Pygame

---

## 🚀 Mission Brief

Your core loop from Lesson 1 works — but a single flat screen gets boring fast. Real games unfold across **levels**: carefully designed spaces that teach, challenge, and surprise the player in sequence. Today, Mission Control assigns you to the Level Design division. You'll learn how professional designers build levels using data instead of hard-coded shapes, how to pace difficulty across a level, and how to guide players using nothing but visual layout — no instructions needed.

## 🎯 Learning Objectives

1. Explain the purpose of **level design** as teaching, pacing, and testing player skills.
2. Describe the **"teach, test, twist"** pattern used to introduce new mechanics.
3. Design levels as **data structures** (lists/dictionaries) rather than hard-coded object placement.
4. Build a Pygame level loader that reads level data and constructs the level from it.
5. Apply **visual guidance** techniques (framing, lighting/contrast, leading lines) to direct player attention without text.

## 🛠️ What You'll Build

A **data-driven level system**: a Python module describing 3 increasingly difficult levels of a simple "collect the gems, avoid the spikes, reach the exit" game, plus a loader that builds each level from that data. This exact pattern is what Project 08 (Physics Puzzle Game) and Project 09 (Capstone Platformer) will use for all their levels.

## 📋 Prerequisites

- Lesson 1 (core loop, balance, flow).
- Comfortable with Python lists, dictionaries, and tuples.
- Basic Pygame rects and collision detection.

## 🔑 Key Concepts

- **Level Design** — arranging space, obstacles, and rewards to create a specific player experience.
- **Data-Driven Design** — describing content (levels, dialogue, items) as data rather than code, so it's easy to add/edit without touching game logic.
- **Teach / Test / Twist** — the classic 3-step pattern: introduce a mechanic safely, test it under pressure, then twist it in a surprising way.
- **Pacing** — the rhythm of tension and release across a level (hard section → breather → hard section).
- **Visual Guidance / Environmental Storytelling** — using color, light, and layout to direct the player's eye and choices without text boxes.
- **Difficulty Curve** — the overall shape of challenge across a full game (usually a gentle upward staircase, not a straight line).

## 📖 Concept Explanation

### Why Levels Exist

A level is not just "more content" — it's a controlled experience. Good level designers think of themselves as *tour guides*: every wall, platform, light, and enemy placement either teaches a skill, tests a skill, or gives the player a moment to breathe. If a level is just "here are more enemies," it isn't designed — it's just harder. Real level design has **intention** behind every choice.

### Teach, Test, Twist

This is the most reliable pattern in level design, used everywhere from *Super Mario Bros.* to modern puzzle games:
1. **Teach** — introduce a new element in a *safe* context. Example: the first spike in a platformer is out in the open, easy to see, with plenty of room to react.
2. **Test** — put the same element in a slightly more demanding context, now combined with something the player already knows (e.g., a spike right after a jump).
3. **Twist** — surprise the player with a new combination or a clever variation (e.g., a moving platform *over* a spike pit) that requires them to apply the mechanic creatively.

You should be able to point at almost any well-designed level and label each section as Teach, Test, or Twist. This lesson's own project levels will do exactly that.

### Data-Driven Level Design

In Lesson 1's prototype, all objects were created randomly in code. That's fine for an endless loop, but a designed *level* needs **precise, repeatable placement** — the same spike in the same spot every time you play, so players can learn and master it. The professional solution: describe each level as **data** (a list of dictionaries, a 2D grid of characters, or a JSON file) and write one generic "level loader" function that turns that data into real game objects. This separates *content* (level layouts) from *logic* (how the game runs), which means:

- Designers (or you, wearing your designer hat) can add new levels without touching game code.
- Levels are easy to visualize, share, and debug — a grid of characters is basically a readable little map.
- The same loader code works for level 1 and level 50.

### Pacing and the Difficulty Curve

Across a full game, difficulty should generally trend upward, but never as a straight line. Think of it as a staircase with landings: hard section, then a **breather** (an easier or purely exploratory bit), then a harder section than before. Constant maximum tension exhausts players; constant ease bores them. The "landings" also give players a chance to feel their own growing mastery — replaying an early, easy section after learning new skills is part of why games feel satisfying.

### Visual Guidance Without Words

Great level design tells the player where to go without a single line of text. Techniques include:
- **Framing** — putting the exit or goal in view, even from a distance, so players have a destination.
- **Contrast/lighting** — making interactive elements (doors, collectibles) brighter or more saturated than the background.
- **Leading lines** — corridors, platforms, or paths that visually point toward important areas.
- **Silhouette clarity** — hazards should be immediately recognizable by shape/color alone (we already did this in Lesson 1: red = danger, yellow = reward).

## 💻 Guided Coding

We'll build a small data-driven level system: a `LEVELS` data structure plus a loader, in a game where the player moves a square around a screen, avoids spikes, collects gems, and reaches an exit.

```python
"""
Level Design Lab - Data-Driven Levels
Lumexa Game Creator Path - Course 03, Lesson 02
"""
import pygame
import sys

pygame.init()
TILE = 40
COLS, ROWS = 20, 14
WIDTH, HEIGHT = COLS * TILE, ROWS * TILE
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Level Design Lab")
clock = pygame.time.Clock()
FPS = 60

WHITE = (235, 235, 240)
BG = (12, 14, 30)
WALL = (70, 80, 110)
SPIKE = (220, 60, 60)
GEM = (255, 210, 60)
EXIT_COLOR = (80, 230, 140)
PLAYER_COLOR = (80, 200, 255)

font = pygame.font.SysFont("consolas", 26)

# ---------------------------------------------------------------------------
# LEVEL DATA: each level is a grid of characters.
#   '#' = wall, '.' = floor, 'S' = spike, 'G' = gem, 'E' = exit, 'P' = player start
# This is the "teach, test, twist" pattern made literal:
#   Level 1 teaches spikes in the open. Level 2 tests spikes near turns.
#   Level 3 twists it by combining gems + spikes in a tight corridor.
# ---------------------------------------------------------------------------
LEVELS = [
    [
        "####################",
        "#P.................#",
        "#.....G............#",
        "#..................#",
        "#........S.........#",
        "#..................#",
        "#.....G......G.....#",
        "#..................#",
        "#..........S.......#",
        "#..................#",
        "#.................E#",
        "#..................#",
        "#..................#",
        "####################",
    ],
    [
        "####################",
        "#P..#..............#",
        "#...#...S..#####...#",
        "#...#...........#..#",
        "#.G.#####...G....#..#"[:20].ljust(20, '#'),
        "#...S.....#....S.#..#"[:20].ljust(20, '#'),
        "#.........#.........#",
        "#####.....#.....#####",
        "#.........#.........#",
        "#...G.....#.....G...#",
        "#.........#.........#",
        "#....S....#....S....#",
        "#..................E#",
        "####################",
    ],
    [
        "####################",
        "#P.................#",
        "#.SGSGSGSGSGSGSGSGS.#",
        "#..................#",
        "#..############....#",
        "#..#..........#....#",
        "#..#.G.SSSS.G.#....#",
        "#..#..........#....#",
        "#..############....#",
        "#..................#",
        "#.SGSGSGSGSGSGSGSGS.#",
        "#..................#",
        "#..................E",
        "####################",
    ],
]


class Level:
    """Parses a level grid (list of strings) into game entities."""
    def __init__(self, grid):
        self.walls = []
        self.spikes = []
        self.gems = []
        self.exit_rect = None
        self.player_start = (TILE, TILE)

        for row_index, row in enumerate(grid):
            for col_index, char in enumerate(row):
                x, y = col_index * TILE, row_index * TILE
                rect = pygame.Rect(x, y, TILE, TILE)
                if char == "#":
                    self.walls.append(rect)
                elif char == "S":
                    self.spikes.append(rect)
                elif char == "G":
                    self.gems.append(rect)
                elif char == "E":
                    self.exit_rect = rect
                elif char == "P":
                    self.player_start = (x + TILE // 2, y + TILE // 2)

    def draw(self, surface):
        for wall in self.walls:
            pygame.draw.rect(surface, WALL, wall)
        for spike in self.spikes:
            cx, cy = spike.center
            pygame.draw.polygon(
                surface, SPIKE,
                [(cx, spike.top + 4), (spike.left + 4, spike.bottom - 4), (spike.right - 4, spike.bottom - 4)]
            )
        for gem in self.gems:
            pygame.draw.circle(surface, GEM, gem.center, TILE // 4)
        if self.exit_rect:
            pygame.draw.rect(surface, EXIT_COLOR, self.exit_rect, border_radius=6)


def load_level(index):
    return Level(LEVELS[index])


def main():
    level_index = 0
    level = load_level(level_index)
    player = pygame.Rect(0, 0, TILE - 12, TILE - 12)
    player.center = level.player_start
    speed = 4
    score = 0
    message = ""
    running = True

    while running:
        clock.tick(FPS)
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        keys = pygame.key.get_pressed()
        dx = dy = 0
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            dx = -speed
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            dx = speed
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            dy = -speed
        if keys[pygame.K_DOWN] or keys[pygame.K_s]:
            dy = speed

        player.x += dx
        for wall in level.walls:
            if player.colliderect(wall):
                player.x -= dx
                break
        player.y += dy
        for wall in level.walls:
            if player.colliderect(wall):
                player.y -= dy
                break

        for spike in level.spikes:
            if player.colliderect(spike):
                player.center = level.player_start
                message = "Ouch! Back to start."

        for gem in level.gems[:]:
            if player.colliderect(gem):
                level.gems.remove(gem)
                score += 10
                message = "Gem collected!"

        if level.exit_rect and player.colliderect(level.exit_rect) and not level.gems:
            level_index += 1
            if level_index >= len(LEVELS):
                message = "All levels complete! You win!"
                level_index = len(LEVELS) - 1
            else:
                level = load_level(level_index)
                player.center = level.player_start
                message = f"Level {level_index + 1} loaded!"
        elif level.exit_rect and player.colliderect(level.exit_rect) and level.gems:
            message = "Collect all gems before exiting!"

        screen.fill(BG)
        level.draw(screen)
        pygame.draw.rect(screen, PLAYER_COLOR, player, border_radius=4)

        hud = font.render(f"Level {level_index + 1}   Score: {score}", True, WHITE)
        screen.blit(hud, (10, HEIGHT - 34))
        if message:
            msg_surface = font.render(message, True, WHITE)
            screen.blit(msg_surface, (10, 6))

        pygame.display.flip()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## 🔍 Code Walkthrough

- **`LEVELS`** is pure data — a list of lists of strings. No game logic lives here at all. This is the essence of data-driven design.
- **`Level.__init__`** is the *loader*: it walks the grid once and converts characters into real `pygame.Rect` objects sorted into lists (`walls`, `spikes`, `gems`). Any level, of any layout, goes through this exact same code — you never write new loading logic per level.
- Notice **Level 1** places a single spike far from anything else (**Teach**), **Level 2** places spikes near turns and corridors (**Test**), and **Level 3** combines rows of alternating gems/spikes with a maze-like inner room (**Twist**) — the teach/test/twist arc made literal in data.
- The **exit gating** (`if ... and not level.gems`) is a small design choice: the player must collect every gem before the exit works, forcing full exploration of the level's space.

## 🏗️ Build Instructions

1. Save the code as `level_lab.py`.
2. Run `python level_lab.py`.
3. Play through all 3 levels using arrow keys or WASD.

## 🧭 Student Mission

Design your **own 4th level** as a new grid string list. It must include: at least 2 gems, at least 2 spikes, one exit, and demonstrate a clear "twist" idea not seen in levels 1–3 (e.g., a narrow one-tile corridor lined with spikes on both sides, or a spiral corridor). Add it to `LEVELS` and confirm it loads and plays correctly.

## 🌌 Challenge

Add a **moving spike** (a spike that shifts left-right each frame using a sine wave or bouncing logic) to your custom level, turning a static hazard into a dynamic timing challenge — a classic "twist" mechanic.

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Rearrange existing tiles in Level 1 without adding new tile types.
- **Intermediate:** Add a new tile type, `'D'` for a locked door that only opens after all gems in that level are collected (separate from the exit).
- **Advanced:** Load level data from an external `.txt` file using Python file I/O (`open(filename).read().splitlines()`) instead of hardcoding the grid in the script — true separation of content from code.

## ⚠️ Common Mistakes

- **Hard-coding object positions in game logic** instead of loading them from data — this makes adding new levels painful and error-prone.
- **Introducing two new mechanics at once** in a "teach" section — always teach one new idea at a time.
- **Forgetting to reset the player position on level transition**, leaving the player stuck outside the new level's walls.

## 🐞 Debugging Tips

- If a level looks wrong, `print()` the grid row by row with the row index — it's easy to miscount columns in a raw string grid.
- Make sure every row in a level string is exactly `COLS` characters long — a short row silently shifts everything after it. Use `len(row)` assertions while developing.
- If gems never register as collected, check that you're iterating over a **copy** of the list (`level.gems[:]`) when removing items during iteration.

## ❓ Check Your Understanding

1. What does "data-driven" mean, and why does it matter for level design?
2. Explain "teach, test, twist" using an example from today's three levels.
3. Why do difficulty curves usually look like a staircase rather than a straight line?
4. Name two visual guidance techniques that don't use text.
5. Why did we gate the exit behind collecting all gems?

## 📝 Mini Quiz (Answer Key at End)

1. A level described as a grid of characters and turned into objects by a loader function is an example of:
   a) Hard-coded design
   b) Data-driven design
   c) Random generation
   d) Procedural audio

2. The "Teach" phase of a mechanic should be:
   a) The hardest version of the challenge
   b) Introduced safely with room to fail without punishment
   c) Skipped entirely for advanced players
   d) Always the last level

3. A "breather" section in pacing is:
   a) A section with no players
   b) An easier or calmer moment between intense challenges
   c) A bug in the level
   d) The final boss

4. Which of the following is a visual guidance technique?
   a) A pop-up text box explaining the level
   b) A brightly lit doorway drawing the eye toward the goal
   c) A loading screen
   d) A pause menu

5. In the code, what data structure represents a single level layout?
   a) A dictionary of sounds
   b) A list of strings (the grid)
   c) A single integer
   d) A Pygame Surface

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

## 🔁 Lesson Recap

You learned that level design is intentional space-crafting, not just "adding more stuff." You practiced the **teach/test/twist** pattern, built a **data-driven level loader**, and used **visual guidance** (color-coded hazards/rewards) to direct players without any text. This data-driven pattern — grid data in, real game objects out — is exactly the architecture you'll reuse for every level in Projects 08 and 09.

## 🏠 Homework / Practice Mission

Design two more full levels (5 and 6) that escalate difficulty from your Student Mission level. For each, write one sentence explaining what new skill or twist it teaches. Bring your six-level set to Lesson 3, where you'll wrap this level system inside a cleaner class-based architecture.

## 🗂️ Portfolio Project Connection

This exact data-driven grid/level-loader pattern is the direct ancestor of the level systems in **Project 08 (Physics Puzzle Game)**, which needs 5+ hand-designed levels of increasing difficulty, and **Project 09 (Capstone Platformer)**, which needs 3+ levels with checkpoints. Whatever level format you invent today, you will extend in those projects.
