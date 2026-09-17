# Lesson 03: Player Mechanics and Movement

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 3 of 8
**Duration:** ~60 minutes
**Difficulty:** Beginner–Intermediate
**Technology:** LocalScript, Humanoid, UserInputService, StarterPlayerScripts

## Mission Brief

Your Beacon calls out across the sector — now other cadets need a ship that actually flies well. Today you take control of player movement itself: walk speed, jump power, and a custom "Boost" ability triggered by pressing a key. This is your first LocalScript, running individually on every player's own machine.

## Learning Objectives

- Understand the difference between server (Script) and client (LocalScript) code and why movement feel is handled client-side.
- Access a player's `Character` and `Humanoid`.
- Use `UserInputService` to detect a key press.
- Modify `Humanoid.WalkSpeed` and `Humanoid.JumpPower` temporarily (a "boost").
- Understand `LocalPlayer` and the client/server boundary.

## What You'll Build

A `LocalScript` in `StarterPlayerScripts` that lets a player press **Left Shift** to sprint (temporarily increased WalkSpeed) with a short cooldown, and press **Space** normally for Roblox's built-in jump (confirming default movement still works).

## Prerequisites

Lessons 01–02 completed.

## Key Concepts

- **LocalScript** — Luau code that runs only on one player's client, not the server, and not other players' screens.
- **Humanoid** — the special Instance inside every character model that controls health, WalkSpeed, JumpPower, and animations.
- **Character** — the player's physical in-game body (a Model containing body parts + Humanoid + HumanoidRootPart).
- **LocalPlayer** — `game.Players.LocalPlayer`, only meaningful inside a LocalScript; it's *your own* Player object.
- **UserInputService** — the service for detecting keyboard/mouse/gamepad input.

## Concept Explanation

Every player's character is a `Model` (found in `Workspace` once spawned) containing parts like `Head`, `Torso`/`UpperTorso`, `HumanoidRootPart` (the invisible part everything else welds to and that determines position), and a `Humanoid` object. The `Humanoid` is the "brain" of movement: change `Humanoid.WalkSpeed` (default 16) to make a character move faster or slower, and `Humanoid.JumpPower` (default 50, or `JumpHeight` on newer Humanoid states) to change jump height.

