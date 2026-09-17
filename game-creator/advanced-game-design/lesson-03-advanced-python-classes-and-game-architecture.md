# Lesson 03 — Advanced Python Classes and Game Architecture

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 3 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Python 3 + Pygame

---

## 🚀 Mission Brief

Your prototypes so far have worked, but the code is starting to sprawl — one giant `main()` function doing everything. Real Lumexa mission software (and real games) can't be built that way; when a spaceship has a hundred systems, engineers organize each into its own clean module. Today you become a **Software Architect**: you'll learn the class-based patterns professional game developers use — entity/component-style objects, state machines, and separation of concerns — so your capstone game stays manageable no matter how big it grows.

## 🎯 Learning Objectives

1. Explain **separation of concerns** and why large `main()` functions become unmaintainable.
2. Design entity classes using inheritance and composition ("entity/component-ish" patterns).
3. Implement a reusable **finite state machine (FSM)** class for game states (menu, playing, paused, game over).
4. Refactor a monolithic game loop into a clean, modular architecture.
5. Apply these patterns directly to their capstone project structure.

## 🛠️ What You'll Build

A refactored, class-based **mini game engine skeleton**: a `GameStateMachine`, an `Entity` base class with subclasses (`Player`, `Collectible`, `Hazard`), and a clean `Game` class that ties it all together — the architectural skeleton every capstone project (07, 08, 09) will follow.

## 📋 Prerequisites

- Lessons 1–2.
- Solid understanding of Python classes, `__init__`, inheritance, and methods.

## 🔑 Key Concepts

- **Separation of Concerns** — each class/module handles one responsibility only.
- **Entity** — any object that exists in the game world (player, enemy, item).
- **Component-ish Composition** — building entities from small reusable pieces of behavior instead of giant inheritance trees.
- **Finite State Machine (FSM)** — a system where the game is always in exactly one named state, with defined rules for transitioning between states.
- **Game Loop Architecture** — organizing `handle_input → update → draw` cleanly across many objects instead of one giant function.
- **Encapsulation** — hiding an object's internal details behind a clean interface (methods).

## 📖 Concept Explanation

### Why Architecture Matters

In Lessons 1–2, all our logic lived inside `main()`. That's fine for a 150-line prototype. But your capstone game will have a title screen, multiple levels, a HUD, sound, save state, and more — likely 800+ lines of code. Without structure, adding one feature risks breaking three others. **Separation of concerns** means each part of your code has exactly one job: `Player` handles the player, `Level` handles level data, `GameStateMachine` handles what screen you're on. When something breaks, you know exactly where to look.

### Entities and Component-ish Composition

An **entity** is anything that "exists" in your game world — the player, an enemy, a coin. The simplest approach is a class hierarchy: a base `Entity` class with shared behavior (position, rect, `update()`, `draw()`), and subclasses like `Player(Entity)` or `Hazard(Entity)` that override or add behavior. This works well for small-to-medium games (which is exactly what your capstone is).

Full "Entity-Component-System" (ECS) architecture — used in big commercial engines — takes this further: instead of inheritance, entities are just IDs, and behavior is attached as swappable **components** (a `HealthComponent`, a `MovementComponent`) that systems process independently. That's powerful for huge games with hundreds of entity types, but it adds real complexity. For a capstone project of this scope, we use a **lightweight, component-ish style**: inheritance for the entity family tree, but we still favor small, focused helper objects and methods (e.g., a separate `Inventory` object *owned by* the player, rather than cramming inventory logic directly into `Player`) wherever composition keeps things cleaner than inheritance. The rule of thumb: **"is-a" relationships (a Player is-an Entity) use inheritance; "has-a" relationships (a Player has-an Inventory) use composition.**

### Finite State Machines (FSM)

Every game is, at the top level, in exactly one **state** at a time: `MENU`, `PLAYING`, `PAUSED`, `GAME_OVER`, `WIN`. A finite state machine formalizes this: it tracks the current state, runs only that state's update/draw logic, and defines the *legal transitions* between states (you can go from `PLAYING` to `PAUSED`, but not directly from `MENU` to `WIN`). This eliminates messy boolean flags like `is_paused`, `is_game_over`, `is_in_menu` all fighting each other, and it is *the* architectural backbone of every project in this course — Project 09 in particular requires a full "menu → playing → win/lose → restart" state machine.

### Encapsulation in Practice

Encapsulation means an object manages its own internal data and exposes clean methods. Instead of external code reaching into `player.health -= 10` from six different places, we write `player.take_damage(10)` — a single method that can also trigger effects (invincibility frames, sounds, death checks) in one place. This is a small habit that prevents huge bugs later.

