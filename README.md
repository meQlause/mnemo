# mnemo · AI-Powered Note Intelligence

**mnemo** is a secure, elegant, rich-text note-taking application infused with AI capabilities. It transforms your personal notes into an intelligent, searchable knowledge base. Write, organize, and chat directly with your notes—where the AI answers your questions using *only* your written context.

---

## Key Features

### Intelligence at the Core
- **AI-Powered Chat (RAG)**: Chat with your notes! The AI fetches surrounding context and answers questions strictly rooted in your documentation.
- **AI Event Extraction**: Automatically extracts dates, confidence, and reasoning for events mentioned in your notes.
- **Smart Metadata Synthesis**: Instantly generates summaries, tags, and sentiment analysis for every note.
- **Intelligent Title Suggestions**: Let the Google Gemini LLM suggest concise, relevant titles based on your content.

### Seamless Writing Experience
- **Rich Text & Markdown**: A robust Tiptap-powered editor that formats and stores your notes in clean Markdown.
- **Semantic Search**: Find connections you might have forgotten using vector-based search that understands meaning, not just keywords.
- **Premium "Paper & Ink" UI**: A beautiful, distraction-free theme engineered for high performance and fluid transitions.

---

## Getting Started with Docker

Running **mnemo** locally is straightforward using Docker Compose.

### 1. Configure Environment
1. Clone the repository and navigate to the folder.
2. Initialize **Backend** environment:
   ```bash
   cp backend/.env.example backend/.env
   # Add your GOOGLE_API_KEY to backend/.env
   ```
3. Initialize **Frontend** environment:
   ```bash
   cp frontend/.env.example frontend/.env
   ```

### 2. Launch Services
Run the following at the root directory:
```bash
docker-compose up --build -d
```

### 3. Initialize Database
Apply migrations to set up the schema:
```bash
docker exec -it rag_notes_api alembic upgrade head
```

### 4. Access the App
- **Web UI**: [http://localhost:8880](http://localhost:8880)
- **API Docs**: [http://localhost:8881/docs](http://localhost:8881/docs)

---

## Project Dependencies

### Backend (Python/FastAPI)
- **Core**: `FastAPI`, `Uvicorn`, `Pydantic`
- **AI/LLM**: `LangChain`, `langchain-google-genai`, `Google Gemini API`
- **Database**: `PostgreSQL`, `SQLAlchemy`, `Alembic`, `pgvector`
- **Security**: `PyJWT`, `Bcrypt`, `FastAPI-CSRF-Protect`
- **Utilities**: `HTTPX`, `Python-Dotenv`, `Loguru`, `Ruff`

### Frontend (React/TypeScript)
- **Framework**: `React 18`, `Vite`, `TypeScript`
- **State & Data**: `Zustand`, `TanStack React Query`
- **Editor**: `Tiptap Editor Suite`, `Tiptap-Markdown`
- **Styling**: `TailwindCSS`, `Lucide React Icons`
- **Routing & UI**: `React Router Dom`, `Sonner` (Toasts), `React Markdown`

---

## Development Commands
- **Stop app**: `docker-compose down`
- **Logs**: `docker logs -f rag_notes_api`
- **Migrations**: `docker exec -it rag_notes_api alembic revision --autogenerate -m "description"`
