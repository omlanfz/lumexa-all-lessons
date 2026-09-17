# Lesson 06 — Playtesting and Iteration

**Course/Path:** Game Creator Path — Course 03: Advanced Game Design (Capstone)
**Lesson Number:** 6 of 8
**Duration:** ~60 minutes
**Difficulty:** Advanced
**Technology:** Python 3 + Pygame

---

## 🚀 Mission Brief

Every system is in place: architecture, levels, UI. But no designer — not even a professional one at a major studio — can perfectly predict how real players will experience their game just by imagining it. Today you join the **Quality Assurance and Playtesting division**. You'll learn how to run structured playtests, collect meaningful feedback (not just "it's fun" or "I liked it"), and turn that feedback into concrete, prioritized changes — the iterative loop that separates good games from great ones.

## 🎯 Learning Objectives

1. Explain why playtesting is essential and what it reveals that self-testing cannot.
2. Distinguish between subjective feedback ("I didn't like it") and actionable feedback ("I died 4 times at the same spike because I couldn't see it in time").
3. Design a structured playtest: observation notes, targeted questions, and metrics.
4. Instrument a Pygame game with lightweight **telemetry** (e.g., counting deaths per level) to gather objective data.
5. Practice the **iterate → test → measure** cycle by making a real balance change based on collected data.

## 🛠️ What You'll Build

A **playtest instrumentation module**: a `Telemetry` class that logs events (deaths, level completions, time spent) to identify exactly where players struggle, plus a structured **Playtest Feedback Form** (as a Python data structure and a printable template) you'll use on a real classmate or family member.

## 📋 Prerequisites

- Lessons 1–5 (you need a playable prototype to test).
- Access to at least one other person (classmate, sibling, parent) willing to playtest for 5–10 minutes.

## 🔑 Key Concepts

- **Playtesting** — having real people (ideally *not* the developer) play your game and observing/recording what happens.
- **Actionable Feedback** — specific, cause-and-effect observations that point to a concrete fix, as opposed to vague opinions.
- **Telemetry** — automatically logged, objective gameplay data (deaths, time-to-complete, choices made).
- **The Iteration Loop** — build → test → analyze → change → test again, repeated continuously throughout development.
- **Confirmation Bias** — a developer's tendency to only notice feedback that confirms what they already believed.
- **A/B Comparison** — testing two versions of a change to see which performs better, using data rather than opinion.

## 📖 Concept Explanation

### Why You Can't Playtest Your Own Game Alone

As the developer, you already know every level layout, every hazard placement, every control scheme. You cannot experience your game the way a first-time player does — you've built up invisible expertise. This is why every professional studio budgets serious time for **external playtesting**: put the game in front of people who have never seen it, and watch (without helping) what actually happens. You will be shocked, every time, by something a first-timer does that you never anticipated — that surprise is exactly the valuable information playtesting exists to surface.

### Subjective vs. Actionable Feedback

"I didn't really like level 2" is feedback, but it's not *actionable* — you don't know what to change. Good playtest facilitation turns vague reactions into specific data through targeted questions and direct observation:
- **Observe silently** while they play — where do they pause? Where do they die repeatedly? Where do they say "wait, what do I do?" out loud?
- **Ask specific, non-leading questions** afterward: "Which part took you the most tries?" instead of "Did you like level 2?" (a leading question invites a polite lie).
- **Never explain the game while they're playing** — if they get stuck, that stuck-ness itself is the data. Jumping in to help erases the exact information you need.

### Telemetry: Data Over Anecdote

A single playtester's experience is one data point; it might be an outlier. **Telemetry** — automatically recording objective events like "player died at position (x, y) at time t" or "player completed level 2 in 47 seconds" — lets you gather *patterns* across many sessions, even sessions you didn't personally watch. If ten different playtesters all die at the exact same spike, that's not bad luck — that's a design problem (the spike is placed unfairly, or poorly telegraphed) that data revealed clearly and quickly, without needing ten simultaneous observers.

### The Iteration Loop

Game design is never "finish it once." It's a loop: **build a version → test it with real players → analyze what happened (using both notes and telemetry) → make a specific, small change → test again**. Each pass should change as few things as possible so you can tell *which* change caused *which* result — changing five balance numbers at once and re-testing tells you nothing about which change mattered.

### Confirmation Bias

As the creator, you *want* your game to be good — this creates a natural bias to notice feedback that agrees with you ("see, they liked the spike level!") and dismiss feedback that doesn't ("well, THEY just aren't very good at games"). Professional playtesting practice fights this by recording raw observations and metrics *before* forming a conclusion, and by taking every "I got stuck here" report seriously rather than explaining it away.

## 💻 Guided Coding

We'll build a `Telemetry` class you can drop into any of your existing prototypes, plus a structured feedback form as data.

