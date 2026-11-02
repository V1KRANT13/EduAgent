# EduAgent | AI-Powered Learning Platform

An enterprise-ready, asynchronous FastAPI backend powering an AI-driven educational platform. Designed for high-throughput code validation, dynamic content synthesis, and automated learning workflows using Retrieval-Augmented Generation (RAG) and specialized LLM agents.

## 🌟 Key Features

- **Automated Code Analysis**: Multi-stage agent pipeline evaluating syntax, runtime behavior, and style checks (`ESLintValidator` & Python checkers).
- **LLM Agent Orchestration**: Modular service layer managing dynamic prompts, session context, and AI-driven course recommendations.
- **Asynchronous Architecture**: Built on FastAPI and SQLAlchemy for high-concurrency API performance.
- **Modern Package Management**: Optimized for fast dependency resolution and reproducible builds using `uv` (Astral).

## 🛠️ Tech Stack

- **Framework**: FastAPI, Uvicorn
- **Language & Runtime**: Python 3.13+
- **Database & ORM**: PostgreSQL / SQLite, SQLAlchemy
- **AI/LLM Stack**: Google GenAI SDK, Custom Agent Pipelines
- **Tooling & Packaging**: `uv`, Docker, Pytest

## Technical Diagram

```mermaid
sequenceDiagram
    participant User
    participant API as API Layer
    participant AgentService
    participant StateManager
    participant PlannerAgent
    participant ExplainerAgent
    participant HTMLAgent
    participant TesterAgent
    participant FlashcardAgent
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
        
        AgentService->>HTMLAgent: run(user_id, state, chapter_content)
        HTMLAgent-->>AgentService: Reveal.js slides
        
        AgentService->>TesterAgent: run(user_id, state, chapter_content)
        TesterAgent-->>AgentService: Test questions
        
        AgentService->>StateManager: save_chapter_content(chapter_1)
    and Process Chapter 2
        AgentService->>ExplainerAgent: run(user_id, state, chapter_content)
        ExplainerAgent-->>AgentService: JSX explanation
        
        AgentService->>HTMLAgent: run(user_id, state, chapter_content)
        HTMLAgent-->>AgentService: Reveal.js slides
        
        AgentService->>TesterAgent: run(user_id, state, chapter_content)
        TesterAgent-->>AgentService: Test questions
        
        AgentService->>StateManager: save_chapter_content(chapter_2)
    and Process Chapter N
        Note over AgentService: ... parallel processing for all chapters
    end
    
    Note over AgentService: All chapters processed
    AgentService->>DB: update_course_status(FINISHED)
    AgentService-->>API: Course creation completed
    API-->>User: Course created successfully

    Note over User: Optional: Generate Flashcards
    User->>API: POST /api/anki/upload (PDF)
    API->>FlashcardAgent: analyze_pdf()
    FlashcardAgent-->>API: PDF analysis
    
    User->>API: POST /api/anki/generate
    API->>FlashcardAgent: generate_flashcards()
    
    alt Testing Flashcards
        FlashcardAgent->>FlashcardAgent: TestingFlashcardAgent.generate_questions()
        FlashcardAgent->>FlashcardAgent: create_testing_deck()
    else Learning Flashcards
        FlashcardAgent->>FlashcardAgent: LearningFlashcardAgent.generate_learning_cards()
        FlashcardAgent->>FlashcardAgent: create_learning_deck()
    end
    
    FlashcardAgent-->>API: .apkg file ready
    API-->>User: Flashcard deck generated

    Note over User,DB: Key Components:
    Note over User,DB: • PlannerAgent: Creates course structure with chapters
    Note over User,DB: • ExplainerAgent: Generates React/JSX explanations  
    Note over User,DB: • HTMLAgent: Creates Reveal.js slide presentations
    Note over User,DB: • TesterAgent: Generates interactive test questions
    Note over User,DB: • FlashcardAgent: Creates Anki flashcard decks
    Note over User,DB: • StateManager: Manages cross-agent state
```

## Getting Started

### 1. Clone the repository

```sh
git clone <repo-url>
cd edu-backend
```

### 2. Set up environment variables

Create a `.env` file in the root directory with at least:

```text
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_HOST=localhost
DB_PORT=3306
DB_NAME=your_app_db
SECRET_KEY=your_secret_key
SESSION_SECRET_KEY=your_session_secret
ACCESS_TOKEN_EXPIRE_MINUTES=20
REFRESH_TOKEN_EXPIRE_MINUTES=360000
SECURE_COOKIE=true
# OAuth keys (Google, GitHub, Discord)
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
GITHUB_CLIENT_ID=...
GITHUB_CLIENT_SECRET=...
DISCORD_CLIENT_ID=...
DISCORD_CLIENT_SECRET=...
```

### 3. Install dependencies

```sh
pip install -r requirements.txt
```

### 4. Set up the database

- Ensure MySQL is running and the database exists.
- (Optional) For local dev, switch to SQLite by editing `src/config/settings.py`.
- Run migrations or create tables (see Alembic or use SQLAlchemy's `Base.metadata.create_all`).

### 5. Run the server

```sh
uvicorn src.main:app --reload
```

## 🚀 API Endpoints Overview

| Module | Base Path | Description |
| :--- | :--- | :--- |
| **Auth** | `/api/auth` | User authentication, token validation, and session management |
| **Courses** | `/api/courses` | Dynamic course content loading and progress tracking |
| **Code Checker**| `/api/code` | Multi-language code evaluation via localized static checkers |
| **Agents** | `/api/agents` | Explainer & reasoning LLM agent execution pipeline |

## 🏗️ Architecture Overview

```text
[ Client Requests ]
        │
        ▼
[ FastAPI Routers ] ──► [ Auth & Middleware ]
        │
        ▼
 [ Service Layer ] ──► [ LLM / Agent Orchestrator ]
        │                      │
        ▼                      ▼
  [ SQLAlchemy ]      [ Code Checker Engine ]
        │
        ▼
   [ Database ]
