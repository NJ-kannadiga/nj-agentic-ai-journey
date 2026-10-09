# Numpy Pandas Essentials

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
- **NumPy:** the library for fast math on arrays of numbers. A NumPy array (`ndarray`) is like a Python list, but all items have the same type, it can have many dimensions (vector, matrix, tensor), and operations run in optimized C code, often 10–100x faster than Python loops.
- **Pandas:** the library for tables (like Excel or SQL in Python). Its main object is the **DataFrame**: rows and named columns. I use it to load, clean, filter, group and save data (CSV, JSON, Excel, Parquet).

In AI terms: **NumPy is how I handle embeddings and vectors; Pandas is how I handle datasets, documents, logs and eval results.**

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Embeddings are NumPy arrays.** A sentence embedding is a vector like `shape (1536,)`. Similarity search = dot products and norms on arrays.
- **Vector search from scratch.** Before using a vector DB, I can do cosine similarity over a matrix of embeddings in 3 lines of NumPy.
- **PyTorch tensors feel like NumPy.** Learning NumPy makes PyTorch easy.
- **Datasets for RAG and fine-tuning** usually come as CSV/JSON/Parquet. Pandas loads and cleans them.
- **Evaluation.** I'll store test questions, model answers, scores, latency and cost in a DataFrame and compare prompt/model versions with `groupby`.
- **Observability.** Analyzing token usage and cost logs is a Pandas job.

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/06-numpy-pandas-essentials.png) -->

**NumPy core ideas**
- **Shape:** `(3,)` is a vector, `(3, 4)` a matrix with 3 rows and 4 columns, `(batch, seq, dim)` a 3D tensor.
- **Vectorization:** `a * 2` applies to every element, so no loops are needed.
- **Broadcasting:** NumPy stretches smaller arrays to match bigger ones, e.g. `matrix (100, 768) - vector (768,)` works row by row.
- **Axis:** `axis=0` goes down rows (per column), `axis=1` goes across columns (per row).
- **Key ops:** `np.array`, `.shape`, `.reshape`, `np.dot` / `@`, `np.linalg.norm`, `.mean/.sum/.max(axis=...)`, `np.argsort`, boolean masks `a[a > 0]`.

```
embeddings matrix E  (5 docs × 4 dims)        query q (4,)
┌──────────────────┐
│ doc0: [.1 .3 .5 .1] │
│ doc1: [.9 .1 .0 .2] │      E @ q  ──►  scores (5,)  ──► argsort ──► top-k doc ids
│ ...                 │
└──────────────────┘
```

**Pandas core ideas**
- `DataFrame` = table, `Series` = one column.
- Load/save: `pd.read_csv`, `pd.read_json(lines=True)`, `df.to_csv`, `df.to_parquet`.
- Inspect: `df.head()`, `df.info()`, `df.describe()`, `df.shape`, `df["col"].value_counts()`.
- Select: `df["col"]`, `df[["a", "b"]]`, `df.loc[row_label, col]`, `df.iloc[row_idx]`.
- Filter: `df[df["score"] > 0.8]`.
- New columns: `df["n_words"] = df["text"].str.split().str.len()`.
- Clean: `dropna`, `fillna`, `drop_duplicates`, `.str.strip()`, `.str.lower()`.
- Aggregate: `df.groupby("model")["latency"].mean()`.
- Combine: `pd.merge(a, b, on="id")`, `pd.concat([a, b])`.

