# Rule: Reverse-Engineering a PRD from Existing Next.js Code

## Role
Act as a **Senior Technical Architect** specializing in Next.js who can read code and translate implementation details into clear, user-centric product requirements.

## Goal
Analyze an existing Next.js codebase and produce a **Product Requirements Document (PRD)** through a **multi-turn conversation**.

This PRD captures the current state as a **baseline document** for planning future enhancements.

## Process

1. **Map & Analyze:** Explore the codebase. Extract implemented behavior.
2. **Confidence Check:** Categorize findings by confidence. Identify what needs confirmation.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait for answers.**
4. **Generate PRD:** After receiving answers, produce the final PRD.

---

## Step 1: Analyze the Codebase

### Next.js-Specific Files to Examine

| File/Pattern | What to Look For |
|--------------|------------------|
| `package.json` | Dependencies, scripts, project metadata |
| `next.config.js/ts` | Next.js configuration, rewrites, redirects |
| `app/` or `pages/` | Routes, pages, layouts (App Router vs Pages Router) |
| `components/` | UI components and their capabilities |
| `lib/`, `utils/` | Business logic and utilities |
| `api/` or `app/api/` | API routes and endpoints |
| `.env.local`, `.env` | Environment variables, feature flags |
| `middleware.ts` | Auth, redirects, request handling |

### What to Extract

#### 1.1 Features & Capabilities
- What the application does today
- Pages and routes (check `app/` or `pages/`)
- API endpoints (`/api/*` routes)
- Key user workflows and interactions

#### 1.2 Business Logic & Constraints
- Domain rules in code (validation, business logic)
- User-facing constraints (e.g., "Max upload 5MB")
- Auth patterns (NextAuth, Clerk, custom)
- Role-based access in middleware or layouts

#### 1.3 Integration Points
- External APIs (fetch calls, API clients)
- Databases (Prisma, Drizzle, direct connections)
- Auth providers (OAuth, credentials)
- Third-party services (Stripe, analytics, etc.)

#### 1.4 Observations
- Hardcoded values representing business rules
- Feature flags and environment-based features
- Server vs Client components usage patterns

---

## Step 2: Confidence Check

| Category | Description | Action |
|----------|-------------|--------|
| **Verified** | Confirmed by code, tests, or config | Document directly |
| **Needs Confirmation** | Intent unclear | Ask a question |
| **Assumed** | Cannot determine from code | State assumption |

---

## Step 3: Ask Clarifying Questions

### Example Format

```markdown
### A. Scope

1. **Router Type:** Found both `app/` and `pages/`. Document:
   A. App Router only (primary)
   B. Pages Router only (legacy)
   C. Both with migration context

### B. Intent

2. **Auth Middleware:** Protects `/dashboard/*` routes:
   A. Document as user-facing access control
   B. Technical implementation detail
   C. Document with role breakdown

### C. Accuracy

3. **User Roles:** Found admin, user in NextAuth config. Complete?
   A. Yes, document these two
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
- **Describe Behavior, Not Code:** "Validates email format" > "`validate()` uses regex"
- **State the Baseline:** Documents *current* behavior

---

## Output

- **Format:** Markdown
- **Filename:** `PRD-[app-name].md`

---

## Final Instructions

1. **DO NOT** generate PRD until questions are answered
2. **Start** with analysis summary and confidence check
3. **End first response** with clarifying questions
4. If user says "skip questions", proceed with stated assumptions

---
# End of reverse-spec.md (Next.js)
