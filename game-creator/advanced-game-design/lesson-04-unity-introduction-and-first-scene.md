# Lesson 04 — Unity Introduction and First Scene

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 4 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Unity Editor + C# (MonoBehaviour)

---

## 🚀 Mission Brief

Cadet, every professional game studio in the galaxy uses a **game engine** — software that handles rendering, physics, and input so developers focus on gameplay. You've been building your own mini-engine in Python/Pygame, which is fantastic for learning the fundamentals. Today's mission is different: a field trip to see how the pros do it, using **Unity**, one of the most widely-used professional game engines in the real world, used for everything from indie darlings to AAA console titles. You will learn real Unity vocabulary and workflow and walk through building your first scene and script, step by step, exactly as you would on your own computer.

> **Important, straight talk:** This lesson's environment (the one running your Python lessons) cannot open or run the Unity Editor — Unity is a large, separate desktop application, and this course's tools cannot install, launch, or export a Unity project here. Everything below is accurate, real, and current Unity Editor knowledge that you follow on **your own installed copy of Unity** (Unity Hub + Unity Editor, both free to download for personal/educational use). Because of this, your **graded capstone portfolio project** (Project 09) is built entirely in Pygame instead — the same engine you've been using all course — so that every student, regardless of what's installed on their machine, has a real, complete, runnable game to submit and be proud of. Today is about *understanding* Unity, not about submitting a Unity project.

## 🎯 Learning Objectives

1. Explain what a game engine is and why professional studios use Unity, Unreal, Godot, etc.
2. Identify the core parts of the Unity Editor: Scene view, Game view, Hierarchy, Inspector, Project window.
3. Describe GameObjects, Components, Prefabs, Rigidbody, and Colliders and how they relate.
4. Understand the MonoBehaviour lifecycle: `Start()` and `Update()`.
5. Write and correctly attach a working C# player-movement script using `Rigidbody` physics.
6. Explain how the concepts learned (core loop, level design, architecture) carry over from Pygame into Unity.

## 🛠️ What You'll Build (on your own machine)

A brand-new Unity 3D project containing one **Scene** with a ground plane, a player Cube with a `Rigidbody`, and a custom C# script (`PlayerMovement.cs`) that lets you drive the cube around using the keyboard — your first real Unity gameplay, built with your own hands in the real Editor.

## 📋 Prerequisites

- Lessons 1–3 (core loop, level design, OOP architecture) — the concepts transfer directly.
- A computer that can install Unity Hub and Unity Editor (Windows, macOS, or Linux) — **not required to complete this lesson's understanding, but required to actually build the scene yourself**.
- Basic familiarity with C#-like syntax is helpful but not required; we introduce it here.

## 🔑 Key Concepts

- **Game Engine** — software providing rendering, physics, input, and audio systems so developers don't build them from scratch.
- **Scene** — a single "level" or screen in Unity, containing all the GameObjects for that moment of the game.
- **GameObject** — the base "thing" in Unity; everything in a scene (player, camera, light, ground) is a GameObject.
- **Component** — a piece of functionality attached to a GameObject (Transform, Rigidbody, Collider, a custom script).
- **MonoBehaviour** — the base C# class your custom scripts inherit from to become attachable Components.
- **Rigidbody / Collider** — physics components that give a GameObject mass, gravity, and collision response.
- **Prefab** — a reusable, saved template of a GameObject (and its components) you can stamp out many times.
- **Inspector / Hierarchy / Project window** — the Editor's core panels for editing, organizing, and browsing assets.

## 📖 Concept Explanation

### What Is a Game Engine, Really?

In Pygame, you personally wrote the game loop, drew every shape with `pygame.draw`, and manually checked `rect.colliderect()` for collisions. A **game engine** like Unity provides all of that (and much more — 3D rendering, realistic physics, lighting, animation, audio mixing, and cross-platform export to PC, mobile, console, and web) as a ready-made toolkit with a visual editor on top. This lets developers spend their time on *game-specific* logic instead of re-inventing rendering and physics every time. The tradeoff: Unity is a much bigger, heavier piece of software than the Python scripts you've written, with its own vocabulary and workflow — which is exactly what this lesson introduces.

### The Unity Editor Layout