## 💻 Guided Coding

We refactor toward a clean architecture: a base `Entity`, subclasses, a reusable `GameStateMachine`, and a `Game` class.

```python
"""
Architecture Lab - Class-Based Game Skeleton
Lumexa Game Creator Path - Course 03, Lesson 03
"""
import pygame
import sys
from enum import Enum, auto

pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Architecture Lab")
clock = pygame.time.Clock()
FPS = 60
font = pygame.font.SysFont("consolas", 28)

WHITE = (235, 235, 240)
BG = (10, 12, 28)
PLAYER_COLOR = (80, 200, 255)
GEM_COLOR = (255, 210, 60)
HAZARD_COLOR = (230, 70, 70)


# ---------------------------------------------------------------------------
# STATE MACHINE
# ---------------------------------------------------------------------------
class GameState(Enum):
    MENU = auto()
    PLAYING = auto()
    PAUSED = auto()
    GAME_OVER = auto()
    WIN = auto()


class GameStateMachine:
    """Tracks the current game state and defines legal transitions."""

    ALLOWED_TRANSITIONS = {
        GameState.MENU: {GameState.PLAYING},
        GameState.PLAYING: {GameState.PAUSED, GameState.GAME_OVER, GameState.WIN},
        GameState.PAUSED: {GameState.PLAYING, GameState.MENU},
        GameState.GAME_OVER: {GameState.MENU, GameState.PLAYING},
        GameState.WIN: {GameState.MENU, GameState.PLAYING},
    }

    def __init__(self, initial_state=GameState.MENU):
        self.state = initial_state

    def change_state(self, new_state):
        if new_state in self.ALLOWED_TRANSITIONS.get(self.state, set()):
            self.state = new_state
            return True
        print(f"Illegal transition: {self.state} -> {new_state}")
        return False

    def is_in(self, *states):
        return self.state in states


# ---------------------------------------------------------------------------
# ENTITIES ("is-a" hierarchy via inheritance)
# ---------------------------------------------------------------------------
class Entity:
    """Base class for anything that exists in the game world."""

    def __init__(self, x, y, width, height, color):
        self.rect = pygame.Rect(x, y, width, height)
        self.color = color
        self.active = True  # inactive entities are removed by the game

    def update(self, dt):
        """Override in subclasses. dt = seconds since last frame."""
        pass

    def draw(self, surface):
        pygame.draw.rect(surface, self.color, self.rect, border_radius=4)


class Inventory:
    """A 'has-a' component owned by Player - composition, not inheritance."""

    def __init__(self):
        self.items = {}

    def add(self, item_name, amount=1):
        self.items[item_name] = self.items.get(item_name, 0) + amount

    def count(self, item_name):
        return self.items.get(item_name, 0)


class Player(Entity):
    """The player entity - 'is-a' Entity, 'has-a' Inventory (composition)."""

    def __init__(self, x, y):
        super().__init__(x, y, 32, 32, PLAYER_COLOR)
        self.speed = 240  # pixels per second
        self.health = 3
        self.inventory = Inventory()  # composition: Player HAS-A Inventory
        self.invincible_timer = 0.0

    def handle_input(self, keys, dt):
        dx = dy = 0
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            dx = -1
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            dx = 1
        if keys[pygame.K_UP] or keys[pygame.K_w]:
            dy = -1
        if keys[pygame.K_DOWN] or keys[pygame.K_s]:
            dy = 1
        self.rect.x += int(dx * self.speed * dt)
        self.rect.y += int(dy * self.speed * dt)
        self.rect.clamp_ip(screen.get_rect())

    def update(self, dt):
        if self.invincible_timer > 0:
            self.invincible_timer -= dt

    def take_damage(self, amount):
        """Encapsulation: outside code never touches self.health directly."""
        if self.invincible_timer > 0:
            return
        self.health -= amount
        self.invincible_timer = 1.0  # 1 second of invincibility after a hit

    def draw(self, surface):
        # Flicker while invincible - visible feedback for a hidden timer
        if self.invincible_timer <= 0 or int(self.invincible_timer * 10) % 2 == 0:
            super().draw(surface)


class Collectible(Entity):
    """A gem the player can pick up."""

    def __init__(self, x, y):
        super().__init__(x, y, 20, 20, GEM_COLOR)

    def draw(self, surface):
        pygame.draw.circle(surface, self.color, self.rect.center, 10)


class Hazard(Entity):
    """A hazard that damages the player on contact."""

    def __init__(self, x, y):
        super().__init__(x, y, 30, 30, HAZARD_COLOR)


# ---------------------------------------------------------------------------
# GAME CLASS - ties architecture together
# ---------------------------------------------------------------------------
class Game:
    def __init__(self):
        self.fsm = GameStateMachine()
        self.player = None
        self.entities = []
        self.score = 0

    def start_new_game(self):
        self.player = Player(WIDTH // 2, HEIGHT // 2)
        self.entities = [
            Collectible(150, 150), Collectible(600, 200), Collectible(400, 450),
            Hazard(300, 300), Hazard(500, 400),
        ]
        self.score = 0
        self.fsm.change_state(GameState.PLAYING)

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RETURN and self.fsm.is_in(GameState.MENU):
                self.start_new_game()
            elif event.key == pygame.K_p and self.fsm.is_in(GameState.PLAYING):
                self.fsm.change_state(GameState.PAUSED)
            elif event.key == pygame.K_p and self.fsm.is_in(GameState.PAUSED):
                self.fsm.change_state(GameState.PLAYING)
            elif event.key == pygame.K_r and self.fsm.is_in(GameState.GAME_OVER, GameState.WIN):
                self.fsm.change_state(GameState.MENU)

    def update(self, dt):
        if not self.fsm.is_in(GameState.PLAYING):
            return
        keys = pygame.key.get_pressed()
        self.player.handle_input(keys, dt)
        self.player.update(dt)

        for entity in self.entities[:]:
            entity.update(dt)
            if not entity.rect.colliderect(self.player.rect):
                continue
            if isinstance(entity, Collectible):
                self.score += 10
                self.player.inventory.add("gem")
                self.entities.remove(entity)
            elif isinstance(entity, Hazard):
                self.player.take_damage(1)

        if self.player.health <= 0:
            self.fsm.change_state(GameState.GAME_OVER)
        elif not any(isinstance(e, Collectible) for e in self.entities):
            self.fsm.change_state(GameState.WIN)

    def draw(self, surface):
        surface.fill(BG)
        if self.fsm.is_in(GameState.MENU):
            self._draw_center_text(surface, "ARCHITECTURE LAB", "Press ENTER to start")
        elif self.fsm.is_in(GameState.PLAYING, GameState.PAUSED):
            for entity in self.entities:
                entity.draw(surface)
            self.player.draw(surface)
            hud = font.render(
                f"Score: {self.score}  HP: {self.player.health}  Gems: {self.player.inventory.count('gem')}",
                True, WHITE,
            )
            surface.blit(hud, (10, 10))
            if self.fsm.is_in(GameState.PAUSED):
                self._draw_center_text(surface, "PAUSED", "Press P to resume")
        elif self.fsm.is_in(GameState.GAME_OVER):
            self._draw_center_text(surface, "GAME OVER", "Press R to return to menu")
        elif self.fsm.is_in(GameState.WIN):
            self._draw_center_text(surface, "YOU WIN!", "Press R to return to menu")

    def _draw_center_text(self, surface, big_text, small_text):
        big = font.render(big_text, True, WHITE)
        small = font.render(small_text, True, WHITE)
        surface.blit(big, (WIDTH // 2 - big.get_width() // 2, HEIGHT // 2 - 30))
        surface.blit(small, (WIDTH // 2 - small.get_width() // 2, HEIGHT // 2 + 10))


def main():
    game = Game()
    running = True
    while running:
        dt = clock.tick(FPS) / 1000.0
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            else:
                game.handle_event(event)
        game.update(dt)
        game.draw(screen)
        pygame.display.flip()
    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## 🔍 Code Walkthrough

- **`GameStateMachine`** stores allowed transitions as a dictionary of sets — attempting an illegal transition (e.g., `MENU` → `WIN`) is rejected and logged, preventing bugs where the game ends up in an impossible state.
- **`Entity` → `Player`/`Collectible`/`Hazard`** demonstrates classic inheritance: shared `rect`/`color`/`update`/`draw` live in the base class; each subclass only overrides what's different.
- **`Inventory`** is deliberately *not* a subclass of `Entity` — it's a composed object (`self.inventory = Inventory()`), demonstrating "has-a" vs. "is-a."
- **`take_damage()`** encapsulates all damage logic (including invincibility frames) so nothing outside `Player` ever manipulates `self.health` directly.
- **`Game.update()`** uses `isinstance()` checks to decide how to react to collisions — a simple, readable way to dispatch behavior per entity type without a full ECS.
- **`dt` (delta time)** is passed to `update()` methods so movement speed is defined in pixels-per-second, independent of frame rate — a critical practice for consistent gameplay across different machines.

## 🏗️ Build Instructions

1. Save as `architecture_lab.py` and run `python architecture_lab.py`.
2. Press ENTER at the menu, move with WASD/arrows, collect gems, avoid hazards, try P to pause.

## 🧭 Student Mission

Add a new `Enemy(Entity)` subclass that moves back and forth automatically (using a `direction` attribute flipped when it hits screen edges) and also damages the player on contact. Confirm your state machine and collision logic require no changes to support it — that's the payoff of good architecture.

## 🌌 Challenge

Refactor `Game` so that `PAUSED` freezes the invincibility timer too (currently `dt` still passed as 0 while paused would naturally do this — verify by testing, and explain in a comment why passing `dt=0` during pause achieves a full freeze without special-casing every entity).

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Add a 4th `GameState` — `SETTINGS` — reachable from `MENU`, with a placeholder screen.
- **Intermediate:** Give `Player` a `to_dict()` method that returns a plain dictionary of its saveable state (position, health, inventory) — the foundation of the save system in Project 07.
- **Advanced:** Split `Game.update()`'s collision-handling block into a separate `CollisionSystem` class/function that takes `player` and `entities` as arguments — practicing further separation of concerns.

## ⚠️ Common Mistakes

- **Multiple boolean flags instead of a state machine** (`is_paused = True`, `is_game_over = True` simultaneously) — this leads to contradictory states. Always prefer one authoritative `state` variable.
- **Reaching into an object's internals from outside** (e.g., `game.player.health = 999` from a debug menu) instead of calling a method — breaks encapsulation and skips invincibility/side-effect logic.
- **Over-using inheritance for "has-a" relationships** — e.g., making `Inventory` inherit from `Entity` just because "everything is an Entity." Only use inheritance for true "is-a" relationships.

## 🐞 Debugging Tips

- If a state transition silently fails, check the console — illegal transitions print a message rather than crashing, by design.
- If `isinstance()` checks aren't matching, confirm you're checking against the imported class, not a re-defined duplicate class in another file.
- If movement feels frame-rate-dependent, confirm you multiplied by `dt`, not just `speed` alone.

## ❓ Check Your Understanding

1. What is "separation of concerns" and why does it matter as a project grows?
2. Give one example of "is-a" and one example of "has-a" from today's code.
3. Why does the state machine reject illegal transitions instead of just allowing any state change?
4. What problem does `take_damage()` solve that direct attribute access wouldn't?
5. Why do we multiply movement by `dt` instead of a fixed number of pixels per frame?

## 📝 Mini Quiz (Answer Key at End)

1. `Player(Entity)` is an example of:
   a) Composition
   b) Inheritance ("is-a")
   c) A finite state machine
   d) Data-driven design

2. `self.inventory = Inventory()` inside `Player.__init__` is an example of:
   a) Inheritance
   b) Composition ("has-a")
   c) Encapsulation violation
   d) A state transition

3. The purpose of a finite state machine in a game is to:
   a) Make code run faster
   b) Ensure the game is always in exactly one well-defined state with legal transitions
   c) Replace all classes with functions
   d) Store level data

4. Encapsulating damage logic inside `take_damage()` instead of `player.health -= 10` prevents:
   a) The game from running
   b) Bypassing side effects like invincibility frames
   c) The player from moving
   d) Levels from loading

5. Multiplying movement by `dt` (delta time) ensures:
   a) The game looks better
   b) Movement speed stays consistent regardless of frame rate
   c) Collisions are more accurate
   d) The state machine works correctly

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

## 🔁 Lesson Recap

You leveled up from writing one big function to writing a real **game architecture**: an `Entity` inheritance hierarchy, composed objects like `Inventory`, and a **finite state machine** governing menu/playing/paused/game-over/win. You practiced encapsulation by hiding damage logic behind a method, and delta-time movement for frame-rate independence. This is the architectural backbone every remaining lesson and all three capstone projects will build on.

## 🏠 Homework / Practice Mission

Sketch (in comments or a short markdown file) the class diagram for your own capstone game idea: what `Entity` subclasses will you need? What states will your `GameStateMachine` have? What will be composition vs. inheritance? Bring this sketch to Lesson 5 when we design UI/HUD around this architecture.

## 🗂️ Portfolio Project Connection

Every project folder in this course (07, 08, 09) uses this exact architecture: a `src/` package with an `Entity` base class and subclasses in `entities/`, a state-machine module, a level-data module, and a `Game` class in `main.py` that ties them together. Project 09 in particular is graded partly on how cleanly it applies today's separation-of-concerns principles.
