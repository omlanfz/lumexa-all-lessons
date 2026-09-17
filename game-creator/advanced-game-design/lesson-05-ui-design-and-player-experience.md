# Lesson 05 — UI Design and Player Experience

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 5 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Python 3 + Pygame

---

## 🚀 Mission Brief

Your game architecture is solid, your levels are designed — but right now, a first-time player dropped into your game would be lost: no title screen, no instructions, no clear health display. Today you join the **UX (User Experience) division** of Mission Control. You'll learn how professional games communicate information instantly through UI/HUD design, build a reusable button system for menus, and design a HUD that never distracts from the gameplay it's supporting.

## 🎯 Learning Objectives

1. Explain the difference between **UI** (menus, buttons) and **HUD** (in-game overlays like health/score).
2. Apply core UX principles: clarity, consistency, hierarchy, and feedback.
3. Build a reusable, class-based **Button** system for menu screens in Pygame.
4. Design and implement a clean HUD showing health, score, and objectives without cluttering the screen.
5. Integrate UI screens (title, pause, game over) into the state machine from Lesson 3.

## 🛠️ What You'll Build

A complete **UI/HUD module**: a `Button` class, a `Menu` screen builder, and a `HUD` renderer, wired into a small game using the `GameStateMachine` pattern from Lesson 3 — the exact module structure your capstone project's `ui.py` will use.

## 📋 Prerequisites

- Lesson 3 (state machine, entity architecture).
- Comfortable with Pygame's `font`, `Rect`, and mouse event handling.

## 🔑 Key Concepts

- **UI (User Interface)** — screens the player actively interacts with outside of moment-to-moment gameplay (menus, settings, pause screens).
- **HUD (Heads-Up Display)** — persistent on-screen information shown *during* gameplay (health bars, score, minimap).
- **Visual Hierarchy** — arranging size, color, and position so the most important information is noticed first.
- **Consistency** — using the same colors, fonts, and button styles everywhere so players learn your interface once.
- **Affordance** — a UI element's appearance suggesting how it can be used (a raised-looking rectangle "looks clickable").
- **Feedback (UI-specific)** — hover states, click animations, and sound cues that confirm an interaction was registered.

## 📖 Concept Explanation

### UI vs. HUD

**UI** covers screens the player *navigates*: title screens, settings menus, pause screens, game-over screens with a "restart" button. **HUD** covers information displayed *during* active gameplay without pausing it: a health bar, a score counter, an ammo count, a minimap. The distinction matters because they have different design goals — UI screens can be static and full-screen since the game is paused; HUD elements must be small, unobtrusive, and never block the player's view of the actual action they're trying to see.

### Visual Hierarchy

Not all information is equally important at a given moment. A dying player desperately needs to see their health bar; they don't need to see the current frame rate. Visual hierarchy uses **size** (bigger = more important), **color** (bright/warm colors draw the eye faster than muted ones), **position** (top-left and center are scanned first in Western reading patterns), and **contrast** (a light HUD element on a dark background reads instantly) to make sure the right information is noticed at the right time. A common technique: make critical warnings (e.g., "low health") flash or grow, since motion draws attention even in peripheral vision.

### Consistency and Affordance

If a "Start" button is a rounded blue rectangle with white text in your title screen, every other button in your game (Restart, Resume, Quit) should share that same visual language. This isn't just aesthetics — it teaches the player, once, how to recognize an interactive element anywhere in your game. **Affordance** is the related idea that an element's look should hint at its function: a rectangle with a subtle border and centered text "looks like a button" even before the player reads it, because of learned conventions from every app and game they've ever used.

### UI Feedback

Every UI interaction needs a visible response. When a player hovers over a button, it should visibly change (lighten, grow slightly, get an outline) *before* they even click, confirming "yes, this is clickable, and my mouse is on it." When they click, a brief flash or scale animation confirms the click registered — this matters most on slower systems where there might be a small delay before the next screen appears; without feedback, players click multiple times, confused about whether it worked.

## 💻 Guided Coding

We'll build a `Button` class, a simple `Menu` class, and a `HUD` renderer, all reusable across screens.

