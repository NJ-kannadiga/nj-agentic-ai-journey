# 🤖 NJ Agentic AI Journey

My Winter Arc ❄️ journey to become a **Gen AI & Agentic AI Developer**, from the basics to production agents.
One concept, one file, written in my own words.

**146 concepts · 19 sections** · Started: Oct 2026

## Learning path
`Foundations → ML/DL essentials → LLM fundamentals → Prompt & context engineering → LLM APIs → Embeddings & vector DBs → RAG → Frameworks → Agents → Agentic patterns → Agent frameworks → Multi-agent → MCP → Evals → Guardrails → Fine-tuning → Backend & deployment → Frontend → Projects`

## How this repo works
- Every concept has its own `.md` note with the same template.
- Diagrams and screenshots go in `assets/<section>/`.
- Runnable code goes in `notebooks/` (`.ipynb`) or in the project folders.
- Daily progress is logged in [`WINTER_ARC_LOG.md`](./WINTER_ARC_LOG.md).

## Roadmap

### 00 · Foundations (only what you need)

- [ ] [Python for AI](./00-foundations/01-python-for-ai.md)
- [ ] [Async Python and Concurrency](./00-foundations/02-async-python-and-concurrency.md)
- [ ] [Pydantic and Type Hints](./00-foundations/03-pydantic-and-type-hints.md)
- [ ] [APIs HTTP and JSON](./00-foundations/04-apis-http-and-json.md)
- [ ] [Git Github and Env Setup](./00-foundations/05-git-github-and-env-setup.md)
- [ ] [Numpy Pandas Essentials](./00-foundations/06-numpy-pandas-essentials.md)
- [ ] [Math Intuition Vectors and Probability](./00-foundations/07-math-intuition-vectors-and-probability.md)

### 01 · ML & Deep Learning Essentials

- [ ] [What Is ML and DL](./01-ml-dl-essentials/01-what-is-ml-and-dl.md)
- [ ] [Neural Networks Intuition](./01-ml-dl-essentials/02-neural-networks-intuition.md)
- [ ] [Training Loss and Gradient Descent](./01-ml-dl-essentials/03-training-loss-and-gradient-descent.md)
- [ ] [PyTorch Basics](./01-ml-dl-essentials/04-pytorch-basics.md)
- [ ] [Overfitting and Evaluation](./01-ml-dl-essentials/05-overfitting-and-evaluation.md)

### 02 · Gen AI & LLM Fundamentals

- [ ] [What Is Generative AI](./02-genai-and-llm-fundamentals/01-what-is-generative-ai.md)
- [ ] [Tokens and Tokenization](./02-genai-and-llm-fundamentals/02-tokens-and-tokenization.md)
- [ ] [Embeddings Intuition](./02-genai-and-llm-fundamentals/03-embeddings-intuition.md)
- [ ] [Transformer Architecture](./02-genai-and-llm-fundamentals/04-transformer-architecture.md)
- [ ] [Attention Mechanism](./02-genai-and-llm-fundamentals/05-attention-mechanism.md)
- [ ] [How LLMs Are Trained Pretraining SFT RLHF](./02-genai-and-llm-fundamentals/06-how-llms-are-trained-pretraining-sft-rlhf.md)
- [ ] [Context Window and Sampling Temperature Top-p](./02-genai-and-llm-fundamentals/07-context-window-and-sampling-temperature-top-p.md)
- [ ] [Hallucinations and Limitations](./02-genai-and-llm-fundamentals/08-hallucinations-and-limitations.md)
- [ ] [Model Landscape Open vs Closed](./02-genai-and-llm-fundamentals/09-model-landscape-open-vs-closed.md)
- [ ] [Reasoning Models](./02-genai-and-llm-fundamentals/10-reasoning-models.md)
- [ ] [Small Language Models and Local LLMs Ollama](./02-genai-and-llm-fundamentals/11-small-language-models-and-local-llms-ollama.md)

### 03 · Prompt & Context Engineering

- [ ] [Prompt Basics and Anatomy](./03-prompt-and-context-engineering/01-prompt-basics-and-anatomy.md)
- [ ] [Zero Shot and Few Shot](./03-prompt-and-context-engineering/02-zero-shot-and-few-shot.md)
- [ ] [Chain of Thought and Reasoning](./03-prompt-and-context-engineering/03-chain-of-thought-and-reasoning.md)
- [ ] [System Prompts and Roles](./03-prompt-and-context-engineering/04-system-prompts-and-roles.md)
- [ ] [Structured Outputs JSON Schema](./03-prompt-and-context-engineering/05-structured-outputs-json-schema.md)
- [ ] [Prompt Templates and Versioning](./03-prompt-and-context-engineering/06-prompt-templates-and-versioning.md)
- [ ] [Context Engineering](./03-prompt-and-context-engineering/07-context-engineering.md)
- [ ] [Prompt Caching](./03-prompt-and-context-engineering/08-prompt-caching.md)
- [ ] [Prompt Injection and Jailbreaks](./03-prompt-and-context-engineering/09-prompt-injection-and-jailbreaks.md)

