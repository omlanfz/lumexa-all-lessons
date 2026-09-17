# Lesson 01: Roblox Studio Setup and First Build

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 1 of 8
**Duration:** ~60 minutes
**Difficulty:** Beginner
**Technology:** Roblox Studio, Explorer, Properties, Parts, Workspace

## Mission Brief

Cadet, welcome to Lumexa Mission Control. Your first assignment as a Game Creator is to establish a base of operations — a small world you build with your own hands inside Roblox Studio. Before you can code planets, launch rockets, or run scoring systems, you need to know your ship's control panel cold: the Explorer, the Properties window, and the Workspace. Today you build your first physical structure and learn to navigate Studio like a pro.

## Learning Objectives

By the end of this lesson, students will be able to:
- Install/open Roblox Studio and create a new place from the Baseplate template.
- Identify and use the Explorer and Properties windows.
- Insert, name, move, resize, and color Parts in the Workspace.
- Understand the difference between Workspace, ServerScriptService, ReplicatedStorage, StarterPlayer, StarterGui, and ServerStorage at a conceptual level.
- Save a place file and use Play / Play Here to test it.

## What You'll Build

A simple three-part landing platform: a starting pad, a small raised platform reached by a ramp, and a floating "beacon" part with a bright color and a point light — the first physical piece of the obstacle course you will build across this course.

## Prerequisites

None — this is the first lesson. A computer capable of running Roblox Studio (Windows or Mac) with Roblox Studio installed and a Roblox account signed in.

## Key Concepts

- **Explorer** — the tree view of every object in your place (Workspace, services, scripts, Parts).
- **Properties** — the panel showing/editing the selected object's settings (Size, Position, Color, Name, Anchored, etc.).
- **Workspace** — the service that holds everything physically visible and simulated in the 3D world.
- **Part** — the basic 3D building block (a Block, Wedge, Cylinder, etc.).
- **Anchored** — whether a Part is fixed in place (true) or affected by gravity/physics (false).
- **Services** — top-level containers in the Explorer with special jobs (Workspace, ServerScriptService, ReplicatedStorage, StarterGui, StarterPlayer, ServerStorage, StarterPack).

## Concept Explanation

