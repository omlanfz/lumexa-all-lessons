# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 3 of 8
**Title:** Prompt Engineering Fundamentals
**Duration:** ~60 minutes
**Difficulty:** Beginner–Intermediate
**Technology:** Python, OpenAI Python SDK (v1.x)

---

## Mission Brief

Cadet, you've made contact with the AI. Now it's time to learn to *command* it precisely. A rocket doesn't fly well just because it has an engine — it needs a well-designed guidance system. Your prompts are that guidance system. Today we study **prompt engineering**: the real, learnable skill of writing inputs that reliably get you the outputs you want.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain why prompt wording measurably changes model output.
2. Apply core prompt engineering techniques: clear instructions, role/persona framing, few-shot examples, and output format constraints.
3. Distinguish system-level prompt design from user-level prompt design.
4. Rewrite a vague prompt into a precise, effective one.
5. Use `temperature` and prompt structure together deliberately to control output style.

---

## Prerequisites

- Completion of Lessons 1–2.
- Comfort running Python scripts and making basic `client.chat.completions.create()` calls.

---

## Concept Explanation

### Why Prompts Matter So Much

Recall from Lesson 1: an LLM predicts the next token based on everything in its context window. Your prompt *is* that context. Change the prompt, and you change every probability the model computes from that point forward. This is why two people can ask "the same question" in different words and get very different quality answers — prompt engineering is the discipline of controlling that variance on purpose.

### Technique 1: Be Specific and Explicit

Vague prompts produce vague, generic answers because the model has to guess what you actually want.

**Weak prompt:**
```
Tell me about Mars.
```

**Strong prompt:**
```
Explain three surprising facts about Mars's atmosphere to a 13-year-old,
in under 150 words, using an excited, adventurous tone.
```

The strong version specifies: **audience**, **content constraint** (three facts, one topic), **length**, and **tone**. Every one of those constraints removes ambiguity and steers the token-prediction process.

### Technique 2: System Prompts Set the Stage, User Prompts Ask the Question

We introduced this in Lesson 2 — now let's use it deliberately:

```python
messages = [
    {
        "role": "system",
        "content": (
            "You are Nova, a mission specialist AI aboard the Lumexa space "
            "station. You explain science concepts clearly and briefly, "
            "always in an encouraging tone, and you always end your answers "
            "with one follow-up question to keep the student curious."
        )
    },
    {
        "role": "user",
        "content": "Why does the Moon not have an atmosphere like Earth?"
    }
]
```

The system prompt defines *persistent behavior* (persona, tone, structure) that should hold across every user message in the conversation. The user prompt defines the *specific ask* for this turn. Mixing them up — cramming persona instructions into every user message — makes behavior inconsistent and wastes tokens.

### Technique 3: Few-Shot Prompting (Show, Don't Just Tell)

Sometimes the clearest way to specify a format is to show examples directly in the prompt. This is called **few-shot prompting** (versus **zero-shot**, where you give no examples).

```python
messages = [
    {
        "role": "system",
        "content": "You convert casual space facts into flashcard Q&A pairs."
    },
    {
        "role": "user",
        "content": (
            "Convert these facts into Q&A flashcards.\n\n"
            "Example:\n"
            "Fact: Venus is the hottest planet.\n"
            "Q: Which planet is the hottest?\nA: Venus\n\n"
            "Fact: Jupiter is the largest planet.\n"
            "Q: Which planet is the largest?\nA: Jupiter\n\n"
            "Now convert this fact:\n"
            "Fact: Saturn has the most visible rings."
        )
    }
]
```

By showing two worked examples before the real task, we dramatically increase the odds the model matches that exact format for the new input.

### Technique 4: Constrain the Output Format

If you need structured output (a list, a fixed number of items, JSON), say so explicitly:

```
Respond with exactly a numbered list of 3 items, no introduction or conclusion text.
```

or, for machine-readable output:

```
Respond ONLY with valid JSON in this exact shape: {"title": string, "steps": [string, ...]}.
Do not include any text outside the JSON.
```

### Technique 5: Give the Model "Room to Think" for Harder Tasks

For reasoning-heavy tasks, asking the model to work step by step before giving a final answer (sometimes called **chain-of-thought prompting**) often improves accuracy:

```
Solve this step by step, showing your reasoning, then give a final answer
on its own line starting with "Answer:".
```

### Temperature Revisited: Pairing Structure with Randomness

Prompt structure and `temperature` work together, not separately:

- Factual/structured tasks (data extraction, math, code): low temperature (`0`–`0.3`) + tightly specified prompt.
- Creative tasks (story writing, brainstorming): higher temperature (`0.7`–`1.2`) + looser, more open-ended prompt.

### A Complete, Runnable Example: Prompt Comparison Script

```python
"""
prompt_lab.py
Compares a weak prompt vs. a strong, engineered prompt on the same topic.
"""

import os
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError("Missing OPENAI_API_KEY. Check your .env file.")

client = OpenAI(api_key=api_key)


def ask(system_prompt: str, user_prompt: str, temperature: float = 0.7) -> str:
    """Send a single chat request and return the assistant's text reply."""
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt},
        ],
        temperature=temperature,
        max_tokens=200,
    )
    return response.choices[0].message.content


weak_system = "You are a helpful assistant."
weak_user = "Tell me about Mars."

strong_system = (
    "You are Nova, a Lumexa mission specialist who explains science to "
    "curious 13-year-olds in short, energetic bursts."
)
strong_user = (
    "Explain three surprising facts about Mars's atmosphere in under "
    "150 words, using an excited, adventurous tone, formatted as a "
    "numbered list."
)

print("=== WEAK PROMPT RESULT ===")
print(ask(weak_system, weak_user, temperature=0.7))

print("\n=== STRONG PROMPT RESULT ===")
print(ask(strong_system, strong_user, temperature=0.7))
```