### 04 · LLM APIs & SDKs

- [ ] [Calling LLM APIs OpenAI Anthropic Gemini](./04-llm-apis-and-sdks/01-calling-llm-apis-openai-anthropic-gemini.md)
- [ ] [Messages and Conversation State](./04-llm-apis-and-sdks/02-messages-and-conversation-state.md)
- [ ] [Streaming Responses](./04-llm-apis-and-sdks/03-streaming-responses.md)
- [ ] [Tool Calling Function Calling](./04-llm-apis-and-sdks/04-tool-calling-function-calling.md)
- [ ] [Multimodal Vision Audio](./04-llm-apis-and-sdks/05-multimodal-vision-audio.md)
- [ ] [Batch and Async Calls](./04-llm-apis-and-sdks/06-batch-and-async-calls.md)
- [ ] [Rate Limits Retries and Costs](./04-llm-apis-and-sdks/07-rate-limits-retries-and-costs.md)
- [ ] [LLM Gateways and LiteLLM](./04-llm-apis-and-sdks/08-llm-gateways-and-litellm.md)

### 05 · Embeddings & Vector Databases

- [ ] [What Are Embeddings](./05-embeddings-and-vector-dbs/01-what-are-embeddings.md)
- [ ] [Similarity Metrics Cosine Dot](./05-embeddings-and-vector-dbs/02-similarity-metrics-cosine-dot.md)
- [ ] [Embedding Models Comparison](./05-embeddings-and-vector-dbs/03-embedding-models-comparison.md)
- [ ] [Vector DB Intro](./05-embeddings-and-vector-dbs/04-vector-db-intro.md)
- [ ] [Chroma FAISS Qdrant pgvector](./05-embeddings-and-vector-dbs/05-chroma-faiss-qdrant-pgvector.md)
- [ ] [Indexing HNSW IVF](./05-embeddings-and-vector-dbs/06-indexing-hnsw-ivf.md)
- [ ] [Metadata Filtering](./05-embeddings-and-vector-dbs/07-metadata-filtering.md)

### 06 · Retrieval-Augmented Generation (RAG)

- [ ] [RAG Basics](./06-rag/01-rag-basics.md)
- [ ] [Document Loaders and Parsing](./06-rag/02-document-loaders-and-parsing.md)
- [ ] [Chunking Strategies](./06-rag/03-chunking-strategies.md)
- [ ] [Retrieval and Top K](./06-rag/04-retrieval-and-top-k.md)
- [ ] [Hybrid Search BM25 + Vector](./06-rag/05-hybrid-search-bm25-plus-vector.md)
- [ ] [Reranking](./06-rag/06-reranking.md)
- [ ] [Query Rewriting and HyDE](./06-rag/07-query-rewriting-and-hyde.md)
- [ ] [Multimodal RAG](./06-rag/08-multimodal-rag.md)
- [ ] [Graph RAG](./06-rag/09-graph-rag.md)
- [ ] [Agentic RAG](./06-rag/10-agentic-rag.md)
- [ ] [RAG Evaluation RAGAS](./06-rag/11-rag-evaluation-ragas.md)

### 07 · LLM Frameworks

- [ ] [LangChain Basics](./07-llm-frameworks/01-langchain-basics.md)
- [ ] [LCEL and Runnables](./07-llm-frameworks/02-lcel-and-runnables.md)
- [ ] [LlamaIndex Basics](./07-llm-frameworks/03-llamaindex-basics.md)
- [ ] [Haystack or DSPy Overview](./07-llm-frameworks/04-haystack-or-dspy-overview.md)
- [ ] [Choosing a Framework vs Raw SDK](./07-llm-frameworks/05-choosing-a-framework-vs-raw-sdk.md)

### 08 · AI Agents — Core Concepts

