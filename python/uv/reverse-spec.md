# Rule: Reverse-Engineering a PRD from Existing Python/uv Code

## Role
Act as a **Senior Technical Architect** specializing in Python ecosystems who can read code and translate implementation details into clear, user-centric product requirements.

## Goal
To analyze an existing ("brownfield") Python codebase managed with **uv** and produce a **concise, product-focused** Product Requirements Document (PRD) through a **multi-turn conversation**.

This PRD serves as a **baseline document** that captures the current state of the system. It is intended to be used alongside a Technical Specification Document (TSD) as context for planning future enhancements, bug fixes, or refactors—not for identifying problems to fix in the current implementation.

> **Note:** For existing systems, technical constraints often dictate product behavior (e.g., "reports update daily" due to batch jobs). These constraints **should** be captured if they impact the user experience.

## Process

1.  **Map & Analyze:** Explore the codebase structure. Extract a structured summary of *implemented* behavior.
2.  **Confidence Check:** Categorize findings by documentation confidence. Identify what needs user confirmation before documenting.
3.  **Ask Clarifying Questions:** Present targeted questions to ensure the documentation is accurate. **STOP and wait for answers.**
4.  **Generate PRD:** After receiving user answers, produce the final PRD document.

**Important:** This is a multi-turn workflow. Steps 1-3 happen in the first response. Step 4 happens only after the user responds.

---

## Step 1: Analyze the Codebase

Examine project artifacts with special attention to Python and uv-specific files.

### Python/uv-Specific Files to Examine

| File/Pattern | What to Look For |
|--------------|------------------|
| `pyproject.toml` | Project metadata, dependencies, scripts, entry points, tool configurations |
| `uv.lock` | Locked dependency versions, resolution details |
| `.python-version` | Python version constraint |
| `src/` or package directories | Main application code |
| `tests/` | Test files describing expected behavior |
| `scripts/` or CLI entry points | User-facing commands and utilities |
| `.env`, `.env.example` | Environment variables, feature flags |
| `Dockerfile`, `docker-compose.yml` | Containerization hints about deployment |
| `README.md`, `docs/` | Existing documentation |

### What to Extract

#### 1.1 Features & Capabilities
-   What the system *actually* does today.
-   CLI commands (check `[project.scripts]` in `pyproject.toml`).
-   API endpoints (FastAPI, Flask, Django routes).
-   Key workflows (start to finish).
-   User roles (inferred from auth middleware or permissions logic).

#### 1.2 Business Logic & Constraints
-   Domain rules embedded in code (e.g., "Order total must be > $0").
-   **User-Facing Technical Constraints:** (e.g., "Data syncs every 24h" or "Max file upload is 10MB").
-   State transitions (e.g., Pending -> Approved -> Shipped).
-   Validation rules in Pydantic models, dataclasses, or form validators.

#### 1.3 Integration Points
-   External APIs called (check `httpx`, `requests`, `aiohttp` usage).
-   Database connections (SQLAlchemy, asyncpg, psycopg, etc.).
-   Message queues (Celery, RQ, Kafka clients).
-   Cloud services (AWS SDK, GCP clients, Azure SDK).

#### 1.4 Observations
-   Code comments indicating business context.
-   Hardcoded values (magic numbers) that represent business rules.
-   Feature flags (environment variables, configuration toggles).
-   Optional dependencies in `pyproject.toml` that indicate feature sets.

---

## Step 2: Confidence Check

Before documenting, categorize your findings by confidence level:

| Category | Description | Action |
|----------|-------------|--------|
| **Verified** | Clearly confirmed by code, tests, or config. | Document directly. |
| **Needs Confirmation** | Code exists, but intent or scope is unclear. | Ask a clarifying question. |
| **Assumed** | Cannot determine from code; will use reasonable assumption. | State assumption; ask user to correct if wrong. |

The purpose of this step is to ensure the PRD accurately reflects the system—not to identify problems or gaps to fix.

---

## Step 3: Ask Clarifying Questions

Ask questions to ensure documentation accuracy. Focus on **scope and intent**, not on identifying issues.

### Question Categories

| Category | Focus |
|----------|-------|
| **A. Scope** | "Should I document feature X?" "Is this workflow in scope for the baseline?" |
| **B. Intent** | "Is this limit a business rule or a technical default?" "Is this the intended behavior?" |
| **C. Accuracy** | "The code does X. Is this correct, or should I document it differently?" |

### Formatting Requirements
-   **Number all questions.**
-   **Provide multiple-choice options** based on patterns found in code.
-   **Reference specific files** to give context.

### Example Format (Python/uv Context)

```markdown
Based on my analysis, I need to confirm the following before documenting:

### A. Scope

1. **CLI Commands:** I found three commands in `[project.scripts]`: `myapp`, `myapp-admin`, and `myapp-migrate`. For this baseline PRD:
   A. Document all three commands.
   B. Document only `myapp` (primary user-facing command).
   C. Document `myapp` and `myapp-admin` (exclude migration tools).

2. **Optional Dependencies:** The project has `[project.optional-dependencies]` for "dev", "test", and "ml". Should the PRD:
   A. Document only the core functionality.
   B. Document the ML features as part of the product.
   C. Document all optional feature sets.

### B. Intent

3. **Rate Limiting:** In `api/middleware.py`, I found a rate limit of `100` requests per minute. For documentation:
   A. This is a business rule (document as a system constraint).
   B. This is a technical default (document as a limitation).
   C. This should not be documented (internal implementation detail).

4. **Async Processing:** Tasks are queued via Celery in `tasks/`. Is this:
   A. Core product behavior (document the async nature).
   B. Implementation detail (document only the user-facing result).

### C. Accuracy

5. **User Roles:** Based on `auth/permissions.py`, I identified roles: Admin, Editor, Viewer. Is this complete?
   A. Complete—document these three.
   B. Incomplete—there are additional roles: _______.
```

---

## Step 4: Generate PRD

After receiving answers, generate the PRD.

### PRD Structure

1.  **System Summary** — What the system is and its primary purpose.
2.  **User Roles & Permissions** — Who uses the system and what they can do.
3.  **Functional Requirements** — Grouped by feature area, numbered for reference.
4.  **Business Rules** — Domain rules and validation logic.
5.  **System Constraints** — Technical limitations that affect user experience.
6.  **Edge Cases & Error Handling** — How the system handles boundaries and failures.
7.  **Assumptions** — Stated assumptions made during documentation.

### Guidelines
-   **Be Explicit:** "Users cannot delete admins" is better than "Manage permissions."
-   **Cite Sources:** (Optional) Reference files for key rules (e.g., "See `auth/permissions.py`").
-   **Describe Behavior, Not Code:** Good: "System validates email format." Bad: "`UserUtil.validate()` uses regex."
-   **State the Baseline:** This PRD documents *current* behavior, not ideal or future state.

---

## Output

-   **Format:** Markdown (`.md`)
-   **Filename:** `PRD-[service-name].md`
-   **Location:** Same directory as the companion TSD.

---

## Final Instructions

1.  **DO NOT** generate the PRD until you have asked clarifying questions AND received answers.
2.  **Start** by mapping the repo if it is large or unfamiliar.
3.  **ALWAYS** present analysis summary (Step 1) and confidence check (Step 2) in your first response.
4.  **ALWAYS** end your first response with clarifying questions (Step 3).
5.  **AFTER** receiving answers, generate the complete PRD (Step 4).
6.  If the user says "skip questions", proceed directly to Step 4, stating your assumptions.

---

# End of reverse-spec.md (Python/uv)
