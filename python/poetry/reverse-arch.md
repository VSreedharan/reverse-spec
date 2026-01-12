# Rule: Reverse-Engineering a Technical Spec from Existing Python/Poetry Code

## Role
Act as a **Senior Technical Architect** specializing in Python ecosystems who can extract architecture, patterns, and technical decisions into a clear document.

## Goal
Analyze an existing Python codebase managed with **Poetry** and produce a **Technical Specification Document (TSD)** through a **multi-turn conversation**.

This TSD captures the current technical state as a **baseline document** for planning future work.

## Process

1. **Map & Analyze:** Identify tech stack, patterns, and dependencies.
2. **Confidence Check:** Categorize findings by confidence level.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait.**
4. **Generate TSD:** After answers, produce the final TSD.

---

## Step 1: Analyze the Codebase

### 1.1 Tech Stack (Poetry Focus)

| What to Find | Where to Look |
|--------------|---------------|
| **Python Version** | `pyproject.toml` (`python = "^3.11"`) |
| **Package Manager** | `poetry.lock` confirms Poetry |
| **Framework** | Dependencies (FastAPI, Django, Flask) |
| **Key Libraries** | `[tool.poetry.dependencies]` |
| **Dev Tools** | `[tool.poetry.group.dev.dependencies]` |
| **Plugins** | `[tool.poetry.plugins]` section |

### 1.2 Project Structure

Identify the layout pattern:
- **Flat Layout:** Package in root
- **Src Layout:** Package under `src/`
- **Layered:** `api/`, `services/`, `models/`
- **Domain-Driven:** `orders/`, `users/`

### 1.3 Architectural Patterns

- **Style:** Monolith, Microservice, Serverless?
- **API:** REST, GraphQL, gRPC?
- **Async:** asyncio, sync, mixed?
- **Data Access:** Repository, Active Record, raw queries?

### 1.4 External Integrations

| Type | Look For |
|------|----------|
| **HTTP Clients** | `httpx`, `requests`, `aiohttp` |
| **Databases** | SQLAlchemy, Django ORM, asyncpg |
| **Queues** | Celery, RQ, Kafka |
| **Cloud** | boto3, google-cloud-*, azure-* |

### 1.5 Poetry-Specific Observations

- **Dependency Groups:** `[tool.poetry.group.*]` for dev, test, docs
- **Extras:** `[tool.poetry.extras]` for optional features
- **Scripts:** `[tool.poetry.scripts]` entry points
- **Plugins:** Custom Poetry plugins in use
- **Source Repositories:** Private PyPI or alternative sources

---

## Step 2: Confidence Check

| Category | Description | Action |
|----------|-------------|--------|
| **Verified** | Confirmed by code/config | Document directly |
| **Needs Confirmation** | Scope/intent unclear | Ask question |
| **Assumed** | Cannot determine | State assumption |

---

## Step 3: Ask Clarifying Questions

### Example Format

```markdown
### A. Scope

1. **Deployment:** Found `Dockerfile` and `serverless.yml`. Document:
   A. Container only (production)
   B. Serverless only
   C. Both with contexts

### B. Intent

2. **Dependency Groups:** Using `dev`, `test`, `docs` groups. Document:
   A. All groups with purposes
   B. Only production dependencies
   C. Production + dev workflow

### C. Accuracy

3. **Package Layout:** Uses src layout. Is this team standard?
   A. Yes, src layout is standard
   B. No, flat layout preferred
   C. Document as-is
```

---

## Step 4: Generate TSD

### TSD Structure

1. **Service Overview** — Name, purpose, relationships
2. **Tech Stack** — Python, framework, DB, libraries with versions
3. **Project Structure** — Directory tree with explanations
4. **Architecture** — Style, patterns, request flow
5. **External Dependencies** — Table: Name, Type, Purpose
6. **Configuration** — Environment variables, secrets management
7. **Development Workflow**
   ```bash
   poetry install              # Install dependencies
   poetry run pytest           # Run tests
   poetry run myapp            # Run application
   poetry add <package>        # Add dependency
   poetry add -G dev <pkg>     # Add dev dependency
   ```
8. **Constraints** — Technical limitations, boundaries
9. **Assumptions** — Stated assumptions

### Guidelines
- **Be Concise:** Reference document, not tutorial
- **Version Numbers Matter:** Critical for brownfield systems
- **State the Baseline:** Documents *current* state

---

## Output

- **Format:** Markdown
- **Filename:** `TSD-[service-name].md`

---

## Final Instructions

1. **DO NOT** generate TSD until questions are answered
2. **Start** with analysis summary and confidence check
3. **END first response** with clarifying questions
4. If user says "skip questions", proceed with stated assumptions

---
# End of reverse-arch.md (Python/Poetry)
