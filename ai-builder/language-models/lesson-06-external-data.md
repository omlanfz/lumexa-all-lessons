# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 6 of 8
**Title:** Connecting to External Data Sources
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Python, OpenAI Python SDK (v1.x), file I/O, `requests` (optional)

---

## Mission Brief

Cadet, recall Lesson 1's honest limitation: **knowledge cutoff**. The model only knows what was in its training data, up to a certain date, and it has no built-in way to check today's weather, your school's specific class schedule, or a document you just wrote. Today's mission: give your AI access to fresh, external, ground-truth information by feeding it directly into the prompt. This technique is the foundation of a pattern called **Retrieval-Augmented Generation (RAG)**, and it's how real production assistants stay accurate and up to date.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Explain why LLMs need external data injection to answer questions beyond their training data or about private/local information.
2. Read data from a local file (text or JSON) and insert it into a prompt as context.
3. Explain the basic idea of Retrieval-Augmented Generation (RAG) at a conceptual level.
4. Build a simple "grounded" Q&A script that only answers based on provided data.
5. Explain the difference between giving a model data directly in-context versus letting it hallucinate an answer without that data.

---

## Prerequisites

- Completion of Lessons 1–5.
- Basic file I/O in Python (`open()`, reading text files).
- Comfort with `client.chat.completions.create()` and system/user prompt design.

---

## Concept Explanation

### The Core Problem: The Model Doesn't Know What You Know

Suppose you want a bot that can answer questions about your school's specific lunch menu, a story you wrote, or your class notes. The model has never seen this information — it isn't on the internet, and even if it were, the model's training data has a cutoff date. Two possible outcomes if you just ask it directly:

1. It admits it doesn't know (good, but not useful).
2. It **hallucinates** a plausible-sounding but false answer (bad, and dangerous if the user trusts it).

The fix isn't to "teach" the model new facts (that would require expensive retraining). Instead, we **give it the facts directly inside the prompt**, every time we ask. This is called **grounding** the model's response in provided context.

### The Simplest Form: Stuffing Context Into the Prompt

```python
"""
grounded_qa.py
Answers questions using ONLY information provided in a local data file —
not the model's general training knowledge.
"""

import os
from dotenv import load_dotenv
from openai import OpenAI, OpenAIError

load_dotenv()
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError("Missing OPENAI_API_KEY. Check your .env file.")

client = OpenAI(api_key=api_key)


def load_reference_data(filepath: str) -> str:
    """Read a local text file and return its full contents as a string."""
    with open(filepath, "r", encoding="utf-8") as f:
        return f.read()


def ask_grounded_question(reference_text: str, question: str) -> str:
    """Ask a question, instructing the model to answer only from reference_text."""
    system_prompt = (
        "You are a Lumexa mission data officer. Answer the user's question "
        "using ONLY the reference information provided below. If the answer "
        "is not contained in the reference information, say clearly: "
        "'I don't have that information in the mission log.' Do not use "
        "outside knowledge or guess.\n\n"
        f"REFERENCE INFORMATION:\n{reference_text}"
    )
    try:
        response = client.chat.completions.create(
            model="gpt-4o-mini",
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": question},
            ],
            temperature=0.2,  # low temperature for factual accuracy
            max_tokens=200,
        )
        return response.choices[0].message.content
    except OpenAIError as e:
        return f"Mission control, we hit an error: {e}"


if __name__ == "__main__":
    # mission_log.txt is a local file with custom, model-unknown data
    reference = load_reference_data("mission_log.txt")

    print("=== Lumexa Grounded Q&A (type 'exit' to end) ===")
    while True:
        question = input("Ask about the mission log: ").strip()
        if question.lower() in ("exit", "quit"):
            break
        answer = ask_grounded_question(reference, question)
        print(f"Data Officer: {answer}\n")
```

An example `mission_log.txt` to pair with it:

```
LUMEXA MISSION LOG — STATION ALPHA
Mission Commander: Dr. Priya Raman
Launch Date: March 3, 2026
Crew Size: 6
Current Objective: Deploy the new solar array on the station's south wing.
Status: On schedule, 72% complete.
Notes: A minor oxygen scrubber malfunction was repaired on day 14.
```

