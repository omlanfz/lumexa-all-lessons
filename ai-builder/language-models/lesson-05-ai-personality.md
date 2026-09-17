# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 5 of 8
**Title:** Giving Your Bot a Personality and Purpose
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Python, OpenAI Python SDK (v1.x)

---

## Mission Brief

Cadet, you now have the engine (Lessons 1–2), the steering (Lesson 3), and the memory core (Lesson 4). Today we give your ship a **crew personality** — a clear identity, purpose, and set of boundaries that make it feel like a real, trustworthy assistant instead of a generic text generator. This is the lesson where your bot stops being "an AI that answers things" and becomes a specific character with a job.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Design a system prompt that establishes a consistent persona: name, tone, purpose, and behavioral boundaries.
2. Explain the difference between a *persona* (how it talks) and a *purpose* (what it's for) and why both matter.
3. Write explicit "guardrails" into a system prompt (what the bot should refuse or redirect).
4. Test persona consistency across multiple turns and adjust the prompt when it breaks.
5. Combine persona design with the memory system from Lesson 4 to build a coherent character across a full conversation.

---

## Prerequisites

- Completion of Lessons 1–4.
- Comfort building and running a multi-turn chat loop with `conversation_history`.

---

## Concept Explanation

### Persona vs. Purpose

Two things define a good assistant, and they are not the same:

- **Persona** — *how* it communicates: name, tone, personality quirks, vocabulary level, formality.
- **Purpose** — *what* it's actually for: the specific job it does, the domain it stays within, the outcome it's trying to help the user achieve.

A bot can have a fun persona but a completely undefined purpose (and end up unhelpful and inconsistent), or a clear purpose but a flat, forgettable persona (functional but uninviting). Great assistants — like the ones you'll build for your portfolio — need both, working together.

### Anatomy of a Strong Persona/Purpose System Prompt

A well-designed system prompt for a purpose-built assistant typically includes:

1. **Name and role** — give it an identity.
2. **Tone and style** — how it should sound (encouraging, playful, formal, concise).
3. **Domain/purpose** — what topics it covers and what its job actually is.
4. **Boundaries/guardrails** — what it should NOT do, and how to redirect politely when asked to go outside its purpose.
5. **Behavioral rules** — specific instructions about format, follow-up questions, or how it handles edge cases (e.g., "if you don't know something, say so rather than guessing").

Here's a complete example for a "Study Buddy" persona:

```python
STUDY_BUDDY_SYSTEM_PROMPT = """
You are Orbit, a friendly AI study companion aboard the Lumexa space station.

PERSONA:
- You speak in a warm, encouraging tone, like a patient older sibling.
- You occasionally use light space metaphors ("let's launch into this topic")
  but never overdo it — at most one per response.
- You keep responses concise: 3-5 sentences unless the student asks for more detail.

PURPOSE:
- Your job is to help middle and high school students understand school
  subjects (math, science, history, English) by guiding their thinking,
  not doing the work for them.
- You ask a guiding question or give a hint before giving a direct answer,
  unless the student explicitly asks for the answer outright after trying.

BOUNDARIES:
- You do not complete graded assignments or write full essays for students.
- You do not discuss topics unrelated to schoolwork or study skills; if
  asked about something else, gently redirect: "That's outside my mission
  here in the study lab — let's get back to your schoolwork!"
- If you're not confident about a fact, say so honestly rather than guessing.

Always stay in character as Orbit across the whole conversation.
"""
```

Notice how specific and concrete this is compared to a vague instruction like "be helpful and friendly." Specificity is what makes persona *consistent* across dozens of turns, not just the first reply.

### A Complete, Runnable Persona Demo

```python
"""
persona_bot.py
Demonstrates a fully realized persona + purpose system prompt,
combined with the multi-turn memory pattern from Lesson 4.
"""

import os
from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

load_dotenv()
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError("Missing OPENAI_API_KEY. Check your .env file.")

client = OpenAI(api_key=api_key)

SYSTEM_PROMPT = """
You are Orbit, a friendly AI study companion aboard the Lumexa space station.

PERSONA:
- Warm, encouraging tone, like a patient older sibling.
- At most one light space metaphor per response.
- Keep responses concise: 3-5 sentences unless asked for more detail.

PURPOSE:
- Help students understand school subjects by guiding their thinking.
- Ask a guiding question or give a hint before a direct answer, unless
  the student explicitly asks for the answer after trying.

BOUNDARIES:
- Never write full graded assignments or essays for the student.
- If asked about something unrelated to schoolwork, redirect politely.
- If unsure about a fact, say so honestly instead of guessing.

Stay in character as Orbit for the entire conversation.
"""

conversation_history = [{"role": "system", "content": SYSTEM_PROMPT}]


def get_ai_reply(history: list) -> str:
    try:
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=history,
            temperature=0.6,
            max_tokens=250,
        )
        return response.choices[0].message.content
    except OpenAIError as e:
        return f"Mission control, we hit an error: {e}"


def main():
    print("=== Orbit the Study Buddy (type 'exit' to end) ===")
    while True:
        user_input = input("You: ").strip()
        if user_input.lower() in ("exit", "quit"):
            print("Orbit: Great work today, cadet. See you next session!")
            break
        conversation_history.append({"role": "user", "content": user_input})
        reply = get_ai_reply(conversation_history)
        conversation_history.append({"role": "assistant", "content": reply})
        print(f"Orbit: {reply}")


if __name__ == "__main__":
    main()
```

### Testing Persona Consistency

A persona is only good if it holds up under pressure. Deliberately test it with:

1. **An off-topic question** ("What's your favorite movie?") — does it redirect gracefully per its boundaries, rather than breaking character?
2. **A boundary-pushing request** ("Just write my whole essay for me") — does it stick to its "guide, don't do it for you" purpose?
3. **A long conversation** — does the tone/persona stay consistent by turn 10, or does it drift back to a generic assistant voice? If it drifts, the fix is usually to make the system prompt *more specific*, not longer and vaguer.

### Personas Are Guardrails, Not Guarantees

Important honesty check for students: a system prompt strongly *influences* behavior but does not guarantee it 100% of the time, especially against a determined adversarial user. For a K-12 learning project this level of guardrail is appropriate and instructive, but production systems handling sensitive contexts add additional safety layers beyond the prompt alone (content moderation, output filtering) — worth knowing exists, even if out of scope for this course.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Persona** | The style, tone, and character traits an assistant consistently expresses. |
| **Purpose** | The specific job or domain an assistant is designed to help with. |
| **Guardrail** | An explicit rule in a system prompt defining what the assistant should refuse or redirect. |
| **Persona consistency** | How reliably an assistant maintains its defined tone/behavior across a long conversation. |
| **In character** | Behavior that matches the defined persona rather than reverting to a generic assistant voice. |

---

## Instructor-Guided Coding

1. Live-build the `STUDY_BUDDY_SYSTEM_PROMPT` piece by piece: start with just a name, run it, show the flat/generic result; add tone; add purpose; add boundaries — rerun after each addition so students see persona sharpen in real time.
2. Deliberately try to break the persona live: ask an off-topic question, then a "just do my homework for me" request. Discuss the response and adjust the guardrail wording if it doesn't redirect well.
3. Swap the persona entirely (e.g., to a strict "no-nonsense flight instructor" tone) using the same purpose/boundaries structure, showing how persona and purpose are independently adjustable.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Design Your Crewmate**

1. Design your own original persona for a Lumexa AI assistant with a purpose of your choosing (not the Study Buddy — pick something else: a fitness coach, a creative writing partner, a coding helper, a trivia host, etc.).
2. Write a full system prompt following the five-part structure (name/role, tone/style, domain/purpose, boundaries, behavioral rules).
3. Build a working chat script using this system prompt, reusing the `conversation_history` memory pattern from Lesson 4.
4. Test your bot with: a normal on-topic question, an off-topic question, and a boundary-pushing request. Record all three responses.
5. Write a short reflection (4-6 sentences) on whether your guardrails held up, and if not, how you'd rewrite the prompt to fix it.

---

## Common Mistakes

- **Vague persona instructions** ("be nice and helpful") that don't survive more than one or two turns before the bot reverts to a generic tone.
- **Defining persona but forgetting purpose** — a fun-sounding bot that doesn't actually help with anything specific.
- **Guardrails that are too soft** ("try not to write full essays") instead of explicit and firm ("never write full graded assignments").
- **Overloading the system prompt with contradictory instructions**, confusing the model about which rule takes priority.
- **Not testing edge cases** — only ever asking on-topic questions and assuming the persona is solid.

---

## Check Your Understanding

1. What's the difference between persona and purpose, and why do you need both?
2. Name the five components of a strong persona/purpose system prompt.
3. Why should boundaries be written explicitly and firmly rather than softly?
4. How would you test whether a persona holds up across a long conversation?
5. Why doesn't a system prompt "guarantee" behavior 100% of the time?

---

## Mini Quiz

1. "Persona" refers to:
 a) The specific job an assistant does
 b) The tone, style, and character traits an assistant expresses
 c) The API key used
 d) The temperature setting

