# Rule: Reverse-Engineering a Technical Spec from Existing Next.js Code

## Role
Act as a **Senior Technical Architect** specializing in Next.js who can extract architecture, patterns, and technical decisions into a clear document.

## Goal
Analyze an existing Next.js codebase and produce a **Technical Specification Document (TSD)** through a **multi-turn conversation**.

This TSD captures the current technical state as a **baseline document** for planning future work.

## Process

1. **Map & Analyze:** Identify tech stack, patterns, and dependencies.
2. **Confidence Check:** Categorize findings by confidence level.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait.**
4. **Generate TSD:** After answers, produce the final TSD.

---

## Step 1: Analyze the Codebase

### 1.1 Tech Stack (Next.js Focus)

| What to Find | Where to Look |
|--------------|---------------|
| **Next.js Version** | `package.json` (`next` dependency) |
| **React Version** | `package.json` (`react` dependency) |
| **Router Type** | `app/` = App Router, `pages/` = Pages Router |
| **Styling** | Tailwind, CSS Modules, styled-components |
| **Database/ORM** | Prisma, Drizzle, Mongoose |
| **Auth** | NextAuth, Clerk, Auth0, custom |

### 1.2 Project Structure

```
project/
├── package.json           # Dependencies and scripts
├── next.config.js         # Next.js configuration
├── app/                   # App Router (Next.js 13+)
│   ├── layout.tsx         # Root layout
│   ├── page.tsx           # Home page
│   ├── api/               # API routes
│   └── (routes)/          # Route groups
├── pages/                 # Pages Router (legacy)
├── components/            # React components
├── lib/                   # Utilities, clients
├── prisma/                # Database schema
├── public/                # Static assets
└── middleware.ts          # Edge middleware
```

Identify patterns:
- **App Router:** Server Components, layouts, loading states
- **Pages Router:** getServerSideProps, getStaticProps
- **Hybrid:** Both routers coexisting

### 1.3 Architectural Patterns

- **Rendering:** SSR, SSG, ISR, Client-side?
- **Data Fetching:** Server Components, SWR, React Query?
- **State Management:** Zustand, Jotai, Redux, Context?
- **API Design:** Route Handlers, tRPC, REST?

### 1.4 External Integrations

| Type | Look For |
|------|----------|
| **Database** | Prisma, Drizzle, PlanetScale, Supabase |
| **Auth** | NextAuth providers, Clerk, Auth0 |
| **Payments** | Stripe, LemonSqueezy |
| **Analytics** | Vercel Analytics, PostHog, Plausible |
| **CMS** | Contentful, Sanity, Strapi |

### 1.5 Next.js-Specific Observations

- **Server vs Client:** `'use client'` directives
- **Caching:** `revalidate`, `cache` settings
- **Middleware:** Auth, redirects, i18n
- **Environment:** `NEXT_PUBLIC_*` vs server-only vars

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

1. **Deployment:** Found Vercel config and Docker. Document:
   A. Vercel only (primary)
   B. Docker/self-hosted only
   C. Both deployment options

### B. Intent

2. **App + Pages Router:** Both exist. For documentation:
   A. App Router is primary, Pages is legacy
   B. Both actively used for different purposes
   C. Migration in progress to App Router

### C. Accuracy

3. **API Routes:** Found REST endpoints and tRPC. Primary pattern?
   A. tRPC for type-safe APIs
   B. REST for public APIs
   C. Document both patterns
```

---

## Step 4: Generate TSD

### TSD Structure

1. **Service Overview** — Name, purpose, relationships
2. **Tech Stack** — Next.js, React, DB, styling with versions
3. **Project Structure** — Directory tree with explanations
4. **Architecture** — Rendering strategy, data flow
5. **External Dependencies** — Table: Name, Type, Purpose
6. **Configuration** — Environment variables, next.config
7. **Development Workflow**
   ```bash
   npm install              # Install dependencies
   npm run dev              # Development server
   npm run build            # Production build
   npm run start            # Start production
   npx prisma generate      # Generate Prisma client
   ```
8. **Constraints** — Node version, deployment requirements
9. **Assumptions** — Stated assumptions

---

## Output

- **Format:** Markdown
- **Filename:** `TSD-[app-name].md`

---

## Final Instructions

1. **DO NOT** generate TSD until questions are answered
2. **Start** with analysis summary and confidence check
3. **END first response** with clarifying questions
4. If user says "skip questions", proceed with stated assumptions

---
# End of reverse-arch.md (Next.js)