- [ ] [What Is an AI Agent](./08-ai-agents-core/01-what-is-an-ai-agent.md)
- [ ] [Agents vs Workflows](./08-ai-agents-core/02-agents-vs-workflows.md)
- [ ] [Agent Loop Perceive Reason Act](./08-ai-agents-core/03-agent-loop-perceive-reason-act.md)
- [ ] [React Pattern](./08-ai-agents-core/04-react-pattern.md)
- [ ] [Tool Design for Agents](./08-ai-agents-core/05-tool-design-for-agents.md)
- [ ] [Planning and Task Decomposition](./08-ai-agents-core/06-planning-and-task-decomposition.md)
- [ ] [Reflection and Self Critique](./08-ai-agents-core/07-reflection-and-self-critique.md)
- [ ] [Agent Memory Short and Long Term](./08-ai-agents-core/08-agent-memory-short-and-long-term.md)
- [ ] [State Management](./08-ai-agents-core/09-state-management.md)
- [ ] [Human In the Loop](./08-ai-agents-core/10-human-in-the-loop.md)
- [ ] [Error Handling and Retries](./08-ai-agents-core/11-error-handling-and-retries.md)

### 09 · Agentic Design Patterns

- [ ] [Prompt Chaining](./09-agentic-design-patterns/01-prompt-chaining.md)
- [ ] [Routing](./09-agentic-design-patterns/02-routing.md)
- [ ] [Parallelization](./09-agentic-design-patterns/03-parallelization.md)
- [ ] [Orchestrator Workers](./09-agentic-design-patterns/04-orchestrator-workers.md)
- [ ] [Evaluator Optimizer](./09-agentic-design-patterns/05-evaluator-optimizer.md)
- [ ] [Plan and Execute](./09-agentic-design-patterns/06-plan-and-execute.md)
- [ ] [Code Agents](./09-agentic-design-patterns/07-code-agents.md)
- [ ] [Computer Use and Browser Agents](./09-agentic-design-patterns/08-computer-use-and-browser-agents.md)

### 10 · Agent Frameworks

- [ ] [LangGraph Basics](./10-agent-frameworks/01-langgraph-basics.md)
- [ ] [LangGraph State Nodes Edges](./10-agent-frameworks/02-langgraph-state-nodes-edges.md)
- [ ] [LangGraph Checkpoints and Memory](./10-agent-frameworks/03-langgraph-checkpoints-and-memory.md)
- [ ] [CrewAI](./10-agent-frameworks/04-crewai.md)
- [ ] [AutoGen AG2](./10-agent-frameworks/05-autogen-ag2.md)
- [ ] [OpenAI Agents SDK](./10-agent-frameworks/06-openai-agents-sdk.md)
- [ ] [Claude Agent SDK](./10-agent-frameworks/07-claude-agent-sdk.md)
- [ ] [Google ADK](./10-agent-frameworks/08-google-adk.md)
- [ ] [Pydantic AI](./10-agent-frameworks/09-pydantic-ai.md)
- [ ] [Framework Comparison](./10-agent-frameworks/10-framework-comparison.md)

### 11 · Multi-Agent Systems

- [ ] [Why Multi Agent](./11-multi-agent-systems/01-why-multi-agent.md)
- [ ] [Supervisor Pattern](./11-multi-agent-systems/02-supervisor-pattern.md)
- [ ] [Hierarchical Agents](./11-multi-agent-systems/03-hierarchical-agents.md)
- [ ] [Swarm and Handoffs](./11-multi-agent-systems/04-swarm-and-handoffs.md)
- [ ] [Agent Communication and A2A Protocol](./11-multi-agent-systems/05-agent-communication-and-a2a-protocol.md)
- [ ] [Shared Memory and Blackboard](./11-multi-agent-systems/06-shared-memory-and-blackboard.md)

### 12 · MCP — Model Context Protocol

- [ ] [What Is MCP](./12-mcp-model-context-protocol/01-what-is-mcp.md)
- [ ] [MCP Architecture Host Client Server](./12-mcp-model-context-protocol/02-mcp-architecture-host-client-server.md)
- [ ] [Tools Resources Prompts](./12-mcp-model-context-protocol/03-tools-resources-prompts.md)
- [ ] [Build an MCP Server Python](./12-mcp-model-context-protocol/04-build-an-mcp-server-python.md)
- [ ] [Connect MCP to Agents](./12-mcp-model-context-protocol/05-connect-mcp-to-agents.md)
- [ ] [Remote MCP and Auth](./12-mcp-model-context-protocol/06-remote-mcp-and-auth.md)

### 13 · Evaluation & Observability

- [ ] [Why Evals Matter](./13-evaluation-and-observability/01-why-evals-matter.md)
- [ ] [Building Eval Datasets](./13-evaluation-and-observability/02-building-eval-datasets.md)
- [ ] [LLM As a Judge](./13-evaluation-and-observability/03-llm-as-a-judge.md)
- [ ] [Agent Evaluation Trajectories](./13-evaluation-and-observability/04-agent-evaluation-trajectories.md)
- [ ] [Tracing LangSmith Langfuse](./13-evaluation-and-observability/05-tracing-langsmith-langfuse.md)
- [ ] [Monitoring Cost Latency Quality](./13-evaluation-and-observability/06-monitoring-cost-latency-quality.md)

