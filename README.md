# EduAgent | End-to-End Multi-Agent ML System for Automated Learning Synthesis

An enterprise-ready, asynchronous backend powering an AI-driven educational platform. This system is designed for high-throughput document analysis, dynamic content synthesis, and automated learning workflows utilizing a custom Retrieval-Augmented Generation (RAG) pipeline and specialized LLM agent orchestration.

## 🌟 Key Features

*   **Multi-Agent Orchestration:** A modular service layer managing dynamic prompts, session context, and AI-driven course generation using 9 specialized LLM agents (Planner, Explainer, HTML/Slide, Tester, Flashcard, Grader, etc.).
*   **Retrieval-Augmented Generation (RAG):** Processes unstructured data (PDFs, documents) into structured courses by embedding text into dense vectors and retrieving top-k context windows to ground LLM outputs.
*   **Adversarial-Resistant Grading:** A specialized grader agent prompted to resist user score manipulation, ensuring unbiased and accurate automated assessments.
*   **Scalable Asynchronous Architecture:** Built on FastAPI and SQLAlchemy for high-concurrency API performance, capable of parallel chapter processing to reduce generation latency.
*   **Automated Code Analysis:** Multi-stage agent pipeline evaluating syntax, runtime behavior, and style checks (`ESLintValidator` & Python checkers).
*   **Modern Python Tooling:** Optimized for fast dependency resolution and reproducible builds using `uv` (Astral) and Docker.

## 🛠️ Tech Stack

*   **Machine Learning & NLP:** Sentence-Transformers (all-MiniLM-L6-v2), Google GenAI SDK, Custom Agent Pipelines, RAG, Vector Databases (ChromaDB).
*   **Backend Framework:** FastAPI, Uvicorn, Python 3.13+
*   **Database & ORM:** PostgreSQL / SQLite, SQLAlchemy
*   **Tooling & DevOps:** `uv`, Docker, Pytest

---

## 🧠 ML Pipeline & System Architecture

Unlike traditional CRUD backends, EduAgent is built around a complex ML data flow:

1.  **Document Ingestion:** Parses unstructured PDFs and text into structured chunks.
2.  **Embedding Generation:** Utilizes `sentence-transformers` to generate dense vector representations of the text.
3.  **Vector Retrieval:** Indexes embeddings into ChromaDB for semantic similarity search.
4.  **Context Injection:** Queries the vector database to retrieve the top-k relevant context windows based on the user's requested course topic.
5.  **Agentic Generation:** Feeds the retrieved context into the LLM agents via Google ADK to generate structured learning materials, ensuring the output is grounded in the uploaded documents.

### Technical Flow Diagram

```mermaid
sequenceDiagram
    participant User
    participant API as API Layer
    participant AgentService
    participant StateManager
    participant PlannerAgent
    participant ExplainerAgent
    participant TesterAgent
    participant DB as Database

    User->>API: POST /api/courses (CourseRequest)
    API->>AgentService: create_course(user_id, course_id, request, task_id)

    Note over AgentService: Bind documents and images to course
    AgentService->>DB: update_document(course_id)
    AgentService->>DB: update_image(course_id)

    Note over AgentService: Query planner agent for course structure
    AgentService->>PlannerAgent: run(user_id, state, content)
    PlannerAgent-->>AgentService: response_planner with chapters[]

    Note over AgentService: Save chapters and update course
    AgentService->>StateManager: save_chapters(user_id, course_id, chapters)
    AgentService->>DB: update_course(chapter_count)

    Note over AgentService: Process chapters in parallel
    par Process Chapter 1
        AgentService->>ExplainerAgent: run(user_id, state, chapter_content)
        ExplainerAgent-->>AgentService: JSX explanation
        AgentService->>TesterAgent: run(user_id, state, chapter_content)
        TesterAgent-->>AgentService: Test questions
        AgentService->>StateManager: save_chapter_content(chapter_1)
    and Process Chapter N
        Note over AgentService: ... parallel processing for all chapters
    end

    Note over AgentService: All chapters processed
    AgentService->>DB: update_course_status(FINISHED)
    AgentService-->>API: Course creation completed
    API-->>User: Course created successfully

    Note over User: Optional: Generate Flashcards
    User->>API: POST /api/anki/upload (PDF)
    API->>ExplainerAgent: analyze_pdf()
    ExplainerAgent-->>API: PDF analysis
    API-->>User: Flashcard deck generated