Because movement *feel* needs to respond instantly with zero network delay, it is handled in a `LocalScript` running on the player's own device. The **client** (a player's computer) and the **server** (Roblox's machine hosting the game) are two separate programs talking over the network. A `LocalScript` only runs on the client; a `Script` only runs on the server. This split matters enormously for fairness (covered fully in Lesson 5), but today it matters because responsive controls must live on the client — there is no round-trip delay to the server for something like "did I press Shift."

`UserInputService` lets a LocalScript detect raw input:
```lua
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then return end -- ignore input the game UI already used (e.g. typing in a chat box)
    if input.KeyCode == Enum.KeyCode.LeftShift then
        -- do something
    end
end)
```
`gameProcessedEvent` is true when Roblox's own UI (like a chat textbox) already consumed the input — checking it first prevents your sprint key from firing while a player is typing.

`LocalPlayer` is how a LocalScript refers to "me":
```lua
local Players = game:GetService("Players")
local player = Players.LocalPlayer
```
To get the current character and its Humanoid, since the character can respawn (and might not exist yet when the script starts), we use:
```lua
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
```
`CharacterAdded:Wait()` pauses the script until a character exists (useful right at game start); `WaitForChild` pauses until a named child exists — both prevent "attempt to index nil" errors from scripts running before the character has fully loaded.

## Roblox Studio Concepts

- **StarterPlayer → StarterPlayerScripts**: the correct home for all client movement/control LocalScripts — Roblox automatically copies every LocalScript here into each player's `PlayerScripts` container when they join.
- **Explorer path for this lesson**: `StarterPlayer` has two important sub-folders: `StarterPlayerScripts` (for LocalScripts) and `StarterCharacterScripts` (rarely used in this course).
- Testing input-based LocalScripts requires **Play** or **Play Here** (not just "Run") since input, characters, and Humanoids only fully exist in Play mode.

## Step-by-Step Setup

1. Open your Lesson 02 place.
2. In the Explorer, expand `StarterPlayer`.
3. Right-click `StarterPlayerScripts` and choose **Insert Object**.
4. Type `LocalScript` in the search box and insert it.
5. Rename the new LocalScript to `MovementController`.
6. Double-click `MovementController` to open the code editor.

## Guided Coding

```lua
-- MovementController.client.lua
-- Runs on each player's own client. Adds a Left-Shift sprint boost with a cooldown.

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer

local DEFAULT_WALK_SPEED = 16
local BOOST_WALK_SPEED = 28
local BOOST_DURATION = 2 -- seconds the boost lasts
local BOOST_COOLDOWN = 5 -- seconds before it can be used again

local isBoosting = false
local isOnCooldown = false

local function getHumanoid()
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    return humanoid
end

local function activateBoost()
    if isBoosting or isOnCooldown then
        return -- already boosting or waiting on cooldown; do nothing
    end

    local humanoid = getHumanoid()
    isBoosting = true
    humanoid.WalkSpeed = BOOST_WALK_SPEED

    task.wait(BOOST_DURATION)

    -- Only reset if the humanoid still exists (player might have respawned)
    if humanoid and humanoid.Parent then
        humanoid.WalkSpeed = DEFAULT_WALK_SPEED
    end
    isBoosting = false

    isOnCooldown = true
    task.wait(BOOST_COOLDOWN)
    isOnCooldown = false
end

UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if gameProcessedEvent then
        return
    end

    if input.KeyCode == Enum.KeyCode.LeftShift then
        task.spawn(activateBoost)
    end
end)

-- Make sure WalkSpeed resets to default every time the character (re)spawns
player.CharacterAdded:Connect(function(character)
    local humanoid = character:WaitForChild("Humanoid")
    humanoid.WalkSpeed = DEFAULT_WALK_SPEED
    isBoosting = false
end)

print("MovementController loaded for " .. player.Name)
```

## Code Walkthrough

- `DEFAULT_WALK_SPEED`, `BOOST_WALK_SPEED`, etc. are written in ALL_CAPS by convention to signal "these are constants, don't change them at runtime" — Luau has no true `const` keyword, so this is a naming convention, not enforced by the language.
- `getHumanoid()` safely fetches the current Humanoid, waiting for the character/Humanoid to exist if needed — this pattern prevents "attempt to index nil with 'WalkSpeed'" errors.
- `isBoosting` and `isOnCooldown` are **guard flags** — booleans that prevent the function from running twice at once (e.g. mashing Shift wouldn't stack boosts).
- `task.spawn(activateBoost)` runs the boost logic in the background so `InputBegan` keeps listening for other keys immediately, instead of freezing input handling for 2+5 seconds.
- `player.CharacterAdded:Connect(...)` guarantees WalkSpeed is reset to normal every time the player's character respawns (e.g. after falling into a pit), so a boost mid-death doesn't "stick" incorrectly.

## Build Instructions

1. Click Play.
2. Check Output for `MovementController loaded for <YourName>`.
3. Walk normally with WASD — default speed.
4. Press Left Shift — you should visibly move faster for 2 seconds, then return to normal, then be unable to boost again for 5 seconds.
5. Try mashing Shift repeatedly during the boost or cooldown — nothing should stack or break.

## Student Mission

Change `BOOST_WALK_SPEED` to 40 and `BOOST_DURATION` to 3, then test how it feels compared to the default.

## Challenge

Add a second key (`Enum.KeyCode.Q`) that triggers a "Jump Boost" — temporarily setting `Humanoid.JumpPower` (or `Humanoid.JumpHeight` if using the newer Humanoid movement mode) higher for a few seconds, using the same guard-flag/cooldown pattern as sprint.

## Experiment / Extension

1. **Change a value:** Adjust `BOOST_COOLDOWN` from 5 to 1 and feel how differently the mechanic plays.
2. **Observe:** Temporarily remove the `if isBoosting or isOnCooldown then return end` guard and observe what happens when you mash Shift (WalkSpeed resets get overwritten mid-boost, causing glitchy behavior) — then put the guard back.
3. **Modify a mechanic:** Make the boost only work while the character's `Humanoid:GetState()` is `Enum.HumanoidStateType.Running` (not while jumping), using an `if humanoid:GetState() == Enum.HumanoidStateType.Running then` check.
4. **Combine:** Combine sprint with a screen effect — tween the player's `Camera.FieldOfView` up slightly during a boost (this previews Lesson 6/7 visual polish techniques).

## Common Mistakes

- Writing this in a `Script` instead of a `LocalScript` — `player.Character` behaves differently and `UserInputService.InputBegan` will not correspond to the right client's input on the server.
- Forgetting `player.CharacterAdded:Connect` — after death/respawn, a leftover boosted WalkSpeed can persist strangely, or the script may throw errors referencing an old, destroyed Humanoid.
- Not guarding with `isBoosting`/`isOnCooldown`, letting players spam the key to stack effects.
- Placing the LocalScript somewhere other than `StarterPlayerScripts` (e.g. directly in Workspace) — it will not run reliably for every player.

## Debugging Tips

- If nothing happens on Shift, confirm the script is a `LocalScript` (check the Explorer icon — it looks different from a `Script`) and is located inside `StarterPlayerScripts`.
- Use `print("boost pressed")` inside the `InputBegan` callback temporarily to confirm the key is even being detected before debugging the WalkSpeed logic.
- If WalkSpeed doesn't reset, check that `humanoid.Parent` still exists before setting properties on it (a destroyed Humanoid throws errors when you try to change it).

## Check Your Understanding

1. Why must movement-feel code run in a LocalScript instead of a Script?
2. What problem do `isBoosting` and `isOnCooldown` solve?
3. What does `player.CharacterAdded:Wait()` do and why is it needed?
4. What is `gameProcessedEvent` used for in `InputBegan`?

## Mini Quiz

1. (Conceptual) Where should client-only movement scripts live?
   a) ServerScriptService b) StarterPlayerScripts c) ServerStorage d) ReplicatedStorage
