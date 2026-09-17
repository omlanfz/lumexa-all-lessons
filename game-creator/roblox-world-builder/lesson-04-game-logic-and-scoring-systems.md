# Lesson 04: Game Logic and Scoring Systems

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 4 of 8
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** ModuleScript, leaderstats, IntValue, Player Data

## Mission Brief

Every good mission needs a scoreboard. Today you build Lumexa's scoring system: a `ScoreManager` module that safely tracks each cadet's points, and `leaderstats` so scores show up automatically in Roblox's built-in in-game leaderboard. This is the foundation for every scoring feature in every project this course.

## Learning Objectives

- Understand and create `leaderstats` so scores appear in Roblox's default leaderboard UI.
- Write and require a `ModuleScript` to organize reusable server logic.
- Use `Players.PlayerAdded` and `Players.PlayerRemoving` to manage per-player data lifecycle.
- Award points server-side in response to gameplay events, safely (never trusting the client).
- Use `IntValue` Instances to store per-player numeric stats.

## What You'll Build

A `ScoreManager` ModuleScript that creates a `leaderstats` folder with a `Score` `IntValue` for every player who joins, plus an `AddScore` function other scripts can call. You will also add a checkpoint Part that awards 10 points the first time each player touches it, using the same touch-tracking technique from Lesson 2.

## Prerequisites

Lessons 01–03 completed.

## Key Concepts

- **leaderstats** — a `Folder` named exactly `leaderstats` inside a `Player` object; any `IntValue`/`NumberValue`/`StringValue` inside it automatically appears in Roblox's built-in leaderboard (the Tab-key player list).
- **ModuleScript** — an Instance that returns a Luau table (usually of functions) via `return`, loaded with `require()`, shareable between multiple scripts.
- **PlayerAdded / PlayerRemoving** — events on the `Players` service that fire when a player joins/leaves, the correct place to set up and clean up per-player data.
- **Server-authoritative** — the principle that the server, never the client, has the final say over anything that affects fairness (like score).

## Concept Explanation

So far every script has lived directly inside a `Script` or `LocalScript`. As logic grows, we want to **organize** reusable code into a `ModuleScript` — a script that doesn't run on its own, but instead returns a table when another script `require()`s it:

```lua
-- ModuleScript named "ScoreManager"
local ScoreManager = {}

function ScoreManager.addScore(player, amount)
    -- ... logic here
end

return ScoreManager
```
```lua
-- A regular Script elsewhere
local ScoreManager = require(game.ServerScriptService.Modules.ScoreManager)
ScoreManager.addScore(somePlayer, 10)
```
This keeps our `GameManager` scripts clean and lets multiple scripts (a checkpoint script, a combat script, an NPC reward script) all call the same trusted scoring logic instead of duplicating it.

**leaderstats** is a Roblox convention, not a special API — Roblox's engine watches for a `Folder` named exactly `"leaderstats"` directly inside a `Player` object, and automatically renders any value objects inside it (like `IntValue`) in the default player list. We build it like this, inside a function that runs every time a player joins:

```lua
local Players = game:GetService("Players")

local function onPlayerAdded(player)
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player

    local score = Instance.new("IntValue")
    score.Name = "Score"
    score.Value = 0
    score.Parent = leaderstats
end

Players.PlayerAdded:Connect(onPlayerAdded)
```
Because the server sets this up, and only server scripts ever change `score.Value`, players cannot edit their own score by cheating on the client — this is the essence of **server-authoritative** design: the client can *ask* for something (via a RemoteEvent, covered in Lesson 5) but never directly *set* a value that matters for fairness.

## Roblox Studio Concepts

- **ServerScriptService → Modules**: a `Folder` we create to hold our `ModuleScript`s, keeping ServerScriptService organized as the project grows.
- `require(path)` — the function used to load a ModuleScript's returned table; `path` is normally a full Explorer path like `game.ServerScriptService.Modules.ScoreManager`.
- Once a game is running, you can view live `leaderstats` values for yourself and (in Studio Team Create/multiple test clients) other players via the Tab key or the player list in the top-right of the Play window.

## Step-by-Step Setup

1. Open your Lesson 03 place.
2. In the Explorer, right-click `ServerScriptService` and choose **Insert Object** → `Folder`. Rename it `Modules`.
3. Right-click `Modules`, **Insert Object** → `ModuleScript`. Rename it `ScoreManager`.
4. Right-click `ServerScriptService` directly (not inside Modules), **Insert Object** → `Script`. Rename it `GameManager`.
5. In the Explorer, select the `RaisedPlatform` Part you built in Lesson 1. Right-click it, **Insert Object** → `Script`. Rename it `CheckpointHandler`.