Every Roblox game lives inside a single **place file**. Studio shows you that place as a tree of objects in the **Explorer** panel (usually docked on the right). At the top of that tree are **services** — special built-in folders each with one job. `Workspace` holds everything physically present in the 3D world: parts, models, terrain, and (importantly) any code that needs to interact with the physical world at runtime often gets *placed* in Workspace too, though script *storage* is different (we'll get to that in Lesson 2).

Other services you'll use constantly across this course:
- **ServerScriptService** — holds `Script` objects that only ever run on the server. Nothing in here is ever sent to or visible by the client, which makes it the safe place for anything that must not be cheated (like scoring).
- **ReplicatedStorage** — a shared shelf both the server and every client can see and read from. This is where we put `RemoteEvent` objects and shared `ModuleScript`s.
- **StarterPlayer → StarterPlayerScripts** — holds `LocalScript` objects that get copied into each player's character/PlayerScripts at runtime — this is where client-side control code lives.
- **StarterGui** — holds `ScreenGui` interface objects that get copied to every player's screen when they join.
- **ServerStorage** — like Workspace but hidden from clients entirely; good for holding models before you spawn them.

Every object also has **Properties** — think of them as the dials and switches for that object. A `Part`'s Properties include `Size` (a Vector3: X, Y, Z in studs), `Position` (a Vector3 in world space), `Color` (or the newer `Color3` picker via BrickColor), `Anchored` (boolean), `CanCollide` (boolean), `Material`, `Transparency`, and `Name`.

## Roblox Studio Concepts

- **New vs. Open**: "New" starts a fresh place from a template; "Open from Roblox" opens a place you already own.
- **Baseplate template**: a single huge flat gray Part called `Baseplate` sitting in Workspace — the default "empty" world to build on.
- **View tab**: toggles which panels (Explorer, Properties, Output, Toolbox) are visible.
- **Home tab → Part**: inserts a new Part directly into Workspace at the camera's focus point.
- **Move / Scale / Rotate tools**: the three manipulation gizmos in the Home tab, each bound to a keyboard shortcut (1 = Select, 2 = Move gizmo shortcut in some layouts — we'll use the toolbar icons for clarity).
- **Play / Play Here**: Play spawns you as a player at the game's SpawnLocation (or a default spot); Play Here drops you exactly where your camera currently is, which is faster for quick tests.

## Step-by-Step Setup

1. Open Roblox Studio.
2. On the New tab of the start screen, click the **Baseplate** template thumbnail. Studio opens a new place with one giant gray Part in Workspace.
3. If the Explorer panel is not visible, click the **View** tab at the top, then click **Explorer** to toggle it on. Do the same for **Properties** if it is hidden.
4. In the Explorer, expand `Workspace` by clicking the small arrow/triangle next to its name. You should see `Baseplate` and a `SpawnLocation` (or `Camera`/`Terrain` depending on template version) listed inside it.
5. Click on `Baseplate` in the Explorer. Notice the Properties panel now shows its `Size`, `Position`, `Color`, `Anchored` (should be checked/true), and `Name`.
6. Go to the **Home** tab in the ribbon at the top of Studio.
7. Click the **Part** button (a cube icon). A new gray Part named `Part` appears in Workspace, usually near the center of the Baseplate.
8. In the Explorer, click on the new `Part` to select it, then look at Properties. Change its `Name` field to `StartPad`.
9. With `StartPad` still selected, find `Size` in Properties and set it to `12, 1, 12` (X, Y, Z) — a flat 12x12 platform.
10. Find `Position` in Properties and set it to `0, 1, 0` so it sits just above the Baseplate.
11. Find `Color` (or `BrickColor`) in Properties and choose a bright color, e.g. `Bright green`.
12. Confirm `Anchored` is checked (true) — an unanchored Part will fall through the Baseplate under gravity.
13. Insert a second Part the same way (Home tab → Part). Rename it `RaisedPlatform`.
14. Set `RaisedPlatform`'s `Size` to `10, 1, 10` and `Position` to `0, 6, 25` (further along the Z axis and higher up).
15. Insert a third Part, rename it `Ramp`. Change its shape: in Properties, find the `Shape` property (only on `Part` class, not `MeshPart`) and set it from `Block` to `Wedge` — Studio may require you to right-click the Part in Explorer and choose "Insert Object" if Shape isn't visible; alternatively use the Home tab's shape dropdown next to the Part button and pick **Wedge** directly when inserting.
16. Size the `Ramp` to roughly `10, 5, 15` and position it at `0, 3, 12` so its slanted face connects `StartPad` to `RaisedPlatform`. Rotate it if needed using the Rotate tool (Home tab) so the slope faces the right direction.
17. Insert a fourth Part named `Beacon`. Set its `Size` to `3, 3, 3`, `Shape` to `Ball`, `Position` to `0, 9, 25` (floating just above `RaisedPlatform`), `Anchored` to true, `CanCollide` to false (so players can walk through it), and `Color` to a bright yellow.
18. With `Beacon` selected, right-click it in the Explorer and choose **Insert Object**, then search for and insert a `PointLight`. Set the `PointLight`'s `Brightness` to `3` and `Range` to `16` so it glows.
19. Save your place: File menu → Save to Roblox As... (or Ctrl+S if already saved once), name it `Lumexa Lesson 01`.

## Guided Coding

This lesson has no scripting yet — Lesson 2 introduces your first `Script`. Today's "code" is entirely Properties values, which is intentional: every Luau script you write later will manipulate these exact same properties (`Position`, `Color`, `Size`, `Anchored`) programmatically, so understanding them by hand first makes the code make sense.

## Code Walkthrough

Not applicable this lesson — see Lesson 2 for the first Code Walkthrough.

## Build Instructions

Follow the Step-by-Step Setup above exactly. When finished, your Explorer under `Workspace` should read:
```
Workspace
 ├─ Baseplate
 ├─ SpawnLocation (from template)
 ├─ StartPad
 ├─ Ramp
 ├─ RaisedPlatform
 └─ Beacon
     └─ PointLight
```

## Student Mission

Click **Play Here** (View tab → or the small dropdown arrow next to the Play button) with your camera positioned near `StartPad`. Walk your character up the `Ramp` onto `RaisedPlatform` and stand next to the glowing `Beacon`. Take a screenshot (or just confirm out loud to your instructor) that you reached it.

## Challenge

Add a second ramp on the opposite side of `RaisedPlatform` so there are two ways up, and add one more small floating "Beacon"-style part elsewhere on the Baseplate, at a different color, that players can walk to.

## Experiment / Extension

1. **Change a value:** Change `Beacon`'s `Color` to red and re-run Play Here — observe the color change instantly.
2. **Observe:** Change `PointLight.Range` from 16 to 40 and notice how much further the glow reaches.
3. **Modify a mechanic:** Set `RaisedPlatform`'s `Anchored` to false and Play Here — watch it fall due to gravity. Set it back to true.
4. **Combine:** Raise `RaisedPlatform`'s height and lengthen `Ramp` to match, keeping the slope walkable.

## Common Mistakes

- Forgetting to set `Anchored` to true on a static Part — it falls off the map when you press Play.
- Confusing `Position` (world coordinates) with `Size` (dimensions) — swapping these fields places parts in strange spots.
- Rotating a Wedge incorrectly so the slope faces backward, making the ramp impossible to climb.
- Not saving before pressing Play — if Studio crashes mid-test, unsaved changes are lost.

## Debugging Tips

- If a Part is invisible after Play, check its `Position` — it may be buried inside the Baseplate or far off the map (huge/negative coordinates are common typos).
- If you can't walk up the Ramp, its slope may be too steep, or `CanCollide` might accidentally be off, causing you to fall through.
- Use the Output window (View tab → Output) throughout the course — it will show errors in red once we start scripting.

## Check Your Understanding

1. What is the difference between the Explorer and the Properties window?
2. Why must a static platform have `Anchored` set to true?
3. Which service holds everything physically simulated in the 3D world?
4. What is the difference between Play and Play Here?

## Mini Quiz

1. (Conceptual) What does the Explorer panel show you?
   a) Only scripts b) The full tree of every object in your place c) Only Parts d) Only Properties
