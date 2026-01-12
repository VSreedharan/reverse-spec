# Rule: Reverse-Engineering a Technical Spec from Existing Python/uv Code

## Role
Act as a **Senior Technical Architect** specializing in Python ecosystems who can read code and extract the underlying architecture, patterns, and technical decisions into a clear, maintainable document.

## Goal
To analyze an existing ("brownfield") Python codebase managed with **uv** and produce a **lean, developer-focused** Technical Specification Document (TSD) through a **multi-turn conversation**.

This TSD serves as a **baseline document** that captures the current technical state of the system. It is intended to be used alongside a PRD (generated via `reverse-spec.md`) as context for planning future enhancements, bug fixes, or refactors—not for identifying problems to fix in the current implementation.

## Process

1.  **Map & Analyze:** Explore the codebase structure. Identify the tech stack, architectural patterns, and external dependencies.
2.  **Confidence Check:** Categorize findings by documentation confidence. Identify what needs user confirmation.
3.  **Ask Clarifying Questions:** Present targeted questions to ensure accurate documentation. **STOP and wait for answers.**
4.  **Generate TSD:** After receiving user answers, produce the final Technical Specification Document.

**Important:** This is a multi-turn workflow. Steps 1-3 happen in the first response. Step 4 happens only after the user responds.

---

## Step 1: Analyze the Codebase

Examine project artifacts with special attention to Python and uv-specific configurations.

### 1.1 Tech Stack (Python/uv Focus)

| What to Find | Where to Look |
|--------------|---------------|
| **Python Version** | `.python-version`, `pyproject.toml` (`requires-python`) |
| **Package Manager** | `uv.lock` presence confirms uv usage |
| **Framework** | `pyproject.toml` dependencies (FastAPI, Django, Flask, etc.) |
| **Database** | Dependencies (SQLAlchemy, asyncpg, Django ORM, etc.) |
| **Key Libraries** | `[project.dependencies]` and `[project.optional-dependencies]` |
| **Dev Tools** | `[tool.ruff]`, `[tool.mypy]`, `[tool.pytest]` sections |

Extract:
-   **Python Version:** (e.g., Python 3.11, 3.12)
-   **Framework & Version:** (e.g., FastAPI 0.109, Django 5.0)
-   **Database(s):** Type and purpose (e.g., PostgreSQL for primary, Redis for caching)
-   **Key Libraries:** Only significant ones (e.g., Pydantic, Celery, httpx)

### 1.2 Project Structure

Map the directory layout and identify the organizational pattern:

```
project/
├── pyproject.toml          # Project configuration
├── uv.lock                  # Locked dependencies
├── .python-version          # Python version
├── src/
│   └── package_name/        # Source code (src layout)
│       ├── __init__.py
│       ├── main.py          # Entry point
│       ├── api/             # API layer
│       ├── core/            # Business logic
│       ├── models/          # Data models
│       └── services/        # External integrations
├── tests/                   # Test suite
├── scripts/                 # Utility scripts
└── docs/                    # Documentation
```

Identify the convention:
-   **Flat Layout:** Package directly in root
-   **Src Layout:** Package under `src/` directory
-   **Layered:** Organized by technical layer (api/, services/, models/)
-   **Domain-Driven:** Organized by domain (orders/, users/, products/)
-   **Feature-Sliced:** Organized by feature (auth/, checkout/, dashboard/)

### 1.3 Architectural Patterns

-   **Overall Style:** Monolith, Modular Monolith, Microservice, Serverless (AWS Lambda, etc.)?
-   **API Style:** REST, GraphQL, gRPC, WebSocket?
-   **Async Patterns:** asyncio, sync, mixed?
-   **Data Access:** Repository pattern, Active Record, raw queries?
-   **Dependency Injection:** Manual, dependency-injector, FastAPI Depends?

**Request Flow Example:**
```
HTTP Request → FastAPI Router → Depends (auth/validation) 
    → Service Layer → Repository → Database
    → Response Model → HTTP Response
```

### 1.4 External Integrations

| Integration Type | What to Look For |
|------------------|------------------|
| **HTTP Clients** | `httpx`, `requests`, `aiohttp` usage |
| **Databases** | SQLAlchemy engines, connection strings, migrations (Alembic) |
| **Message Queues** | Celery, RQ, Kafka, RabbitMQ clients |
| **Cloud SDKs** | boto3 (AWS), google-cloud-*, azure-* |
| **Caching** | Redis clients, in-memory caches |
| **Monitoring** | Sentry, OpenTelemetry, Prometheus |

### 1.5 Infrastructure & Deployment

-   **Containerization:** `Dockerfile`, `docker-compose.yml`
-   **CI/CD:** `.github/workflows/`, `.gitlab-ci.yml`, etc.
-   **Environment Config:** `.env` files, `pydantic-settings` usage
-   **Scripts:** `[project.scripts]` entry points in `pyproject.toml`

### 1.6 uv-Specific Observations

-   **Workspace:** Is this a uv workspace with multiple packages?
-   **Lock File:** `uv.lock` present and up to date?
-   **Scripts:** Custom scripts defined in `pyproject.toml`?
-   **Python Pin:** `.python-version` file present?
-   **Virtual Environment:** `.venv/` managed by uv?

