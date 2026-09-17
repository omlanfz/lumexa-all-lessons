# Lesson 08: Publishing and Sharing with Friends

**Path:** Game Creator Path — Course 01: Roblox World Builder
**Lesson:** 8 of 8
**Duration:** ~60 minutes
**Difficulty:** Beginner–Intermediate
**Technology:** Roblox Publish workflow, Game Settings, Access permissions, Thumbnails/Icons

## Mission Brief

Launch day, Cadet. Today your obstacle course leaves the hangar and becomes a real, joinable Roblox experience your friends and family can play. You'll learn Roblox's actual publish workflow, configure game settings (name, description, access, thumbnail), and understand how to keep improving your game after launch using updates.

## Learning Objectives

- Publish a place to Roblox as a live, joinable experience.
- Configure basic Game Settings: name, description, genre, thumbnail/icon.
- Understand and set access permissions (Public vs. Friends vs. Private/unpublished).
- Share a game's link so friends can join, including how multiple people can test it together.
- Understand the concept of "updating a published game" without losing player data structure.

## What You'll Build

No new gameplay — this final lesson takes your completed Lesson 1–7 obstacle course and turns it into a real, published Roblox experience with proper settings, ready to share.

## Prerequisites

Lessons 01–07 completed, with a Roblox account able to publish places (an adult/parent-supervised account setup may be required depending on platform policy and age).

## Key Concepts

- **Publish** — the action of uploading your local place to Roblox's servers as a live "Experience" (game).
- **Game Settings** — the configuration page (Name, Description, Thumbnails, Access, Permissions) for a published experience.
- **Access permission** — who is allowed to join: Public (anyone), Friends Only, or Private (only people you specifically invite as collaborators, useful during development).
- **Experience / Place** — an "Experience" is the overall published game; a "Place" is one map/level within it (Course 01 games in this course each use a single place).
- **Updating** — re-publishing a place after making changes, so players get your new version next time they join.

## Concept Explanation

Everything you've built so far only exists inside Studio, on your own computer. **Publishing** uploads a copy of your place to Roblox's servers, creating a real, playable Experience with its own permanent link. From the File menu, **Publish to Roblox** does this the first time (asking for a Name and creator), and subsequent **Publish to Roblox** (or Ctrl+S once already published) pushes updates to the same live experience.

Once published, the **Game Settings** page (accessible via File → Game Settings, or from the Creator Dashboard on the Roblox website) lets you control:
- **Basic Info**: Name, Description, Genre.
- **Thumbnails & Icon**: images shown in search results and the game's page — first impressions matter, and Roblox lets you set custom images here.
- **Permissions → Access**: whether the experience is Public (anyone on Roblox can find and join), or restricted (e.g., to only people you invite while still testing).
- **Permissions → Collaborators** (Studio access): who can *edit* the place in Studio, separate from who can *play* it.

A crucial distinction for young developers: **Public** means anyone on Roblox can discover and join your game — a big responsibility, since real strangers will be interacting with your work. Many students (and their teachers/parents) prefer to keep early projects set to **Friends** or a restricted access level while testing, and only go fully Public once a game is genuinely ready and (if required) age-appropriate settings and moderation features are understood and in place. Always follow your platform's/school's/family's own account and safety guidelines before making anything Public.

**Sharing a link**: once published (even at a restricted access level), the Roblox website page for your experience has a shareable URL. Anyone with that link and the right access permission can click "Play" from their browser or the Roblox app to join your server.

**Updating after launch**: because a real game keeps improving, you will regularly go back into Studio, make a change (a new checkpoint, a bug fix, a new visual effect), and re-publish. Existing players simply get the newest version next time they join a server — this is exactly how professional games ship updates.

## Roblox Studio Concepts