## Guided Coding

**`ServerScriptService/Modules/ScoreManager.module.lua`:**
```lua
-- ScoreManager (ModuleScript)
-- Central, server-only authority for player scores. Nothing else may set Score directly.

local Players = game:GetService("Players")

local ScoreManager = {}

-- Called once per player when they join; builds their leaderstats folder.
function ScoreManager.setupPlayer(player)
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player

    local score = Instance.new("IntValue")
    score.Name = "Score"
    score.Value = 0
    score.Parent = leaderstats
end

-- Safely adds points to a player's score. This is the ONLY function allowed
-- to change Score.Value -- always call this instead of editing Score directly.
function ScoreManager.addScore(player, amount)
    if not player or not player.Parent then
        return -- player already left; do nothing
    end
    if type(amount) ~= "number" then
        warn("ScoreManager.addScore called with a non-number amount!")
        return
    end

    local leaderstats = player:FindFirstChild("leaderstats")
    if not leaderstats then
        return
    end

    local score = leaderstats:FindFirstChild("Score")
    if not score then
        return
    end

    score.Value = score.Value + amount
end

function ScoreManager.getScore(player)
    local leaderstats = player:FindFirstChild("leaderstats")
    if not leaderstats then
        return 0
    end
    local score = leaderstats:FindFirstChild("Score")
    return score and score.Value or 0
end

return ScoreManager
```

**`ServerScriptService/GameManager.server.lua`:**
```lua
-- GameManager (Script)
-- Sets up leaderstats for every player who joins or is already in the game.

local Players = game:GetService("Players")
local ScoreManager = require(game.ServerScriptService.Modules.ScoreManager)

Players.PlayerAdded:Connect(function(player)
    ScoreManager.setupPlayer(player)
    print(player.Name .. " joined -- leaderstats created.")
end)

-- Handle players already in the server when this script runs (e.g. Studio Play testing)
for _, player in ipairs(Players:GetPlayers()) do
    ScoreManager.setupPlayer(player)
end
```

**`Workspace/RaisedPlatform/CheckpointHandler.server.lua`:**
```lua
-- CheckpointHandler (Script)
-- Awards 10 points the first time each player reaches this checkpoint.

local Players = game:GetService("Players")
local ScoreManager = require(game.ServerScriptService.Modules.ScoreManager)

local checkpointPart = script.Parent
local POINTS_AWARDED = 10

local awardedPlayers = {}

local function onTouched(otherPart)
    local character = otherPart.Parent
    local player = Players:GetPlayerFromCharacter(character)

    if not player then
        return
    end
    if awardedPlayers[player.UserId] then
        return -- already scored this checkpoint
    end

    awardedPlayers[player.UserId] = true
    ScoreManager.addScore(player, POINTS_AWARDED)
    print(player.Name .. " reached the checkpoint! +" .. POINTS_AWARDED .. " points.")
end

checkpointPart.Touched:Connect(onTouched)
```

## Code Walkthrough

- `ScoreManager` is required by *both* `GameManager` and `CheckpointHandler` — this is the point of a ModuleScript: one source of truth, multiple callers.
- `ScoreManager.setupPlayer` and `ScoreManager.addScore` are written as `function ScoreManager.name(...)` — this is Luau's dot-notation for adding a function to a table (equivalent to `ScoreManager.name = function(...) ... end`).
- `player:FindFirstChild("leaderstats")` is safer than `player.leaderstats` because it returns `nil` instead of erroring if the folder doesn't exist yet (e.g. a race condition right at join time).
- The `for _, player in ipairs(Players:GetPlayers())` loop in `GameManager` handles the common Studio-testing case where a script starts running *after* players (in Studio Team Test / multiple clients) already exist, so nobody is missed.
- `awardedPlayers[player.UserId] = true` is the same "already touched" tracking pattern from Lesson 2, now driving real scoring instead of just a print statement.
- Score is *never* changed anywhere except inside `ScoreManager.addScore` — this single-entry-point design makes it much easier to guarantee fairness and to add validation/logging in one place later.

## Build Instructions

1. Save and click Play.
2. Confirm Output shows `<YourName> joined -- leaderstats created.`
3. Press Tab (or open the player list) to see the built-in leaderboard — you should see your name with a `Score` column at 0.
4. Walk to `RaisedPlatform` (up the Ramp) — Output should show `+10 points` and the leaderboard's Score should update to 10 live.
5. Touch it again (or linger) — confirm it does NOT add another 10 (already-awarded guard working).

