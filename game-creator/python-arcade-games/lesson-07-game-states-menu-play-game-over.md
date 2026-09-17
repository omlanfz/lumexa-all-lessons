# Lesson 07: Game States — Menu, Play, Game Over

**Path:** Game Creator Path
**Course:** Course 02 — Python Arcade Games
**Lesson:** 7 of 8
**Duration:** 60 minutes
**Difficulty:** Intermediate
**Technology:** Python 3.10+, Pygame 2.x

---

## Mission Brief

Cadet, mission control requires a proper flight plan: launch sequence, active mission, and debrief. Every real game — including all three projects in this course — needs a **state machine**: a menu screen, a playing screen, a game-over/win screen, and clean transitions between them, including restarting without relaunching the whole program. Today you build that structure once, cleanly, so it never has to be reinvented.

## Learning Objectives

1. Explain what a game state machine is and why games need one.
2. Represent states cleanly using a simple enum-like pattern.
3. Implement separate update/draw logic for each state (menu, playing, game over).
4. Handle state transitions triggered by input (start game, restart, return to menu).
5. Reset game data correctly when restarting, avoiding leftover state bugs.

## What You'll Build

A complete mini-game with three real screens: a **Menu** screen (press ENTER/SPACE to start), a **Playing** screen (the shooting-gallery mechanics from earlier lessons), and a **Game Over** screen (shows final score, press R to restart or ESC to quit) — all inside one running program, with correct state resets on restart.

## Prerequisites

- Lessons 1–6 completed (sprites, collisions, enemies, sound).
- Comfortable reading conditional branches and simple enums/constants.

## Key Concepts

- **State machine** — a system that is always in exactly one of a fixed set of states, with defined rules for transitioning between them.
- **Enum-like pattern in Python** — using a class of constants (or `enum.Enum`) to represent named states instead of "magic strings" scattered through the code.
- **Per-state update/draw functions** — instead of one giant `update()`/`draw()` with nested `if`s everywhere, dedicate a function (or method) per state.
- **State reset** — when restarting, every piece of mutable game data (score, health, sprite groups) must be recreated or reset — a extremely common bug source is stale data leaking between runs.
- **Input handling per state** — the same key can mean different things in different states (ENTER starts the game in the menu, but does nothing during play).

## Concept Explanation

Up to now, every lesson's `main()` function has done one thing forever: play the shooting gallery until you quit. Real games have a lifecycle: you see a title screen, you play, you die or win, you see a summary, and you can play again — all without closing and reopening the program. This lifecycle is a **state machine**: a variable (commonly named `state`) that holds exactly one value at a time — `MENU`, `PLAYING`, or `GAME_OVER` — and the main loop's behavior branches based on that value.

Rather than one enormous `if/elif` chain crammed with logic, the cleanest approach in Pygame is to give each state its own **update** and **draw** logic — either as separate functions, or (as shown here) as `if state == GameState.MENU: ... elif state == GameState.PLAYING: ...` blocks, each calling out to focused helper functions. The critical discipline is **state reset**: when the player restarts after game over, you must recreate the player, clear all sprite groups, and reset the score to zero — otherwise leftover enemies, bullets, or an already-dead player will cause confusing bugs on the second playthrough.

## Guided Coding

