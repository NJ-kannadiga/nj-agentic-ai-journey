# Python for AI

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
Python is the main language of AI. Almost every model SDK (OpenAI, Anthropic, Gemini), framework (LangChain, LlamaIndex, LangGraph, CrewAI) and ML library (PyTorch, NumPy, Hugging Face) is written for Python first.

"Python for AI" does **not** mean all of Python. It means the subset I use every day when building LLM apps and agents:
- core data types: `str`, `list`, `dict`, `tuple`, `set`
- functions, `*args` / `**kwargs`, default arguments
- comprehensions, f-strings, slicing
- classes and dataclasses (to model messages, tools, agent state)
- modules, packages and virtual environments
- reading and writing files and JSON
- error handling with `try / except`
- generators and `yield` (used for streaming tokens)
- decorators (used to register tools: `@tool`, `@app.get`)
- type hints (covered properly in note 03)

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Every SDK is Python first.** New features land in the Python SDK before anything else.
- **LLM data is just dicts and lists.** A chat history is `list[dict]`, a tool call is a `dict`, and a JSON response is parsed into a `dict`. If I'm fast with dicts, I'm fast with LLMs.
- **Agents are loops plus functions.** An agent is a `while` loop that calls the model, checks if it asked for a tool, runs a Python function, and appends the result to the message list.
- **Tools are Python functions.** Frameworks read the function name, docstring and type hints to build the tool schema the model sees.
- **Streaming uses generators.** Tokens arrive one by one, and `for chunk in stream:` is just a generator.
- **Glue code.** Most AI engineering is glue: load docs, clean text, call the API, parse the output, save results. That's plain Python.

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/01-python-for-ai.png) -->

**The mental model: an LLM app is a Python program that moves data in and out of a model.**

```
user input (str)
     │
     ▼
build messages  ──►  list[dict]  e.g. [{"role": "user", "content": "..."}]
     │
     ▼
call model API  ──►  response object / dict
     │
     ▼
parse output    ──►  str, dict (JSON), or tool call
     │
     ├── tool call? ──► run Python function ──► append result ──► call model again
     │
     ▼
final answer (str)
```

**The Python features that show up again and again:**

| Feature | Where I'll see it in AI code |
|---|---|
| `dict` / `list` | messages, JSON payloads, tool arguments |
| f-strings | building prompts: `f"Summarize this:\n{text}"` |
| list comprehension | `[chunk.text for chunk in chunks]` |
| functions with docstrings | tools for agents |
| `**kwargs` | passing model params: `client.create(**params)` |
| dataclasses / classes | agent state, config, documents |
| generators (`yield`) | streaming responses, lazy loading of big files |
| decorators | `@tool`, `@app.post("/chat")`, `@retry` |
| context managers (`with`) | opening files, HTTP clients, DB sessions |
| `try / except` | API errors, bad JSON from the model |
| `os.environ` / `.env` | API keys |
| `pathlib` | reading document folders for RAG |

**Packages and environments:**
- `venv` (or `uv`) creates an isolated environment per project.
- `pip install` / `uv add` installs packages.
- `requirements.txt` or `pyproject.toml` lists dependencies so others can reproduce my setup.

## 4. Code example
```python
# A tiny "fake agent" that shows the core Python patterns used in real agent code.
import json
from dataclasses import dataclass, field


# 1. Tools are just functions with a docstring and type hints
def get_weather(city: str) -> str:
    """Return the current weather for a city."""
    fake_db = {"pune": "28°C, sunny", "delhi": "18°C, foggy"}
    return fake_db.get(city.lower(), "unknown")


def add(a: float, b: float) -> float:
    """Add two numbers."""
    return a + b


TOOLS = {fn.__name__: fn for fn in [get_weather, add]}  # dict comprehension


# 2. State lives in a dataclass
@dataclass
class AgentState:
    messages: list[dict] = field(default_factory=list)

    def add(self, role: str, content: str) -> None:
        self.messages.append({"role": role, "content": content})


# 3. A generator that "streams" text word by word
def stream_text(text: str):
    for word in text.split():
        yield word + " "


# 4. Pretend model output: a JSON tool call (real models return something similar)
fake_model_output = '{"tool": "get_weather", "args": {"city": "Pune"}}'

state = AgentState()
state.add("user", "What's the weather in Pune?")

try:
    call = json.loads(fake_model_output)          # str -> dict
    fn = TOOLS[call["tool"]]
    result = fn(**call["args"])                   # unpack dict into kwargs
    state.add("tool", result)
except (json.JSONDecodeError, KeyError) as e:
    state.add("tool", f"error: {e}")

answer = f"The weather in Pune is {state.messages[-1]['content']}."
for token in stream_text(answer):
    print(token, end="", flush=True)
print()
print(json.dumps(state.messages, indent=2, ensure_ascii=False))
```

## 5. Key points to remember
- I don't need "all of Python". I need dicts, lists, functions, classes, generators, decorators, errors, files/JSON and environments.
- An LLM chat is a `list[dict]`. Master dict and list operations.
- `**kwargs` and `fn(**my_dict)` are everywhere in SDK and tool code.
- Generators (`yield`) are how streaming works.
- Docstrings and type hints aren't decoration. Frameworks turn them into tool schemas the model reads.
- Always use a virtual environment per project and pin dependencies.
- Never hardcode API keys. Read them from environment variables.
- `json.loads` can fail on model output, so always wrap it in `try / except`.

## 6. Common mistakes / interview questions
**Mistakes**
- Mutable default arguments: `def f(history=[])` shares the same list across calls. Use `None` or `field(default_factory=list)`.
- Installing packages globally instead of in a venv, which leads to version conflicts.
- Forgetting `encoding="utf-8"` when reading files, which breaks on Windows with non-English text.
- Trusting model output to be valid JSON without validation.
- Mixing `print` debugging everywhere instead of using `logging`.
- Modifying a list while iterating over it.

**Interview questions**
- What is the difference between a list and a tuple? A list and a generator?
- What does `*args` / `**kwargs` do? How do you unpack a dict into function arguments?
- What is a decorator? Write one that times a function.
- What is a context manager? How does `with` work (`__enter__` / `__exit__`)?
- Why are mutable default arguments dangerous?
- What is the GIL, and why does it matter less for LLM apps (they're I/O-bound)?
- What is the difference between `is` and `==`?
- How would you represent a chat conversation in Python?

## 7. Resources I used
- [Official Python Tutorial](https://docs.python.org/3/tutorial/)
- [Real Python](https://realpython.com/): generators, decorators, dataclasses
- [Python `json` module docs](https://docs.python.org/3/library/json.html)
- [uv: fast Python package manager](https://docs.astral.sh/uv/)
- *Fluent Python* by Luciano Ramalho (for going deeper)

## 8. What I'll build / practice next
- [ ] Write a decorator `@timer` that prints how long a function took.
- [ ] Write a generator that reads a big text file line by line and yields chunks of 500 characters.
- [ ] Build a `Conversation` dataclass with `add_user`, `add_assistant`, `to_json`, `from_json`.
- [ ] Build a mini tool registry: register functions with a decorator, then call them by name from a dict.
- [ ] Solve 5 easy LeetCode problems with dicts and lists (Two Sum, Valid Anagram, Group Anagrams...).
