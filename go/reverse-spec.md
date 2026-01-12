# Rule: Reverse-Engineering a PRD from Existing Go Code

## Role
Act as a **Senior Technical Architect** specializing in Go ecosystems who can read code and translate implementation details into clear, user-centric product requirements.

## Goal
Analyze an existing Go codebase using **Go modules** and produce a **Product Requirements Document (PRD)** through a **multi-turn conversation**.

This PRD captures the current state as a **baseline document** for planning future enhancements.

## Process

1. **Map & Analyze:** Explore the codebase. Extract implemented behavior.
2. **Confidence Check:** Categorize findings by confidence. Identify what needs confirmation.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait for answers.**
4. **Generate PRD:** After receiving answers, produce the final PRD.

---

## Step 1: Analyze the Codebase

### Go-Specific Files to Examine

| File/Pattern | What to Look For |
|--------------|------------------|
| `go.mod` | Module path, Go version, dependencies |
| `go.sum` | Locked dependency versions |
| `cmd/` | CLI entry points and commands |
| `internal/` | Private application code |
| `pkg/` | Public reusable packages |
| `api/` | API definitions (OpenAPI, Proto files) |
| `configs/`, `.env` | Configuration and environment variables |
| `Dockerfile`, `Makefile` | Build and deployment hints |

### What to Extract

#### 1.1 Features & Capabilities
- What the system does today
- CLI commands (check `cmd/` subdirectories)
- API endpoints (HTTP handlers, gRPC services)
- Key workflows and user roles

#### 1.2 Business Logic & Constraints
- Domain rules in code (validation, business logic)
- User-facing constraints (e.g., "Max upload 10MB")
- State transitions and validation rules

#### 1.3 Integration Points
- External APIs (`net/http` clients, gRPC clients)
- Databases (database/sql, GORM, sqlx, pgx)
- Message queues (Kafka, NATS, RabbitMQ clients)

#### 1.4 Observations
- Hardcoded values representing business rules
- Feature flags and build tags
- Interface definitions indicating capabilities

---

## Step 2: Confidence Check

| Category | Description | Action |
|----------|-------------|--------|
| **Verified** | Confirmed by code, tests, or config | Document directly |
| **Needs Confirmation** | Intent unclear | Ask a question |
| **Assumed** | Cannot determine from code | State assumption |

---

## Step 3: Ask Clarifying Questions

Format questions with:
- **Numbered questions**
- **Multiple-choice options** based on code patterns
- **File references** for context

### Example Format

```markdown
### A. Scope

1. **CLI Commands:** Found `cmd/server/` and `cmd/cli/`. Document:
   A. Both commands
   B. Only server (primary)
   C. Other: _______

### B. Intent

2. **Rate Limit (100/min):** In `internal/middleware/ratelimit.go`:
   A. Business rule (document as constraint)
   B. Technical default (document as limitation)
   C. Internal detail (do not document)

### C. Accuracy

3. **User Roles:** Found Admin, User, Guest in `internal/auth/`. Complete?
   A. Yes, document these three
   B. No, additional roles: _______
```

---

## Step 4: Generate PRD

### PRD Structure

1. **System Summary** — Purpose and primary function
2. **User Roles & Permissions** — Who uses it and capabilities
3. **Functional Requirements** — Grouped by feature, numbered
4. **Business Rules** — Domain rules and validation
5. **System Constraints** — Technical limitations affecting UX
6. **Edge Cases & Error Handling** — Boundary behaviors
7. **Assumptions** — Stated assumptions

### Guidelines
- **Be Explicit:** "Users cannot delete admins" > "Manage permissions"
- **Describe Behavior, Not Code:** "Validates email format" > "`Validate()` uses regex"
- **State the Baseline:** Documents *current* behavior

---

## Output

- **Format:** Markdown
- **Filename:** `PRD-[service-name].md`

---

## Final Instructions

1. **DO NOT** generate PRD until questions are answered
2. **Start** with analysis summary and confidence check
3. **End first response** with clarifying questions
4. If user says "skip questions", proceed with stated assumptions

---
# End of reverse-spec.md (Go)
