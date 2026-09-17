# Lumexa Mission Log: Language Models

**Course / Path:** AI & Language Models
**Lesson:** 8 of 8
**Title:** Deployment and Sharing
**Duration:** ~60 minutes
**Difficulty:** Intermediate
**Technology:** Streamlit Community Cloud, GitHub, `requirements.txt`, `.gitignore`

---

## Mission Brief

Cadet, this is your final mission in the Language Models track. Your ship is built, tested, and flight-worthy — now it's time to launch it into orbit where the rest of the galaxy (friends, family, teachers, your future self) can actually use it. Today we deploy a Streamlit app to the real internet, safely, using GitHub and Streamlit Community Cloud, without ever exposing your secret API key.

---

## Learning Objectives

By the end of this lesson, students will be able to:

1. Prepare a project for deployment: `requirements.txt`, `.gitignore`, and a clean folder structure.
2. Push a project to GitHub while correctly excluding secrets.
3. Deploy a Streamlit app to Streamlit Community Cloud.
4. Configure API keys securely in a cloud environment using platform secrets management (not `.env` files, which don't travel with your repo).
5. Explain what to do if a key is accidentally exposed (rotate it immediately).

---

## Prerequisites

- Completion of Lessons 1–7.
- A working Streamlit app from Lesson 7 (or any project in this course).
- A free GitHub account and a free Streamlit Community Cloud account.

---

## Concept Explanation

### Why Deployment Is a Different Beast Than Local Development

So far, every project has run on your own computer, reading secrets from a local `.env` file. But `.env` files are deliberately excluded from version control — meaning when your code goes to GitHub (and then to a hosting platform), **the `.env` file does not travel with it.** This is correct and intentional — but it means we need a new way to give the deployed app its API key: **platform secrets management**.

### Step 1: Prepare Your Project Folder

A deployable project needs a clean, minimal structure:

```
my-lumexa-bot/
├── app.py
├── requirements.txt
├── .env                (local only — never committed)
├── .env.example         (committed — template only)
└── .gitignore
```

**`requirements.txt`** lists every package your app needs, so the hosting platform knows what to install:

```
openai>=1.0.0
python-dotenv>=1.0.0
streamlit>=1.30.0
```

**`.gitignore`** tells Git which files to never track/commit:

```
.env
__pycache__/
*.pyc
.venv/
venv/
```

The single most important line in that file for this course is `.env` — if it's missing, your real secret key could end up on GitHub, publicly visible to anyone, forever (even if you delete it later, it often remains in the repo's history).

### Step 2: Push to GitHub Safely

```bash
git init
git add .
git status   # <- ALWAYS check this before committing!
```

**Before running `git commit`, always check `git status` and confirm `.env` is NOT listed** as a file to be committed. If it appears, something is wrong with your `.gitignore` — fix it before proceeding, never commit anyway "just this once."

```bash
git commit -m "Initial commit: Lumexa AI chat app"
git branch -M main
git remote add origin https://github.com/your-username/my-lumexa-bot.git
git push -u origin main
```

### Step 3: Deploy to Streamlit Community Cloud

1. Go to Streamlit Community Cloud and sign in (typically with your GitHub account).
2. Click "New app," select your repository, branch, and the main file path (`app.py`).
3. Before your first deploy (or any time after, via app settings), open the app's **Secrets** management panel. This is where you paste your real API key — it is stored securely by the platform, never inside your repository.

In the Secrets panel, you'd enter something like:

```toml
OPENAI_API_KEY = "sk-your-real-secret-key-here"
```

4. Streamlit automatically makes anything in this Secrets panel available through `st.secrets`, and (helpfully) also injects it into `os.environ`, so **the exact same `os.environ.get("OPENAI_API_KEY")` code you've used all course still works, unchanged, in the deployed app** — no code rewrite required.

### Adapting Your Code for Both Local and Deployed Use