Now the bot can correctly answer "Who is the mission commander?" or "What's the current objective?" — facts it could not possibly know from training data, because we gave them to it directly, and it correctly declines to guess about anything not in the file (e.g., "What's the crew's favorite snack?").

### Why Low Temperature Matters Here

For factual, grounded Q&A, we deliberately use a **low temperature** (`0.1`–`0.3`). Creative variation is the opposite of what we want when accuracy against a specific reference matters.

### Structured Data: Working with JSON

External data is often structured, not just plain text. Here's the same pattern using JSON:

```python
import json

def load_json_data(filepath: str) -> dict:
    """Read and parse a local JSON file into a Python dictionary."""
    with open(filepath, "r", encoding="utf-8") as f:
        return json.load(f)


def ask_about_json_data(data: dict, question: str) -> str:
    """Convert dict data to a readable string and ground the model's answer in it."""
    data_as_text = json.dumps(data, indent=2)
    system_prompt = (
        "You are a Lumexa data officer. Answer using ONLY the JSON data "
        "below. If the answer isn't in the data, say so honestly.\n\n"
        f"DATA:\n{data_as_text}"
    )
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": question},
        ],
        temperature=0.2,
        max_tokens=200,
    )
    return response.choices[0].message.content
```

Example `crew_roster.json`:

```json
{
  "crew": [
    {"name": "Dr. Priya Raman", "role": "Mission Commander"},
    {"name": "Alex Chen", "role": "Systems Engineer"},
    {"name": "Sofia Mendes", "role": "Science Officer"}
  ]
}
```

### What Is Retrieval-Augmented Generation (RAG)? (Conceptual Overview)

The pattern above — "find relevant information, then paste it into the prompt" — is the foundation of **RAG**. In a small script, we manually "retrieve" by loading one whole file. In a large real-world system with thousands of documents, an actual **retrieval step** searches a database (often a *vector database* storing document embeddings) to find just the few most relevant chunks of text, and only *those* get inserted into the prompt — because you can't fit thousands of documents inside one context window.

The three-step RAG pattern, at a conceptual level:

1. **Retrieve** — search your data source for the most relevant piece(s) of information given the user's question.
2. **Augment** — insert that retrieved information into the prompt as context (exactly what we did above, just simplified to "load the whole file").
3. **Generate** — let the model answer using that grounded context.

You won't build a full vector-search RAG pipeline in this course, but understanding this three-step pattern means you already understand the core idea behind how real production AI assistants (customer support bots, internal company chatbots, research assistants) stay both current and accurate.

### A Note on Live External Data (APIs)

The same "stuff it into the prompt" technique works for real-time data too — for example, fetching current weather from a weather API with Python's `requests` library, then handing that text to the model to summarize or answer questions about. The mechanism is identical: fetch fresh information from outside the model, format it as text, insert it into the system or user prompt, and let the model reason over it.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Grounding** | Providing an LLM with specific reference information in-context so its answer is based on real, given facts rather than general training knowledge. |
| **Retrieval-Augmented Generation (RAG)** | A pattern where relevant external information is retrieved and inserted into a prompt before generation, keeping answers current and accurate. |
| **Context injection** | The technique of inserting external text/data directly into a system or user prompt. |
| **Vector database** | A database that stores text as numeric embeddings, enabling search by meaning/similarity — used in large-scale RAG systems (conceptual, not built in this course). |

---

## Instructor-Guided Coding

1. Live-build `grounded_qa.py` and `mission_log.txt` together, then ask a question the file answers, and a question it does NOT answer (e.g., "What's the crew's favorite pizza topping?") to demonstrate the honest "I don't have that information" response.
2. Deliberately remove the "answer ONLY from reference information" instruction and rerun the same unanswerable question — show the model may now hallucinate a plausible but fabricated answer. This is a powerful, concrete demonstration of grounding's importance.
3. Convert to the JSON example live, showing `json.dumps()` turning structured data into model-readable text.
4. Sketch the three-step RAG diagram (Retrieve → Augment → Generate) on the board and map each script's code to one of the three steps.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Mission Data Officer**

