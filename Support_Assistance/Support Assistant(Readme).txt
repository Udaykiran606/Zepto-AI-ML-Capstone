# Zepto Support Assistant (RAG Module)

This module implements a Retrieval-Augmented Generation (RAG) support assistant for Zepto policy questions.  
It lives at `/support_assistant` in the repository and includes:

- Corpus files (8 Zepto policy documents)
- LangGraph + FastAPI application code (`main.py`)
- `Dockerfile` for containerized deployment
- This README with architecture description and example call transcripts

## Quick Start

### Local run (Python)

```bash
cd /support_assistant
pip install -r requirements.txt
python main.py
```

The script will:
- Initialize ChromaDB with the 8 policy documents.
- Run two example queries (one policy, one general) and print their JSON responses.

### Local run (FastAPI + uvicorn)

```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

Then call:

```bash
curl -X POST http://localhost:8000/ask \
  -H "Content-Type: application/json" \
  -d '{"query": "What is the delivery fee for orders below INR 149?"}'
```

### Docker build & run

```bash
docker build -t zepto-support-assistant .
docker run -p 8000:8000 zepto-support-assistant
```

The API will be available at `http://localhost:8000/ask`.

## Architecture

This project implements a RAG pipeline with the following stages:

1. **Ingestion**  
   - Handled by: `initialize_vector_store()` in `main.py`.  
   - Reads 8 Zepto policy documents from `CORPUS_FILES`, writes them to disk under `DOCS_DIR`, and ingests them into a ChromaDB collection (`zepto_policy_corpus`).

2. **Embedding**  
   - Handled by: `SentenceTransformerEmbeddingFunction(model_name="all-MiniLM-L6-v2")` attached to the ChromaDB collection in `initialize_vector_store()`.  
   - All document chunks are embedded using this model and stored in ChromaDB.

3. **Retrieval**  
   - Handled by: `retrieve_and_answer_node` in the LangGraph graph (`main.py`).  
   - For `policy_question` intents, queries ChromaDB with `collection.query(query_texts=[query], n_results=3)` to fetch the top 3 chunks.

4. **Generation**  
   - Handled by:
     - Mock mode (`MOCK_LLM=1`): `retrieve_and_answer_node` and `direct_answer_node` construct canned responses without calling any external LLM.
     - Real-LLM mode (`MOCK_LLM=0`): A placeholder `call_real_llm()` function (with retry logic) would use `STRUCTURED_PROMPT_TEMPLATE` to generate answers from the retrieved context.

5. **Routing / Branching on `MOCK_LLM`**  
   - The **generation stage** branches on `MOCK_LLM`:
     - When `MOCK_LLM=1` (default): generation uses fixed canned strings in `retrieve_and_answer_node` and `direct_answer_node`; no network calls.
     - When `MOCK_LLM=0`: generation would call `call_real_llm()` with the structured prompt and retrieved context; this is where the real LLM would be invoked.

Intent classification (`classify_intent_node`) and routing (`route_condition`) are part of the LangGraph workflow but do not depend on `MOCK_LLM`.

## Example Call Transcripts (MOCK_LLM=1)

Run with:

```bash
MOCK_LLM=1 python main.py
```

### Example 1 – Policy Question

**Query:**

```json
{"query": "What is the delivery fee for orders below INR 149?"}
```

**Response:**

```json
{
  "answer": "Based on the retrieved context: Standard delivery is free on orders over INR 149; orders below this threshold incur a flat INR 25 delivery fee...",
  "sources": ["doc_01"],
  "confidence": 1.0
}
```

### Example 2 – General Question

**Query:**

```json
{"query": "Who won the last cricket World Cup?"}
```

**Response:**

```json
{
  "answer": "I can only answer questions about Zepto policies right now.",
  "sources": [],
  "confidence": 1.0
}
```

*(Replace the above JSON blocks with the actual output from your `python main.py` run.)*

## Optional Extensions (if implemented)

### Real-LLM Free-Tier Usage Notes

*(Fill this in only if you added a real LLM path with a free-tier provider.)*

- Provider: e.g., Groq / Hugging Face Inference API / Ollama  
- Environment variables required: `LLM_API_KEY`, `LLM_MODEL`, etc.  
- How to switch: set `MOCK_LLM=0` and provide the necessary env vars.

### Live Hugging Face Space URL

*(Fill this in only if you deployed.)*

- Space URL: `https://huggingface.co/spaces/<your-username>/zepto-support-assistant`  
- Notes on deployment (e.g., `docker push`, `HF_TOKEN`, etc.)