A robust pattern that works identically in both environments:

```python
import os
from dotenv import load_dotenv

# Locally: load_dotenv() reads your local .env file.
# On Streamlit Cloud: there is no .env file, but this call is harmless —
# it simply finds nothing to load and moves on, and the platform's
# Secrets panel already populated os.environ for you.
load_dotenv()

api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise ValueError(
        "OPENAI_API_KEY not found. Locally: check your .env file. "
        "Deployed: check your app's Secrets settings."
    )
```

This single pattern — `load_dotenv()` + `os.environ.get(...)` + a clear error message — is what makes your code **portable** between your laptop and the cloud, with zero changes needed.

### Step 4: Share and Maintain Your App

Once deployed, Streamlit Community Cloud gives you a public URL (something like `https://your-app-name.streamlit.app`) that you can share with anyone. Any time you push new commits to your GitHub repo's main branch, the deployed app automatically updates — this is a simple form of **continuous deployment**.

### What To Do If a Key Ever Leaks

Mistakes happen — a key gets committed by accident, pasted in a public chat, or shown on a screen share. The correct emergency response is always the same:

1. **Immediately revoke/rotate the key** in your OpenAI account dashboard (generate a new key, delete the old one). Do this the moment you realize — don't wait.
2. Update your local `.env` and your platform's Secrets panel with the new key.
3. If the key was committed to a public GitHub repo, know that simply deleting the file in a new commit is **not enough** — the old key remains visible in the repository's commit history unless that history is rewritten (an advanced Git operation). The safe assumption once a key is exposed publicly is: **that key is burned — always rotate it**, rather than trying to "clean up" the history.

This is why we've emphasized `.env` + `.gitignore` from Lesson 2 onward: prevention is far easier than cleanup.

---

## Key Vocabulary

| Term | Definition |
|---|---|
| **Deployment** | The process of making an application available to run somewhere other than your own computer, typically publicly accessible. |
| **`requirements.txt`** | A file listing a Python project's dependencies so a hosting platform can install them. |
| **`.gitignore`** | A file telling Git which files/folders to exclude from version control. |
| **Platform secrets management** | A hosting platform's secure system for storing sensitive values (like API keys) separately from your code repository. |
| **`st.secrets`** | Streamlit's built-in interface for accessing values configured in the platform's Secrets panel. |
| **Continuous deployment** | Automatically updating a live deployed app whenever new code is pushed to the connected repository. |
| **Key rotation** | Revoking a compromised or old API key and replacing it with a newly generated one. |

---

## Instructor-Guided Coding

1. Walk through preparing a sample project's folder structure together: create `requirements.txt` and `.gitignore` live, and run `git status` to confirm `.env` is excluded before any commit happens.
2. Push a sample (non-sensitive) demo project to a real or simulated GitHub repo, narrating each command.
3. Walk through the Streamlit Community Cloud deployment flow live (screen-share the dashboard), specifically pausing at the Secrets panel to reinforce: "this is the only place your real key goes, once deployed."
4. Discuss, as a class discussion (not hands-on): "What would you do right now if you just noticed your API key was committed to a public repo?" Walk through the key rotation steps together.

---

## Student Mission / Guided Challenge

**Explorer Challenge: Launch Sequence**

1. Take any Streamlit project you've built in this course (Lesson 7's app or a portfolio project).
2. Prepare it for deployment: write a correct `requirements.txt` and `.gitignore`.
3. Initialize a Git repository, run `git status`, and confirm `.env` does not appear before your first commit.
4. Push the repository to your own GitHub account.
5. Deploy it on Streamlit Community Cloud, correctly configuring your API key in the Secrets panel (not in code).
6. Test the live, public URL to confirm the deployed app works, then share the link with a classmate to test independently.
7. Write a short "mission debrief" (4-6 sentences) describing any issues you hit during deployment and how you solved them.

---

## Common Mistakes