```python
"""
Playtest Telemetry Module
Lumexa Game Creator Path - Course 03, Lesson 06
Drop this class into any prototype to start collecting objective playtest data.
"""
import time
import json


class Telemetry:
    """Collects lightweight, objective gameplay events during a playtest session."""

    def __init__(self):
        self.session_start = time.time()
        self.events = []          # a full timestamped log of every event
        self.death_positions = [] # (level, x, y) for every death - reveals hotspots
        self.level_times = {}     # level_index -> list of completion times in seconds
        self._level_start_time = None

    def log_event(self, event_type, **details):
        self.events.append({
            "type": event_type,
            "time_since_start": round(time.time() - self.session_start, 2),
            **details,
        })

    def record_level_start(self, level_index):
        self._level_start_time = time.time()
        self.log_event("level_start", level=level_index)

    def record_death(self, level_index, x, y):
        self.death_positions.append((level_index, x, y))
        self.log_event("death", level=level_index, x=x, y=y)

    def record_level_complete(self, level_index):
        if self._level_start_time is not None:
            duration = round(time.time() - self._level_start_time, 2)
            self.level_times.setdefault(level_index, []).append(duration)
            self.log_event("level_complete", level=level_index, duration=duration)

    def find_death_hotspots(self, min_deaths=3, radius=40):
        """Groups nearby deaths to find hazard placements that killed multiple times.
        This is exactly how a designer spots an unfair/unclear hazard using data
        rather than guessing from a handful of watched sessions.
        """
        hotspots = []
        checked = set()
        for i, (level_a, x_a, y_a) in enumerate(self.death_positions):
            if i in checked:
                continue
            cluster = [(level_a, x_a, y_a)]
            for j, (level_b, x_b, y_b) in enumerate(self.death_positions):
                if j <= i or j in checked:
                    continue
                if level_a == level_b and abs(x_a - x_b) < radius and abs(y_a - y_b) < radius:
                    cluster.append((level_b, x_b, y_b))
                    checked.add(j)
            if len(cluster) >= min_deaths:
                avg_x = sum(p[1] for p in cluster) / len(cluster)
                avg_y = sum(p[2] for p in cluster) / len(cluster)
                hotspots.append({"level": level_a, "x": round(avg_x), "y": round(avg_y), "deaths": len(cluster)})
        return hotspots

    def summary_report(self):
        report_lines = ["=== PLAYTEST TELEMETRY SUMMARY ==="]
        report_lines.append(f"Total events logged: {len(self.events)}")
        report_lines.append(f"Total deaths: {len(self.death_positions)}")
        for level, times in sorted(self.level_times.items()):
            avg_time = sum(times) / len(times)
            report_lines.append(f"Level {level}: completed {len(times)} time(s), avg {avg_time:.1f}s")
        hotspots = self.find_death_hotspots()
        if hotspots:
            report_lines.append("Death hotspots (likely unfair/unclear hazards):")
            for spot in hotspots:
                report_lines.append(f"  Level {spot['level']} near ({spot['x']}, {spot['y']}): {spot['deaths']} deaths")
        else:
            report_lines.append("No death hotspots detected yet - keep collecting sessions.")
        return "\n".join(report_lines)

    def save_to_file(self, filename="playtest_log.json"):
        with open(filename, "w") as f:
            json.dump(self.events, f, indent=2)


# ---------------------------------------------------------------------------
# STRUCTURED PLAYTEST FEEDBACK FORM (use this with a real human playtester)
# ---------------------------------------------------------------------------
PLAYTEST_FORM_TEMPLATE = {
    "playtester_name": "",
    "date": "",
    "observation_notes": {
        "where_did_they_pause_or_look_confused": "",
        "where_did_they_die_repeatedly": "",
        "did_they_discover_all_mechanics_on_their_own": "",
    },
    "post_play_questions": {
        "which_part_took_the_most_tries": "",
        "was_there_a_moment_you_didnt_know_what_to_do": "",
        "which_part_felt_the_most_satisfying": "",
        "on_a_scale_of_1_to_5_how_hard_did_this_feel": "",
    },
    "designer_analysis": {
        "concrete_change_to_make": "",
        "why_this_change_addresses_the_observation": "",
    },
}


def print_playtest_form():
    """Prints a fillable version of the structured feedback form to the console."""
    print("=" * 50)
    print("LUMEXA PLAYTEST FEEDBACK FORM")
    print("=" * 50)
    for section, fields in PLAYTEST_FORM_TEMPLATE.items():
        print(f"\n[{section.upper()}]")
        if isinstance(fields, dict):
            for key in fields:
                print(f"  - {key.replace('_', ' ')}: ______________________")
        else:
            print(f"  {section.replace('_', ' ')}: ______________________")


if __name__ == "__main__":
    # Demo: simulate a short playtest session with fake telemetry data.
    telemetry = Telemetry()
    telemetry.record_level_start(1)
    telemetry.record_death(1, 320, 240)
    telemetry.record_death(1, 325, 245)
    telemetry.record_death(1, 318, 238)
    telemetry.record_level_complete(1)
    telemetry.record_level_start(2)
    telemetry.record_level_complete(2)

    print(telemetry.summary_report())
    print()
    print_playtest_form()
```

## 🔍 Code Walkthrough

