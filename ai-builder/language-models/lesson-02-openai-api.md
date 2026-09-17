# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 2 of 8
**Title:** OpenAI API Setup and Your First Call
**Duration:** ~60 minutes
**Difficulty:** Beginner
**Technology:** Python, OpenAI Python SDK (v1.x), python-dotenv

---

## Mission Brief

Cadet, yesterday you learned how the engine works. Today you get your hands on the controls. We're going to set up a real connection to a live language model and send our very first message from code — no more just talking *about* AI, now we talk *to* it, programmatically.

This is also the lesson where we introduce Lumexa's most important safety rule, one that applies to every single project for the rest of this course:

> **CRITICAL SECURITY RULE: Never hardcode an API key directly in your code.** API keys are like a spaceship's launch codes — if they leak (get pushed to GitHub, shared in a screenshot, pasted in a public forum), someone else can rack up huge bills or misuse your account. We always load keys from environment variables.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Create an OpenAI account, generate an API key, and understand what it's for.
2. Explain why API keys must never be hardcoded, and set up a `.env` file correctly.
3. Install and import the OpenAI Python SDK (v1.x style).
4. Write and run a script that sends a prompt to the API and prints the response.
5. Explain the roles used in a chat request: **system**, **user**, and **assistant**.
6. Identify and adjust the `model`, `messages`, and `temperature` parameters.

---

## Prerequisites

- Completion of Lesson 1 (How LLMs Work).
- Python 3.9+ installed, plus basic familiarity with running a `.py` file from a terminal.
- Ability to install packages with `pip`.

---

## Concept Explanation

### Getting an API Key

To use OpenAI's models from code, you need an **API key** — a secret string that identifies your account and authorizes billing. You get one from the OpenAI developer platform after creating an account. Treat this key exactly like a password: anyone who has it can use your account and spend your credits.

### The Golden Rule: Environment Variables, Not Hardcoded Strings

**Never do this:**

```python
# NEVER DO THIS — the key is exposed in your source code
client = OpenAI(api_key="sk-abc123REALSECRETKEY...")
```

If this file is ever committed to GitHub, shared as a zip, or even screen-shared during a stream, your key is compromised. Instead, we store the key **outside** the code, in an environment variable, and load it at runtime.

### The `.env` / `.env.example` Pattern

Every project in this course (and in the real world) follows this pattern:

1. **`.env`** — a file in your project folder containing your *real* secret key. This file is never shared, never committed to version control (it goes in `.gitignore`), and only exists on your own machine.

 ```
 OPENAI_API_KEY=sk-your-real-secret-key-here
 ```

2. **`.env.example`** — a *template* file that IS committed and shared. It shows other developers (or your future self) exactly which variables are needed, without exposing any real secret.

 ```
 OPENAI_API_KEY=your_api_key_here
 ```

3. **`python-dotenv`** — a small library that reads the `.env` file and loads its values into `os.environ` so your Python code can access them safely.

Install it alongside the OpenAI SDK:

```bash
pip install openai python-dotenv
```

### Loading the Key in Code

```python
import os
from dotenv import load_dotenv

# load_dotenv() reads the .env file in the project root
# and adds its key=value pairs to the environment variables
# for this running process.
load_dotenv()

api_key = os.environ.get("OPENAI_API_KEY")

if not api_key:
    raise ValueError(
        "OPENAI_API_KEY not found. Did you create a .env file "
        "based on .env.example and add your real key?"
    )
```

This pattern — check that the key exists, raise a clear error if it doesn't — will appear in every project we build from here on.

### Making Your First API Call

The current OpenAI Python SDK (v1.x) uses a `client` object. Here is a complete, runnable first script:

```python
"""
first_call.py
Lumexa Mission: send your first message to a real language model.
"""

import os
from dotenv import load_dotenv
from openai import OpenAI

# Step 1: load environment variables from .env
load_dotenv()

# Step 2: read the API key safely (never hardcoded)
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError("Missing OPENAI_API_KEY. Check your .env file.")

# Step 3: create the client. Passing api_key explicitly is optional —
# the SDK automatically checks os.environ["OPENAI_API_KEY"] if you
# omit it — but being explicit makes the code easier to read.
client = OpenAI(api_key=api_key)

# Step 4: send a chat completion request
response = client.chat.completions.create(
    model="gpt-4o-mini",       # a fast, low-cost model good for learning
    messages=[
        {
            "role": "system",
            "content": "You are a friendly assistant aboard the Lumexa space station."
        },
        {
            "role": "user",
            "content": "In one sentence, what is a language model?"
        }
    ],
    temperature=0.7,
    max_tokens=100,
)

# Step 5: extract and print the assistant's reply
reply = response.choices[0].message.content
print("Lumexa AI says:", reply)
```

Run it with:

```bash
python first_call.py
```

### Understanding the `messages` List and Roles

Every chat request sends a **list of message dictionaries**, each with a `role` and `content`:

- **`system`** — sets the assistant's behavior, personality, and rules for the whole conversation. The user never sees this directly; it's your instructions to the model.
- **`user`** — represents what the human is saying/asking.
- **`assistant`** — represents what the model has said in the past (used when we build multi-turn conversations in Lesson 4).

Order and role matter: the model reads this list top to bottom, treating it as the entire conversation so far, and predicts what the *next* assistant message should be.

### Key Parameters

- **`model`** — which model to use (`gpt-4o-mini` is a great low-cost default for learning; `gpt-4o` is more capable but pricier).
- **`messages`** — the conversation list described above.
- **`temperature`** (0.0–2.0) — controls randomness. `0` = very focused/deterministic, good for factual tasks. `1.0`+ = more creative/varied, good for story generation. We'll tune this deliberately in later lessons.
- **`max_tokens`** — the maximum number of tokens the model is allowed to generate in its reply (helps control cost and response length).

### Handling Errors Gracefully

Real code should anticipate problems — a missing key, a network issue, a rate limit. A simple, robust pattern:

```python
from openai import OpenAI, OpenAIError

try:
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[{"role": "user", "content": "Say hello from orbit."}],
    )
    print(response.choices[0].message.content)
except OpenAIError as e:
    print(f"Mission control, we have a problem: {e}")
```

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **API key** | A secret credential that authenticates your requests to a service like OpenAI. |
| **Environment variable** | A named value stored outside your code (often via `.env`), accessed with `os.environ`. |
| **`.env` file** | A local, un-shared file holding real secrets for your project. |
| **`.env.example`** | A shared template showing which environment variables a project needs, with placeholder values. |
| **SDK (Software Development Kit)** | A library provided by a company (here, OpenAI) that makes calling their API easier from your code. |
| **`client.chat.completions.create()`** | The SDK method used to send a chat request and get a model's response. |
| **System role** | The message role used to set the assistant's behavior/persona for the conversation. |
| **User role** | The message role representing the human's input. |
| **Assistant role** | The message role representing the model's own past replies. |
| **Temperature** | A parameter controlling the randomness/creativity of generated text. |

---

## Instructor-Guided Coding

Live-code this together, step by step, narrating each decision:

1. Create a new project folder: `mkdir lumexa-first-api && cd lumexa-first-api`.
2. Create a virtual environment (optional but recommended) and install packages: `pip install openai python-dotenv`.
3. Create `.env` with a placeholder key first, so students see the error-handling path fire (`ValueError: Missing OPENAI_API_KEY`), then swap in a real key (instructor's own, not shared on-screen).
4. Create `.env.example` alongside it, and explicitly point out: "This one goes on GitHub. That one never does."
5. Build `first_call.py` piece by piece, running it after each step so students see immediate feedback.
6. Change `temperature` from `0.2` to `1.5` and re-run the same prompt twice at each setting — discuss the difference in output variety.

---

## Student Mission / Guided Challenge

**Explorer Challenge: First Contact**

1. Set up your own project folder with `.env`, `.env.example`, and `first_call.py`.
2. Write a system prompt that gives the assistant a space-themed persona of your choosing (e.g., "You are the AI aboard a Mars rover" or "You are a wisecracking robot navigator").
3. Ask it three different user questions in three separate runs, and record the responses.
4. Experiment with `temperature` at `0.0`, `0.7`, and `1.5` using the *same* user question each time. Write two sentences comparing the outputs.
5. Intentionally rename your `.env` file temporarily to trigger your `ValueError` — confirm your error handling works, then rename it back.

---

## Common Mistakes

- **Hardcoding the key "just for testing."** Even "temporary" hardcoded keys get committed by accident. Always use `.env` from the very first line of code.
- **Forgetting to call `load_dotenv()`** before reading `os.environ` — without it, the `.env` file is never actually loaded.
- **Committing `.env` to version control.** Always add `.env` to `.gitignore`; only `.env.example` should be shared.
- **Mixing up roles** — putting instructions meant for the model into the `user` message instead of `system`, which makes behavior inconsistent across turns.
- **Not checking `response.choices[0].message.content`** correctly — forgetting the `.message.content` chain and trying to print the whole response object.

---

## Check Your Understanding

1. Why is hardcoding an API key dangerous, even in a "private" script?
2. What's the difference between `.env` and `.env.example`?
3. What does `load_dotenv()` actually do?
4. What are the three message roles, and what is each one for?
5. What happens if you set `temperature=0` versus `temperature=1.5`?

---

## Mini Quiz

1. Where should a real OpenAI API key be stored?
 a) Directly in the Python file
 b) In a `.env` file loaded via environment variables
 c) In `.env.example`
 d) In a public GitHub repo

2. Which role sets the assistant's overall behavior and persona?
 a) `user`
 b) `assistant`
 c) `system`
 d) `developer`

3. `client.chat.completions.create()` requires which two key arguments at minimum?
 a) `model` and `messages`
 b) `key` and `temperature`
 c) `prompt` and `tokens`
 d) `user` and `system`

4. A higher `temperature` value generally makes output:
 a) Shorter
 b) More deterministic
 c) More random/varied
 d) Free of hallucinations

5. `.env.example` should contain:
 a) Your real API key
 b) A placeholder value showing which variables are needed
 c) Nothing at all
 d) Your billing information

**Answer Key:** 1-b, 2-b, 3-a, 4-c, 5-b

---

## Lesson Recap

You made first contact today: you set up an OpenAI account and API key, learned the non-negotiable security rule of loading secrets from environment variables via `.env`/`.env.example`/`python-dotenv`, installed the OpenAI SDK, and sent your first real chat completion request using `client.chat.completions.create()`. You now understand the `system`/`user`/`assistant` roles and how `temperature` shapes creativity. Every project for the rest of this course builds directly on this setup.

---

## Homework / Extension Mission

Build a small script called `mission_briefing.py` that: loads the API key securely from `.env`, sets a system prompt giving the assistant a unique Lumexa persona of your own design, asks it to generate a short "daily mission briefing" for a space explorer, and prints the result nicely with a header like `=== TODAY'S MISSION BRIEFING ===`. Include basic error handling for a missing API key.

**Extension (optional):** Modify your script to accept the user's question from `input()` at runtime instead of a hardcoded string, so it behaves like a tiny one-turn chatbot.

---

## Portfolio Connection

This lesson's setup — secure key loading, the `client.chat.completions.create()` call, and the system/user/assistant role structure — is the exact foundation of the **Recipe Chatbot with Memory** project. Every project going forward (Study Assistant Bot, Creative Story Generator) reuses this same `.env` pattern and API call structure, so keep this script as a reference template.
