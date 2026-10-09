# Pydantic and Type Hints

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
**Type hints** are labels I put on variables, function arguments and return values to say what type they should be: `def greet(name: str) -> str:`. Python itself does **not** enforce them at runtime. They're for editors, linters (`mypy`, `pyright`) and libraries that read them.

**Pydantic** is a library that **does** enforce types at runtime. I define a class with typed fields, and Pydantic:
1. **validates** incoming data (raises a clear error if it's wrong),
2. **coerces** where sensible (`"42"` → `42`),
3. **serializes** to dict / JSON (`model_dump()`, `model_dump_json()`),
4. **generates a JSON Schema** (`model_json_schema()`).

That last one is the magic for AI: a Pydantic model can be turned into a JSON Schema the LLM must follow.

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Structured outputs.** I can ask an LLM to return data matching a Pydantic model. OpenAI, Anthropic, Gemini, `instructor` and LangChain all accept Pydantic models or their JSON Schema.
- **Validating LLM output.** Models sometimes return wrong types, missing fields or extra text. Pydantic catches it so I can retry or fix it.
- **Tool definitions.** Agent frameworks build tool schemas from type hints / Pydantic models. Good types = the model calls tools correctly.
- **Agent state.** LangGraph, PydanticAI and others use typed state objects.
- **APIs.** FastAPI uses Pydantic for request and response bodies.
- **Config.** `pydantic-settings` loads API keys and settings from `.env` with validation.

Basically: **LLMs produce text, and my code needs reliable data. Pydantic is the bridge.**

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/03-pydantic-and-type-hints.png) -->

**Type hints cheat sheet (Python 3.10+)**

| Hint | Meaning |
|---|---|
| `int`, `str`, `float`, `bool` | basic types |
| `list[str]` | list of strings |
| `dict[str, int]` | dict with str keys, int values |
| `str \| None` | string or None (optional) |
| `Literal["low", "high"]` | only these exact values |
| `tuple[int, int]` | fixed-size tuple |
| `Callable[[str], int]` | a function taking str, returning int |
| `Any` | anything (avoid when possible) |

**Pydantic flow in an LLM app**

```
Pydantic model ──► model_json_schema() ──► sent to LLM as "response format" / tool schema
                                                        │
                                                        ▼
                                              LLM returns JSON text
                                                        │
                                                        ▼
               Model.model_validate_json(text)  ──► valid?  ── yes ──► typed Python object
                                                        │
                                                        no ──► ValidationError ──► retry with error message
```

**Key Pydantic (v2) API**
- `class X(BaseModel): ...` defines a model.
- `Field(description=..., ge=0, max_length=...)` adds constraints and descriptions (descriptions go into the schema, so the LLM reads them!).
- `X(**data)` / `X.model_validate(dict)` / `X.model_validate_json(str)` parses and validates.
- `obj.model_dump()` / `obj.model_dump_json()` serializes.
- `X.model_json_schema()` returns the JSON Schema.
- `@field_validator("field")` adds custom validation logic.
- Nested models and `list[OtherModel]` work naturally.

## 4. Code example
```python
# pip install pydantic
import json
from typing import Literal
from pydantic import BaseModel, Field, ValidationError, field_validator


class ActionItem(BaseModel):
    task: str = Field(description="What needs to be done")
    owner: str | None = Field(default=None, description="Person responsible, if mentioned")
    priority: Literal["low", "medium", "high"] = "medium"


class MeetingSummary(BaseModel):
    title: str = Field(max_length=80)
    sentiment_score: float = Field(ge=-1, le=1, description="-1 negative, 1 positive")
    action_items: list[ActionItem]

    @field_validator("title")
    @classmethod
    def no_empty_title(cls, v: str) -> str:
        if not v.strip():
            raise ValueError("title cannot be empty")
        return v.strip()


# 1. The schema I would send to the LLM
print(json.dumps(MeetingSummary.model_json_schema(), indent=2)[:400], "...")

# 2. Pretend this came back from the LLM
good_output = """{
  "title": "Q4 planning",
  "sentiment_score": "0.6",
  "action_items": [{"task": "Draft roadmap", "owner": "Asha", "priority": "high"}]
}"""
summary = MeetingSummary.model_validate_json(good_output)
print(summary.sentiment_score, type(summary.sentiment_score))  # 0.6 <class 'float'> (coerced)
print(summary.action_items[0].owner)                          # attribute access, typed

# 3. Bad LLM output gets caught
bad_output = '{"title": "", "sentiment_score": 3, "action_items": [{"priority": "urgent"}]}'
try:
    MeetingSummary.model_validate_json(bad_output)
except ValidationError as e:
    print(e.error_count(), "errors")
    for err in e.errors():
        print(err["loc"], "->", err["msg"])
    # In a real app: send these errors back to the LLM and ask it to fix its output.
```

## 5. Key points to remember
- Type hints = documentation + editor help. **Not enforced** by Python.
- Pydantic = runtime validation + parsing + serialization + JSON Schema.
- `Field(description=...)` matters: the description ends up in the schema the LLM reads. It's part of my prompt.
- Use `Literal[...]` for fixed choices (classification labels, priorities, tool names).
- Use `model_validate_json` on raw LLM text. Catch `ValidationError` and retry with the error message.
- Keep schemas simple and flat for LLMs. Deep nesting and huge schemas reduce accuracy.
- Pydantic v2 is the current version (`model_dump`, not the old v1 `.dict()`).
- `pydantic-settings` + `.env` is the clean way to manage API keys and config.

## 6. Common mistakes / interview questions
**Mistakes**
- Assuming type hints validate data at runtime (they don't, only Pydantic/validators do).
- Using Pydantic v1 syntax (`.dict()`, `.parse_raw()`, `@validator`) with v2.
- Writing `Optional[str]` but forgetting `= None`. In v2 the field is still **required** without a default.
- Mutable defaults like `tags: list[str] = []`. Pydantic handles this safely, but prefer `Field(default_factory=list)` for clarity.
- No descriptions on fields, so the LLM has to guess what each field means.
- Not handling `ValidationError`, so one bad LLM response crashes the app.

**Interview questions**
- What's the difference between type hints and Pydantic validation?
- How do you get structured JSON output from an LLM reliably?
- What happens when the LLM returns invalid JSON? How do you recover?
- What is JSON Schema and how does Pydantic relate to it?
- Difference between `dataclass` and Pydantic `BaseModel`?
- How does FastAPI use Pydantic?

## 7. Resources I used
- [Pydantic docs](https://docs.pydantic.dev/latest/)
- [Python typing docs](https://docs.python.org/3/library/typing.html)
- [mypy cheat sheet](https://mypy.readthedocs.io/en/stable/cheat_sheet_py3.html)
- [Instructor library: structured outputs with Pydantic](https://python.useinstructor.com/)
- [JSON Schema: Getting started](https://json-schema.org/learn/getting-started-step-by-step)

## 8. What I'll build / practice next
- [ ] Define a `Resume` model and extract it from raw resume text using an LLM with structured outputs.
- [ ] Write a retry loop: call LLM → validate → on `ValidationError` send errors back → max 3 tries.
- [ ] Load my API keys using `pydantic-settings` and a `.env` file.
- [ ] Run `mypy` on one of my scripts and fix the type errors.
