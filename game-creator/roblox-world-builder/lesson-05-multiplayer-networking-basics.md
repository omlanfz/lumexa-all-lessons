# Lesson 05: Multiplayer Networking Basics

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 5 of 8
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** RemoteEvent, ReplicatedStorage, FireServer/FireClient, server validation

## Mission Brief

Mission Control and every cadet ship need to talk to each other without anyone forging messages. Today you learn Roblox's real client-server communication tool: `RemoteEvent`. You'll build a UI button that asks the server to restart the race, and — critically — learn *why* the server must always double-check anything a client claims before acting on it.

## Learning Objectives

- Explain the client-server model and why RemoteEvents exist.
- Create a `RemoteEvent` in `ReplicatedStorage`.
- Fire a RemoteEvent from client to server (`FireServer`) and from server to client (`FireClient`/`FireAllClients`).
- Understand why the server must never trust client-supplied data at face value.
- Build a simple "Restart Race" request/response flow.

## What You'll Build

A `ReplicatedStorage/Remotes` folder containing a `RestartRace` RemoteEvent. A LocalScript fires it when a TextButton is clicked; a server Script listens, validates the request, resets the checkpoint-awarded state and each player's Score to 0, then fires back a confirmation RemoteEvent the client uses to print a message.

## Prerequisites

Lessons 01–04 completed (leaderstats/ScoreManager from Lesson 4 required).

## Key Concepts

- **Client** — each player's own computer, running LocalScripts and rendering the game they personally see.
- **Server** — Roblox's machine, the single authority for the shared game state, running Scripts.
- **RemoteEvent** — an Instance placed in a shared location (usually `ReplicatedStorage`) that lets client and server send messages to each other.
- **FireServer(...)** — called from a LocalScript to send a message to the server.
- **OnServerEvent:Connect(function(player, ...))** — how a server Script receives what a client fired; Roblox always automatically supplies the sending `player` as the first argument.
- **FireClient(player, ...)** / **FireAllClients(...)** — called from the server to send a message to one or all clients.
- **Server validation** — the server re-checking that a request actually makes sense/is allowed before acting on it, instead of blindly trusting the client.

## Concept Explanation

Every Roblox multiplayer game is really two separate programs: the **server** (one copy, the source of truth) and a **client** for every connected player (each running its own copy of LocalScripts and its own view of the world). They can't call each other's functions directly — the only sanctioned way for a client to ask the server to do something (or vice versa) is a `RemoteEvent`.

A `RemoteEvent` must live somewhere both sides can see — that's exactly what `ReplicatedStorage` is for (its whole purpose is being visible to server AND every client).

**Client → Server:**
```lua
-- LocalScript
local remote = game.ReplicatedStorage.Remotes.RestartRace
remote:FireServer() -- can also send extra arguments: remote:FireServer("reason")
```
```lua
-- Script (server)
local remote = game.ReplicatedStorage.Remotes.RestartRace
remote.OnServerEvent:Connect(function(player, ...)
    -- 'player' is ALWAYS automatically supplied by Roblox as arg 1 -- you cannot fake it
    print(player.Name .. " requested a restart")
end)
```
Roblox guarantees `player` is the real, verified `Player` who fired the event — a client cannot lie about who they are. This is why `OnServerEvent`'s first parameter is trustworthy even when nothing else is.

**Server → Client:**
```lua
-- Script (server) -- send to one player
remote:FireClient(somePlayer, "Race restarted!")

-- Script (server) -- send to everyone
remote:FireAllClients("Race restarted!")
```
```lua
-- LocalScript
remote.OnClientEvent:Connect(function(message)
    print("Server says: " .. message)
end)
```

The single most important networking lesson: **never trust the client.** A LocalScript runs on a player's own computer, and a sufficiently determined player can modify or fake what a LocalScript sends (there are cheat tools that let players fire arbitrary Remote arguments). So a server script must always **re-derive or re-check** anything that matters, rather than believing a number the client claims. For example, if a (bad) client-side script fired `remote:FireServer(9999)` claiming "give me 9999 points," a well-written server script must ignore that number entirely and instead decide the correct point value itself (as `CheckpointHandler` already does in Lesson 4 — it never lets the client say how many points to award).

## Roblox Studio Concepts

- **ReplicatedStorage → Remotes**: a `Folder` we create to hold every `RemoteEvent`, keeping them organized and easy to find from both client and server code.
- Inserting a `RemoteEvent`: right-click a Folder in Explorer → Insert Object → search `RemoteEvent`.
- `OnServerEvent` only exists on the server-received side; `OnClientEvent` only exists on the client-received side; `FireServer` only works from a LocalScript; `FireClient`/`FireAllClients` only work from a Script.

## Step-by-Step Setup