```python
"""
UI and HUD Lab
Lumexa Game Creator Path - Course 03, Lesson 05
"""
import pygame
import sys
from enum import Enum, auto

pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("UI and HUD Lab")
clock = pygame.time.Clock()
FPS = 60

# ---- Consistent color palette (used everywhere = consistency principle) ----
BG = (10, 12, 28)
PANEL = (24, 28, 54)
BUTTON = (46, 58, 110)
BUTTON_HOVER = (70, 90, 160)
ACCENT = (80, 220, 255)
WHITE = (235, 235, 240)
HEALTH_GREEN = (80, 220, 130)
HEALTH_RED = (230, 70, 70)

title_font = pygame.font.SysFont("consolas", 48, bold=True)
ui_font = pygame.font.SysFont("consolas", 26)
hud_font = pygame.font.SysFont("consolas", 22)


class Button:
    """A reusable, class-based button with hover feedback and a click callback."""

    def __init__(self, rect, text, on_click):
        self.rect = pygame.Rect(rect)
        self.text = text
        self.on_click = on_click
        self.hovered = False

    def update(self, mouse_pos):
        self.hovered = self.rect.collidepoint(mouse_pos)

    def handle_event(self, event):
        if event.type == pygame.MOUSEBUTTONDOWN and event.button == 1:
            if self.rect.collidepoint(event.pos):
                self.on_click()

    def draw(self, surface):
        color = BUTTON_HOVER if self.hovered else BUTTON
        pygame.draw.rect(surface, color, self.rect, border_radius=10)
        pygame.draw.rect(surface, ACCENT, self.rect, width=2, border_radius=10)
        label = ui_font.render(self.text, True, WHITE)
        surface.blit(
            label,
            (self.rect.centerx - label.get_width() // 2, self.rect.centery - label.get_height() // 2),
        )


class Menu:
    """A full-screen menu made of a title and a vertical stack of buttons."""

    def __init__(self, title, button_defs):
        """button_defs: list of (label, callback) tuples."""
        self.title = title
        self.buttons = []
        start_y = HEIGHT // 2 - 20
        for i, (label, callback) in enumerate(button_defs):
            rect = (WIDTH // 2 - 120, start_y + i * 70, 240, 50)
            self.buttons.append(Button(rect, label, callback))

    def handle_event(self, event):
        for button in self.buttons:
            button.handle_event(event)

    def update(self, mouse_pos):
        for button in self.buttons:
            button.update(mouse_pos)

    def draw(self, surface):
        surface.fill(BG)
        label = title_font.render(self.title, True, ACCENT)
        surface.blit(label, (WIDTH // 2 - label.get_width() // 2, 110))
        for button in self.buttons:
            button.draw(surface)


class HUD:
    """Persistent in-game overlay: health bar, score, and objective text.
    Designed to sit in the corners so it never blocks the play area's center.
    """

    def __init__(self):
        self.max_health = 5

    def draw(self, surface, health, score, objective_text):
        # --- Health bar (top-left, high visual priority - critical info) ---
        bar_width, bar_height = 160, 22
        x, y = 16, 16
        pygame.draw.rect(surface, PANEL, (x - 2, y - 2, bar_width + 4, bar_height + 4), border_radius=6)
        fill_ratio = max(0, health) / self.max_health
        fill_color = HEALTH_GREEN if fill_ratio > 0.4 else HEALTH_RED
        pygame.draw.rect(surface, fill_color, (x, y, int(bar_width * fill_ratio), bar_height), border_radius=4)
        hp_label = hud_font.render(f"HP {health}/{self.max_health}", True, WHITE)
        surface.blit(hp_label, (x + bar_width + 12, y - 2))

        # --- Score (top-right, secondary priority) ---
        score_label = hud_font.render(f"Score: {score}", True, WHITE)
        surface.blit(score_label, (WIDTH - score_label.get_width() - 16, 16))

        # --- Objective (bottom-center, guidance without blocking play area) ---
        obj_label = hud_font.render(objective_text, True, WHITE)
        surface.blit(obj_label, (WIDTH // 2 - obj_label.get_width() // 2, HEIGHT - 34))


class AppState(Enum):
    MENU = auto()
    PLAYING = auto()
    PAUSED = auto()
    GAME_OVER = auto()


class App:
    def __init__(self):
        self.state = AppState.MENU
        self.hud = HUD()
        self.health = 5
        self.score = 0
        self.main_menu = Menu("UI & HUD LAB", [
            ("Start Game", self.start_game),
            ("Quit", self.quit_game),
        ])
        self.pause_menu = Menu("PAUSED", [
            ("Resume", self.resume_game),
            ("Main Menu", self.go_to_menu),
        ])
        self.game_over_menu = Menu("GAME OVER", [
            ("Restart", self.start_game),
            ("Main Menu", self.go_to_menu),
        ])

    def start_game(self):
        self.health = 5
        self.score = 0
        self.state = AppState.PLAYING

    def resume_game(self):
        self.state = AppState.PLAYING

    def go_to_menu(self):
        self.state = AppState.MENU

    def quit_game(self):
        pygame.quit()
        sys.exit()

    def handle_event(self, event):
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_p and self.state == AppState.PLAYING:
                self.state = AppState.PAUSED
            elif event.key == pygame.K_SPACE and self.state == AppState.PLAYING:
                self.score += 10  # simulate scoring for the demo
            elif event.key == pygame.K_h and self.state == AppState.PLAYING:
                self.health -= 1  # simulate taking damage for the demo
                if self.health <= 0:
                    self.state = AppState.GAME_OVER

        if self.state == AppState.MENU:
            self.main_menu.handle_event(event)
        elif self.state == AppState.PAUSED:
            self.pause_menu.handle_event(event)
        elif self.state == AppState.GAME_OVER:
            self.game_over_menu.handle_event(event)

    def update(self, mouse_pos):
        if self.state == AppState.MENU:
            self.main_menu.update(mouse_pos)
        elif self.state == AppState.PAUSED:
            self.pause_menu.update(mouse_pos)
        elif self.state == AppState.GAME_OVER:
            self.game_over_menu.update(mouse_pos)

    def draw(self, surface):
        if self.state == AppState.MENU:
            self.main_menu.draw(surface)
        elif self.state == AppState.PLAYING:
            surface.fill(BG)
            self.hud.draw(surface, self.health, self.score, "Objective: SPACE to score, H to take damage, P to pause")
        elif self.state == AppState.PAUSED:
            surface.fill(BG)
            self.hud.draw(surface, self.health, self.score, "Paused")
            self.pause_menu.draw(surface)
        elif self.state == AppState.GAME_OVER:
            self.game_over_menu.draw(surface)


def main():
    app = App()
    running = True
    while running:
        clock.tick(FPS)
        mouse_pos = pygame.mouse.get_pos()
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            else:
                app.handle_event(event)
        app.update(mouse_pos)
        app.draw(screen)
        pygame.display.flip()
    pygame.quit()
    sys.exit()


if __name__ == "__main__":
    main()
```

