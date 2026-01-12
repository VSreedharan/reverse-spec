# Rule: Reverse-Engineering a Technical Spec from Existing Vanilla JavaScript Code

## Role
Act as a **Senior Technical Architect** who can extract architecture, patterns, and technical decisions from vanilla JavaScript into a clear document.

## Goal
Analyze an existing vanilla JavaScript codebase and produce a **Technical Specification Document (TSD)** through a **multi-turn conversation**.

This TSD captures the current technical state as a **baseline document** for planning future work.

## Process

1. **Map & Analyze:** Identify tech stack, patterns, and dependencies.
2. **Confidence Check:** Categorize findings by confidence level.
3. **Ask Clarifying Questions:** Present targeted questions. **STOP and wait.**
4. **Generate TSD:** After answers, produce the final TSD.

---

## Step 1: Analyze the Codebase

### 1.1 Tech Stack (Vanilla JS Focus)

| What to Find | Where to Look |
|--------------|---------------|
| **Module System** | ES Modules, CommonJS, IIFE, AMD |
| **Build Tool** | Webpack, Vite, Rollup, Parcel, none |
| **Package Manager** | npm, yarn, pnpm (lockfiles) |
| **Transpilation** | Babel config, TypeScript |
| **Testing** | Jest, Mocha, Vitest |

Check `package.json` for:
```json
{
  "type": "module",        // ES Modules
  "main": "index.js",      // Entry point
  "scripts": {},           // Build/dev commands
  "dependencies": {},      // Runtime deps
  "devDependencies": {}    // Build/test deps
}
```

### 1.2 Project Structure

```
project/
├── package.json           # Project config
├── package-lock.json      # Locked versions
├── index.html             # Entry HTML
├── src/                   # Source code
│   ├── index.js           # Main entry
│   ├── modules/           # Feature modules
│   ├── utils/             # Utilities
│   └── components/        # UI components
├── dist/                  # Build output
├── tests/                 # Test files
└── public/                # Static assets
```

Identify patterns:
- **Single File:** All code in one file
- **Module-based:** ES modules with imports
- **Feature-based:** Organized by feature
- **MVC/MVP:** Model-View-Controller structure

### 1.3 Architectural Patterns

- **Module Pattern:** IIFE, revealing module
- **Event-Driven:** Custom events, pub/sub
- **State Management:** Global state, stores
- **DOM Strategy:** Direct manipulation, virtual DOM lib
- **Async Patterns:** Promises, async/await, callbacks

### 1.4 External Integrations

| Type | Look For |
|------|----------|
| **HTTP** | fetch, axios, XMLHttpRequest |
| **Storage** | LocalStorage, IndexedDB, cookies |
| **UI Libraries** | jQuery, Alpine.js, HTMX |
| **Utilities** | Lodash, date-fns, moment |
| **Build** | Webpack loaders, Vite plugins |

### 1.5 Vanilla JS Observations

- **Browser APIs:** Canvas, Web Workers, WebSocket
- **Polyfills:** Core-js, whatwg-fetch
- **Feature Detection:** Modernizr, manual checks
- **Error Handling:** try/catch, error boundaries
- **Performance:** Lazy loading, debouncing

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

1. **Build Process:** Found Webpack config. Document:
   A. Source code only
   B. Build configuration included
   C. Both source and build pipeline

### B. Intent

2. **jQuery Dependency:** Used for DOM and AJAX:
   A. Core dependency (document usage)
   B. Legacy, being removed
   C. Specific features only

### C. Accuracy

3. **Module System:** Mix of ES modules and script tags:
   A. ES modules is standard
   B. Script tags for legacy support
   C. Document hybrid approach
```

---

## Step 4: Generate TSD

### TSD Structure

1. **Service Overview** — Name, purpose, browser targets
2. **Tech Stack** — JS version, build tools, key libraries
3. **Project Structure** — Directory tree with explanations
4. **Architecture** — Module pattern, state, event handling
5. **External Dependencies** — Table: Name, Type, Purpose
6. **Configuration** — Build config, environment vars
7. **Development Workflow**
   ```bash
   npm install              # Install dependencies
   npm run dev              # Development server
   npm run build            # Production build
   npm run test             # Run tests
   ```
8. **Browser Support** — Target browsers, polyfills
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
# End of reverse-arch.md (Vanilla JS)