- **File → Publish to Roblox**: the first-time publish action; asks for experience Name and where to publish it (your account or a Group).
- **File → Publish to Roblox As...**: lets you publish as a *new*, separate experience instead of updating an existing one (useful if you want to keep an older version separate).
- **File → Game Settings**: opens the configuration dialog with Basic Info, Monetization, Access, Permissions, Security tabs.
- **Creator Dashboard** (on the Roblox website, create.roblox.com): the web-based control panel for all your published experiences, where you can also edit settings, view basic analytics, and manage the experience outside of Studio.

## Step-by-Step Setup

1. Open your Lesson 07 place (fully working obstacle course with scoring, restart, and polish).
2. Do a final Play-mode test pass: confirm checkpoints score correctly, the restart button works, and lighting/effects look right (reuse your Lesson 07 testing checklist).
3. Go to the **File** menu (top-left of Studio) and click **Publish to Roblox**.
4. In the dialog, enter a **Name** (e.g. "Lumexa Obstacle Course") and a short **Description**.
5. Choose your account (or an authorized Group, if applicable) as the destination, then click **Create**/**Publish**.
6. Wait for the upload to finish — Studio shows a progress indicator and confirms when done.
7. Back in Studio, go to **File → Game Settings**.
8. In the **Basic Info** tab, double-check Name/Description, and set a **Genre** (e.g. "Obby"/"Adventure").
9. In the **Access** tab (may also be under Permissions), choose your access level (Public, Friends, or Private) according to your platform's/family's guidance.
10. Click **Save** to apply Game Settings.
11. On the Roblox website, visit your Creator Dashboard (create.roblox.com) and confirm your experience appears with the correct name and settings.
12. Copy the experience's page link and share it with the friends/family/classmates you're allowed to invite, according to your chosen access level.

## Guided Coding

This lesson has no new scripting; instead, here is a small, optional **version/update marker** pattern worth adding before your first publish, so you (and later, players via Output if they check) can always tell which build is running:

**Add to the top of `ServerScriptService/GameManager.server.lua`:**
```lua
-- Simple version marker, bumped manually each time you publish an update.
local GAME_VERSION = "1.0.0"
print("Lumexa Obstacle Course -- version " .. GAME_VERSION .. " loaded.")
```

## Code Walkthrough

- `GAME_VERSION` is just a plain string constant, printed once at server start — a tiny but genuinely useful professional habit: when debugging a live game, being able to confirm "is the server actually running my latest published version?" saves huge amounts of confusion.
- Bump this string (e.g. to `"1.0.1"`) every time you publish a meaningful update, and note in your own notes what changed — this is a lightweight, beginner-friendly stand-in for real changelog practices used by professional studios.

## Build Instructions

1. Complete the Step-by-Step Setup above to publish your game.
2. Verify the version-marker print appears in Output when you Play-test the published behavior locally (it still runs the same in Studio).
3. From the Roblox website or app (on a different device, or via a friend's account if available), open your experience's link and join.
4. Confirm scoring, restart, and visual polish all behave identically to your Studio testing.
5. Make one small tweak (e.g., change `GAME_VERSION` to `"1.0.1"` and adjust one checkpoint's point value), then re-publish via **File → Publish to Roblox**, and confirm the new version loads for players next time they join a fresh server.

## Student Mission

Write a one-paragraph "patch note" describing what's in your version 1.0.0 (e.g. "3 checkpoints worth 10/20/30 points, a restart button, sunset lighting, particle+sound feedback") to practice communicating changes the way real developers do.

## Challenge

Set your experience's access to Friends-only (if not already), invite at least one other classmate/friend with a Roblox account, and have them join your server while you're also in it — confirm the leaderboard shows both of you and that scores stay independent per player.

## Experiment / Extension

1. **Change a value:** Update the experience Name or Description in Game Settings and confirm the change reflects on the experience's website page.
2. **Observe:** Publish a small visible change (e.g., a new checkpoint color) and observe how existing players see the new version the next time they join a server (not instantly mid-session, but on next join/server restart).
3. **Modify a mechanic:** Add a second `GAME_VERSION`-style print for `ScoreManager` reporting how many players have ever scored (accumulate a simple counter) as an early example of tracking basic play stats.
4. **Combine:** Update your patch notes paragraph each time you publish a real change, building a running changelog exactly as professional studios do.

## Common Mistakes

- Publishing with access set fully Public before confirming the game is actually ready and appropriate to share widely — always test thoroughly first, and follow your platform's/family's/school's safety guidance about who can access your published work.
- Forgetting to re-publish after making a fix — students sometimes fix a bug in Studio, then wonder why their friend "still sees the old bug" (because the fix was never actually published).
- Confusing **Publish to Roblox** (updates the same experience) with **Publish to Roblox As...** (creates a brand new, separate experience) — using the wrong one can accidentally fragment your work across multiple disconnected places.
- Not testing the *actual published* experience at least once (only ever testing in Studio) — occasionally something behaves subtly differently in a live server versus local Studio testing, so a final live check matters.

## Debugging Tips

- If a published game doesn't reflect your latest Studio changes, confirm you actually clicked Publish (not just Save locally) and that you edited/published the same place file, not a different one.
- Use the version-marker print (`GAME_VERSION`) to quickly confirm, via Output, that a live server really is running your intended build.
- If friends can't join, first double-check the experience's Access setting in Game Settings — a Private or Friends-only setting will correctly block anyone outside that circle.

## Check Your Understanding

1. What is the difference between Publish to Roblox and Publish to Roblox As?
2. Why might a beginner choose Friends-only or Private access instead of Public at first?
3. What does the Game Settings Access tab control versus the Permissions/Collaborators section?
4. Why is a simple version-marker print statement useful once a game is live?

## Mini Quiz

1. (Conceptual) What does publishing a place actually do?
   a) Deletes your Studio file b) Uploads your place to Roblox's servers as a live, joinable Experience c) Only changes the Description d) Makes the game run faster
2. (Conceptual) Which access level restricts a game to only people you allow?
   a) Public b) Friends/Private c) Genre d) Thumbnail
3. (Prediction) If you fix a bug in Studio but never publish again, what will players already playing your live experience see?
   a) The bug fix instantly b) The old, unfixed behavior until you publish an update c) A crash d) A message asking them to wait
4. (Conceptual) What is the purpose of a Thumbnail/Icon in Game Settings?
   a) It changes gameplay b) It's the image shown in search results/the game page, forming first impressions c) It sets the game's access level d) It's required for scripting
5. (Code-reading) What does printing `GAME_VERSION` at server start help you do?
   a) Increase player count b) Confirm which build is actually running on a live server c) Fix bugs automatically d) Change access permissions

### Answer Key
1. b — uploads the place as a live Experience.
2. b — Friends/Private restricts to specific people.
3. b — players see the old behavior until you republish.
4. b — first-impression image shown in search/game page.
5. b — confirms which build is running.

## Lesson Recap

You published your Lumexa Obstacle Course as a real, live Roblox Experience, configured its Name/Description/Access in Game Settings, learned the difference between Publish and Publish As, shared it with others under an appropriate access level, and adopted a simple version-marker habit for tracking updates.

## Homework / Practice Mission

Plan (in writing, no code required) three future updates you'd like to add to your published obstacle course — e.g., a new obstacle type, a leaderboard reset button, a new area — and note which lesson's techniques (checkpoints, RemoteEvents, tweens) each would use.

## Portfolio Project Connection

This publishing workflow — final testing pass, version marker, Game Settings configuration, and appropriate access-level choice — is exactly the process you'll repeat for **Project 1: Obstacle Course & Scoring**, **Project 2: Multiplayer Arena & Teams**, and **Project 3: Custom Game World & NPCs** once you build them from Part B of this course, each capped off by the same publish-and-share ritual you just practiced.
