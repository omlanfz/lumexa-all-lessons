# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 4 of 8
**Title:** Building Conversational Memory
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Python, OpenAI Python SDK (v1.x)

---

## Mission Brief

Cadet, recall the key fact from Lesson 1: an LLM has **no persistent memory**. Every API call is stateless — the model sees exactly the tokens you send it, and nothing more. So how does ChatGPT seem to "remember" what you said three messages ago? Today's mission reveals the trick: **we** hold the memory, not the model. We resend the whole conversation, every single time. Let's build it.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain why LLM APIs are stateless and how conversational memory is actually implemented.
2. Build and maintain a growing list of role/content message dictionaries across multiple turns.
3. Implement a basic multi-turn chat loop in Python.
4. Explain the relationship between conversation length, token usage, and the context window.
5. Implement a simple strategy for trimming old messages when a conversation grows too long.

---

## Prerequisites

- Completion of Lessons 1–3.
- Comfort with Python lists, dictionaries, and `while` loops.
- Working `.env` setup and OpenAI SDK installed.

---

## Concept Explanation

### The Illusion of Memory

When you chat with an AI assistant across many messages and it correctly refers back to something you said earlier, it feels like it "remembers." In reality, here's what's happening:

1. Your app keeps a growing list of every message exchanged so far.
2. Every time you send a new message, your app sends the **entire list** — not just the new message — back to the API.
3. The model has no idea any of this is a "conversation" in an ongoing sense; it simply reads the whole list of messages as its context and predicts the next assistant message.

This means **conversational memory lives in your application code, not in the model.** If you don't resend earlier messages, the model has genuinely forgotten them.

### Building the Message History

We maintain memory as a plain Python list of dictionaries, exactly like the `messages` argument from earlier lessons — except now we append to it after every turn:

```python
"""
memory_chat.py
A minimal multi-turn chatbot that manages its own conversation memory.
"""

import os
from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

load_dotenv()
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError("Missing OPENAI_API_KEY. Check your .env file.")

client = OpenAI(api_key=api_key)

SYSTEM_PROMPT = (
    "You are Nova, a friendly AI aboard the Lumexa space station. "
    "Keep answers concise and remember details the user shares with you "
    "during the conversation."
)

# The conversation history starts with just the system message.
# Every user message and every assistant reply gets appended here.
conversation_history = [
    {"role": "system", "content": SYSTEM_PROMPT}
]


def get_ai_reply(history: list) -> str:
    """Send the full conversation history and return the assistant's reply."""
    try:
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=history,
            temperature=0.7,
            max_tokens=250,
        )
        return response.choices[0].message.content
    except OpenAIError as e:
        return f"Mission control, we hit an error: {e}"


def main():
    print("=== Lumexa Nova Chat (type 'exit' to end mission) ===")
    while True:
        user_input = input("You: ").strip()
        if user_input.lower() in ("exit", "quit"):
            print("Nova: Mission log closed. Safe travels, cadet.")
            break

        # 1. Add the user's new message to history
        conversation_history.append({"role": "user", "content": user_input})

        # 2. Send the FULL history so far to the model
        reply = get_ai_reply(conversation_history)

        # 3. Add the assistant's reply to history too, so future turns
        #    include it as context
        conversation_history.append({"role": "assistant", "content": reply})

        print(f"Nova: {reply}")


if __name__ == "__main__":
    main()
```

Try this: tell it your name, then a few messages later, ask "What's my name?" Because your name is still in `conversation_history`, it answers correctly. Then imagine skipping step 1 or step 3 above — the model would have no idea what you're talking about, because that information was never actually in its context.

### Remembering Specific Facts (Preferences) Mid-Conversation

Because the *entire* history is resent every turn, anything the user mentions — a name, a preference, a constraint like "I'm allergic to peanuts" — is automatically available to the model on every future turn, **as long as it's still in the list**. This is the exact mechanism your Recipe Chatbot project will use to remember dietary restrictions stated mid-conversation: no special "memory database" is needed for within-session memory — just a correctly maintained list.

### The Context Window Problem

Every model has a maximum context window (for example, many current models support well over 100,000 tokens, but smaller/cheaper models may have tighter limits). If a conversation runs long enough, the growing `conversation_history` list can exceed that limit, and the API call will fail or the oldest messages will effectively be unusable.

Two common strategies to manage this:

**1. Sliding window (keep only the N most recent turns):**

```python
MAX_TURNS = 10  # keep the system prompt + last 10 user/assistant exchanges

def trim_history(history: list, max_turns: int = MAX_TURNS) -> list:
    """Keep the system message plus only the most recent max_turns*2 messages."""
    system_msg = history[0]
    recent_messages = history[1:]
    max_messages = max_turns * 2  # each turn = 1 user + 1 assistant message
    if len(recent_messages) > max_messages:
        recent_messages = recent_messages[-max_messages:]
    return [system_msg] + recent_messages
```

Call `conversation_history = trim_history(conversation_history)` right before sending each request.

**2. Summarization (compress old turns into a short summary):**