Running this side by side makes the value of prompt engineering immediately visible.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Prompt engineering** | The practice of designing inputs to reliably steer an LLM's output. |
| **Zero-shot prompting** | Asking a model to perform a task with no examples given. |
| **Few-shot prompting** | Providing worked examples in the prompt to demonstrate the desired format/behavior. |
| **Chain-of-thought prompting** | Instructing the model to reason step by step before giving a final answer. |
| **Output format constraint** | An explicit instruction limiting the structure/shape of the response (list, JSON, word count). |
| **System prompt** | Persistent instructions defining the assistant's persona and rules for the whole conversation. |

---

## Instructor-Guided Coding

1. Live-run `prompt_lab.py` and compare outputs on the projector.
2. As a class, rewrite the weak prompt collaboratively, adding one constraint at a time (audience → length → tone → format), running the script after each addition to show incremental improvement.
3. Demonstrate few-shot prompting live: give the model one example, get an inconsistent format; add a second example, show the format stabilize.
4. Demonstrate a JSON-only output constraint, and show what happens (and how to fix it) when the model adds explanatory text outside the JSON despite instructions.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Prompt Refinement**

1. Start with this weak prompt: `"Write about space travel."`
2. Rewrite it three times, each version adding one new constraint (audience, tone, format, length).
3. Run all four versions (original + 3 rewrites) through `prompt_lab.py`'s `ask()` function and record outputs.
4. Write a short paragraph (5–6 sentences) explaining which version worked best and why, using the vocabulary terms **prompt engineering**, **output format constraint**, and at least one of **zero-shot** or **few-shot**.
5. Bonus: build a two-example few-shot prompt that turns a plain sentence into a "mission log" style sentence, and test it on a new plain sentence.

---

## Common Mistakes

- **Assuming the model should "just know" what you want.** Ambiguous prompts get ambiguous answers — specificity is the student's responsibility, not a flaw in the model.
- **Putting persona/behavior rules in the user message instead of the system message**, causing them to be forgotten or inconsistently applied across turns.
- **Giving contradictory instructions** (e.g., "be brief" and "explain in exhaustive detail") within the same prompt.
- **Forgetting to constrain format** and then being surprised the output isn't structured the way you needed for further processing.
- **Only testing a prompt once.** Because output has randomness (especially at higher temperature), one good or bad result isn't proof — test a few times.

---

## Check Your Understanding

1. Why do small wording changes in a prompt lead to different outputs?
2. What's the difference between zero-shot and few-shot prompting?
3. Where should persona and behavior rules live — system or user message — and why?
4. Give one example of an output format constraint and why you'd use it.
5. When would you use chain-of-thought prompting?

---

## Mini Quiz

1. Prompt engineering is best defined as:
 a) Writing the model's source code
 b) Designing inputs to reliably steer an LLM's output
 c) A method of training a model from scratch
 d) A way to bypass API rate limits

2. Few-shot prompting means:
 a) Giving the model no examples
 b) Providing worked examples in the prompt
 c) Asking five different questions at once
 d) Limiting the model to five tokens

3. Persona and tone instructions belong best in:
 a) The `system` message
 b) The `assistant` message
 c) A separate API call
 d) The `temperature` parameter

4. Chain-of-thought prompting is useful for:
 a) Reducing token cost
 b) Reasoning-heavy tasks where step-by-step thinking improves accuracy
 c) Making responses shorter
 d) Guaranteeing zero hallucination

5. Which is an example of an output format constraint?
 a) "Tell me something interesting."
 b) "Respond only with valid JSON in this exact shape: {...}"
 c) "You are a helpful assistant."
 d) `temperature=0.7`

**Answer Key:** 1-b, 2-b, 3-a, 4-b, 5-b

---

## Lesson Recap

Today you learned that prompts are not casual small talk with the AI — they're precise engineering inputs. You practiced specificity, system-vs-user prompt separation, few-shot examples, output format constraints, and chain-of-thought reasoning, and you paired these techniques deliberately with `temperature`. This turns prompt writing from guesswork into a repeatable, testable skill — one you'll rely on in every remaining project.

---

## Homework / Extension Mission

Build `format_lab.py`: write a system prompt establishing a "Lumexa Data Officer" persona, then use few-shot examples to get the model to reliably convert three different plain-English planet facts into a strict JSON shape: `{"planet": string, "fact": string, "category": string}`. Test with at least three different input facts and confirm the JSON is valid and consistent each time.

**Extension (optional):** Add a Python check using the built-in `json` module (`json.loads(...)`) that verifies the model's output actually parses as valid JSON, and prints a friendly error message if it doesn't.

---

## Portfolio Connection

Prompt engineering is the backbone of the assistant's *personality* in your **Recipe Chatbot with Memory** (system prompt defining its recipe-focused persona), the *guide-don't-tell* tutoring behavior in your **Study Assistant Bot**, and the *structured creative control* (genre, tone, characters) in your **Creative Story Generator**. Every system prompt you write in those projects directly applies today's techniques.
