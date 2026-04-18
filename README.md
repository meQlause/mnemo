# mnemo · AI-Powered Note Intelligence

**mnemo** is a secure, elegant, rich-text note-taking application infused with AI capabilities. It utilizes a **Retrieval-Augmented Generation (RAG)** architecture to transform your personal notes into an intelligent, searchable knowledge base. You can write, organize, and chat directly with your notes—where the AI answers your questions using *only* your written context.

![Mnemo App Interface]() <!-- You can add a screenshot here -->

## Features

- **Rich Text & Markdown Editing**: Write seamlessly using a robust rich-text editor, powered by Tiptap, that securely formats and stores your notes in Markdown.
- **AI-Powered Chat (RAG)**: Chat directly with your notes! The AI fetches surrounding context and answers questions strictly rooted in your documentation. It smartly recognizes when the answer *isn't* in your notes.
- **Intelligent Title Suggestions**: Let the Google Gemini LLM instantly generate concise, relevant titles for your notes based on their content.
- **Semantic Search**: Traditional keyword search combined with vector-based semantic search to find connections you might have forgotten.
- **Native-Like UI**: Features a beautiful "Paper & Ink" theme engineered to feel like a premium native application, devoid of jarring scroll bounces and messy layouts.

## Architecture & Stack

The application is split into specialized containers:

- **Frontend (`/frontend`)**: React 18, Vite, Typescript, Zustand, TailwindCSS + custom Vanilla CSS for premium styling. Designed exclusively for high-performance and fluid layouts.
- **Backend (`/backend`)**: FastAPI, Python, SQLAlchemy, Alembic (for migrations), Pydantic (for data validation), and LangChain. Integrates seamlessly with Google's Gemini models for embeddings and text generation.
- **Database (`postgres`)**: A PostgreSQL instance equipped with the `pgvector` extension locally to store dense vector embeddings.

---

## Getting Started with Docker Compose

Running **mnemo** locally is straightforward using Docker Compose. The configuration provided will spin up the Frontend container, the API Backend, and the PostgreSQL server with `pgvector` pre-installed.

### Prerequisites
- [Docker](https://docs.docker.com/get-docker/) & [Docker Compose](https://docs.docker.com/compose/install/)
- A [Google Gemini API Key](https://aistudio.google.com/app/apikey)

### Step 1: Clone & Configure

1. Navigate to the root directory of the project.
2. Initialize your Environment Variables:
   Copy the example environment files for both the frontend and backend.

   **Backend:**
   ```bash
   cp backend/.env.example backend/.env
   ```
   Open `backend/.env` and insert your Gemini API Key:
   ```env
   GOOGLE_API_KEY=your_actual_api_key_here
   ```

   **Frontend:**
   ```bash
   cp frontend/.env.example frontend/.env
   ```
   Open `frontend/.env` and point it to the local Docker API if needed:
   ```env
   VITE_API_TARGET=http://localhost:8881
   ```

### Step 2: Build & Spin Up

Run the following command at the root directory to build the images and start the services in detached mode:

```bash
docker-compose up --build -d
```

### Step 3: Run Database Migrations

Before using the app, you need to instantiate the database schema using Alembic. Run this command to apply migrations inside the running API container:

```bash
docker exec -it rag_notes_api alembic upgrade head
```

### Step 4: Access the Application

- **Frontend UI**: [http://localhost:8880](http://localhost:8880)
- **Backend API Docs (Swagger)**: [http://localhost:8881/docs](http://localhost:8881/docs)

Create an account, drop in some notes, and start chatting with your knowledge base!

---

## Database Schema / ERD

The database runs on PostgreSQL and manages relational data alongside high-dimensional vector embeddings via `pgvector`. The schema consists of three primary tables:

1. **`users`**
   - **Fields**: `id` (PK), `username`, `email`, `created_at`.
   - **Purpose**: Manages user accounts and identities.

2. **`notes`**
   - **Fields**: `id` (PK), `user_id` (FK -> `users.id`), `title`, `content`, `metadata_` (JSONB), `created_at`.
   - **Purpose**: Captures the parent document, storing the complete raw markdown string alongside AI-synthesized metadata.

3. **`note_chunks`**
   - **Fields**: `id` (PK), `note_id` (FK -> `notes.id`), `chunk_content`, `chunk_index`, `embedding` (`VECTOR(768)`).
   - **Index**: Uses a Hierarchical Navigable Small World (**HNSW**) index on the `embedding` column, highly optimizing it for cosine similarity searches (`vector_cosine_ops`).
   - **Purpose**: Stores the fractured semantic segments corresponding to the parent note, crucial for the Retrieval phase.

---

## How it Works Under the Hood

1. **Ingestion & Chunking**: When you save a note, the backend cleans the markdown and splits it into smaller **chunks** (e.g., 1000 characters).
2. **Embeddings**: Each chunk is passed through `models/text-embedding-004` to generate a high-dimensional vector.
3. **Storage**: Both the text and the vector are securely stored in the PostgreSQL database using the `pgvector` extension.
4. **Retrieval**: When you ask a question in the **Chat Feature**, the backend turns your query into a vector, calculates the closest mathematical pairs (cosine similarity) existing in your database, retrieves the *context window* around those chunks, and feeds it entirely to the Gemini LLM to construct a reliable, sourced answer.

## Typical Development Commands

- Stop containers: `docker-compose down`
- View API logs: `docker logs -f rag_notes_api`
- Create new migration (after modifying DB models): 
  `docker exec -it rag_notes_api alembic revision --autogenerate -m "description"`