For longer-running assistants, instead of dropping old messages entirely, you periodically ask the model to summarize the earlier part of the conversation into one or two sentences, replace those old messages with a single system/assistant note containing the summary, and continue from there. This preserves important facts (like stated preferences) while saving tokens. We won't implement full summarization in this lesson, but knowing the technique exists matters for real-world assistants that run for a long time.

### Why This Matters for Cost, Too

Every token in `conversation_history` gets billed on every single request, because the whole list is resent each time. A very long, untrimmed conversation gets **more expensive with every single turn**, even if the new message is short. This is a very real production concern, not just an academic one.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Stateless API** | An API where each request is handled independently, with no memory of previous requests. |
| **Conversation history** | The list of role/content messages your application maintains and resends to simulate memory. |
| **Sliding window** | A memory strategy that keeps only the most recent N turns and discards older ones. |
| **Summarization (memory)** | A memory strategy that compresses older turns into a short summary instead of discarding them outright. |
| **Multi-turn conversation** | An exchange spanning more than one user/assistant pair, requiring accumulated context to remain coherent. |

---

## Instructor-Guided Coding

1. Run `memory_chat.py` live. Tell it your favorite food. Two turns later, ask what your favorite food is — show that it answers correctly.
2. Comment out the line that appends the user message to history (`conversation_history.append(...)` for the user), rerun, and demonstrate the assistant now has no idea what's being discussed — reinforcing that memory is not automatic.
3. Add a `print(f"[DEBUG] History has {len(conversation_history)} messages]")` line before each API call so students can watch the list grow in real time.
4. Implement `trim_history()` together and demonstrate it capping the list length after many turns.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Memory Core**

1. Extend `memory_chat.py` so that when a user says something starting with "Remember that", the assistant explicitly confirms what it stored (e.g., "Got it — noted that you like pineapple on pizza.").
2. Add `trim_history()` to your script with `MAX_TURNS = 5`, and test with a conversation of at least 12 turns to confirm the history is being capped correctly (add a debug print showing the list length each turn).
3. Write two to three sentences explaining, using the term **stateless**, why the assistant would "forget" something if you didn't append it to `conversation_history`.

---

## Common Mistakes

- **Forgetting to append the assistant's own reply to history** — this breaks multi-turn coherence because the model won't see its own previous answers as context.
- **Resetting `conversation_history` accidentally** (e.g., redeclaring it inside a loop) — wipes memory every turn.
- **Assuming trimming loses nothing important** — a naive sliding window can drop an important early fact (like a stated allergy); production systems often pin critical facts or summarize instead of blindly dropping.
- **Not accounting for token cost of long histories** — leads to surprise API costs in real applications.

---

## Check Your Understanding

1. Why is an LLM API call described as "stateless"?
2. What Python data structure holds the conversation memory in our examples?
3. What happens if you forget to append the assistant's reply to the history list?
4. Name one strategy for managing a growing conversation history and one downside of it.
5. Why does a longer conversation cost more per turn, even if each new message is short?

---

## Mini Quiz

1. Conversational memory in an LLM app is implemented by:
 a) The model automatically remembering past sessions
 b) The application resending the full conversation history each request
 c) A separate memory chip in the API
 d) Increasing the temperature parameter

2. If you forget to append the user's new message to the history list before calling the API, the result is:
 a) No effect at all
 b) The model won't see that message as part of the context
 c) The API call fails immediately
 d) The temperature resets

3. A "sliding window" memory strategy:
 a) Keeps the entire conversation forever
 b) Keeps only the most recent N turns and drops older ones
 c) Deletes the system prompt after 5 turns
 d) Increases the context window automatically

4. Why does a long, untrimmed conversation history increase API cost over time?
 a) Longer conversations use a different, pricier model automatically
 b) The entire history is resent and billed on every request
 c) Temperature increases with conversation length
 d) It doesn't — cost stays flat regardless of history length

5. Which best describes a "stateless" API?
 a) One that stores your conversation on the server between calls
 b) One where each request is handled independently with no built-in memory
 c) One that never returns errors
 d) One that only accepts a single message ever

**Answer Key:** 1-b, 2-b, 3-b, 4-b, 5-b

---

## Lesson Recap

Today you demystified AI "memory": there is none, built into the model — it's an illusion created by resending the growing conversation history with every request. You built a working multi-turn chatbot that appends user and assistant messages to a shared list, learned why context windows and cost both grow with conversation length, and implemented a sliding-window trimming strategy to keep things manageable.

---

## Homework / Extension Mission

Extend `memory_chat.py` into `preferences_chat.py`: have the assistant track user-stated preferences (e.g., dietary restrictions, favorite topics) purely through conversation history (no external database yet), and add a special command `/history` that prints the full current `conversation_history` list so students can see exactly what the model is receiving.

**Extension (optional):** Implement a simple summarization step: after 8 turns, make one extra API call asking the model to summarize the conversation so far in 2 sentences, replace all but the last 2 turns with a single system message containing that summary, and continue the conversation from there.

---

## Portfolio Connection

This lesson is the direct technical foundation of your **Recipe Chatbot with Memory** project: it must remember dietary preferences and constraints stated mid-conversation using exactly this conversation-history technique. Your **Study Assistant Bot** also depends on this to maintain a coherent tutoring session via `st.session_state` (Lesson 7 shows how this pattern moves into Streamlit).