2. A guardrail in a system prompt is:
 a) A rule defining what the assistant should refuse or redirect
 b) A way to increase token limits
 c) A type of API error
 d) A method for storing conversation history

3. Which is the best example of a firm, explicit guardrail?
 a) "Try to avoid writing essays sometimes."
 b) "Never write full graded assignments or essays for the student."
 c) "Be helpful."
 d) "Answer questions."

4. Why might a persona "drift" back to a generic assistant voice over a long conversation?
 a) The temperature always increases automatically
 b) The system prompt wasn't specific enough to reinforce persona consistently
 c) The API caches only the first message
 d) Persona drift is impossible

5. A system prompt's influence on behavior is:
 a) A 100% guarantee against any deviation
 b) A strong but not absolute influence, especially against adversarial prompting
 c) Irrelevant to output
 d) Only relevant to temperature

**Answer Key:** 1-b, 2-a, 3-b, 4-b, 5-b

---

## Lesson Recap

Today you learned to design a complete AI persona — not just a name and tone, but a real purpose with explicit boundaries — and you tested that persona under pressure with off-topic and boundary-pushing questions. You now understand persona and purpose as two distinct, complementary design choices, and you combined this with Lesson 4's memory system to build a coherent character across a full conversation.

---

## Homework / Extension Mission

Design and fully implement one more original persona/purpose bot (different from your in-class Explorer Challenge one), complete with a five-part system prompt and a working multi-turn chat script. Write a one-paragraph "character bio" for your assistant as if introducing it in a Lumexa crew roster.

**Extension (optional):** Add a guardrail-testing suite: a list of 5 tricky test prompts (off-topic, boundary-pushing, ambiguous) that you run automatically through your bot in a loop, printing each response so you can review persona consistency at a glance.

---

## Portfolio Connection

This lesson directly produces the **personality and purpose design** for all three of your portfolio projects: the recipe-focused, dietary-aware persona in your **Recipe Chatbot with Memory**, the "guide, don't just give answers" tutoring persona in your **Study Assistant Bot**, and (in a lighter, more creative-license form) the narrative voice you'll configure in your **Creative Story Generator**. The five-part system prompt structure you practiced today is exactly what you'll write for each of those projects' `README`-documented personas.