- **`Telemetry.log_event()`** is a general-purpose logger — every specific method (`record_death`, `record_level_complete`) calls it, so you always have one full timestamped history plus specialized summaries.
- **`find_death_hotspots()`** groups nearby deaths into clusters — this is a simplified version of the exact technique real studios use (often visualized as "heatmaps") to find unfair or unclear hazards *without watching every single session personally*.
- **`PLAYTEST_FORM_TEMPLATE`** turns "please give feedback" into specific, answerable prompts — notice none of the questions are yes/no or "did you like it," they all point toward a concrete observation.
- **`save_to_file()`** persists raw event data as JSON, so you can compare telemetry across multiple playtest sessions over time (an iteration history).

## 🏗️ Build Instructions

1. Save as `telemetry_lab.py` and run `python telemetry_lab.py` to see the demo report and blank form print.
2. Pick one of your Lesson 1–5 prototypes. Add a `Telemetry` instance, and call `record_death()`/`record_level_complete()` at the right points in your game loop.
3. Run a real playtest: hand your game to a classmate or family member for 5–10 minutes. Watch silently, take notes, and ask the post-play questions from the form afterward.

## 🧭 Student Mission

Instrument your Lesson 2 or Lesson 3 prototype with real `Telemetry` calls (death position, level completion time), run one real playtest session with another person, and generate a `summary_report()`. Identify one concrete, specific change you would make based on what you observed (not a vague guess).

## 🌌 Challenge

Extend `Telemetry` with a `record_choice(level, choice_id)` method for tracking player decisions (useful for Project 07's branching dialogue) and add a method that reports what percentage of playtesters chose each option — the beginning of real design analytics.

## 🧪 Experiment / Extension (Progressive)

- **Beginner:** Run the demo script, read the summary report, and explain in your own words what a "death hotspot" tells a designer.
- **Intermediate:** Add a `total_playtime()` method to `Telemetry` and use it to flag if a level takes dramatically longer than expected (a possible sign of confusion, not just difficulty).
- **Advanced:** Build a simple text-based comparison tool that loads two saved `playtest_log.json` files (before and after a balance change) and prints which one had fewer deaths per level — real A/B iteration.

## ⚠️ Common Mistakes

- **Helping the playtester when they get stuck** — this destroys the exact signal you're trying to capture. Let them struggle; take notes instead.
- **Asking leading questions** ("Wasn't that level fun?") that bias the answer toward what you want to hear.
- **Changing many balance numbers at once** and re-testing — you lose the ability to know which change actually mattered.
- **Ignoring data that contradicts your assumptions** (confirmation bias) — a repeated death hotspot is real signal even if you personally never struggled there.

## 🐞 Debugging Tips

- If `find_death_hotspots()` never reports anything, check your `radius` and `min_deaths` thresholds — they may be too strict for a short test session with few deaths.
- If telemetry timestamps look wrong, confirm `session_start` is set once in `__init__` and never reset mid-session.
- If `save_to_file()` throws a permission error, confirm you have write access to the current working directory, or provide an absolute path.

## ❓ Check Your Understanding

1. Why can't a developer reliably playtest their own game alone?
2. Give an example of vague feedback and rewrite it as actionable feedback.
3. What is telemetry, and what problem does it solve that a single observed session cannot?
4. Why should you change only one variable at a time between playtest iterations?
5. What is confirmation bias, and how does structured data help fight it?

## 📝 Mini Quiz (Answer Key at End)

1. "I died a lot at the third spike because I couldn't see it until it was too late" is an example of:
   a) Vague feedback
   b) Actionable feedback
   c) Telemetry
   d) A state machine

2. A cluster of many player deaths at the same location in level data is called a:
   a) Core loop
   b) Death hotspot
   c) Prefab
   d) Feedback loop

3. Why should a playtest facilitator avoid helping a stuck player?
   a) It's rude
   b) It destroys the signal that reveals a real design problem
   c) It takes too much time
   d) Pygame doesn't allow it

4. Confirmation bias in game design refers to:
   a) A bug in collision code
   b) The tendency to notice feedback that agrees with what you already believe
   c) A type of state transition
   d) A rendering technique

5. Why change only one variable between test iterations?
   a) It's faster to code
   b) So you can identify which specific change caused which result
   c) Pygame requires it
   d) It makes the game harder

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

## 🔁 Lesson Recap

You learned why playtesting by real, external players is irreplaceable, how to turn vague reactions into **actionable feedback** through careful observation and targeted questions, and how to build lightweight **telemetry** to gather objective data like death hotspots and completion times. You practiced the **iterate → test → measure** loop that drives real game development, and built tools you'll use directly on your capstone project.

## 🏠 Homework / Practice Mission

Run one full structured playtest session (5–10 minutes) on any prototype from this course with a real person. Fill out the `PLAYTEST_FORM_TEMPLATE` completely, generate a telemetry summary if you instrumented your game, and write down one concrete balance or design change you will make as a result. Bring your findings to Lesson 7.

## 🗂️ Portfolio Project Connection

Before submitting any of the three capstone projects, you are expected to run at least one real playtest session using this lesson's form and/or the `Telemetry` class, and to make at least one concrete balance change as a result — this iteration is part of what elevates a "finished" project into a genuinely *polished* one, which is exactly the standard Lesson 8's final polish pass holds you to.
