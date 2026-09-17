# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 7 of 8
**Title:** Building a UI with Streamlit
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Python, Streamlit, OpenAI Python SDK (v1.x)

---

## Mission Brief

Cadet, your bots have worked beautifully in the terminal — but mission control needs a real cockpit, not a command line. Today we build an actual visual interface using **Streamlit**, a Python framework that turns a script into a shareable web app in minutes, no HTML/CSS/JavaScript required. By the end of this lesson, your chatbot will run in a browser window with a real chat interface.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain what Streamlit is and how it differs from a normal Python script (rerun model).
2. Use `st.session_state` to persist data (like conversation history) across reruns.
3. Build a chat interface using `st.chat_message` and `st.chat_input`.
4. Combine Streamlit's chat UI with the OpenAI API and Lesson 4's memory pattern.
5. Run a Streamlit app locally with `streamlit run`.

---

## Prerequisites

- Completion of Lessons 1–6.
- Comfort with the `conversation_history` multi-turn pattern from Lesson 4.
- Python installed; ability to install packages with `pip`.

---

## Concept Explanation

### What Is Streamlit?

**Streamlit** is a Python library that turns a plain script into an interactive web application. You write ordinary Python; Streamlit handles rendering it as a browser UI — text, buttons, chat bubbles, forms, sidebars — with just a few function calls.

Install it:

```bash
pip install streamlit
```

Run any Streamlit app with:

```bash
streamlit run app.py
```

This opens a local web server (usually at `http://localhost:8501`) and displays your app in the browser.

### The Rerun Model (The Most Important Streamlit Concept)

This is the concept students most often find surprising: **Streamlit re-runs your ENTIRE script from top to bottom every single time the user interacts with the page** — clicking a button, typing in a chat box, changing a dropdown. There's no manual event-loop code; instead, Streamlit just reruns the whole file and redraws the UI based on the current state.

This raises an obvious problem: if the whole script reruns every time, how does anything "remember" previous state, like a growing conversation history? The answer is `st.session_state`.

### `st.session_state`: Streamlit's Memory Across Reruns

`st.session_state` is a special dictionary-like object that **persists across reruns for the same user session**, even though the rest of your script's variables get reset every time. This is exactly the tool we need to hold our conversation history from Lesson 4 inside a Streamlit app.

```python
import streamlit as st

# Initialize conversation history ONCE, the first time the app loads.
# On every rerun, this check prevents wiping out existing history.
if "messages" not in st.session_state:
    st.session_state.messages = [
        {"role": "system", "content": "You are a helpful Lumexa assistant."}
    ]
```

Because of the `if "messages" not in st.session_state:` guard, this list is created only the very first time, and survives every later rerun untouched (unless you explicitly modify it).

### Building a Chat Interface

Streamlit provides purpose-built chat elements:

- **`st.chat_message(role)`** — a styled chat bubble container for a given role ("user" or "assistant").
- **`st.chat_input(placeholder)`** — a text input pinned to the bottom of the page, styled like a real chat box, that returns the typed text (or `None` if nothing was submitted this rerun).

### A Complete, Runnable Streamlit Chat App

```python
"""
app.py
A Lumexa AI chat assistant with a real Streamlit chat interface.
Run with: streamlit run app.py
"""

import os
import streamlit as st
from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

# --- Setup: load API key securely ---
load_dotenv()
api_key = os.environ.get("OPENAI_API_KEY")

st.set_page_config(page_title="Lumexa AI Chat", page_icon="🚀")
st.title("🚀 Lumexa Mission Chat")

if not api_key:
    st.error(
        "OPENAI_API_KEY not found. Please create a .env file based on "
        ".env.example and add your API key."
    )
    st.stop()  # halts the app here so nothing below runs without a key

client = OpenAI(api_key=api_key)

SYSTEM_PROMPT = (
    "You are Nova, a friendly AI aboard the Lumexa space station. "
    "Keep answers clear, encouraging, and concise."
)

# --- Persistent state: initialize conversation history once ---
if "messages" not in st.session_state:
    st.session_state.messages = [{"role": "system", "content": SYSTEM_PROMPT}]

# --- Render existing conversation history (skip the system message) ---
for message in st.session_state.messages:
    if message["role"] == "system":
        continue
    with st.chat_message(message["role"]):
        st.markdown(message["content"])

# --- Chat input pinned to the bottom of the page ---
user_input = st.chat_input("Ask Nova something...")

if user_input:
    # 1. Add the user's message to session state and display it
    st.session_state.messages.append({"role": "user", "content": user_input})
    with st.chat_message("user"):
        st.markdown(user_input)

    # 2. Call the API with the full history so far
    with st.chat_message("assistant"):
        with st.spinner("Nova is thinking..."):
            try:
                response = client.chat.completions.create(
                    model="gpt-4o-mini",
                    messages=st.session_state.messages,
                    temperature=0.7,
                    max_tokens=300,
                )
                reply = response.choices[0].message.content
            except OpenAIError as e:
                reply = f"Mission control, we hit an error: {e}"
        st.markdown(reply)

    # 3. Add the assistant's reply to session state so it persists
    st.session_state.messages.append({"role": "assistant", "content": reply})
```

Walk through this carefully with students:

- The `if "messages" not in st.session_state` block only runs once ever (per browser session), which is why history survives reruns.
- Every time the loop `for message in st.session_state.messages:` runs, it redraws the **entire** chat history from scratch — because remember, the whole script reruns every interaction. This is normal and expected in Streamlit.
- `st.chat_input()` returns `None` on most reruns (when the user hasn't just submitted something), so the `if user_input:` block only executes right after a real submission.
- `st.stop()` is a clean way to halt execution partway through the script (here, when there's no API key) without needing nested `if/else` for the rest of the file.

### Adding a Sidebar (Optional Polish)

```python
with st.sidebar:
    st.header("Mission Controls")
    if st.button("Clear conversation"):
        st.session_state.messages = [{"role": "system", "content": SYSTEM_PROMPT}]
        st.rerun()  # force an immediate rerun to refresh the displayed chat
```

`st.rerun()` manually triggers a rerun immediately (instead of waiting for the next user interaction) — useful right after changing state you want reflected immediately, like clearing history.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Streamlit** | A Python framework for building interactive web apps from plain scripts. |
| **Rerun model** | Streamlit's behavior of re-executing the entire script top-to-bottom on every user interaction. |
| **`st.session_state`** | A persistent, dictionary-like object that survives reruns for a given user session. |
| **`st.chat_message`** | A Streamlit UI element that renders a styled chat bubble for a given role. |
| **`st.chat_input`** | A Streamlit UI element providing a chat-style text box pinned to the bottom of the page. |
| **`st.rerun()`** | A function that manually forces Streamlit to rerun the script immediately. |

---

## Instructor-Guided Coding

1. Install Streamlit together and run a trivially small "Hello World" app (`st.title("Hello")`) to confirm setup works before building anything complex.
2. Add a button with no `session_state` and click it repeatedly, showing that any variable not stored in `session_state` resets every rerun — this makes the rerun model concrete before introducing the fix.
3. Build `app.py` above piece by piece, running after each major section.
4. Add the sidebar "Clear conversation" button live, demonstrating `st.rerun()`.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Cockpit Build**

1. Build your own Streamlit chat app based on `app.py`, using a persona/purpose system prompt you designed in Lesson 5.
2. Add a sidebar with a "Clear conversation" button using `st.rerun()`.
3. Add a `st.caption()` under the title showing how many messages are currently in the conversation (`len(st.session_state.messages)`), to make Streamlit's persistent state visible.
4. Test: refresh the browser page — confirm the conversation resets (this is expected; `session_state` persists across reruns *within* a session, but a full page reload starts a new session). Write one sentence explaining why this happens.

---

## Common Mistakes

- **Storing conversation history in a plain variable instead of `st.session_state`** — it silently resets on every interaction, which is confusing because the app "worked" for exactly one exchange.
- **Forgetting the `if "messages" not in st.session_state:` guard** — without it, history gets wiped back to just the system prompt on every single rerun.
- **Calling `st.chat_input()` inside a loop or conditional incorrectly**, leading to it not rendering at the bottom of the page as expected.
- **Expecting `st.rerun()` calls or button clicks to behave like a normal Python loop** — remember, the whole script reruns; there's no persistent "running" function like in a terminal chat loop.
- **Not calling `st.stop()` when the API key is missing**, causing confusing errors further down the script instead of a clean message.

---

## Check Your Understanding

1. What does Streamlit do every time a user interacts with the page?
2. Why is a plain Python variable insufficient for storing conversation history in a Streamlit app?
3. What is `st.session_state`, and how does it solve the rerun problem?
4. What does `st.chat_input()` return when nothing has just been submitted?
5. What does `st.rerun()` do, and when would you use it?

---

## Mini Quiz

1. Streamlit's "rerun model" means:
 a) The script only runs once, ever
 b) The entire script re-executes top to bottom on every user interaction
 c) Only the last line of the script re-executes
 d) Streamlit runs in a separate background thread automatically

2. `st.session_state` is used to:
 a) Store secret API keys securely
 b) Persist data across reruns within a user's session
 c) Style the chat bubbles
 d) Connect to the OpenAI API directly

3. `st.chat_input()` returns `None` when:
 a) The API key is missing
 b) Nothing has just been submitted on this rerun
 c) The user session has ended
 d) `st.session_state` is empty

4. To run a Streamlit app, you use:
 a) `python app.py`
 b) `streamlit run app.py`
 c) `pip run app.py`
 d) `openai run app.py`

5. `st.rerun()` is used to:
 a) Permanently stop the app
 b) Manually force an immediate script rerun
 c) Clear the API key
 d) Install new packages

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

---

## Lesson Recap

Today you left the terminal behind and built a real, browser-based chat interface using Streamlit. You learned the single most important Streamlit concept — the rerun model — and how `st.session_state` solves the memory problem it creates, exactly as `conversation_history` did back in Lesson 4, just adapted to Streamlit's execution style. You used `st.chat_message` and `st.chat_input` to build a polished chat UI, and added sidebar controls with `st.rerun()`.

---

## Homework / Extension Mission

Take one of your persona bots from Lesson 5 and rebuild it as a full Streamlit app using today's pattern. Add at least one piece of UI polish beyond the base example: a sidebar with a persona description, a message counter, or a "download conversation" text display using `st.text_area()`.

**Extension (optional):** Add a `st.selectbox()` in the sidebar letting the user choose between two different personas (two different system prompts), and reset the conversation with `st.rerun()` whenever the persona selection changes.

---

## Portfolio Connection

This lesson is the direct technical foundation of your **Study Assistant Bot** and **Creative Story Generator** projects — both are real Streamlit apps using `st.session_state`, `st.chat_message`/`st.chat_input` or `st.form`, exactly as shown here. Even your terminal-based **Recipe Chatbot with Memory** could be upgraded into a Streamlit app using this exact pattern as an extension.