1. Create your own `mission_log.txt` (or JSON file) with at least 8 unique, invented facts about a fictional Lumexa mission of your design.
2. Build a working grounded Q&A script based on `grounded_qa.py`.
3. Ask it 3 questions the file *can* answer, and 2 questions it *cannot* — confirm it answers correctly and honestly declines the unanswerable ones.
4. Temporarily remove the "answer ONLY from reference information" instruction, rerun one of your unanswerable questions, and record what happens.
5. Write a short paragraph (using the term **grounding**) explaining why the instruction mattered.

---

## Common Mistakes

- **Forgetting to instruct the model to stick to the provided data** — without an explicit instruction, the model may blend in outside knowledge even when given context.
- **Using a high temperature for factual grounded Q&A**, increasing the chance of creative deviation from the source data.
- **Trying to stuff an enormous document into the prompt** and hitting context window limits — a real reason large-scale RAG needs an actual retrieval step, not just "paste everything."
- **Not testing unanswerable questions** — only testing questions the data covers, missing whether the bot handles gaps honestly.
- **Confusing "the model browsed the file" with reality** — the model never touches your file directly; your Python code reads it and pastes the text into the prompt.

---

## Check Your Understanding

1. Why can't an LLM answer questions about information created after its training cutoff, or about private local data?
2. What does "grounding" mean in the context of prompting?
3. Describe the three steps of the RAG pattern in your own words.
4. Why is low temperature preferred for grounded factual Q&A?
5. What happens if you remove the "answer only from provided data" instruction from a grounded prompt?

---

## Mini Quiz

1. "Grounding" a model's response means:
 a) Turning off the API
 b) Providing specific reference information in-context so the answer is based on real given facts
 c) Increasing temperature to maximum
 d) Training a new model from scratch

2. RAG stands for:
 a) Random Answer Generation
 b) Retrieval-Augmented Generation
 c) Recursive API Gateway
 d) Rapid Assistant Growth

3. The three steps of RAG, in order, are:
 a) Generate, Retrieve, Augment
 b) Retrieve, Augment, Generate
 c) Augment, Generate, Retrieve
 d) Retrieve, Generate, Augment

4. For factual, grounded Q&A, which temperature setting is generally preferred?
 a) 2.0
 b) 1.5
 c) A low value like 0.2
 d) Temperature doesn't matter for factual tasks

5. If you don't instruct the model to only use provided reference data, what risk increases?
 a) The API will refuse to respond
 b) The model may blend in outside knowledge or hallucinate for unanswerable questions
 c) The context window doubles
 d) Token cost decreases

**Answer Key:** 1-b (LLMs lack post-cutoff/private data by design), 2-b, 3-b, 4-c, 5-b

---

## Lesson Recap

Today you solved one of the LLM's biggest limitations from Lesson 1: knowledge cutoff and lack of access to private data. You learned to ground model responses by loading external text or JSON data and injecting it directly into the prompt, you saw firsthand how an explicit "only use this data" instruction prevents hallucination, and you learned the conceptual three-step RAG pattern (Retrieve, Augment, Generate) that underlies real production AI systems.

---

## Homework / Extension Mission

Build `study_notes_qa.py`: create a text file containing your own class notes on any subject (at least 15 sentences of real content), then build a grounded Q&A bot that only answers using those notes, correctly declining questions outside them. Test with at least 5 questions (3 answerable, 2 not) and record all results in a comments block at the top of your script.

**Extension (optional):** Extend your script to load *two* separate files (e.g., notes from two different subjects) and let the user pick which one to query at the start of the program, demonstrating basic multi-source retrieval selection.

---

## Portfolio Connection

Grounding techniques from this lesson upgrade your **Study Assistant Bot**, letting it reference specific class material rather than only general knowledge, and could extend your **Recipe Chatbot with Memory** to pull from a curated local recipe database instead of relying purely on the model's general training knowledge. This lesson's honesty principle — "say you don't know rather than guess" — should also be written directly into every system prompt you build from here forward.
