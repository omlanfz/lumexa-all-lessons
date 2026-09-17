# Lesson 07: Testing, Debugging, and Polishing

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 7 of 8
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Output window, pcall, multiple test clients, print/warn/error, defensive coding

## Mission Brief

Before Mission Control certifies a ship for launch, every system gets stress-tested. Today you become Lumexa's Quality Assurance officer: you'll learn to read errors properly, use `pcall` to catch problems safely, test with multiple simulated players at once, and hunt down (and fix) common bugs in your own obstacle course.

## Learning Objectives

- Read and interpret Output window messages (print, warn, error) including line numbers and stack traces.
- Use `pcall` to safely call code that might fail, without crashing the whole script.
- Use Studio's multiple-client testing tools to test multiplayer behavior locally.
- Identify and fix common bugs: nil indexing, race conditions, duplicate connections, and unguarded events.
- Apply a basic testing checklist before considering a feature "done."

## What You'll Build

No new gameplay feature — this lesson is a full audit-and-fix pass on everything built in Lessons 1–6, plus a small `pcall`-wrapped defensive utility function you'll reuse project-wide, and a first real multi-client test session.

## Prerequisites

Lessons 01–06 completed.

## Key Concepts

- **Output window** — shows `print()` (white), `warn()` (orange), and runtime `error()` (red, with a stack trace and clickable line number).
- **pcall** — "protected call": runs a function and catches any error it throws, returning `true, result` on success or `false, errorMessage` on failure, instead of crashing the whole script.
- **Race condition** — a bug caused by two things happening in an unexpected order (e.g. a script reading a value before another script has set it up).
- **Multiple test clients** — Studio's ability to simulate several players joining the same local test server at once, essential for testing anything multiplayer.
- **Defensive coding** — writing code that checks its assumptions (does this object exist? is this the right type?) instead of assuming everything will always go right.

## Concept Explanation

Every script you've written has assumed things will go smoothly — but real games have race conditions, disconnects, and edge cases. `pcall` lets you attempt something risky (like calling a function that *might* error) without crashing your whole script if it fails:
```lua
local success, result = pcall(function()
    return riskyFunctionThatMightError()
end)

if success then
    print("It worked:", result)
else
    warn("It failed:", result) -- result here is the error message
end
```
This is especially useful around anything involving external data, timing-sensitive object lookups, or code you don't fully control (e.g. a third-party module, or an operation on an object that might have just been destroyed by another script).

A very common real bug is the **race condition**: for example, if `CheckpointHandler` runs before `GameManager` has finished creating `leaderstats` for a just-joined player, `ScoreManager.addScore` could silently fail to find the `leaderstats` folder. Our Lesson 4 code already defends against this with `FindFirstChild` checks that return early instead of erroring — that *is* defensive coding in action.

Another common bug is **duplicate connections**: if a script accidentally connects the same event twice (e.g. because a piece of setup code runs more than once), your effects (like `playFeedback()`) fire twice per touch. The fix is either ensuring setup code only runs once, or explicitly disconnecting old connections before making new ones.

Studio supports **multiple test clients** specifically so you can test multiplayer logic (like our RemoteEvents and per-player scoring) without publishing your game or convincing real friends to join. This is essential — testing multiplayer code alone (Play Here) will never reveal problems that only appear with two or more real, distinct Player objects (like our restart cooldown, or team assignment in Project 2).

## Roblox Studio Concepts

- **Test tab → multiple clients**: in Studio's ribbon, click the **Test** tab, and use the dropdown near "Players" (or "Start") to select 2+ (e.g. "2 Players") before clicking **Start**. This launches one Server window and 2+ Client windows simultaneously, each acting as a separate connected player.
- **Output window filters**: Output can be filtered by Server/Client and by message type (Info/Warning/Error) using the small dropdown filters at the top of the Output panel — invaluable once multiple clients are running at once and printing overlapping messages.
- **Ctrl+Z / Undo history**: Studio keeps deep undo history in Edit mode — use it liberally while experimenting.

## Step-by-Step Setup

