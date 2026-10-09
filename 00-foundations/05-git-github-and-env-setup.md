# Git Github and Env Setup

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
- **Git:** a version control tool on my computer. It takes snapshots (**commits**) of my project so I can go back in time, see what changed, and work on features in **branches** without breaking the main code.
- **GitHub:** a website that hosts Git repositories online. It's my backup, my portfolio, and where teams collaborate with **pull requests (PRs)**, issues and CI (GitHub Actions).
- **Env setup:** preparing a clean, reproducible place to run my code:
  - a **virtual environment** (isolated Python packages per project),
  - a **dependency file** (`requirements.txt` / `pyproject.toml`),
  - **environment variables** for secrets (API keys) in a `.env` file that is **never committed**.

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Portfolio.** Recruiters look at GitHub. This repo *is* my proof of learning.
- **Safety net.** Prompt and agent changes break things in surprising ways. Git lets me compare and roll back.
- **Prompts are code.** Version-controlling prompts and eval results lets me track what improved the agent.
- **API keys are money.** A leaked OpenAI/Anthropic key on GitHub gets abused within minutes. `.env` + `.gitignore` is non-negotiable.
- **AI libraries change fast.** LangChain, LlamaIndex etc. break between versions. Pinned dependencies + venvs keep projects working.
- **Deployment** (Docker, cloud, CI) all starts from a clean repo with a clear environment.

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/05-git-github-and-env-setup.png) -->

**Git's three areas + remote**

```
 working directory ──git add──► staging area ──git commit──► local repo ──git push──► GitHub (remote)
        ▲                                                         │                          │
        └──────────────────────── git checkout / restore ─────────┘◄────── git pull ─────────┘
```

**Daily Git commands**

| Command | What it does |
|---|---|
| `git init` / `git clone <url>` | start a repo / copy one from GitHub |
| `git status` | what changed? (run this all the time) |
| `git add <file>` / `git add .` | stage changes |
| `git commit -m "msg"` | save a snapshot |
| `git log --oneline` | history |
| `git diff` | see unstaged changes |
| `git switch -c feature/rag` | create + switch to a new branch |
| `git switch main` | go back to main |
| `git merge feature/rag` | merge a branch into the current one |
| `git push -u origin feature/rag` | upload a branch to GitHub |
| `git pull` | download + merge remote changes |
| `git restore <file>` | discard local changes to a file |
| `git stash` / `git stash pop` | temporarily shelve changes |

**Feature-branch workflow (the GitHub flow)**
1. `git switch -c feature/x`
2. Code → `git add` → `git commit` (small, meaningful commits)
3. `git push -u origin feature/x`
4. Open a **Pull Request** on GitHub → review → merge into `main`
5. `git switch main && git pull`

**Python environment setup (Windows / PowerShell)**

```powershell
# Option A: built-in venv + pip
python -m venv .venv
.venv\Scripts\Activate.ps1            # macOS/Linux: source .venv/bin/activate
pip install httpx pydantic python-dotenv
pip freeze > requirements.txt

# Option B: uv (much faster, modern)
uv init
uv add httpx pydantic python-dotenv   # creates .venv + pyproject.toml + uv.lock
uv run main.py
```

**Secrets setup**

```
.env               ← real keys, NEVER committed
.env.example       ← same variable names, empty values, committed so others know what to set
.gitignore         ← contains .env, .venv/, __pycache__/, *.ipynb_checkpoints
```

## 4. Code example
```python
# .env file (not committed):
#   OPENAI_API_KEY=sk-...
#   ANTHROPIC_API_KEY=sk-ant-...
#   MODEL_NAME=some-model

# pip install python-dotenv
import os
from dotenv import load_dotenv

load_dotenv()  # reads .env into environment variables

api_key = os.getenv("OPENAI_API_KEY")
model = os.getenv("MODEL_NAME", "default-model")   # with a fallback

if not api_key:
    raise RuntimeError("OPENAI_API_KEY is missing. Copy .env.example to .env and fill it in.")

print(f"Using model={model}, key starts with {api_key[:5]}...")  # never print the full key
```

A good starter `.gitignore` for AI projects:
```gitignore
# secrets
.env
.env.*
!.env.example

# python
.venv/
__pycache__/
*.pyc

# notebooks / data
.ipynb_checkpoints/
data/raw/
*.sqlite
chroma_db/
```

## 5. Key points to remember
- `git status` early and often.
- Commit small, focused changes with clear messages ("Add retry to LLM client", not "update").
- Work on branches; keep `main` working.
- `.env` in `.gitignore` **before** the first commit. Commit `.env.example` instead.
- If a key is ever pushed, **revoke/rotate it immediately**. Deleting the commit isn't enough, since history and forks keep it.
- One virtual environment per project. Never install AI libs globally.
- Pin dependencies (`requirements.txt`, `uv.lock`) so the project works in 6 months.
- Don't commit large data, model weights or vector DB files. Use `.gitignore` (or Git LFS if needed).

## 6. Common mistakes / interview questions
**Mistakes**
- Committing `.env` or hardcoding keys in notebooks (notebook outputs can leak keys too!).
- Committing `.venv/` or `node_modules/`.
- Giant commits with mixed changes, which are hard to review and hard to revert.
- Working directly on `main` and force-pushing.
- Running code with the wrong Python interpreter (VS Code: "Python: Select Interpreter" → `.venv`).
- On Windows: PowerShell blocks `Activate.ps1`. Fix with `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`.

**Interview questions**
- What's the difference between Git and GitHub?
- `git merge` vs `git rebase`?
- `git fetch` vs `git pull`?
- How do you undo the last commit? (`git revert` vs `git reset`)
- How do you resolve a merge conflict?
- What would you do if you accidentally pushed an API key?
- How do you manage secrets in development vs production?
- Why use virtual environments?

## 7. Resources I used
- [Pro Git book (free)](https://git-scm.com/book/en/v2)
- [GitHub Docs: Hello World / GitHub flow](https://docs.github.com/en/get-started/start-your-journey/hello-world)
- [Learn Git Branching (interactive)](https://learngitbranching.js.org/)
- [Python docs: venv](https://docs.python.org/3/library/venv.html)
- [uv docs](https://docs.astral.sh/uv/)
- [python-dotenv](https://pypi.org/project/python-dotenv/)
- [GitHub: secret scanning](https://docs.github.com/en/code-security/secret-scanning)

## 8. What I'll build / practice next
- [ ] Add a proper `.gitignore` and `.env.example` to this repo.
- [ ] Create a branch for each section of this journey, open a PR, and merge it.
- [ ] Set up a `uv` project with `httpx`, `pydantic`, `python-dotenv` and run a script with `uv run`.
- [ ] Finish the first 10 levels of Learn Git Branching.
- [ ] Practice resolving a merge conflict on purpose.
