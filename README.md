# Reverse Spec

**Reverse-engineer Product Requirements and Technical Specifications from existing code.**

While tools like [AI Dev Tasks](https://github.com/snarktank/ai-dev-tasks) excel at generating specs for *greenfield* projects (new features), `reverse-spec` is designed for **brownfield** projects (existing applications).

It guides an AI assistant to analyze your codebase, ask clarifying questions, and generate baseline documentation that captures what *actually* exists—ready to be used for planning enhancements and bug fixes.

## Why This Matters

Enterprises inherit legacy systems with incomplete or outdated documentation. Before enhancing a system, you need to understand:

1.  **What does it do?** → PRD (Product Requirements Document)
2.  **How is it built?** → TSD (Technical Specification Document)

These two documents form a **baseline**. Future enhancements and bug fixes can reference them to understand scope, impact, and context.

## Workflow

```
┌─────────────────┐     ┌─────────────────┐
│  Existing Code  │────▶│  reverse-spec   │────▶ PRD-[service].md
└─────────────────┘     └─────────────────┘
         │
         │              ┌─────────────────┐
         └─────────────▶│  reverse-arch   │────▶ TSD-[service].md
                        └─────────────────┘
```

Both prompts follow the same multi-turn approach:

1.  **Analyze** — AI scans the code and summarizes findings.
2.  **Confidence Check** — AI categorizes findings as Verified, Needs Confirmation, or Assumed.
3.  **Clarify** — AI asks targeted questions (you answer with "1A, 2B, 3C").
4.  **Generate** — AI produces the final document.

## Files

| File | Purpose | Output |
|------|---------|--------|
| `reverse-spec.md` | Extract *functional* requirements | `PRD-[service].md` |
| `reverse-arch.md` | Extract *technical* architecture | `TSD-[service].md` |

---

## Usage

### Step 1: Generate PRD (Functional Requirements)

Start with the PRD to document *what* the system does.

**Prompt:**
```text
Use @reverse-spec.md

Analyze the codebase at @/path/to/your/service and generate a Product Requirements Document (PRD).

This service is: [brief description, e.g., "an order management microservice that handles order creation, payment processing, and fulfillment tracking"].
```

The AI will:
1. Analyze the code and present a summary
2. Ask clarifying questions (e.g., "Should I document v1 and v2 endpoints, or just v2?")
3. Wait for your answers (respond with "1A, 2B, 3C" format)
4. Generate `PRD-[service].md`

### Step 2: Generate TSD (Technical Specification)

After the PRD is complete, generate the TSD to document *how* it's built.

**Prompt:**
```text
Use @reverse-arch.md

Analyze the codebase at @/path/to/your/service and generate a Technical Specification Document (TSD).

The companion PRD is at @PRD-[service].md for functional context.
```

The AI will follow the same flow and generate `TSD-[service].md`.

### Step 3: Use for Enhancements

With both documents in place, planning future work becomes easier:

**Prompt:**
```text
I need to add a new feature to this service.

Context:
- PRD: @PRD-order-service.md
- TSD: @TSD-order-service.md

Enhancement Request:
Add support for partial refunds. Users should be able to refund individual line items from an order, not just the full order.

Please generate a list of tasks and sub-tasks for this enhancement.
```

---

## Recommended Workflow

| Step | Action | Output |
|------|--------|--------|
| 1 | Run `reverse-spec.md` | `PRD-[service].md` |
| 2 | Run `reverse-arch.md` | `TSD-[service].md` |
| 3 | Use both docs for enhancement/bugfix planning | Task list |

**Why sequential?**
- PRD establishes functional context that TSD can reference
- Keeps questions focused (functional vs. technical)
- Smaller, more manageable chat sessions

**Alternative:** Run both prompts in parallel (separate chat sessions) if you're familiar with the codebase and want speed.

---

## Tips

- **Be specific about scope:** If analyzing a monorepo, point to the specific service directory.
- **Provide context:** A one-line description of what the service does helps the AI focus.
- **Skip questions if needed:** Say "skip questions" to have the AI proceed with stated assumptions.
- **Iterate:** If the output misses something, ask the AI to add or revise specific sections.

---

## Contributing

Contributions are welcome. If you improve the prompts for specific frameworks, languages, or edge cases, please open a PR.

## License

Apache-2.0