- **Committing `.env` because `.gitignore` was added too late** (after the first commit already tracked it) — once a file is tracked, adding it to `.gitignore` afterward does not automatically untrack it; it must be explicitly removed from tracking.
- **Forgetting to add a package to `requirements.txt`** — the app works locally (where the package happens to already be installed) but crashes on deployment with a `ModuleNotFoundError`.
- **Pasting the API key directly into `app.py` "just to test deployment quickly"** — this defeats the entire security model for the sake of convenience, exactly the mistake this course has warned against since Lesson 2.
- **Assuming deleting a file in a later commit removes it from history** — it doesn't; the safe assumption after any public exposure is always to rotate the key.
- **Not testing the live deployed app** after deployment, missing environment-specific issues (like a missing secret) that didn't show up locally.

---

## Check Your Understanding

1. Why doesn't `.env` travel with your project when you push it to GitHub, and why is that actually correct behavior?
2. What is `requirements.txt` for, and what happens if a needed package is missing from it?
3. Where does your real API key live once an app is deployed to Streamlit Community Cloud?
4. Why is `load_dotenv()` still safe to call even in a deployed environment with no `.env` file?
5. If an API key is accidentally exposed publicly, what is the correct first response?

---

## Mini Quiz

1. `.gitignore` is used to:
 a) List package dependencies
 b) Tell Git which files/folders to exclude from version control
 c) Store your API key securely in the cloud
 d) Deploy your app automatically

2. On Streamlit Community Cloud, your real API key should be configured:
 a) Directly inside `app.py`
 b) In the `.env.example` file
 c) In the platform's Secrets management panel
 d) In `requirements.txt`

3. If your API key is accidentally committed to a public GitHub repository, the correct first step is to:
 a) Delete the file in a new commit and consider it resolved
 b) Immediately rotate/revoke the key
 c) Make the repository private and do nothing else
 d) Ignore it since GitHub scans for this automatically

4. `requirements.txt` is important because:
 a) It stores your API key
 b) It tells the hosting platform which packages to install
 c) It replaces `.env`
 d) It is only used for local development

5. Why does the same `os.environ.get("OPENAI_API_KEY")` code work both locally and when deployed?
 a) Because Streamlit ignores environment variables entirely
 b) Because both `.env` (locally, via `load_dotenv()`) and the platform Secrets panel (deployed) populate `os.environ` with the same variable name
 c) Because the API key is hardcoded in both cases
 d) It doesn't work in both — code must be rewritten for deployment

**Answer Key:** 1-b, 2-c, 3-c, 4-b, 5-b

---

## Lesson Recap

You completed your final mission in the Language Models track today: preparing a project for deployment with `requirements.txt` and `.gitignore`, safely pushing it to GitHub while excluding secrets, deploying it live on Streamlit Community Cloud, and configuring your API key securely through platform secrets management rather than any file in your repository. You also learned the correct emergency response if a key is ever exposed: rotate it immediately, don't rely on deleting files after the fact.

---

## Homework / Extension Mission

Deploy one full portfolio project (Recipe Chatbot, Study Assistant, or Creative Story Generator) to Streamlit Community Cloud from start to finish, and write a short "Mission Complete" report documenting: your GitHub repo URL, your live app URL, the exact steps you took, and one problem you ran into and how you fixed it.

**Extension (optional):** Add a simple `README.md` badge or link at the top of your GitHub repo pointing to your live deployed app, so anyone visiting your code can immediately try the working version.

---

## Portfolio Connection

This lesson completes the full lifecycle for all three portfolio projects — **Recipe Chatbot with Memory**, **Study Assistant Bot**, and **Creative Story Generator** — taking each from a local script to a live, shareable, publicly accessible application, deployed the same secure way real production AI products are shipped. Congratulations, cadet: you've completed the full Lumexa Language Models mission track, from understanding how transformers predict tokens all the way to launching a real AI product into the world.
