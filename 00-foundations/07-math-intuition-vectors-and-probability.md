# Math Intuition Vectors and Probability

> **Section:** Foundations (only what you need) · **Status:** ⬜ Not started · **Day:** __ · **Date:** ____

## 1. What is it? (in my own words)
The small amount of math that explains how LLMs and AI apps actually work. I don't need to prove theorems. I need **intuition** for two areas:

**1. Vectors (linear algebra)**
- A **vector** is a list of numbers, e.g. `[0.2, -1.3, 0.7]`. It can be seen as a point or an arrow in space.
- AI represents *meaning* as vectors (**embeddings**). Similar meaning = vectors pointing in similar directions.
- A **matrix** is a grid of numbers; multiplying a vector by a matrix transforms it. Neural networks are mostly matrix multiplications.

**2. Probability**
- A **probability distribution** assigns a chance to each possible outcome; all chances add up to 1.
- An LLM is a machine that, given text so far, outputs a probability distribution over the **next token**, then samples from it.
- **Softmax**, **temperature**, **top-p**, **log-probabilities** and **cross-entropy loss** are all probability ideas.

## 2. Why does it matter for a Gen AI / Agentic AI developer?
- **Embeddings and RAG:** semantic search is "find the vectors closest to my query vector". Cosine similarity, dot product and distance are vector math.
- **Vector DBs:** choosing cosine vs dot vs Euclidean, understanding normalization, dimensions and approximate search (HNSW) all need this intuition.
- **Attention:** the core of transformers is `softmax(Q·Kᵀ / √d) · V`, which is dot products + softmax.
- **Sampling settings:** temperature, top-k and top-p only make sense once I see the model output as a probability distribution.
- **Hallucinations:** the model picks *likely* tokens, not *true* ones. Probability explains why fluent text can be wrong.
- **Evaluation:** accuracy, precision/recall, confidence, variance between runs and A/B testing prompts are statistics.
- **Logprobs:** APIs can return token log-probabilities, which are useful for confidence scores and classification.

## 3. How it works
<!-- Steps, intuition, flow. Add a diagram: ![diagram](../assets/00-foundations/07-math-intuition-vectors-and-probability.png) -->

### Part A: Vectors

**Dot product:** multiply matching elements, then add.
`a · b = a₁b₁ + a₂b₂ + ... + aₙbₙ`
Big positive = same direction, ~0 = unrelated (perpendicular), negative = opposite.

**Length (norm):** `‖a‖ = √(a₁² + a₂² + ... + aₙ²)`

**Cosine similarity:** the angle between two vectors, ignoring length.
`cos(a, b) = (a · b) / (‖a‖ ‖b‖)`, ranging from -1 (opposite) through 0 (unrelated) to 1 (same direction).
If vectors are **normalized** (length 1), cosine similarity = dot product. That's why many embedding models return normalized vectors.

**Euclidean distance:** straight-line distance `‖a − b‖`. Smaller = more similar.

```
          ▲ "royalty"
          │      • queen
          │    • king
          │
          │                 • banana
          │              • apple
          └──────────────────────────► "food"

  king and queen point in similar directions  → high cosine similarity
  king and banana point in different directions → low cosine similarity
```

**Famous intuition:** `king − man + woman ≈ queen`. Directions in embedding space capture concepts.

**Matrix × vector:** a layer in a neural network takes an input vector, multiplies by a weight matrix, adds a bias, and applies a non-linearity: `output = activation(W·x + b)`. Stack many of these and you get a deep network.

**Dimensions:** real embeddings have hundreds to thousands of dimensions (e.g. 384, 768, 1536, 3072). I can't picture them, but the 2D/3D intuition still holds.

### Part B: Probability

**Basics**
- `P(A)` is between 0 and 1. All outcomes sum to 1.
- **Conditional probability** `P(A | B)`: the probability of A given B happened. An LLM computes `P(next token | all previous tokens)`.
- **Chain rule:** P(whole sentence) = P(w₁) · P(w₂|w₁) · P(w₃|w₁,w₂) · ... This is exactly how LLMs generate text, one token at a time.
- **Bayes' rule:** `P(A|B) = P(B|A)·P(A) / P(B)`, i.e. updating beliefs with evidence.
- **Expected value:** the average outcome weighted by probability (useful for cost estimates: avg tokens × price).

**From scores to probabilities: softmax**
The model outputs raw scores (**logits**) for every token in its vocabulary. Softmax turns them into probabilities:
`P(token_i) = e^(z_i / T) / Σ e^(z_j / T)`, where `T` = **temperature**.

```
logits:        "Paris" 5.0   "Lyon" 2.0   "London" 1.0
T = 1.0  →     0.94          0.05         0.02          (confident)
T = 0.2  →     ~1.00         ~0.00        ~0.00         (almost deterministic)
T = 2.0  →     0.74          0.16         0.10          (more random / creative)
```

**Sampling strategies**
- **Greedy** (temperature ≈ 0): always pick the highest-probability token, giving consistent output.
- **Top-k:** only sample from the k most likely tokens.
- **Top-p (nucleus):** only sample from the smallest set of tokens whose probabilities add up to p (e.g. 0.9).

**Log-probabilities:** probabilities get tiny when multiplied over many tokens, so we use `log(P)`. Logs turn products into sums, and they're always ≤ 0. Closer to 0 = more confident.

**Cross-entropy loss:** how LLMs are trained. Loss = `−log(P(correct next token))`. If the model gave the right token probability 0.9, the loss is 0.105 (good). If it gave 0.01, the loss is 4.6 (bad). Training pushes the probability of the right tokens up.