When you open Unity, you'll see several key panels:
- **Scene view** — a 3D (or 2D) workspace where you visually place and arrange GameObjects, like an artist's canvas.
- **Game view** — shows exactly what the player's camera sees when you press Play — this is the "real" running game.
- **Hierarchy** — a list of every GameObject currently in the open Scene, shown as a tree (parents and children).
- **Inspector** — when you click a GameObject, the Inspector shows every Component attached to it and lets you edit their values (position, rotation, scale, script variables, etc.).
- **Project window** — your file browser for the whole project's assets: scripts, prefabs, materials, textures, scenes.

### GameObjects and Components

Everything that exists inside a Unity Scene — the player, the ground, a light, the camera — is a **GameObject**. On its own, a GameObject is just an empty container with one guaranteed Component: a **Transform** (position, rotation, scale). Everything else — how it looks, how it behaves, how it physically collides — is added by attaching more **Components**. This is Unity's version of the "composition over inheritance" idea from Lesson 3: instead of writing a `Player` class that inherits a giant tree of behavior, you build a Player GameObject by *combining* small components: a `Mesh Renderer` (how it looks), a `Rigidbody` (physics), a `Box Collider` (its physical shape), and your own custom script (its unique behavior). This is exactly the "has-a" composition thinking from Lesson 3, just expressed through Unity's Editor instead of Python code.

### Rigidbody, Collider, and Physics

Adding a **Rigidbody** component to a GameObject tells Unity's physics engine "this object should be affected by gravity and forces, and should move realistically." Adding a **Collider** component (e.g., Box Collider, Sphere Collider) defines the object's physical shape for collision detection — without a Collider, objects pass through each other even with a Rigidbody attached. Together, Rigidbody + Collider is how a ball rolls, bounces, and stops when it hits a wall in Unity — all handled by the engine's physics simulation, not by code you write yourself (compare this to Project 08 in Pygame, where you manually coded gravity and collision response — Unity gives you this for free, but with less low-level control).

### MonoBehaviour: Start() and Update()

Custom gameplay logic in Unity is written in **C#** scripts that inherit from `MonoBehaviour` — the base class that lets Unity treat your script as an attachable Component. Two special methods form the heartbeat of almost every script:
- **`Start()`** runs exactly once, right before the first frame the object is active — used for one-time setup (getting references to other components, initializing variables).
- **`Update()`** runs once every single frame (just like the `while running:` loop body in Pygame) — used for input handling, movement, and per-frame logic.

This should feel familiar: it's the same "setup once, then loop every frame" pattern you've used in every Pygame project this whole course — Unity just wires it up through these two method names instead of a manual `while` loop.

### Prefabs

A **Prefab** is a saved, reusable template of a GameObject and all its components — think of it as a blueprint. If you build one enemy GameObject with all its components configured, you can save it as a Prefab and then instantiate (spawn) as many copies as you want, in code or by dragging it into scenes, and editing the Prefab updates every instance at once. This is Unity's equivalent of a Python class you can instantiate many times — the same "many objects from one blueprint" idea from Lesson 3's `Entity` classes.

## 💻 Guided Walkthrough — Building Your First Scene (do this in Unity on your own machine)

**Step 1 — Install and open Unity.**
Download **Unity Hub** from unity.com, sign in with a (free) Unity ID, and install the latest **LTS (Long Term Support)** version of the Unity Editor through the Hub. In Unity Hub, click **New Project**, choose the **3D (Core)** template, name it `LumexaFirstScene`, and click **Create project**.

**Step 2 — Explore the Editor.**
Once the Editor opens, locate: the **Hierarchy** (top-left, currently showing `Main Camera` and `Directional Light`), the **Scene view** (center, your 3D workspace), the **Game view** tab (next to Scene view — what the camera sees), the **Inspector** (right side, empty until you select something), and the **Project window** (bottom, your file assets).

**Step 3 — Create the ground.**
In the Hierarchy, right-click → **3D Object → Plane**. Rename it `Ground` (double-click its name in the Hierarchy). In the Inspector, set its **Transform → Position** to `(0, 0, 0)` and **Scale** to `(2, 1, 2)` to make it larger.

**Step 4 — Create the player.**
Right-click in the Hierarchy → **3D Object → Cube**. Rename it `Player`. Set its **Position** to `(0, 1, 0)` so it starts just above the ground.

**Step 5 — Add physics components.**
With `Player` selected, in the Inspector click **Add Component**, type `Rigidbody`, and add it. A Cube already has a Box Collider by default (visible in the Inspector) — confirm it's there; if not, add a **Box Collider** component too. Press the **Play** button at the top of the Editor — you should see the Cube fall (via gravity, automatically) and land on the Ground plane, stopped by its Collider. Press Play again to stop.