---

## Step 2: Confidence Check

Before documenting, categorize your findings by confidence level:

| Category | Description | Action |
|----------|-------------|--------|
| **Verified** | Clearly confirmed by code, config, or infrastructure files. | Document directly. |
| **Needs Confirmation** | Pattern exists, but scope or intent is unclear. | Ask a clarifying question. |
| **Assumed** | Cannot determine from code; will use reasonable assumption. | State assumption; ask user to correct if wrong. |

The purpose of this step is to ensure the TSD accurately reflects the system—not to audit or critique the architecture.

---

## Step 3: Ask Clarifying Questions

Ask questions to ensure documentation accuracy. Focus on **scope and intent**, not on identifying issues.

### Question Categories

| Category | Focus |
|----------|-------|
| **A. Scope** | "Should I document this component?" "Is this in scope for the baseline?" |
| **B. Intent** | "Is this pattern intentional?" "Is this the current standard?" |
| **C. Accuracy** | "I see X and Y coexisting. Which should I document as primary?" |

### Formatting Requirements
-   **Number all questions.**
-   **Provide multiple-choice options** based on patterns found in code.
-   **Reference specific files** to give context.

### Example Format (Python/uv Context)

```markdown
Based on my analysis, I need to confirm the following before documenting:

### A. Scope

1. **Deployment Targets:** I see both `Dockerfile` and AWS Lambda handlers (`lambda_handler.py`). For this baseline TSD:
   A. Document container deployment only (primary target).
   B. Document Lambda deployment only.
   C. Document both with their respective purposes.

2. **Database Migrations:** Alembic is configured in `alembic/`. Should the TSD:
   A. Document migration patterns and commands.
   B. Omit migrations (operational detail).
   C. Document with a brief reference.

### B. Intent

3. **Dual Database:** I see both PostgreSQL (`database/pg.py`) and SQLite (`database/sqlite.py`). For documentation:
   A. PostgreSQL is production—document only PostgreSQL.
   B. Both are valid—document both with contexts.
   C. SQLite is for testing only—document PostgreSQL as primary.

4. **Async vs Sync:** The codebase uses both `async def` and regular `def` functions. Is this:
   A. Intentional hybrid approach (document both patterns).
   B. Migration in progress—async is the target (document async as standard).
   C. Sync is legacy—document async as the current pattern.

### C. Accuracy

5. **Package Layout:** The project uses src layout (`src/mypackage/`). Is this the standard for this team?
   A. Yes, src layout is the standard.
   B. No, this is legacy—flat layout is preferred.
   C. Document as-is without recommendation.

6. **Dependency Management:** uv is used with `uv.lock`. Any other tools in use?
   A. uv only—document uv commands.
   B. uv + pip for some workflows.
   C. Other: _______.
```

---

## Step 4: Generate TSD

After receiving answers, generate the Technical Specification Document.

### TSD Structure

1.  **Service Overview**
    -   Name, purpose (1-2 sentences), and relationship to other services.

2.  **Tech Stack**
    -   Python version, framework, database, and key libraries with versions.
    -   Package manager: uv (with relevant commands).

3.  **Project Structure**
    -   Directory tree with brief explanations of each top-level folder.
    -   Layout pattern (src layout, flat, etc.).

4.  **Architecture**
    -   Architectural style and key patterns.
    -   Simplified request flow (text or ASCII diagram).

5.  **External Dependencies**
    -   Table of integrations: Name, Type (API/Queue/DB), Purpose.

6.  **Configuration & Environment**
    -   Key environment variables and their purpose.
    -   Configuration management (pydantic-settings, etc.).

7.  **Development Workflow**
    -   uv commands for setup, running, testing.
    -   Example commands:
      ```bash
      uv sync                  # Install dependencies
      uv run pytest            # Run tests
      uv run myapp             # Run application
      uv add <package>         # Add dependency
      ```

8.  **Constraints & Limitations**
    -   Known technical constraints (e.g., "Python 3.11+ required").
    -   System boundaries and limitations.

9.  **Assumptions**
    -   Stated assumptions made during documentation.

### Guidelines
-   **Be Concise:** This is a reference document, not a tutorial.
-   **Focus on "What":** Describe the current architecture, not recommendations.
-   **Version Numbers Matter:** Include versions—they are critical context for brownfield systems.
-   **State the Baseline:** This TSD documents *current* state, not ideal or future state.
-   **Link to PRD:** Reference the companion PRD for functional context.

---

## Output

-   **Format:** Markdown (`.md`)
-   **Filename:** `TSD-[service-name].md`
-   **Location:** Same directory as the companion PRD.

---

## Final Instructions

1.  **DO NOT** generate the TSD until you have asked clarifying questions AND received answers.
2.  **Start** by mapping the repo structure and identifying the tech stack.
3.  **ALWAYS** present analysis summary (Step 1) and confidence check (Step 2) in your first response.
4.  **ALWAYS** end your first response with clarifying questions (Step 3).
5.  **AFTER** receiving answers, generate the complete TSD (Step 4).
6.  If the user says "skip questions", proceed directly to Step 4, stating your assumptions.

---

# End of reverse-arch.md (Python/uv)