1. Open your Lesson 04 place.
2. In the Explorer, right-click `ReplicatedStorage`, **Insert Object** → `Folder`. Rename it `Remotes`.
3. Right-click `Remotes`, **Insert Object** → `RemoteEvent`. Rename it `RestartRace`.
4. Right-click `Remotes` again, **Insert Object** → `RemoteEvent`. Rename it `RaceRestarted` (server → client confirmation).
5. In the Explorer, right-click `StarterGui`, **Insert Object** → `ScreenGui`. Rename it `GameUI`.
6. Right-click `GameUI`, **Insert Object** → `TextButton`. Rename it `RestartButton`. In Properties, set `Text` to `Restart Race`, `Size` to `{0, 160}, {0, 50}`, and `Position` to `{0, 20}, {0, 20}`.
7. Right-click `GameUI`, **Insert Object** → `LocalScript`. Rename it `UIController`.
8. In the Explorer, right-click `ServerScriptService`, **Insert Object** → `Script`. Rename it `RestartHandler`.

## Guided Coding

**`ReplicatedStorage/Remotes/README.md`** (documentation only, not code):
See the project's own `Remotes/README.md` files in Part B for the full remote-naming reference used from Lesson 5 onward.

**`StarterGui/GameUI/UIController.client.lua`:**
```lua
-- UIController (LocalScript)
-- Wires the Restart Race button to the server and listens for the server's confirmation.

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local restartRaceRemote = remotes:WaitForChild("RestartRace")
local raceRestartedRemote = remotes:WaitForChild("RaceRestarted")

local screenGui = script.Parent
local restartButton = screenGui:WaitForChild("RestartButton")

restartButton.MouseButton1Click:Connect(function()
    print("Requesting race restart from server...")
    restartRaceRemote:FireServer()
end)

raceRestartedRemote.OnClientEvent:Connect(function(message)
    print("Server confirmed: " .. message)
end)
```

**`ServerScriptService/RestartHandler.server.lua`:**
```lua
-- RestartHandler (Script)
-- Server-authoritative restart: resets every player's Score to 0.
-- NOTE: we never trust anything the client sends -- we ignore any arguments
-- and decide entirely for ourselves what "restart" means.

local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")
local ScoreManager = require(game.ServerScriptService.Modules.ScoreManager)

local remotes = ReplicatedStorage:WaitForChild("Remotes")
local restartRaceRemote = remotes:WaitForChild("RestartRace")
local raceRestartedRemote = remotes:WaitForChild("RaceRestarted")

-- Simple cooldown so one player can't spam-restart the race for everyone.
local lastRestartTime = 0
local RESTART_COOLDOWN_SECONDS = 5

local function onRestartRequested(player)
    local now = os.clock()
    if now - lastRestartTime < RESTART_COOLDOWN_SECONDS then
        print(player.Name .. " tried to restart too soon -- ignored.")
        return -- validation: refuse a spammed request
    end
    lastRestartTime = now

    for _, p in ipairs(Players:GetPlayers()) do
        local leaderstats = p:FindFirstChild("leaderstats")
        if leaderstats then
            local score = leaderstats:FindFirstChild("Score")
            if score then
                score.Value = 0
            end
        end
    end

    print(player.Name .. " restarted the race for everyone.")
    raceRestartedRemote:FireAllClients("The race has been restarted by " .. player.Name .. "!")
end

restartRaceRemote.OnServerEvent:Connect(onRestartRequested)
```

## Code Walkthrough

- `WaitForChild` is used everywhere Remotes are accessed because a LocalScript can start running *before* ReplicatedStorage has fully replicated to the client — `WaitForChild` blocks safely until the object exists, instead of erroring.
- `restartButton.MouseButton1Click:Connect(...)` is the standard event for a TextButton left-click.
- Notice `onRestartRequested(player)` takes `player` as its only parameter, even though `FireServer()` was called with zero explicit arguments — Roblox always inserts the calling player as argument 1 automatically for `OnServerEvent`.
- `RESTART_COOLDOWN_SECONDS` and `lastRestartTime` demonstrate **server-side validation**: even a legitimate button click can be refused if it violates a game rule (spam protection), which the server decides regardless of what the client wants.
- `FireAllClients(message)` broadcasts to every connected player at once, appropriate for game-wide events like a restart.
- Resetting `score.Value = 0` directly here (inside `RestartHandler`, a server script) is acceptable because it's still fully server-side code — the rule from Lesson 4 was "never let the *client* set Score," not "only ScoreManager may ever touch Score." In the actual Project 1 code, this reset is further centralized into `ScoreManager.resetScore` for consistency — see the Portfolio Project Connection below.

## Build Instructions

1. Save and click Play.
2. Get some points via a checkpoint (from Lesson 4) so your Score isn't 0.
3. Click the `Restart Race` button (top-left of your screen).
4. Confirm Output shows the request, the cooldown check passing, and the confirmation message; confirm your leaderboard Score resets to 0.
5. Click the button again immediately — confirm Output shows "tried to restart too soon -- ignored."

