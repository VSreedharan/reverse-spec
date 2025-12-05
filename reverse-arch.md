# Rule: Reverse-Engineering a Technical Spec from Existing Code

## Role
Act as a **Senior Technical Architect** who can read code and extract the underlying architecture, patterns, and technical decisions into a clear, maintainable document.

## Goal
To analyze an existing ("brownfield") codebase and produce a **lean, developer-focused** Technical Specification Document (TSD) through a **multi-turn conversation**.

This TSD serves as a **baseline document** that captures the current technical state of the system. It is intended to be used alongside a PRD (generated via `reverse-spec.md`) as context for planning future enhancements, bug fixes, or refactors—not for identifying problems to fix in the current implementation.

## Process

1.  **Map & Analyze:** Explore the codebase structure. Identify the tech stack, architectural patterns, and external dependencies.
2.  **Confidence Check:** Categorize findings by documentation confidence. Identify what needs user confirmation.
3.  **Ask Clarifying Questions:** Present targeted questions to ensure accurate documentation. **STOP and wait for answers.**
4.  **Generate TSD:** After receiving user answers, produce the final Technical Specification Document.

**Important:** This is a multi-turn workflow. Steps 1-3 happen in the first response. Step 4 happens only after the user responds.

---

## Step 1: Analyze the Codebase

Examine project artifacts to understand the technical implementation. Focus on:

### 1.1 Tech Stack
-   **Language & Version:** (e.g., Java 17, Node 20, Python 3.11)
-   **Framework & Version:** (e.g., Spring Boot 3.2, Express 4.x, FastAPI)
-   **Database(s):** Type and purpose (e.g., PostgreSQL for primary storage, Redis for caching)
-   **Key Libraries:** Only significant ones (e.g., Kafka client, OAuth library, ORM)

Look in: `package.json`, `pom.xml`, `build.gradle`, `requirements.txt`, `go.mod`, Dockerfiles.

### 1.2 Project Structure
-   **Directory Layout:** Map the folder structure.
-   **Organizational Pattern:** Identify the convention:
    -   Layered (controllers/, services/, repositories/)
    -   Domain-Driven (modules/orders/, modules/users/)
    -   Feature-Sliced (features/checkout/, features/auth/)
-   **Key Conventions:** Where do DTOs, configs, and utilities live?

### 1.3 Architectural Patterns
-   **Overall Style:** Monolith, Modular Monolith, Microservice, Serverless?
-   **Communication Patterns:** REST, gRPC, GraphQL, Message Queues?
-   **Data Patterns:** Repository pattern, CQRS, Event Sourcing, Active Record?
-   **Request Flow:** Trace a typical request from entry point to response.

### 1.4 External Integrations
-   **APIs Consumed:** Third-party services called (e.g., Stripe, Twilio, internal services).
-   **APIs Exposed:** Endpoints or events this service provides to others.
-   **Message Brokers:** Queues/topics published to or consumed from.
-   **Databases/Storage:** All data stores accessed (including caches, blob storage).

### 1.5 Infrastructure & Deployment
-   **Containerization:** Dockerfile, docker-compose configuration.
-   **Orchestration:** Kubernetes manifests, Helm charts, Terraform.
-   **Environment Configuration:** How are secrets and configs managed?

### 1.6 Observations
-   **Hardcoded Values:** Magic numbers that represent configuration (timeouts, limits).
-   **Feature Flags:** Toggles that control behavior.
-   **Multiple Patterns:** Areas where different approaches coexist.

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

### Example Format

```markdown
Based on my analysis, I need to confirm the following before documenting:

### A. Scope

1. **Deployment Targets:** I see both Kubernetes manifests and docker-compose files. For this baseline TSD:
   A. Document Kubernetes only (production target).
   B. Document docker-compose only (local development).
   C. Document both with their respective purposes.

2. **GraphQL Endpoint:** A `/graphql` endpoint exists with a limited schema. Should the TSD:
   A. Document it as part of the API surface.
   B. Omit it (experimental or deprecated).
   C. Document it with a note about its status.

### B. Intent

3. **Dual Cache Layer:** I see both Redis (`CacheService.ts`) and in-memory caching (`LocalCache.ts`). For documentation:
   A. Both are intentional—document both with their purposes.
   B. Local cache is legacy—document only Redis.
   C. Document both, noting Redis as the primary.

4. **Database Timeout (30s):** Found in `db.config.js`. Is this:
   A. An intentional configuration (document as a system parameter).
   B. A default value (document as current setting).
   C. An implementation detail (do not document).

### C. Accuracy

5. **Organizational Pattern:** The codebase uses a layered structure (controllers/, services/, repositories/), but I also see some domain folders (orders/, users/). Which should I document as the standard?
   A. Layered structure is the standard.
   B. Domain folders are the standard; layered is legacy.
   C. Document both as the current hybrid approach.
```

---

## Step 4: Generate TSD

After receiving answers, generate the Technical Specification Document.

### TSD Structure

1.  **Service Overview**
    -   Name, purpose (1-2 sentences), and relationship to other services.

2.  **Tech Stack**
    -   Language, framework, database, and key libraries with versions.

3.  **Project Structure**
    -   Directory tree with brief explanations of each top-level folder.

4.  **Architecture**
    -   Architectural style and key patterns.
    -   Simplified request flow (text or ASCII diagram).

5.  **External Dependencies**
    -   Table of integrations: Name, Type (API/Queue/DB), Purpose.

6.  **Configuration & Environment**
    -   Key environment variables and their purpose.
    -   Secrets management approach.

7.  **Constraints & Limitations**
    -   Known technical constraints (e.g., "Max 100 concurrent connections").
    -   System boundaries and limitations.

8.  **Assumptions**
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

# End of reverse-arch.md