**Step 6 — Create your first script.**
In the Project window, right-click → **Create → C# Script**, name it `PlayerMovement`. Double-click it to open it in your code editor (Visual Studio or VS Code, whichever Unity is configured to use), and replace its contents with the script below.

**Step 7 — Attach the script.**
Drag `PlayerMovement.cs` from the Project window onto the `Player` GameObject in the Hierarchy (or select `Player` and use **Add Component → PlayerMovement**). Press **Play** and use the arrow keys / WASD to move the Cube.

### Complete C# Script — `PlayerMovement.cs`

```csharp
using UnityEngine;

// Every Unity gameplay script inherits from MonoBehaviour, which is what
// lets Unity treat this class as an attachable Component in the Inspector.
public class PlayerMovement : MonoBehaviour
{
    [Header("Movement Settings")]
    public float moveSpeed = 5f;      // units per second, editable in the Inspector
    public float jumpForce = 6f;      // upward force applied when jumping

    [Header("Ground Check")]
    public float groundCheckDistance = 0.6f;

    private Rigidbody rb;             // cached reference to this object's Rigidbody
    private bool isGrounded;

    // Start() runs once, before the first frame - used for one-time setup.
    void Start()
    {
        rb = GetComponent<Rigidbody>();
        if (rb == null)
        {
            Debug.LogError("PlayerMovement requires a Rigidbody component on this GameObject.");
        }
    }

    // Update() runs once per frame - used for input reading and per-frame logic.
    void Update()
    {
        // Read raw input axes (configured in Unity's Input Manager by default:
        // Horizontal = A/D or Left/Right, Vertical = W/S or Up/Down).
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");

        Vector3 moveDirection = new Vector3(horizontal, 0f, vertical).normalized;
        Vector3 targetVelocity = moveDirection * moveSpeed;

        // Preserve the current vertical velocity (gravity/jumping) while
        // overwriting horizontal movement - this is standard for physics-based
        // character movement so gravity is not fought against every frame.
        rb.linearVelocity = new Vector3(targetVelocity.x, rb.linearVelocity.y, targetVelocity.z);

        // Simple grounded check using a downward raycast from the player's center.
        isGrounded = Physics.Raycast(transform.position, Vector3.down, groundCheckDistance);

        if (isGrounded && Input.GetKeyDown(KeyCode.Space))
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
        }
    }
}
```

> **Note on Unity API versions:** in recent Unity versions, `Rigidbody.velocity` was renamed to `Rigidbody.linearVelocity`. If your installed Unity version is older and shows a compile error on `linearVelocity`, use `rb.velocity` instead — the Console window (Window → General → Console) will always tell you the exact error and line if something doesn't compile, which is your primary debugging tool in Unity, equivalent to reading a Python traceback.

**Step 8 — Tune it like a designer.** In the Inspector, with `Player` selected, you'll see `Move Speed` and `Jump Force` fields (because they're `public` fields) — try changing them *while the game is running* in Play mode and watch the values update live. This is one of Unity's biggest advantages over a plain script: instant, visual tuning, exactly the kind of balance experimentation you did with numbers in Lesson 1.

**Step 9 — Turn the player into a Prefab.** Drag the `Player` GameObject from the Hierarchy into the Project window to create a Prefab. Now you have a reusable player blueprint you could place in multiple Scenes/levels.

## 🏗️ Build Instructions (recap)

1. Install Unity Hub + latest LTS Editor.
2. New 3D project → add a Plane (`Ground`) and a Cube (`Player`).
3. Add `Rigidbody` (and confirm `Box Collider`) to `Player`.
4. Create and attach `PlayerMovement.cs` as shown above.
5. Press Play, move with WASD/arrows, jump with Space.
6. Save the Prefab.

## 🧭 Student Mission

On your own installed Unity, add a second Cube named `Obstacle` with a `Box Collider` (no Rigidbody needed for a static obstacle) somewhere on the Ground, and confirm your Player cube physically collides with and stops against it — direct proof that Colliders work without any code from you.

## 🌌 Challenge

