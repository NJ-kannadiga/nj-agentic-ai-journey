# Async Python and Concurrency

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
**Concurrency** means handling many tasks during the same period of time, without waiting for one to fully finish before starting the next.

**Async Python** (`asyncio`, `async` / `await`) is Python's way of doing concurrency on a **single thread**. When a task is waiting (for an API response, a database, a file), it "pauses" with `await` and lets another task run. When the response arrives, it resumes.

Analogy: a waiter in a restaurant. They don't stand at the kitchen waiting for one dish. They take an order, hand it to the kitchen, take the next order, and come back when the food is ready. One waiter, many tables.

Three ways to do things "at the same time" in Python:

| Approach | Best for | How |
|---|---|---|
| `asyncio` | many I/O tasks (API calls, DB, web) | one thread, cooperative switching at `await` |
| `threading` | I/O tasks with sync (blocking) libraries | many threads, OS switches them |
| `multiprocessing` | CPU-heavy work (math, image processing) | many processes, true parallelism |

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **LLM calls are slow I/O.** One call can take 2–30 seconds. My program is mostly *waiting*. Async lets me wait on 50 calls at once instead of one after another.
- **Parallel tool calls.** An agent might need to search the web, query a DB and read a file. Running them concurrently cuts latency.
- **Batch processing.** Embedding 10,000 chunks or evaluating 500 test cases is 10x+ faster with concurrent requests (while respecting rate limits).
- **Streaming servers.** FastAPI is async. Streaming tokens to many users at once needs async.
- **Frameworks are async.** LangGraph, LlamaIndex, OpenAI/Anthropic SDKs, MCP servers all have async APIs (`ainvoke`, `AsyncOpenAI`, etc.).
- **Multi-agent systems** run several agents at the same time.

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/02-async-python-and-concurrency.png) -->

**Key terms**
- `async def` defines a **coroutine function**. Calling it returns a coroutine object. It does **not** run yet.
- `await x` means "pause here until `x` is done, let others run meanwhile". Only allowed inside `async def`.
- **Event loop**: the scheduler that runs coroutines and switches between them at each `await`.
- `asyncio.run(main())` starts the event loop and runs `main()`.
- `asyncio.gather(*coros)` runs many coroutines concurrently and returns all results (in order).
- `asyncio.create_task(coro)` schedules a coroutine to run in the background.
- `asyncio.Semaphore(n)` limits how many tasks run at once (essential for rate limits).
- `asyncio.wait_for(coro, timeout)` cancels if it takes too long.
- `async for` / async generators let me consume streams (like token streams).

**Sequential vs concurrent (3 API calls, 2s each)**

```
Sequential:   [call 1 ──2s──][call 2 ──2s──][call 3 ──2s──]   = 6s

Concurrent:   [call 1 ──2s──]
              [call 2 ──2s──]                                  = ~2s
              [call 3 ──2s──]
```

**The golden rule:** never call blocking code inside an async function.
`time.sleep(2)` or `requests.get(...)` blocks the *whole* event loop, so every other task freezes. Use `await asyncio.sleep(2)` and `httpx.AsyncClient` instead. If I must use a blocking library, wrap it: `await asyncio.to_thread(blocking_fn, arg)`.

## 4. Code example
```python
import asyncio
import random
import time


async def fake_llm_call(prompt: str) -> str:
    """Simulate a slow LLM API call (1-2 seconds)."""
    await asyncio.sleep(random.uniform(1, 2))     # non-blocking wait
    return f"answer to: {prompt}"


async def limited_call(sem: asyncio.Semaphore, prompt: str) -> str:
    async with sem:                               # at most N calls at once
        try:
            return await asyncio.wait_for(fake_llm_call(prompt), timeout=5)
        except asyncio.TimeoutError:
            return f"timeout for: {prompt}"


async def main() -> None:
    prompts = [f"question {i}" for i in range(10)]

    # 1. Sequential: 3 calls one after another, ~4-5s
    start = time.perf_counter()
    for p in prompts[:3]:
        await fake_llm_call(p)
    print(f"sequential (3 calls): {time.perf_counter() - start:.1f}s")

    # 2. Concurrent with a limit of 5 at a time: 10 calls in ~3-4s
    sem = asyncio.Semaphore(5)
    start = time.perf_counter()
    results = await asyncio.gather(*(limited_call(sem, p) for p in prompts))
    print(f"concurrent (10 calls): {time.perf_counter() - start:.1f}s")
    print(results[:2])

    # 3. Running a blocking function without freezing the loop
    def blocking_work() -> str:
        time.sleep(1)
        return "done blocking work"

    print(await asyncio.to_thread(blocking_work))


if __name__ == "__main__":
    asyncio.run(main())
```

**Real-world version** (same pattern with a real async client):
```python
# client = AsyncOpenAI()  /  AsyncAnthropic()  /  httpx.AsyncClient()
# results = await asyncio.gather(*(client.some_call(...) for item in items))
```

## 5. Key points to remember
- Async = concurrency for **I/O-bound** work. It does not make CPU-heavy code faster.
- Calling an `async def` function without `await` does nothing (and Python warns "coroutine was never awaited").
- `asyncio.gather` is the workhorse for "do these N things at once".
- Always limit concurrency with a `Semaphore` when calling LLM APIs, or I'll hit rate limits (HTTP 429).
- Never use `time.sleep` or `requests` inside async code. Use `asyncio.sleep` and `httpx.AsyncClient`.
- Use `asyncio.to_thread` to run blocking code safely.
- `gather(..., return_exceptions=True)` stops one failure from killing all results.
- In Jupyter notebooks the loop is already running, so use `await main()` directly instead of `asyncio.run(main())`.

## 6. Common mistakes / interview questions
**Mistakes**
- Forgetting `await` and getting a coroutine object instead of a result.
- Using `asyncio.run()` inside Jupyter or inside an already-running loop ("event loop is already running").
- Blocking the loop with sync libraries (`requests`, `time.sleep`, sync DB drivers).
- Firing 1,000 requests at once with no semaphore, which leads to rate limits and bans.
- Thinking async = parallel. It's one thread switching between tasks.
- Not handling exceptions in `gather`, so one failed call loses all results.

**Interview questions**
- What is the difference between concurrency and parallelism?
- When would you use asyncio vs threading vs multiprocessing?
- What is the event loop? What happens at an `await`?
- What is the GIL and why doesn't it hurt I/O-bound async code?
- How do you limit the number of concurrent API calls?
- What's the difference between `gather` and `create_task`?
- How do you call a blocking function from async code?
- How would you process 10,000 documents through an LLM API as fast as possible without hitting rate limits?

## 7. Resources I used
- [Python docs: asyncio](https://docs.python.org/3/library/asyncio.html)
- [Real Python: Async IO in Python](https://realpython.com/async-io-python/)
- [HTTPX async client](https://www.python-httpx.org/async/)
- [FastAPI: Concurrency and async/await](https://fastapi.tiangolo.com/async/)

## 8. What I'll build / practice next
- [ ] Rewrite the example with `httpx.AsyncClient` fetching 20 URLs concurrently and compare timing vs `requests`.
- [ ] Build an async "batch summarizer" that sends 50 texts to an LLM with `Semaphore(5)` and retries.
- [ ] Write an async generator that yields fake tokens and consume it with `async for`.
- [ ] Try `asyncio.TaskGroup` (Python 3.11+) as a modern alternative to `gather`.
