# Lesson 02: Scripting Basics in Lua

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 2 of 8
**Duration:** ~60 minutes
**Difficulty:** Beginner
**Technology:** Luau (Roblox's Lua dialect), Script, Output window

## Mission Brief

Mission Control has picked up your beacon signal from Lesson 1 — now it's time to bring your station to life with code. Today you write your first lines of Luau, the programming language every Lumexa spacecraft (and every Roblox game) runs on. You'll make the Beacon pulse, print messages to Mission Control's log (the Output window), and understand variables, functions, and events — the building blocks of everything you script from here on.

## Learning Objectives

- Insert and run a `Script` inside `Workspace` and read output in the Output window.
- Understand variables, data types (string, number, boolean), and comments in Luau.
- Use `game:GetService()` to access a service.
- Write and call functions, and understand parameters and return values.
- Connect to a basic event (`Touched`) and understand event-driven programming.

## What You'll Build

A `Script` that makes the `Beacon` Part from Lesson 1 slowly change color in a loop, prints a startup message to Output, and detects when a player touches it, printing their name.

## Prerequisites

Lesson 01 completed (Studio setup, Explorer/Properties familiarity, and the `StartPad`/`Ramp`/`RaisedPlatform`/`Beacon` structure built).

## Key Concepts

- **Script** — an Instance that runs Luau code on the server.
- **Variable** — a named container for a value (`local x = 5`).
- **Data types** — `number`, `string`, `boolean`, `nil`, `table`.
- **Function** — a reusable block of code, defined with `function ... end`.
- **Event** — something that happens (a Part being touched, a player joining) that code can "listen" for using `:Connect()`.
- **Output window** — where `print()` and warnings/errors appear.

## Concept Explanation

Luau code lives inside **Script** objects (server-side), **LocalScript** objects (client-side, covered later), and **ModuleScript** objects (reusable code libraries, covered later). Today we use a plain `Script`.

A **variable** stores a value under a name so you can reuse it:
```lua
local playerName = "Cadet Nova"
local score = 0
local isReady = true
```
`local` means the variable only exists within its current scope (the current script or block) — always prefer `local` over global variables for performance and to avoid naming collisions.

Roblox exposes its engine features through **services**, accessed via `game:GetService("ServiceName")`. For example:
```lua
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
```
This is the single most common line you will write in every script this course.

A **function** groups code you want to reuse:
```lua
local function greet(name)
    print("Hello, " .. name .. "!")
end
greet("Cadet Nova")
```
`name` is a **parameter** — a placeholder for whatever value gets passed in when the function is called. The `..` operator joins ("concatenates") strings together.

Roblox is **event-driven**: instead of scripts constantly checking "did something happen yet?", you **connect** a function to an event, and Roblox calls that function automatically when the event fires:
```lua
part.Touched:Connect(function(otherPart)
    print(otherPart.Name .. " touched the part!")
end)
```
`Touched` fires whenever any BasePart touches this Part. The function you connect receives the other Part as its argument. Note: `Touched` fires once per body part of a character that makes contact (e.g. a leg and a torso can both trigger it), so real games usually track "already touched" state per player — we do exactly this in Lesson 4's checkpoint system.

## Roblox Studio Concepts

- **Script vs LocalScript vs ModuleScript**: covered fully starting Lesson 3; today we only use `Script`.
- **Output window**: View tab → Output. Shows `print()` messages in white, `warn()` in orange, and errors in red with a line number you can click to jump to.
- **Explorer icons**: a `Script` shows a small document-with-gears icon; a disabled script (unchecked "Disabled" property or greyed out) will not run.

## Step-by-Step Setup

1. Open your Lesson 01 place in Roblox Studio.
2. In the Explorer, right-click `Beacon` (inside `Workspace`).
3. Choose **Insert Object**.
4. In the search box that appears, type `Script` and select the plain **Script** (not LocalScript, not ModuleScript).
5. A new `Script` appears nested inside `Beacon`. Rename it `BeaconController` (click it once in Explorer, press F2 or click the name to rename).
6. Double-click `BeaconController` to open it in the code editor tab.
7. Delete the default placeholder line (`print("Hello world!")`) if present.
8. Ensure the View tab → Output panel is visible so you can see printed messages.

## Guided Coding

Type the following complete script into `BeaconController`:

```lua
-- BeaconController.server.lua
-- Runs on the server. Makes the Beacon pulse colors and greets players who touch it.

local beaconPart = script.Parent -- the Beacon Part this script lives inside

print("Beacon online. Lumexa Mission Control standing by.")

-- A small table of colors to cycle through
local colorSequence = {
    Color3.fromRGB(255, 221, 0),   -- yellow
    Color3.fromRGB(0, 200, 255),   -- cyan
    Color3.fromRGB(255, 80, 200),  -- pink
}

local function pulseBeacon()
    local index = 1
    while true do
        beaconPart.Color = colorSequence[index]
        index = index + 1
        if index > #colorSequence then
            index = 1
        end
        task.wait(1) -- pause 1 second between color changes
    end
end

-- Track which players have already been greeted this session
local greetedPlayers = {}

local function onBeaconTouched(otherPart)
    local character = otherPart.Parent
    local player = game:GetService("Players"):GetPlayerFromCharacter(character)

    if player and not greetedPlayers[player.UserId] then
        greetedPlayers[player.UserId] = true
        print(player.Name .. " has reached the Beacon!")
    end
end

beaconPart.Touched:Connect(onBeaconTouched)

-- Start the pulsing loop as a background task so it doesn't block this script
task.spawn(pulseBeacon)
```

## Code Walkthrough

- `script.Parent` refers to the Instance this Script is nested under — since we inserted it inside `Beacon`, `script.Parent` **is** the `Beacon` Part.
- `Color3.fromRGB(r, g, b)` builds a color from 0–255 red/green/blue values — this is the standard way to set colors in code (the Properties panel color picker does the same thing behind the scenes).
- The `colorSequence` table is a Luau **array** — an ordered list, indexed starting at 1 (not 0, unlike many other languages).
- `#colorSequence` returns the length of the table (3 in this case).
- `while true do ... end` is an infinite loop; `task.wait(1)` pauses execution for 1 second each pass so the loop doesn't run thousands of times per second.
- `task.spawn(pulseBeacon)` starts `pulseBeacon` as a separate background task so the rest of the script (connecting `Touched`) still runs immediately, instead of getting stuck forever inside the `while true` loop.
- `Players:GetPlayerFromCharacter(character)` converts a character model (the thing that touched the beacon) into the `Player` object that owns it — critical because a Part's `Touched` event gives you *parts*, not players, directly.
- `greetedPlayers` is a table used as a **set**, keyed by `player.UserId` (a unique permanent number per Roblox account) so we only greet each player once, even though `Touched` can fire many times.

## Build Instructions

1. Click Play (or Play Here) to test.
2. Watch the Output window — you should immediately see `Beacon online. Lumexa Mission Control standing by.`
3. Walk your character up to and through the `Beacon` part.
4. Confirm your player name is printed exactly once, even if you linger touching it.
5. Watch the Beacon's color cycle yellow → cyan → pink every second, forever.

## Student Mission

Modify the `colorSequence` table to include a fourth color of your choice, and change the pulse speed from 1 second to 0.5 seconds using `task.wait(0.5)`.

## Challenge

Add a `print()` statement inside `onBeaconTouched` that prints a *different* message the second time a different player touches it, so you can tell multiple players apart in the Output window (hint: include `player.Name` in every print, not just the first).

## Experiment / Extension

1. **Change a value:** Change one RGB value in `colorSequence` and observe the new color in Play mode.
2. **Observe:** Change `task.wait(1)` to `task.wait(0.1)` and watch how much faster (and more chaotic) the pulse becomes.
3. **Modify a mechanic:** Make `greetedPlayers` reset (clear the table) every 10 seconds using another `task.spawn` loop, so players get re-greeted periodically.
4. **Combine:** Make the Beacon's `PointLight.Brightness` also pulse in sync with the color change.

## Common Mistakes

- Inserting a `LocalScript` instead of a `Script` by accident — a LocalScript placed in Workspace under a Part will not run at all on newer Roblox engine versions for server-owned content; always check the Explorer icon.
- Forgetting `task.spawn` and putting the `while true do` loop directly in the main script body — this blocks the rest of the script from ever running (the `Touched:Connect` line would never execute).
- Using `player.UserId` on something that isn't a `Player` object (forgetting to check `if player then` first) — causes a nil-indexing error.
- Off-by-one errors with `#colorSequence` when manually cycling an index (forgetting Luau arrays start at 1).

## Debugging Tips

- Always check Output first — red text tells you the exact line number of an error.
- If nothing prints at all, verify the Script's `Disabled` property is unchecked and that it is a `Script` (not LocalScript) inside a server-visible location.
- Use extra `print()` statements liberally while learning — e.g. `print("touched by:", otherPart.Name)` to see exactly what's happening.

## Check Your Understanding

1. What is the difference between a Script and a LocalScript?
2. Why do we use `task.spawn()` around our infinite loop?
3. What does `script.Parent` refer to in `BeaconController`?
4. Why do we track `greetedPlayers` in a table instead of just always printing?

## Mini Quiz

1. (Conceptual) Which keyword should you use for most variables in Luau?
   a) global b) local c) var d) let