2. (Conceptual) Which Property controls whether a Part is affected by gravity?
   a) CanCollide b) Anchored c) Transparency d) Material
3. (Code-reading/Properties-reading) If a Part's Position is `0, 1, 0` and another's is `0, 6, 25`, which one is higher off the ground?
   a) The first b) The second c) They're equal d) Cannot tell
4. (Prediction) If you set `CanCollide` to false on a Part shaped like a wall, what happens when a player walks toward it?
   a) They bounce off b) They walk straight through it c) The game crashes d) Nothing changes visually or physically
5. (Conceptual) Which service should hold `LocalScript`s meant to run on each player's client?
   a) ServerScriptService b) StarterPlayer → StarterPlayerScripts c) ServerStorage d) Workspace

### Answer Key
1. b — Explorer shows every object; Properties shows details of whichever is selected.
2. b — Anchored.
3. b — height is the Y value; 6 > 1.
4. b — CanCollide false lets players pass through with no physical collision.
5. b — StarterPlayerScripts is specifically for client-side LocalScripts.

## Lesson Recap

You opened Roblox Studio, learned to navigate the Explorer and Properties panels, and hand-built your first physical structure using Parts, Size, Position, Color, Anchored, and a PointLight. You now understand the roles of Workspace, ServerScriptService, ReplicatedStorage, StarterPlayer, StarterGui, and ServerStorage well enough to know where things belong as we start scripting next lesson.

## Homework / Practice Mission

At home, build a small 3-part structure of your own design (a bridge, a tower, a maze corner) using only Parts and Properties — no scripts yet. Practice naming things clearly and keeping everything Anchored unless you want it to fall.

## Portfolio Project Connection

The `StartPad`, `Ramp`, and platform Parts you built today are the direct ancestors of **Project 1: Obstacle Course & Scoring** — in that project, your `Workspace/Course` folder will contain a full sequence of checkpoints and obstacles built exactly the way you practiced today, and the `Beacon` Part previews the visual reward feedback (glowing, colored parts) used throughout Projects 1–3.
