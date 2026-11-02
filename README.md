# Edu-Backend | AI-Powered Learning Platform

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
