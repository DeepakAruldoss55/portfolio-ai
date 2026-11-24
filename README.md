# Deepak Aruldoss — Portfolio AI Service ("Sara" RAG Backend)

Standalone FastAPI + ChromaDB RAG microservice powering **Sara**, the conversational AI companion on Deepak Aruldoss's static portfolio site ([deepakaruldoss55.github.io](https://deepakaruldoss55.github.io/)).

---

## Architecture

1. **Knowledge Base (`data/`)**: Structured Markdown documents covering Deepak's bio, skills, experience, projects, certifications, resume, and FAQs.
2. **Vector Store (`chroma_db/`)**: Persistent ChromaDB collection (`deepak_portfolio`) embedded with `sentence-transformers/all-MiniLM-L6-v2`.
3. **Inference (`rag_engine.py`)**: OpenAI-compatible Cloud LLM (Groq) with strict grounding and conversational guardrails.
4. **API (`main.py`)**: FastAPI server with CORS enabled for the GitHub Pages frontend and local development.

---

## Quick Setup & Run

### 1. Create Virtual Environment & Install Dependencies

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2. Configure Environment

Copy `.env.example` to `.env` and set your API key:

```bash
cp .env.example .env
```

```env
LLM_PROVIDER=openai_compatible
OPENAI_BASE_URL=https://api.groq.com/openai/v1
OPENAI_API_KEY=gsk_your_groq_api_key_here
OPENAI_MODEL=llama-3.3-70b-versatile

CHROMA_PERSIST_DIR=./chroma_db
EMBEDDING_MODEL=sentence-transformers/all-MiniLM-L6-v2
PORT=8000
```

### 3. Ingest Knowledge Base

```bash
./venv/bin/python ingest.py
```

### 4. Start the FastAPI Server

```bash
# Development (with auto-reload)
./venv/bin/uvicorn main:app --host 0.0.0.0 --port 8000 --reload

# Production (e.g., on Instabox)
./venv/bin/uvicorn main:app --host 0.0.0.0 --port 8000
```

---

## Connecting the GitHub Pages Frontend

Once this service is deployed (e.g., on Instabox), configure the portfolio repository's GitHub Actions variable:

- **Variable Name:** `REACT_APP_RAG_API_URL`
- **Value:** Your public HTTPS service URL (without trailing slash), e.g. `https://8000-<your-box>.instabox.tech`

The portfolio's `Chatbot.jsx` automatically uses `REACT_APP_RAG_API_URL` at build time and falls back to the offline client-side bot if unreachable.

---

## API Endpoints

- `GET /` — Service status and endpoint directory
- `GET /api/health` — Backend health and indexed ChromaDB chunk count
- `GET /api/suggestions` — Starter prompt suggestions
- `POST /api/chat` — RAG-grounded conversational response
  ```json
  {
    "message": "What projects has Deepak built?",
    "history": []
  }
  ```