**Perplexity** = `e^(average loss)`, roughly "how many tokens the model is confused between". Lower is better.

## 4. Code example
```python
import numpy as np

# ---------- Vectors ----------
king   = np.array([0.9, 0.8, 0.1])
queen  = np.array([0.85, 0.75, 0.2])
banana = np.array([0.1, 0.0, 0.95])


def cosine(a: np.ndarray, b: np.ndarray) -> float:
    return float(a @ b / (np.linalg.norm(a) * np.linalg.norm(b)))


print("dot(king, queen)     =", round(float(king @ queen), 3))
print("cos(king, queen)     =", round(cosine(king, queen), 3))    # ~0.99, very similar
print("cos(king, banana)    =", round(cosine(king, banana), 3))   # ~0.15, unrelated
print("euclid(king, banana) =", round(float(np.linalg.norm(king - banana)), 3))

# Normalized vectors: dot product == cosine similarity
k_n, q_n = king / np.linalg.norm(king), queen / np.linalg.norm(queen)
print("dot of normalized    =", round(float(k_n @ q_n), 3))

# ---------- Probability: softmax + temperature ----------
tokens = ["Paris", "Lyon", "London", "pizza"]
logits = np.array([5.0, 2.0, 1.0, -1.0])        # raw model scores for "The capital of France is"


def softmax(z: np.ndarray, temperature: float = 1.0) -> np.ndarray:
    z = z / temperature
    z = z - z.max()                             # numerical stability trick
    e = np.exp(z)
    return e / e.sum()


for T in [0.2, 1.0, 2.0]:
    probs = softmax(logits, T)
    print(f"T={T}:", {t: round(float(p), 3) for t, p in zip(tokens, probs)})

# Sampling 10 tokens at T=1.0
rng = np.random.default_rng(0)
probs = softmax(logits, 1.0)
print("samples:", rng.choice(tokens, size=10, p=probs))

# Top-p (nucleus) filtering with p=0.9
order = np.argsort(probs)[::-1]
cumulative = np.cumsum(probs[order])
keep = order[: np.searchsorted(cumulative, 0.9) + 1]
print("top-p 0.9 keeps:", [tokens[i] for i in keep])

# Cross-entropy loss: the correct answer is "Paris"
p_correct = probs[tokens.index("Paris")]
print(f"P(Paris)={p_correct:.3f}  loss={-np.log(p_correct):.3f}  logprob={np.log(p_correct):.3f}")
```

## 5. Key points to remember
- **Embedding = meaning as a vector.** Similar meaning → similar direction.
- **Dot product** measures alignment; **cosine similarity** is the dot product of normalized vectors (range −1 to 1).
- For normalized embeddings, cosine, dot product and Euclidean distance give the **same ranking**.
- Neural networks = lots of `W·x + b` plus non-linearities. Matrix multiplication is the core operation (that's why GPUs matter).
- An LLM outputs a **probability distribution over the next token**, then samples one, repeat.
- **Softmax** turns logits into probabilities. **Temperature** sharpens (low T) or flattens (high T) the distribution.
- Low temperature for extraction, classification, code and agents' tool calls; higher for brainstorming and creative writing.
- **Log-probs** measure confidence; **cross-entropy** is the training loss; **perplexity** summarizes it.
- High probability ≠ true. That's the root of hallucinations.

## 6. Common mistakes / interview questions
**Mistakes**
- Comparing embeddings from **different models**. Their vector spaces are unrelated.
- Using a dot product on non-normalized vectors and calling it cosine similarity.
- Thinking temperature 0 is always fully deterministic (it's close, but GPU math and batching can still cause small differences).
- Treating the model's confidence as correctness.
- Setting both temperature and top-p aggressively at once. Usually tune one.
- Forgetting that cosine similarity scores are relative: 0.8 can be "very similar" in one model and "meh" in another.

**Interview questions**
- What is an embedding? How do you measure similarity between two embeddings?
- Cosine similarity vs dot product vs Euclidean distance: when does it matter?
- What does temperature do mathematically? What about top-p vs top-k?
- What is softmax and why is it used in attention and at the output layer?
- How is an LLM trained? What is cross-entropy loss?
- What is perplexity?
- Why do LLMs hallucinate (from a probability point of view)?
- What are logprobs and how could you use them in an application?

## 7. Resources I used
- [3Blue1Brown: Essence of Linear Algebra (YouTube)](https://www.3blue1brown.com/topics/linear-algebra)
- [3Blue1Brown: Neural Networks / Transformers series](https://www.3blue1brown.com/topics/neural-networks)
- [Khan Academy: Statistics and Probability](https://www.khanacademy.org/math/statistics-probability)
- [Jay Alammar: The Illustrated Word2vec](https://jalammar.github.io/illustrated-word2vec/)
- *Mathematics for Machine Learning* (Deisenroth et al., free PDF) for going deeper

## 8. What I'll build / practice next
- [ ] Get real embeddings for 10 words (king, queen, man, woman, apple, banana...) and print a cosine similarity matrix.
- [ ] Try `king − man + woman` with real embeddings and find the nearest word.
- [ ] Call an LLM API with logprobs enabled and look at the top alternatives for each token.
- [ ] Run the same prompt 5 times at temperature 0, 0.7 and 1.5 and compare the outputs.
- [ ] Watch 3Blue1Brown chapters 1–3 (vectors, linear combinations, matrices) and the attention video.