## Student Mission

Change `RESTART_COOLDOWN_SECONDS` to 10 and verify the cooldown behavior still works as expected.

## Challenge

Add a `RemoteEvent` named `RequestScoreSync` that a client can fire to ask the server "what's my current score," with the server responding via `FireClient` with the real value read from `leaderstats` — demonstrating a request/response round trip that doesn't change any state, only reads it.

## Experiment / Extension

1. **Change a value:** Lower the cooldown to 1 second and observe how easy the restart becomes to spam (still blocked, just more frequently allowed).
2. **Observe:** Temporarily delete the cooldown check entirely and observe that the restart still works, but is now spammable — this is exactly the vulnerability we prevent in real games.
3. **Modify a mechanic:** Make `RestartHandler` require at least 2 players to be present before allowing a restart (a game-design rule enforced server-side).
4. **Combine:** Make the `RestartButton` visually disable itself (`restartButton.Active = false`, `restartButton.AutoButtonColor = false`) for a few seconds client-side after being clicked, purely as UX polish — while the server-side cooldown remains the real (authoritative) protection.

## Common Mistakes

- Calling `FireServer` from inside a `Script` (server) — `FireServer` only exists meaningfully from the client side; using Remotes backward causes confusing errors.
- Forgetting `OnServerEvent`'s first parameter is always `player` — writing `function(amount)` instead of `function(player, amount)` silently reads the wrong value.
- Trusting a client-sent number (e.g. `remote:FireServer(9999)`) as if it were true, instead of validating or ignoring it.
- Placing a RemoteEvent inside `ServerScriptService` or `ServerStorage` instead of `ReplicatedStorage` — the client would never be able to see or fire it.

## Debugging Tips

- If `FireServer` seems to do nothing, confirm the RemoteEvent's Explorer path matches exactly on both the LocalScript and Script (a typo'd folder/name is the #1 cause).
- Add `print()` statements immediately inside every `OnServerEvent`/`OnClientEvent` callback while debugging, to confirm the message is even arriving before debugging the logic beyond it.
- Remember Remotes have no built-in security — always ask "what happens if a hostile client sends garbage or spams this?" for every RemoteEvent you create.

## Check Your Understanding

1. Why can't a client just directly change a server value like Score?
2. What is guaranteed to be true about the `player` argument in `OnServerEvent`?
3. Why does the server enforce its own cooldown instead of trusting the client not to spam?
4. What's the difference between `FireClient` and `FireAllClients`?

## Mini Quiz

1. (Conceptual) Where should shared RemoteEvents be placed?
   a) ServerScriptService b) ReplicatedStorage c) StarterGui d) ServerStorage
2. (Code-reading) In `onRestartRequested(player)`, where does `player` come from if `FireServer()` was called with no arguments?
   a) It's nil b) Roblox automatically supplies the calling player c) The LocalScript must pass it manually d) It's read from leaderstats
3. (Prediction) If the cooldown check is removed, what could a malicious player do?
   a) Nothing changes b) Spam-restart the race repeatedly, disrupting other players c) Crash the server instantly d) Gain extra points
4. (Conceptual) What is the core rule of server-authoritative design?
   a) The client decides scores b) The server must validate and decide anything that affects fairness c) LocalScripts should hold all game logic d) RemoteEvents are optional
5. (Code-reading) Which function would the server use to tell every connected player the race restarted?
   a) FireServer b) OnServerEvent c) FireAllClients d) WaitForChild

### Answer Key
1. b/(explained) — because clients aren't trusted; only server scripts should change shared authoritative state.
2. b — Roblox automatically supplies the real sending player.
3. b — spamming a shared action.
4. b — server must validate/decide fairness-affecting outcomes.
5. c — FireAllClients.

## Lesson Recap

You built your first client-server communication: a `Remotes` folder in `ReplicatedStorage`, a `RestartRace` RemoteEvent fired from a UI button, server-side validation (a cooldown) that ignores what the client wants when it conflicts with game rules, and a confirmation broadcast via `FireAllClients`. You now understand why "never trust the client" is the single most important rule in multiplayer game security.

## Homework / Practice Mission

Add server-side validation to `RestartHandler` that also requires the requesting player to actually be in the server's player list (defensive check using `Players:GetPlayers()`), printing a warning if somehow a stale player reference is used.

## Portfolio Project Connection

The exact `Remotes` folder, `RestartRace`/`RaceRestarted` RemoteEvent pattern, and server-cooldown validation you built today are used directly in **Project 1: Obstacle Course & Scoring**'s `ReplicatedStorage/Remotes` (with `RestartRace` and `RaceFinished`), and the identical request/validate/broadcast pattern powers **Project 2: Multiplayer Arena & Teams**'s `TagPlayer` and `RestartRound` RemoteEvents, where server validation is even more critical because it directly determines combat outcomes.