1. Open your Lesson 06 place.
2. Click the **Test** tab in the ribbon.
3. Find the **Players** dropdown (sometimes labeled next to the Start button) and set it to `2 Players` (or higher, up to Studio's local limit, typically 8).
4. Click **Start**. Studio opens one Server window and two Client windows (you can arrange them side-by-side).
5. Also open the **Output** window (View tab → Output) — note it can be filtered per-window (Server/Client 1/Client 2) using its top dropdown.

## Guided Coding

Add this small reusable defensive-utility ModuleScript, useful across every project going forward:

**`ServerScriptService/Modules/SafeCall.module.lua`:**
```lua
-- SafeCall (ModuleScript)
-- A tiny reusable helper: runs a function with pcall and logs failures with warn(),
-- instead of every script re-writing the same pcall boilerplate.

local SafeCall = {}

-- Usage: SafeCall.run(function() ... end, "label for debugging")
function SafeCall.run(fn, label)
    local success, errorMessage = pcall(fn)
    if not success then
        warn(("[SafeCall] '%s' failed: %s"):format(label or "unnamed", tostring(errorMessage)))
    end
    return success
end

return SafeCall
```

Now use it inside `CheckpointHandler.server.lua` around the feedback call, since visual effects touching Instances that might have just been destroyed (e.g. a player leaving mid-tween) are a realistic source of rare errors:

```lua
-- Inside CheckpointHandler.server.lua, replace the direct playFeedback() call with:
local SafeCall = require(game.ServerScriptService.Modules.SafeCall)

-- ... inside onTouched, after ScoreManager.addScore(player, POINTS_AWARDED):
SafeCall.run(function()
    playFeedback()
end, "CheckpointHandler.playFeedback")
```

## Code Walkthrough

- `pcall(fn)` returns two values: a boolean success flag, and either the function's return value (on success) or the error message string (on failure) — this is why `local success, errorMessage = pcall(fn)` captures both.
- `("...%s..."):format(a, b)` is Luau's string-formatting method call syntax, equivalent to `string.format("...%s...", a, b)` — `%s` inserts a string version of the argument.
- Wrapping `playFeedback()` in `SafeCall.run` means that even if, say, the `CheckpointDing` sound was somehow deleted by another script, the *scoring* still succeeds and only the feedback portion logs a warning — a good example of **failing gracefully**: one broken visual effect should never block the core game logic (awarding points) from working.
- This module is intentionally tiny and general — you will reuse `SafeCall` in Projects 1–3 anywhere a "nice to have" side effect (sound, tween, particle) shouldn't be allowed to break core gameplay if something unexpected happens.

## Build Instructions

1. With 2 test clients running (from Step-by-Step Setup), walk each client's character to the checkpoint separately.
2. Confirm each client sees only their own leaderboard Score change appropriately, and both see the same particle/sound feedback.
3. In the Server Output window, confirm you see both players' "reached the checkpoint" messages and no unexpected warnings.
4. Click the Restart Race button from *one* client, then immediately click it from the *other* client — confirm the second click is correctly ignored by the cooldown (Lesson 5's `RestartHandler`), and check Output for the "too soon" message.
5. Stop the test session using the **Stop** button (red square) in the ribbon.

## Student Mission

Intentionally break something: temporarily rename `CheckpointDing` to `CheckpointDingX` in Explorer, re-run Play, and confirm your `SafeCall`-wrapped code degrades gracefully (still scores, just no sound, likely a warning in Output) rather than halting the whole script. Then rename it back.

## Challenge

Add a `pcall`-wrapped `require()` for `ScoreManager` inside `CheckpointHandler`, so that if the module ever has a typo/error, the checkpoint script reports a clear warning instead of the whole Script silently failing to load with a confusing error at the top of Output.

## Experiment / Extension

1. **Change a value:** Set the multi-client test count to 4 and observe how many separate windows/Output streams you now manage.
2. **Observe:** Deliberately introduce a bug — remove the `awardedPlayers[player.UserId]` guard in `CheckpointHandler` — and watch, with 2 clients, how one client standing still can rack up unlimited points while the other correctly gets only one award. Then fix it back.
3. **Modify a mechanic:** Add a `warn()` inside `ScoreManager.addScore` any time `leaderstats` isn't found, to make that race condition visible instead of silent.
4. **Combine:** Wrap the entire `onRestartRequested` body from Lesson 5 in `SafeCall.run`, so a bug in restart logic can never crash the whole `RestartHandler` script for all players.

## Common Mistakes

- Testing multiplayer features (Remotes, teams, scoring across players) using only Play Here/Play with one player — many bugs only appear with 2+ real Player objects.
- Ignoring orange `warn()` text because "it's not a real error" — warnings are often early signs of a race condition or missing object that will become a real bug later.
- Using `pcall` to silently swallow *every* error everywhere, hiding real bugs instead of fixing them — `pcall` should be used deliberately around specific risky operations, with a `warn()` so you still see what happened, not as a blanket "ignore all problems" tool.
- Forgetting to click **Stop** before editing scripts — edits made while a local test server is still running can behave unpredictably.

## Debugging Tips

- Read errors bottom-to-top in a stack trace; the very first line usually names the actual failure (e.g. "attempt to index nil with 'Value'"), and clicking it jumps straight to the offending line.
- If a bug "only happens sometimes," suspect a race condition — something is running in an assumed order that isn't guaranteed (e.g. code assuming `leaderstats` already exists).
- Use Output's Server/Client filter dropdown to isolate which of your 2+ test clients printed a given message when debugging multiplayer state.

## Check Your Understanding

1. What two values does `pcall` return, and what does each represent?
2. Why is testing with only one player insufficient for multiplayer features?
3. What is a race condition, and how does `FindFirstChild` help defend against one?
4. Why shouldn't `pcall` be used to blanket-hide every possible error?

## Mini Quiz

1. (Conceptual) What color does `warn()` print in the Output window?
   a) White b) Orange c) Red d) Blue