Extend `PlayerMovement.cs` with a `Camera` reference (drag the Main Camera into a new public field) and, in `Update()`, use `Vector3.Lerp` to smoothly move the camera to follow the player's position every frame — a simple, real third-person camera-follow script.

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Change `moveSpeed` and `jumpForce` values in the Inspector and describe how the game "feels" different, connecting back to Lesson 1's balance concepts.
- **Intermediate:** Add a `Rigidbody`-based collectible Sphere with a script using `OnTriggerEnter` (with its Collider's "Is Trigger" checked) that destroys itself and prints a message when the Player touches it.
- **Advanced:** Research Unity's **Prefab Variants** and explain, in your own words, how they'd let you make multiple enemy types that share most behavior but override specific values — connecting to Lesson 3's inheritance ideas.

## ⚠️ Common Mistakes

- **Forgetting a Collider** — a Rigidbody with no Collider will still fall through the floor if the floor also lacks a Collider, or pass through everything if it itself has none.
- **Putting movement code in `Start()` instead of `Update()`** — `Start()` only runs once, so nothing will move continuously.
- **Directly setting `transform.position` for a physics object** instead of using `Rigidbody` forces/velocity — this bypasses physics and can cause jittery, unrealistic collisions.
- **Not checking the Console window** when something doesn't work — compile errors and runtime exceptions both appear there, just like a Python traceback.

## 🐞 Debugging Tips

- The **Console** (Window → General → Console) is your best friend — read the exact error message and double-click it to jump to the offending line, just like a Python stack trace.
- If nothing moves, confirm the script is actually attached (visible in the Player's Inspector) and that `rb` isn't `null` (the `Debug.LogError` in `Start()` will warn you).
- If the player flies away wildly, check for duplicate scripts adding force every frame, or a `jumpForce`/`moveSpeed` value that's far too large.

## ❓ Check Your Understanding

1. What is the difference between the Scene view and the Game view?
2. What two components does an object need to realistically fall and collide?
3. What do `Start()` and `Update()` each do, and how do they map to concepts you already know from Pygame?
4. What is a Prefab, and how is it similar to a Python class?
5. Why can't this lesson's environment run Unity itself, and what do you do instead to complete this lesson?

## 📝 Mini Quiz (Answer Key at End)

1. Which Unity panel shows exactly what the player's camera sees when the game runs?
   a) Hierarchy
   b) Inspector
   c) Game view
   d) Project window

2. A script that can be attached to a GameObject as a Component must inherit from:
   a) GameObject
   b) MonoBehaviour
   c) Rigidbody
   d) Transform

3. Which method runs once per frame in a Unity script?
   a) Start()
   b) Awake()
   c) Update()
   d) Create()

4. What two components together let an object fall under gravity AND physically collide with the ground?
   a) Transform + Camera
   b) Rigidbody + Collider
   c) Light + Material
   d) Prefab + Script

5. A Prefab is best described as:
   a) A rendering setting
   b) A reusable, saved GameObject template/blueprint
   c) A type of Collider
   d) A sound file format

**Answer Key:** 1-c, 2-b, 3-c, 4-b, 5-b

## 🔁 Lesson Recap

Today you took a field trip into professional game engine territory. You learned Unity's core vocabulary — Scene, GameObject, Component, Rigidbody, Collider, MonoBehaviour, Prefab — and walked through building and scripting your very first real Unity scene with actual physics and a working C# movement script. You saw how the ideas from Lessons 1–3 (composition, the update loop, balance/tuning) show up again inside Unity's own workflow. Because this course's environment can't run the Unity Editor, your graded capstone (Project 09) stays in Pygame — but the engine concepts you learned today transfer to any engine you use in the future.

## 🏠 Homework / Practice Mission

On your own machine (if you have Unity installed), extend today's scene: add a second platform Cube floating above the ground, and modify `PlayerMovement.cs` so double-tapping Space performs a small double-jump (hint: track a `jumpsRemaining` counter, reset it in your grounded check). If you don't have Unity installed, instead write a half-page comparison: list three things that felt similar between Unity and Pygame's architecture, and three things that felt genuinely different.

## 🗂️ Portfolio Project Connection

While Project 09 is delivered fully in Pygame (per this course's environment constraints), the architectural thinking is identical: GameObjects/Components in Unity map directly to the `Entity` classes and composed objects you built in Lesson 3, and Unity's Inspector-tunable public fields map directly to the constants you'll tune in `settings.py` across all three capstone projects. If you continue building games after this course on your own machine, Unity is a natural next engine to build in using everything you've learned here.