## Student Mission

Add a second checkpoint: insert a `Script` named `CheckpointHandler` (with the same code, just change `POINTS_AWARDED`) into a *new* Part you place further along your course, worth 20 points.

## Challenge

Refactor so `CheckpointHandler` doesn't hardcode `POINTS_AWARDED` in the script, but instead reads it from an `IntValue` child of the checkpoint Part named `PointValue` (so a level designer could tune point values from the Properties panel without touching code).

## Experiment / Extension

1. **Change a value:** Change `POINTS_AWARDED` to 50 and observe the leaderboard.
2. **Observe:** Remove the `awardedPlayers[player.UserId]` guard entirely and observe the score climbing rapidly while standing on the checkpoint (then restore the guard).
3. **Modify a mechanic:** Make `ScoreManager.addScore` refuse negative amounts less than -100 (a safety clamp), demonstrating defensive coding.
4. **Combine:** Add a `Players.PlayerRemoving` connection in `GameManager` that prints a "farewell" message including the player's final score via `ScoreManager.getScore`.

## Common Mistakes

- Creating `leaderstats` as anything other than exactly the string `"leaderstats"` (case-sensitive) — Roblox's built-in leaderboard UI will not recognize it.
- Letting a LocalScript (client) attempt to set `Score.Value` directly — even though this can technically be scripted, it must never be trusted; only the server's `ScoreManager.addScore` should ever write scores. We enforce this simply by never writing client-side scoring code at all.
- Forgetting the per-player "already awarded" guard, allowing infinite point farming by standing still on a checkpoint.
- Requiring a ModuleScript with a typo'd path (e.g. `game.ServerScriptService.ScoreManager` instead of `game.ServerScriptService.Modules.ScoreManager`) — causes an immediate "attempt to call a nil value" or "infinite yield" error.

## Debugging Tips

- If `require()` hangs or errors, double check the exact Explorer path and that the ModuleScript's `Name` matches what you typed.
- If leaderstats don't show, confirm the Folder is literally named `leaderstats` and is parented directly to the `Player` object (not to the Character).
- Print `ScoreManager.getScore(player)` after every `addScore` call while debugging to confirm values are changing as expected.

## Check Your Understanding

1. Why must `leaderstats` be named exactly that?
2. What problem does putting all scoring logic inside one ModuleScript solve?
3. Why is it dangerous to let a LocalScript directly set a player's Score value?
4. What does `Players:GetPlayers()` return, and why do we loop over it in `GameManager`?

## Mini Quiz

1. (Conceptual) What must a Folder be named for Roblox's built-in leaderboard to display its contents?
   a) Stats b) leaderstats c) Scoreboard d) PlayerData
2. (Code-reading) In `ScoreManager.addScore`, what happens if `amount` is the string `"10"` instead of the number `10`?
   a) It still adds 10 b) `warn()` fires and nothing is added c) The game crashes d) It subtracts 10
3. (Prediction) If two checkpoints both require `ScoreManager` and both call `addScore`, do they share the same Score value per player?
   a) No, each keeps a separate score b) Yes, because leaderstats is per-player and Score is the single shared IntValue c) Only if they're the same script d) Only in Studio
4. (Conceptual) What is the safest place to change score-related state?
   a) Any LocalScript b) Directly on Score.Value from any script c) Only through ScoreManager.addScore on the server d) In StarterGui
5. (Code-reading) What does `FindFirstChild` return if the named child doesn't exist?
   a) An error b) nil c) 0 d) An empty table

### Answer Key
1. b — leaderstats (case-sensitive, exact).
2. b — type check fails, warn() fires, function returns early.
3. b — Score is one shared IntValue per player under leaderstats.
4. c — always route changes through ScoreManager.addScore.
5. b — nil.

## Lesson Recap

You built a real scoring system: a `ScoreManager` ModuleScript as the single source of truth, `leaderstats` for automatic UI, `PlayerAdded`/checkpoint-driven point awards, and per-player "already scored" guards — all server-authoritative so nothing can be cheated from the client.

## Homework / Practice Mission

Add a third checkpoint worth 30 points near the end of your course, and print each player's total score via `ScoreManager.getScore` every time they reach it.

## Portfolio Project Connection

`ScoreManager.module.lua` and the checkpoint-touch pattern you built today are used **verbatim in structure** by **Project 1: Obstacle Course & Scoring**'s `source/ServerScriptService/Modules/ScoreManager.module.lua` and its `Checkpoints` folder, and the same `leaderstats`/`Score` naming is reused by **Project 2: Multiplayer Arena & Teams** for team scoring.