## 4. Code example
```python
# pip install numpy pandas
import numpy as np
import pandas as pd

# ---------- NumPy: mini vector search ----------
docs = ["cats are pets", "dogs are loyal", "python is a language", "rust is fast", "kittens are cute"]
rng = np.random.default_rng(42)
E = rng.normal(size=(len(docs), 8))           # pretend embeddings: 5 docs × 8 dims
E[4] = E[0] + rng.normal(scale=0.1, size=8)   # make "kittens" similar to "cats"

query = E[0] + rng.normal(scale=0.05, size=8)  # a query close to "cats are pets"


def cosine_sim(matrix: np.ndarray, vec: np.ndarray) -> np.ndarray:
    matrix_norm = matrix / np.linalg.norm(matrix, axis=1, keepdims=True)
    vec_norm = vec / np.linalg.norm(vec)
    return matrix_norm @ vec_norm             # (5, 8) @ (8,) -> (5,)


scores = cosine_sim(E, query)
top_k = np.argsort(scores)[::-1][:3]          # indices of the 3 highest scores
for i in top_k:
    print(f"{scores[i]:.3f}  {docs[i]}")

# ---------- Pandas: analyzing eval results ----------
df = pd.DataFrame({
    "question_id": [1, 1, 2, 2, 3, 3],
    "model": ["small", "large"] * 3,
    "correct": [True, True, False, True, True, True],
    "latency_s": [0.8, 2.1, 0.7, 2.5, None, 1.9],
    "cost_usd": [0.0002, 0.003, 0.0002, 0.0031, 0.0002, 0.0029],
})

df["latency_s"] = df["latency_s"].fillna(df["latency_s"].median())
summary = df.groupby("model").agg(
    accuracy=("correct", "mean"),
    avg_latency=("latency_s", "mean"),
    total_cost=("cost_usd", "sum"),
)
print(summary)
print(df[df["correct"] == False])             # failed cases to inspect
df.to_csv("eval_results.csv", index=False)
```

## 5. Key points to remember
- Always check `.shape`. Most NumPy/PyTorch bugs are shape bugs.
- Avoid Python loops over arrays; use vectorized operations.
- `@` is matrix multiplication; `*` is element-wise.
- Cosine similarity = dot product of **normalized** vectors.
- `np.argsort(scores)[::-1][:k]` gives the top-k indices.
- In Pandas, inspect first: `head()`, `info()`, `isna().sum()`.
- `groupby(...).agg(...)` is the most useful Pandas tool for comparing models/prompts.
- Use `.loc` for labels, `.iloc` for positions. Avoid chained indexing like `df[a][b] = x`.
- For big data, prefer Parquet over CSV (smaller and faster, keeps types).

## 6. Common mistakes / interview questions
**Mistakes**
- Shape mismatch: `(768,)` vs `(1, 768)` vs `(768, 1)`. Use `reshape` or `keepdims=True`.
- Using `*` when I meant `@`.
- Forgetting to normalize vectors before calling a dot product "cosine similarity".
- `SettingWithCopyWarning`: modifying a filtered slice. Use `.loc` or `.copy()`.
- Comparing to `None` with `==` in Pandas. Use `.isna()`.
- Loading a 5 GB CSV into memory at once. Use `chunksize` or Parquet.
- Iterating rows with `for ... in df.iterrows()` for math, which is very slow. Vectorize.

**Interview questions**
- Why is NumPy faster than Python lists?
- What is broadcasting? Give an example.
- How do you compute cosine similarity between a query and 1M embeddings efficiently?
- What's the difference between `axis=0` and `axis=1`?
- `loc` vs `iloc`?
- How do you handle missing values in a DataFrame?
- How would you compare the accuracy and cost of two LLMs on a test set with Pandas?

## 7. Resources I used
- [NumPy: absolute beginners guide](https://numpy.org/doc/stable/user/absolute_beginners.html)
- [Pandas: 10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html)
- [Kaggle Learn: Pandas course (free)](https://www.kaggle.com/learn/pandas)
- *Python for Data Analysis* by Wes McKinney (creator of Pandas), free online edition

## 8. What I'll build / practice next
- [ ] Get real embeddings for 20 sentences (from an embedding API or `sentence-transformers`) and build top-k search in NumPy.
- [ ] Load a public Q&A dataset (CSV/JSONL) with Pandas, clean it, and save it as Parquet.
- [ ] Create an eval DataFrame for two prompts and compare accuracy, latency and cost with `groupby`.
- [ ] Do the Kaggle Pandas course exercises.