2. (Code-reading) In `colorSequence[index]`, what does `index` represent?
   a) A color value b) A position in the array c) A player's name d) A Part
3. (Prediction) If you forget `task.spawn` and write the `while true do` loop directly at the top level of the script, what happens to the `Touched:Connect` line below it?
   a) It runs first b) It never runs because the loop never finishes c) It runs twice d) It causes a syntax error
4. (Conceptual) What does `game:GetService("Players")` return?
   a) A list of Parts b) The Players service, letting you find Player objects c) A ModuleScript d) The Workspace
5. (Code-reading) What does `#colorSequence` evaluate to given the script above?
   a) 1 b) 2 c) 3 d) 0

### Answer Key
1. b — Scripts run server-side; LocalScripts run client-side.
2. b — spawning it as a background task so it doesn't block the rest of the script.
3. b — never runs, because the infinite loop above it blocks execution forever.
4. b — it returns the Players service.
5. c — the table has 3 entries.

## Lesson Recap

You wrote your first real Luau script: variables, a color-cycling loop using `task.spawn` and `task.wait`, a `Touched` event connection, and a per-player tracking table using `UserId` as a key. You now understand event-driven scripting, which is the backbone of every interactive Roblox mechanic.

## Homework / Practice Mission

Add a `warn()` call (instead of `print()`) that fires the very first time *any* player touches the Beacon in a session, so you can see the difference between `print` (white) and `warn` (orange) in Output.

## Portfolio Project Connection

`onBeaconTouched`'s pattern — detect a touch, look up the `Player` from the character, and track per-player state in a table — is exactly the pattern used by `CheckpointManager.module.lua` in **Project 1: Obstacle Course & Scoring**, where each checkpoint Part uses this exact technique to award points only once per player per checkpoint.