2. (Code-reading) What value does `humanoid.WalkSpeed` return to after the boost duration ends in this script?
   a) 0 b) 28 c) 16 d) It stays at 28 forever
3. (Prediction) If you remove the `if gameProcessedEvent then return end` line, what could happen while a player types in chat?
   a) Nothing changes b) Pressing Shift while typing a chat message could still trigger the boost c) The game crashes d) Chat stops working entirely
4. (Conceptual) What is `Humanoid` responsible for?
   a) UI rendering b) Character movement, health, and animation state c) Networking d) Lighting
5. (Code-reading) What happens if a player presses Left Shift during the 5-second cooldown?
   a) Boost activates again immediately b) `activateBoost` returns early and does nothing c) The game errors d) WalkSpeed doubles

### Answer Key
1. b — client responsiveness needs zero network round-trip; server code has network delay.
2. c — 16 (DEFAULT_WALK_SPEED).
3. b — pressing Shift while chatting could still trigger the boost since the guard is gone.
4. b — Humanoid controls movement, health, animation state.
5. b — the guard clause returns immediately, doing nothing.

## Lesson Recap

You wrote your first LocalScript, learned the client/server split conceptually, used `UserInputService` to detect a keypress, and safely modified `Humanoid.WalkSpeed` with guard flags and a respawn-safe reset via `CharacterAdded`.

## Homework / Practice Mission

Add a small `print()` (or later, a UI label from Lesson 4) showing the remaining cooldown time by counting down in a loop during `task.wait(BOOST_COOLDOWN)` (hint: use a loop of `task.wait(1)` five times with a decrementing counter instead of one big wait).

## Portfolio Project Connection

The `getHumanoid()` / guard-flag / `CharacterAdded` reset pattern you learned today reappears directly in **Project 2: Multiplayer Arena & Teams**, where `CameraController.client.lua` and the tag-cooldown logic use the identical structure to prevent spam-tagging, and character respawns correctly reset combat-related state.