2. (Code-reading) In `local success, errorMessage = pcall(fn)`, what is `errorMessage` when `success` is true?
   a) nil b) The function's actual return value c) Always an empty string d) The word "success"
3. (Prediction) If two Studio test clients both click Restart Race within the same second, what should happen given Lesson 5's cooldown logic?
   a) Both restarts succeed b) The first succeeds, the second is ignored by the cooldown c) The server crashes d) Both are ignored
4. (Conceptual) What is the main benefit of Studio's multiple test clients feature?
   a) Faster script compiling b) Testing multiplayer behavior locally without publishing c) Better graphics d) Automatic bug fixing
5. (Code-reading) What does `SafeCall.run(fn, "label")` do if `fn()` throws an error?
   a) Crashes the whole script b) Silently ignores it with no trace c) Logs a warning with the label and error message, and returns false d) Automatically retries fn()

### Answer Key
1. b/(explained) — a success boolean and either the return value or the error message.
2. b — the function's actual return value.
3. b — the first succeeds, the second is blocked by the cooldown.
4. b — testing multiplayer behavior locally without publishing.
5. c — logs a warning including the label and error, returns false.

## Lesson Recap

You learned to read Output messages properly, use `pcall` and a reusable `SafeCall` helper for defensive coding, test multiplayer behavior with Studio's multiple test clients, and hunt down classic bugs like race conditions, duplicate connections, and unguarded events across your own obstacle course project.

## Homework / Practice Mission

Go back through every script from Lessons 1–6 and add at least one `FindFirstChild`/type-check/guard clause you hadn't already included, specifically anywhere you directly indexed an object that could theoretically not exist yet.

## Portfolio Project Connection

`SafeCall.module.lua` is included and used directly across all three Part B projects (Obstacle Course, Arena Teams, and NPC World) anywhere a visual/audio "nice-to-have" effect runs alongside core, must-not-fail gameplay logic — and the multi-client testing workflow you practiced today is exactly how you should test **Project 2: Multiplayer Arena & Teams**, whose team assignment and combat features cannot be verified with a single player.
