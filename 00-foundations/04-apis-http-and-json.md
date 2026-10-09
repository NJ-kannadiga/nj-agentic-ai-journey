# APIs HTTP and JSON

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
- **API (Application Programming Interface):** a contract that lets one program ask another program to do something. "Send me this data in this shape, and I'll send you back that data."
- **HTTP:** the protocol most web APIs use. A **client** sends a **request** (method + URL + headers + body), and the **server** sends back a **response** (status code + headers + body).
- **JSON (JavaScript Object Notation):** the text format almost every API uses for the body. It maps directly to Python: object → `dict`, array → `list`, string → `str`, number → `int`/`float`, `true/false` → `True/False`, `null` → `None`.

Every LLM is used through an API: I send JSON (model, messages, settings) over HTTP, and I get JSON back (the generated text, token usage, tool calls).

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Every LLM call is an HTTP request.** SDKs are just nice wrappers around `POST /v1/...` with JSON. When something breaks, I need to read the raw request/response.
- **Tools are usually APIs.** Weather, search, GitHub, Slack, my company's backend: agents call these through HTTP.
- **Error handling.** `401` (bad key), `429` (rate limit), `500/529` (server overloaded) all need different handling: fix, back off and retry, or fail over.
- **Streaming** uses HTTP with **Server-Sent Events (SSE)** so tokens arrive one by one.
- **I'll build APIs too.** My agent will be exposed as a FastAPI endpoint that other apps call.
- **Webhooks and MCP** are built on the same ideas (MCP uses JSON-RPC over stdio or HTTP).

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/04-apis-http-and-json.png) -->

**Anatomy of a request and response**

```
REQUEST                                         RESPONSE
POST https://api.example.com/v1/chat            HTTP/1.1 200 OK
Headers:                                        Headers:
  Content-Type: application/json                  Content-Type: application/json
  Authorization: Bearer sk-xxxx                   x-ratelimit-remaining: 99
Body (JSON):                                    Body (JSON):
  {                                               {
    "model": "some-model",                          "id": "msg_123",
    "messages": [                                   "content": "Hi! How can I help?",
      {"role": "user", "content": "Hi"}             "usage": {"input_tokens": 8,
    ],                                                        "output_tokens": 7}
    "max_tokens": 200                             }
  }
```

**HTTP methods (REST)**

| Method | Meaning | Example |
|---|---|---|
| `GET` | read data | `GET /users/42` |
| `POST` | create / do an action | `POST /v1/messages` (LLM call) |
| `PUT` / `PATCH` | replace / partially update | `PATCH /users/42` |
| `DELETE` | delete | `DELETE /files/9` |

**Status codes I must know**

| Code | Meaning | What I do |
|---|---|---|
| `200` / `201` | OK / created | parse the body |
| `400` | bad request (my JSON is wrong) | fix the request, don't retry |
| `401` / `403` | bad / missing API key, no permission | check the key, don't retry |
| `404` | not found | check the URL / model name |
| `408` / timeout | took too long | retry |
| `422` | validation error | fix the fields |
| `429` | rate limited | **wait and retry** with exponential backoff (respect `Retry-After`) |
| `500` / `502` / `503` / `529` | server error / overloaded | retry with backoff |

**Other pieces**
- **Query params:** `GET /search?q=rag&limit=5`
- **Headers:** metadata like auth, content type, API version.
- **Auth:** usually an API key in a header (`Authorization: Bearer ...` or a custom header like `x-api-key`).
- **Idempotency:** `GET` is safe to retry; `POST` may not be (could create things twice).
- **Streaming (SSE):** the server keeps the connection open and sends `data: {...}` lines as tokens are generated.
- **Python clients:** `requests` (sync, classic), `httpx` (sync + async, modern, recommended).

## 4. Code example
```python
# pip install httpx
import json
import time
import httpx

BASE_URL = "https://jsonplaceholder.typicode.com"   # free fake REST API for practice


def get_with_retry(client: httpx.Client, url: str, max_retries: int = 3) -> dict:
    """GET a URL, retrying on 429 / 5xx with exponential backoff."""
    for attempt in range(max_retries):
        try:
            resp = client.get(url, timeout=10)
            if resp.status_code == 429 or resp.status_code >= 500:
                wait = float(resp.headers.get("retry-after", 2 ** attempt))
                print(f"status {resp.status_code}, retrying in {wait}s")
                time.sleep(wait)
                continue
            resp.raise_for_status()          # raise for other 4xx errors
            return resp.json()               # JSON text -> Python dict
        except httpx.TimeoutException:
            time.sleep(2 ** attempt)
    raise RuntimeError(f"failed after {max_retries} retries: {url}")


with httpx.Client(base_url=BASE_URL) as client:
    # GET with a path param
    post = get_with_retry(client, "/posts/1")
    print(post["title"])

    # GET with query params
    comments = client.get("/comments", params={"postId": 1}).json()
    print(len(comments), "comments")

    # POST a JSON body (this is exactly the shape of an LLM API call)
    payload = {"title": "My agent", "body": "learning HTTP", "userId": 1}
    resp = client.post("/posts", json=payload, headers={"Authorization": "Bearer fake-key"})
    print(resp.status_code)                  # 201 Created
    print(json.dumps(resp.json(), indent=2))
```

## 5. Key points to remember
- An LLM SDK call = an HTTP `POST` with a JSON body + an API key header.
- JSON ↔ Python: `json.loads(str) → dict`, `json.dumps(dict) → str`. With httpx: `resp.json()` and `json=payload`.
- Always set a **timeout**. LLM calls can hang.
- Retry only on **429, 5xx and timeouts**, using **exponential backoff** (+ jitter). Never retry on 400/401.
- Read rate-limit headers (`retry-after`, `x-ratelimit-*`) when the API provides them.
- Keep API keys in environment variables, never in code or Git.
- Use `httpx` because it supports both sync and async with the same API.
- Know how to debug with `curl` or a tool like Postman/Bruno: look at the raw request and response.

## 6. Common mistakes / interview questions
**Mistakes**
- No timeout, so the app hangs forever.
- Retrying everything (including `400`), which wastes money and never succeeds.
- Retrying instantly in a tight loop on `429`, which makes the rate limiting worse.
- Committing API keys to GitHub.
- Sending `data=` instead of `json=`, so the body is form-encoded and the API says "invalid JSON".
- Assuming the response always has the expected keys. Use `.get()` or validate with Pydantic.

**Interview questions**
- What happens when you call an LLM API, step by step?
- What's the difference between `GET` and `POST`? `PUT` and `PATCH`?
- How would you handle a `429 Too Many Requests`?
- What is exponential backoff and why add jitter?
- What is idempotency? Why does it matter when retrying?
- What is REST? What is Server-Sent Events (SSE) and how is it different from WebSockets?
- How do you secure API keys in an application?

## 7. Resources I used
- [MDN: An overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [MDN: HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [HTTPX docs](https://www.python-httpx.org/)
- [JSONPlaceholder: free fake API](https://jsonplaceholder.typicode.com/)
- [MDN: Server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)

## 8. What I'll build / practice next
- [ ] Call a real LLM API **without** the SDK using raw `httpx` + JSON, then compare with the SDK version.
- [ ] Build a tiny FastAPI endpoint `POST /echo` that accepts JSON and returns JSON.
- [ ] Write a reusable `retry_with_backoff` decorator with jitter.
- [ ] Use `curl -v` on one API call and label every part of the request and response.
