# Rule: Reverse-Engineering a Technical Spec from Existing Go Code

## Role
Act as a **Senior Technical Architect** specializing in Go ecosystems who can extract architecture, patterns, and technical decisions into a clear document.

## Goal
Analyze an existing Go codebase using **Go modules** and produce a **Technical Specification Document (TSD)** through a **multi-turn conversation**.

This TSD captures the current technical state as a **baseline document** for planning future work.

## Process

1. **Map & Analyze:** Identify tech stack, patterns, and dependencies.
2. **Confidence Check:** Categorize findings by confidence level.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait.**
4. **Generate TSD:** After answers, produce the final TSD.

---

## Step 1: Analyze the Codebase

### 1.1 Tech Stack (Go Modules Focus)

| What to Find | Where to Look |
|--------------|---------------|
| **Go Version** | `go.mod` (`go 1.21`) |
| **Module Path** | `go.mod` (`module github.com/org/repo`) |
| **Dependencies** | `go.mod` require blocks |
| **Framework** | Gin, Echo, Chi, Fiber, stdlib `net/http` |
| **Database** | GORM, sqlx, pgx, database/sql |

### 1.2 Project Structure

Standard Go project layout:
```
project/
├── go.mod              # Module definition
├── go.sum              # Dependency checksums
├── main.go             # Entry point (simple projects)
├── cmd/                # CLI entry points
│   ├── server/         # Server binary
│   └── cli/            # CLI tool
├── internal/           # Private packages
│   ├── handler/        # HTTP/gRPC handlers
│   ├── service/        # Business logic
│   ├── repository/     # Data access
│   └── model/          # Domain models
├── pkg/                # Public reusable packages
├── api/                # API definitions (OpenAPI, Proto)
└── configs/            # Configuration files
```

Identify the pattern:
- **Flat:** Single package, simple projects
- **Standard Layout:** `cmd/`, `internal/`, `pkg/`
- **Domain-Driven:** Packages by domain (`order/`, `user/`)
- **Hexagonal:** `adapters/`, `ports/`, `domain/`

### 1.3 Architectural Patterns

- **Style:** Monolith, Microservice, Serverless (AWS Lambda)?
- **API:** REST, gRPC, GraphQL?
- **Concurrency:** Goroutines, channels, worker pools?
- **Data Access:** Repository pattern, direct queries?
- **DI:** Wire, fx, manual injection?

### 1.4 External Integrations

| Type | Look For |
|------|----------|
| **HTTP Clients** | `net/http`, `resty`, custom clients |
| **Databases** | GORM, sqlx, pgx, mongodb-driver |
| **Queues** | Kafka, NATS, RabbitMQ clients |
| **Cloud** | aws-sdk-go, google-cloud-go |
| **Observability** | OpenTelemetry, Prometheus, Zap/Zerolog |

### 1.5 Go-Specific Observations

- **Build Tags:** `//go:build` for conditional compilation
- **Generate Directives:** `//go:generate` for code generation
- **Interfaces:** Key interfaces defining contracts
- **Error Handling:** Custom error types, wrapping patterns
- **Testing:** Table-driven tests, mocks, test fixtures

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

1. **Binaries:** Found `cmd/server/` and `cmd/migrate/`. Document:
   A. Both binaries
   B. Server only (primary)
   C. Server + migration as operational tool

### B. Intent

2. **gRPC + REST:** Both `api/grpc/` and `api/http/` exist:
   A. Both are primary (document both)
   B. gRPC is internal, REST is public
   C. REST is legacy, gRPC is standard

### C. Accuracy

3. **Database Pattern:** Using repository pattern in `internal/repository/`:
   A. Yes, repository is the standard
   B. Mixed—some direct queries exist
   C. Document as-is
```

---

## Step 4: Generate TSD

### TSD Structure

1. **Service Overview** — Name, purpose, relationships
2. **Tech Stack** — Go version, framework, DB, key libraries
3. **Project Structure** — Directory tree with explanations
4. **Architecture** — Style, patterns, request flow
5. **External Dependencies** — Table: Name, Type, Purpose
6. **Configuration** — Environment variables, config files
7. **Development Workflow**
   ```bash
   go mod download          # Download dependencies
   go build ./cmd/server    # Build server binary
   go test ./...            # Run all tests
   go run ./cmd/server      # Run server
   go generate ./...        # Run code generators
   ```
8. **Constraints** — Go version requirements, CGO needs
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
# End of reverse-arch.md (Go)