## 🔍 Code Walkthrough

- **`Button`** encapsulates its own rect, hover state, and click callback — this exact class can be reused in every menu across your capstone game without modification.
- **`Menu`** takes a list of `(label, callback)` tuples and auto-lays-out buttons vertically — a small data-driven pattern (Lesson 2's philosophy applied to UI).
- **`HUD.draw()`** deliberately places elements in the **corners and bottom edge** — never the center — so the HUD never blocks the player's view of the actual gameplay area, a core UX rule.
- **Health bar color changes to red below 40%** — this is visual hierarchy in action: the HUD itself escalates urgency as danger increases, without any text warning.
- **`App`** wires the whole thing to the state-machine pattern from Lesson 3 — notice how similar this `AppState` enum and `self.state` pattern is to `GameStateMachine`.

## 🏗️ Build Instructions

1. Save as `ui_hud_lab.py`, run `python ui_hud_lab.py`.
2. Click "Start Game," press SPACE to add score, H to lose health, P to pause, and confirm the HUD/menus all behave correctly.

## 🧭 Student Mission

Add a **Settings menu** reachable from the main menu with a "Volume" slider-style control (a draggable button on a horizontal track, no audio needed — just visually functional) and a "Back" button that returns to the main menu, applying the same `Button`/`Menu` classes.

## 🌌 Challenge

Add a **smooth hover animation**: instead of an instant color swap, make `Button` track a `hover_progress` float (0 to 1) that eases up/down each frame (`hover_progress += (target - hover_progress) * 0.2`) and use it to interpolate the button's color and size — a common professional UI polish technique.

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Change the color palette, keeping every button/panel consistent, and confirm the game still communicates health/danger clearly.
- **Intermediate:** Add a toast-style temporary message (e.g., "+10 points!") that fades out over 1 second after each score event, using a timer similar to the invincibility timer from Lesson 3.
- **Advanced:** Extract `Button`, `Menu`, and `HUD` into a separate `ui.py` module and import them into a fresh `main.py` — practicing the modular file structure your capstone project needs.

## ⚠️ Common Mistakes

- **Centering HUD elements over the play area** — this blocks the player's view of exactly the area they need to watch most.
- **Inconsistent button styles** across different screens, forcing players to re-learn what's clickable on every new screen.
- **No hover/click feedback**, leaving players unsure if their click registered, leading to frustrated double-clicking.
- **Overloading the HUD** with too many numbers/icons at once — only show what the player needs *right now*.

## 🐞 Debugging Tips

- If buttons don't respond to clicks, confirm you're checking `event.type == pygame.MOUSEBUTTONDOWN` (not `MOUSEMOTION`) and comparing `event.pos`, not `pygame.mouse.get_pos()`, inside `handle_event`.
- If hover highlighting doesn't work, confirm `update(mouse_pos)` is called every frame before `draw()`.
- If text looks blurry or the wrong size, confirm you're using one consistent `pygame.font.SysFont` instance per font size rather than creating new Font objects every frame (which also hurts performance — a preview of Lesson 7!).

## ❓ Check Your Understanding

1. What's the difference between UI and HUD, with one example of each?
2. Name two ways visual hierarchy can be created without adding new information.
3. Why should critical HUD elements avoid the center of the screen?
4. What is "affordance" and how does our `Button` class create it?
5. Why is UI feedback (hover/click response) important even when the click "worked" anyway?

## 📝 Mini Quiz (Answer Key at End)

1. A pause menu is an example of:
   a) HUD
   b) UI
   c) A physics component
   d) A core loop