```python
"""
Lumexa Game Creator Path - Course 02, Lesson 07
A full state machine: Menu -> Playing -> Game Over -> restart.
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
GRAY = (150, 150, 160)


class GameState:
    """Simple enum-like pattern: named constants instead of magic strings."""
    MENU = "menu"
    PLAYING = "playing"
    GAME_OVER = "game_over"


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
        self.speed = 520

    def update(self, dt, keys=None):
        self.rect.y -= self.speed * dt
        if self.rect.bottom < 0:
            self.kill()


class Enemy(pygame.sprite.Sprite):
    def __init__(self, x, y):
        super().__init__()
        self.image = pygame.Surface((36, 28), pygame.SRCALPHA)
        pygame.draw.polygon(self.image, RED, [(0, 0), (36, 0), (18, 28)])
        self.rect = self.image.get_rect(center=(x, y))
        self.direction = random.choice([-1, 1])
        self.speed = 90

    def update(self, dt, keys=None):
        self.rect.x += self.direction * self.speed * dt
        if self.rect.left <= 0 or self.rect.right >= SCREEN_WIDTH:
            self.direction *= -1


class Game:
    """Owns the state machine and all mutable session data."""

    def __init__(self, screen, font, big_font):
        self.screen = screen
        self.font = font
        self.big_font = big_font
        self.state = GameState.MENU
        self.final_score = 0
        # Placeholders; real values are created in start_new_game()
        self.player = None
        self.all_sprites = None
        self.bullets = None
        self.enemies = None
        self.score = 0

    def start_new_game(self):
        """Reset ALL mutable state - this is the critical anti-bug step."""
        self.player = Player(SCREEN_WIDTH // 2, SCREEN_HEIGHT - 80)
        self.all_sprites = pygame.sprite.Group(self.player)
        self.bullets = pygame.sprite.Group()
        self.enemies = pygame.sprite.Group()
        self.score = 0

        for i in range(6):
            enemy = Enemy(100 + i * 110, 60)
            self.enemies.add(enemy)
            self.all_sprites.add(enemy)

        self.state = GameState.PLAYING

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                return False  # signal quit
            if self.state == GameState.MENU:
                if event.key in (pygame.K_RETURN, pygame.K_SPACE):
                    self.start_new_game()
            elif self.state == GameState.PLAYING:
                if event.key == pygame.K_SPACE:
                    bullet = Bullet(self.player.rect.centerx, self.player.rect.top)
                    self.bullets.add(bullet)
                    self.all_sprites.add(bullet)
            elif self.state == GameState.GAME_OVER:
                if event.key == pygame.K_r:
                    self.start_new_game()
        return True

    def update(self, dt, keys):
        if self.state == GameState.PLAYING:
            self.player.update(dt, keys)
            self.enemies.update(dt)
            self.bullets.update(dt)

            hits = pygame.sprite.groupcollide(self.bullets, self.enemies, True, True)
            if hits:
                self.score += 25 * sum(len(v) for v in hits.values())

            rammed = pygame.sprite.spritecollide(self.player, self.enemies, True)
            for _ in rammed:
                self.player.take_damage(30)

            if len(self.enemies) == 0:
                for i in range(6):
                    enemy = Enemy(100 + i * 110, 60)
                    self.enemies.add(enemy)
                    self.all_sprites.add(enemy)

            if self.player.health <= 0:
                self.final_score = self.score
                self.state = GameState.GAME_OVER

    def draw(self):
        self.screen.fill(BLACK)
        if self.state == GameState.MENU:
            self._draw_menu()
        elif self.state == GameState.PLAYING:
            self._draw_playing()
        elif self.state == GameState.GAME_OVER:
            self._draw_game_over()
        pygame.display.flip()

    def _draw_menu(self):
        title = self.big_font.render("LUMEXA ARCADE", True, CYAN)
        prompt = self.font.render("Press ENTER or SPACE to start", True, WHITE)
        hint = self.font.render("Arrow keys/A-D to move, SPACE to shoot, ESC to quit", True, GRAY)
        self.screen.blit(title, (SCREEN_WIDTH // 2 - title.get_width() // 2, 220))
        self.screen.blit(prompt, (SCREEN_WIDTH // 2 - prompt.get_width() // 2, 300))
        self.screen.blit(hint, (SCREEN_WIDTH // 2 - hint.get_width() // 2, 340))

    def _draw_playing(self):
        self.all_sprites.draw(self.screen)
        hud = self.font.render(
            f"Score: {self.score}   Health: {self.player.health}", True, WHITE
        )
        self.screen.blit(hud, (10, 10))

    def _draw_game_over(self):
        title = self.big_font.render("GAME OVER", True, RED)
        score_text = self.font.render(f"Final Score: {self.final_score}", True, WHITE)
        prompt = self.font.render("Press R to restart or ESC to quit", True, GRAY)
        self.screen.blit(title, (SCREEN_WIDTH // 2 - title.get_width() // 2, 220))
        self.screen.blit(score_text, (SCREEN_WIDTH // 2 - score_text.get_width() // 2, 300))
        self.screen.blit(prompt, (SCREEN_WIDTH // 2 - prompt.get_width() // 2, 340))


def main():
    pygame.init()
    screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
    pygame.display.set_caption("Lumexa Game States - Lesson 07")
    clock = pygame.time.Clock()
    font = pygame.font.SysFont(None, 28)
    big_font = pygame.font.SysFont(None, 64)

    game = Game(screen, font, big_font)
    running = True

    while running:
        dt = clock.tick(FPS) / 1000.0

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            else:
                running = game.handle_event(event)
                if not running:
                    break

        keys = pygame.key.get_pressed()
        game.update(dt, keys)
        game.draw()

    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## Code Walkthrough

- `class GameState:` — the enum-like pattern: plain string constants (`MENU`, `PLAYING`, `GAME_OVER`) grouped in one class purely for organization and to avoid typos from scattered raw strings. (Python's built-in `enum.Enum` is an alternative, but this simpler pattern is easy to read and extend for students.)
- `class Game:` — a container object owning **all** mutable session state (`player`, `score`, sprite groups) plus the current `state`. Wrapping this in a class (rather than loose global variables in `main()`) makes resetting everything in one method (`start_new_game`) straightforward and safe.
- `self.state = GameState.MENU` in `__init__` — the game always starts on the menu screen, never straight into gameplay.
- `def start_new_game(self):` — the single place where every piece of gameplay state is (re)created from scratch: a fresh `Player`, empty `bullets`/`enemies` groups repopulated with new enemies, and `score` reset to `0`. This method runs both the *first* time the game starts and every *restart* — guaranteeing no leftover data from a previous run.
- `def handle_event(self, event):` — routes input differently depending on `self.state`: ENTER/SPACE only starts the game from the menu; SPACE only shoots while playing; `R` only restarts from game over. This prevents, for example, accidentally "shooting" while still on the menu screen.
- `return False` for `K_ESCAPE` — a way for `Game` to signal "please quit" back up to `main()`'s loop, since `Game` doesn't control `running` directly.
- `def update(self, dt, keys):` — logic only runs for the `PLAYING` state; the menu and game-over screens are static (no physics to update), which is why the `if` only covers that one branch.
- `if self.player.health <= 0: self.final_score = self.score; self.state = GameState.GAME_OVER` — the actual state **transition**: playing ends and the game-over screen begins, and we snapshot `self.score` into `self.final_score` so it can still be displayed even after the next restart resets `self.score` to 0.
- `def draw(self):` dispatches to `_draw_menu`, `_draw_playing`, or `_draw_game_over` based on `self.state` — each is a small, focused method, instead of one giant function with nested conditionals for every possible screen element.
- In `main()`, `game.handle_event(event)` returns `True`/`False` to control the outer `running` flag, keeping quit-handling centralized while state-specific input logic lives inside `Game`.

## Build Instructions

1. `pip install pygame`.
2. Save as `main.py`, run `python main.py`.
3. You'll see the Lumexa Arcade menu screen. Press ENTER or SPACE to begin.
4. Play with arrow keys/A-D and SPACE to shoot; when health reaches zero, the Game Over screen appears with your final score.
5. Press `R` to restart (verify enemies, health, and score are all freshly reset) or ESC to quit from any screen.

## Student Mission

Add a fourth state, `GameState.PAUSED`. Pressing `P` during `PLAYING` should switch to `PAUSED` (freeze `update()`, but still `draw()` the last playing frame with a semi-transparent "PAUSED" overlay); pressing `P` again resumes `PLAYING`.

## Challenge

Add a **high score** that persists across restarts within the same program run (a class attribute or a variable stored outside `Game`, or an attribute like `self.high_score` that is NOT reset in `start_new_game()`, only compared/updated when `final_score` is set). Display "Best: X" on both the menu and game-over screens.

## Experiment / Extension (Progressive)

1. **Change a value** — change the menu's title text and colors to make it feel more "Lumexa space mission" themed (e.g., "MISSION: STARFIELD DEFENSE").
2. **Observe** — remove the `self.state = GameState.MENU` initial assignment (or set it to `PLAYING` directly) and notice the game skips the menu entirely, confirming that state alone controls flow.
3. **Modify a mechanic** — make the game-over screen show a different message depending on whether the player beat their previous high score.
4. **Combine mechanics** — add a short "3, 2, 1, GO!" countdown state (`GameState.COUNTDOWN`) between the menu and playing states, using a timer inside `Game` to auto-transition to `PLAYING` after 3 seconds.
5. **Build independently** — design a `GameState.WIN` state, triggered after surviving a fixed number of enemy waves, with its own draw method and its own restart-to-menu flow, separate from `GAME_OVER`.

## Common Mistakes

- **Forgetting to reset a piece of state in `start_new_game()`** — a very common bug is resetting `score` and enemies but forgetting to fully rebuild `bullets`, leaving old bullets from the previous run still flying on screen.
- **Checking/updating game logic regardless of state** — forgetting to wrap `update()` logic in `if self.state == GameState.PLAYING:` means enemies keep moving even while paused or on the menu.
- **Using raw string literals** (`"menu"`, `"Menu"`, `"MENU "`) inconsistently instead of the `GameState` constants — a typo like `"mnu"` silently fails every comparison with no error message, since Python string comparison just returns `False`.
- **Not distinguishing between quitting the window (`pygame.QUIT`) and quitting via ESC** — both should stop the loop, but conflating their handling can cause one path to be missed.

## Debugging Tips

- Print `self.state` once per second (using an accumulating timer, not every frame) whenever you're debugging a state transition that isn't happening as expected.
- If the "PLAYING" screen still shows enemies from the previous life, double-check `start_new_game()` recreates `self.enemies` as a brand-new `Group()`, not just clears the old one incompletely.
- If pressing a key seems to "leak" between states (e.g., SPACE both starts the game AND immediately fires a bullet), verify your `handle_event` branches are mutually exclusive `if`/`elif` blocks on `self.state`, not independent `if` statements that could both fire in the same event.
- Use VS Code's debugger to set a breakpoint at the exact line `self.state = GameState.GAME_OVER` to confirm it's only reached when you expect (i.e., health truly reached 0).

## Check Your Understanding

- Why do we handle input differently depending on `self.state`?
- What could go wrong if `start_new_game()` reused the old `self.bullets` group instead of creating a new one?
- Why is `self.final_score` a separate attribute from `self.score`?

## Mini Quiz

1. What is a "state machine" in the context of this lesson?
   a) A physical arcade cabinet  b) A system that is always in exactly one of a defined set of states, with rules for transitioning between them  c) A type of `pygame.Rect`  d) A synonym for the game loop

2. Why is `start_new_game()` called both at first launch and every restart?
   a) It's not — it's only called once  b) To guarantee gameplay state (score, sprites, health) is freshly created every time, avoiding leftover-data bugs  c) It only resets the score  d) It initializes Pygame itself

3. Predict: if a player presses SPACE while `self.state == GameState.MENU`, what happens in the guided code?
   a) A bullet is fired  b) The game starts (transitions to PLAYING)  c) Nothing, SPACE only works during PLAYING  d) The game crashes

4. Why is `self.final_score` set before transitioning to `GameState.GAME_OVER`?
   a) It has no purpose  b) Because `self.score` gets reset to 0 the next time `start_new_game()` runs, so the game-over screen needs its own saved copy  c) `self.score` cannot be displayed directly  d) `final_score` is required by Pygame

5. What would happen if `update()` did NOT check `if self.state == GameState.PLAYING:` before updating enemies?
   a) Nothing, this check is optional  b) Enemies would keep moving/updating even during the menu or game-over screens  c) The game would run faster  d) Enemies would disappear

6. (Code reading) Which method is responsible for drawing the correct screen based on `self.state`?
   a) `handle_event`  b) `start_new_game`  c) `draw`  d) `update`

### Answer Key

1. b — always in exactly one defined state, with transition rules
2. b — guarantees fresh gameplay state every time, avoiding stale-data bugs
3. c — nothing; SPACE only fires bullets during PLAYING per the `elif` branching
4. b — `self.score` resets on the next `start_new_game()`, so game-over needs its own snapshot
5. b — enemies would keep updating regardless of state
6. c — `draw`

## Lesson Recap

You built a complete, production-shaped state machine: a `GameState` enum-like pattern, a `Game` class owning all mutable session data, per-state `update`/`draw` logic, state-aware input handling, and — most importantly — a correct, centralized `start_new_game()` reset routine that prevents the single most common bug in student game projects: leftover state leaking between playthroughs. Every project from here forward is built on top of exactly this structure.

## Homework / Practice Mission

Add a `GameState.INSTRUCTIONS` screen, reachable by pressing `I` from the menu, showing a fuller explanation of controls and scoring, with a "Press ESC to return to menu" prompt (ESC here should return to `MENU`, not quit the game — meaning you'll need to make ESC's meaning state-dependent too, similar to how SPACE already is).

## Portfolio Project Connection

The `GameState` pattern and the `Game` class's `start_new_game()`/`handle_event()`/`update()`/`draw()` structure become the backbone of `src/game.py` in **all three portfolio projects**. **Project 04 (Space Shooter)** adds a `WIN`-style "wave cleared" flow on top of this exact skeleton; **Project 05 (Maze Runner Timer)** adds `WIN` and `TIMEOUT` states using the same pattern; **Project 06 (Brick Breaker Clone)** adds `LEVEL_CLEAR` and `GAME_OVER` states identically structured to what you built today.
