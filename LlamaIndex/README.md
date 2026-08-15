# LlamaIndex: Zero to Advanced

A comprehensive, hands-on Jupyter notebook that teaches [LlamaIndex](https://www.llamaindex.ai/)
from the ground up — from your first RAG query to agents, multi-agent workflows, and
production best practices.

- **LLM backend:** [Groq](https://groq.com) (fast inference over open-weight models)
- **Embeddings:** Local HuggingFace model (`BAAI/bge-small-en-v1.5`) — free, runs on CPU, no extra API key
- **Format:** Single self-contained `.ipynb`, 35 sections, runnable top to bottom

## Contents

| # | Section |
|---|---|
| 0 | Prerequisites & Installation |
| 1 | What is LlamaIndex? Core Concepts |
| 2 | Setting Up Groq (LLM) and Embeddings |
| 3 | Documents & Nodes |
| 4 | Loading Data (Readers) |
| 5 | Text Splitting / Node Parsing |
| 6 | Your First VectorStoreIndex |
| 7 | Querying: The Query Engine |
| 8 | Response Modes & Response Synthesis |
| 9 | Persisting and Loading Indexes |
| 10 | Other Index Types (Summary, Keyword, Tree) |
| 11 | Retrievers Deep Dive |
| 12 | Node Postprocessors & Reranking |
| 13 | Customizing the Query Engine |
| 14 | Structured Outputs with Pydantic |
| 15 | Metadata: Extraction & Filtering |
| 16 | Chat Engines |
| 17 | Memory Management |
| 18 | Prompt Customization |
| 19 | Streaming Responses |
| 20 | Tools and Function Calling |
| 21 | Agents: FunctionAgent / ReAct |
| 22 | Multi-Step & Multi-Document Reasoning |
| 23 | Router Query Engine |
| 24 | Sub-Question Query Engine |
| 25 | Query Transformations (HyDE & friends) |
| 26 | Advanced Retrieval: Sentence Window & Auto-Merging |
| 27 | External Vector Stores (Chroma) |
| 28 | Evaluation of RAG Pipelines |
| 29 | Observability, Callbacks & Tracing |
| 30 | Workflows: Event-Driven Pipelines |
| 31 | Building a Multi-Agent System |
| 32 | Putting It Together: An End-to-End RAG App |
| 33 | Production Best Practices |
| 34 | Next Steps & Resources |

## Requirements

- Python 3.9+
- Jupyter (Notebook, JupyterLab, or VS Code's notebook support)
- A free [Groq API key](https://console.groq.com/keys)
- Internet access (to install packages and download the small embedding model once)

No GPU is required — the embedding model runs comfortably on CPU.

## Setup

1. **Clone or download** this notebook into a folder.

2. **Create a virtual environment** (recommended):
   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Windows: .venv\Scripts\activate
   ```

3. **Get a Groq API key** from https://console.groq.com/keys and store it as an
   environment variable. The easiest way is a `.env` file in the same folder as the
   notebook:
   ```
   GROQ_API_KEY=gsk_your_key_here
   ```
   The notebook loads this automatically via `python-dotenv` in Section 2.

4. **Open the notebook** in Jupyter/JupyterLab/VS Code and run the cells **in order**,
   starting with Section 0 — it installs all required packages
   (`llama-index`, `llama-index-llms-groq`, `llama-index-embeddings-huggingface`,
   `llama-index-vector-stores-chroma`, `chromadb`, etc.).

5. Section 4 auto-generates a small sample dataset (`./data/*.txt`), so the notebook
   works immediately with no files of your own. Swap in your own documents any time by
   pointing `SimpleDirectoryReader` at a different folder.

## Notes & Tips

- **Model choice:** The notebook defaults to `llama-3.3-70b-versatile` on Groq. Swap in
  `llama-3.1-8b-instant` for lower latency/cost on simpler tasks. Check
  https://console.groq.com/docs/models for the current model list, since names change
  over time.
- **Tool/function calling** (used by agents in Sections 20–21, 31) requires a Groq model
  that supports it — the Llama 3.x instruct models do.
- **Persistence:** Sections 9 and 27 write local folders (`./storage`, `./chroma_db`,
  `./chroma_db_app`). Delete these if you want to rebuild indexes from scratch.
- **Costs:** Groq's free tier has rate limits. If you hit them, add delays between cells
  or switch to a smaller model.
- Cells are meant to be run sequentially — later sections (chat engines, agents,
  workflows) reuse the `index`, `llm`, and `documents` objects created earlier.

## Resources

- LlamaIndex docs: https://docs.llamaindex.ai
- LlamaHub (readers, tools, packs): https://llamahub.ai
- Groq docs: https://console.groq.com/docs