2. A health bar shown during active gameplay is an example of:
   a) UI
   b) HUD
   c) A state machine
   d) A Prefab

3. Making a health bar turn red at low health is an example of:
   a) Data-driven design
   b) Visual hierarchy / escalating urgency
   c) A finite state machine
   d) Object pooling

4. Why do professional games avoid placing critical HUD elements at the center of the screen?
   a) It looks unprofessional
   b) It blocks the player's view of the actual gameplay
   c) Pygame doesn't allow it
   d) Center text renders slower

5. Consistency in UI design means:
   a) Every screen should look completely different
   b) The same visual language (colors, button style) is used everywhere
   c) Buttons should have random colors each time
   d) Only one screen should have buttons

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

## 🔁 Lesson Recap

You learned the distinction between **UI** (menus you navigate) and **HUD** (persistent gameplay info), and applied UX principles — hierarchy, consistency, affordance, and feedback — to build a reusable `Button`, `Menu`, and `HUD` system wired into a state machine. This exact module is the direct ancestor of the `ui.py` file every capstone project will include.

## 🏠 Homework / Practice Mission

Design (on paper or in comments) the full UI/HUD flow for your own capstone game: list every screen (title, pause, win, lose) and every HUD element (health? score? inventory?) it needs, and sketch where each HUD element sits on-screen to avoid blocking gameplay. Bring this to Lesson 6 for playtesting.

## 🗂️ Portfolio Project Connection

Every capstone project (07, 08, 09) includes a dedicated UI/HUD module built on today's `Button`/`Menu`/`HUD` pattern. Project 09 in particular is graded on having a "polished title/UI/HUD" — today's lesson is the direct blueprint for that requirement.