### 14 · Guardrails, Safety & Security

- [ ] [Guardrails Input Output](./14-guardrails-safety-security/01-guardrails-input-output.md)
- [ ] [PII and Data Privacy](./14-guardrails-safety-security/02-pii-and-data-privacy.md)
- [ ] [Prompt Injection Defense](./14-guardrails-safety-security/03-prompt-injection-defense.md)
- [ ] [Tool Permissions and Sandboxing](./14-guardrails-safety-security/04-tool-permissions-and-sandboxing.md)
- [ ] [Responsible AI](./14-guardrails-safety-security/05-responsible-ai.md)

### 15 · Fine-Tuning & Optimization

- [ ] [RAG vs Fine Tuning vs Prompting](./15-fine-tuning-and-optimization/01-rag-vs-fine-tuning-vs-prompting.md)
- [ ] [Dataset Preparation](./15-fine-tuning-and-optimization/02-dataset-preparation.md)
- [ ] [LoRA and QLoRA](./15-fine-tuning-and-optimization/03-lora-and-qlora.md)
- [ ] [Instruction Tuning with Hugging Face](./15-fine-tuning-and-optimization/04-instruction-tuning-with-hugging-face.md)
- [ ] [DPO and Preference Tuning](./15-fine-tuning-and-optimization/05-dpo-and-preference-tuning.md)
- [ ] [Quantization and GGUF](./15-fine-tuning-and-optimization/06-quantization-and-gguf.md)
- [ ] [Distillation](./15-fine-tuning-and-optimization/07-distillation.md)
- [ ] [Serving Open Models vLLM](./15-fine-tuning-and-optimization/08-serving-open-models-vllm.md)

### 16 · Backend & Deployment for Gen AI

- [ ] [FastAPI for LLM Apps](./16-backend-and-deployment/01-fastapi-for-llm-apps.md)
- [ ] [Streaming with SSE and Websockets](./16-backend-and-deployment/02-streaming-with-sse-and-websockets.md)
- [ ] [Postgres and pgvector](./16-backend-and-deployment/03-postgres-and-pgvector.md)
- [ ] [Redis Caching and Semantic Cache](./16-backend-and-deployment/04-redis-caching-and-semantic-cache.md)
- [ ] [Background Jobs and Queues](./16-backend-and-deployment/05-background-jobs-and-queues.md)
- [ ] [Docker](./16-backend-and-deployment/06-docker.md)
- [ ] [CI CD Github Actions](./16-backend-and-deployment/07-ci-cd-github-actions.md)
- [ ] [Deploying to Cloud](./16-backend-and-deployment/08-deploying-to-cloud.md)
- [ ] [Scaling and Cost Optimization](./16-backend-and-deployment/09-scaling-and-cost-optimization.md)

### 17 · Frontend for AI Apps

- [ ] [React and Next.js Basics](./17-frontend-for-ai-apps/01-react-and-nextjs-basics.md)
- [ ] [Chat UI and Streaming](./17-frontend-for-ai-apps/02-chat-ui-and-streaming.md)
- [ ] [Vercel AI SDK](./17-frontend-for-ai-apps/03-vercel-ai-sdk.md)
- [ ] [Generative UI](./17-frontend-for-ai-apps/04-generative-ui.md)

### 18 · Projects (portfolio)

- [ ] [Chatbot with Memory](./18-projects/01-chatbot-with-memory.md)
- [ ] [PDF QA RAG App](./18-projects/02-pdf-qa-rag-app.md)
- [ ] [Structured Data Extractor](./18-projects/03-structured-data-extractor.md)
- [ ] [Tool Calling Agent](./18-projects/04-tool-calling-agent.md)
- [ ] [LangGraph Research Agent](./18-projects/05-langgraph-research-agent.md)
- [ ] [Custom MCP Server](./18-projects/06-custom-mcp-server.md)
- [ ] [Multi Agent Content Team](./18-projects/07-multi-agent-content-team.md)
- [ ] [Agentic RAG with Evals](./18-projects/08-agentic-rag-with-evals.md)
- [ ] [Voice or Browser Agent](./18-projects/09-voice-or-browser-agent.md)
- [ ] [Capstone Fullstack Agentic SaaS](./18-projects/10-capstone-fullstack-agentic-saas.md)

## Status legend
⬜ Not started · 🟨 In progress · ✅ Done
