# Reverse Spec

**Reverse-engineer Product Requirements and Technical Specifications from existing code.**

While tools like [AI Dev Tasks](https://github.com/snarktank/ai-dev-tasks) excel at generating specs for *greenfield* projects (new features), `reverse-spec` is designed for **brownfield** projects (existing applications).

It guides an AI assistant to analyze your codebase, ask clarifying questions, and generate baseline documentation that captures what *actually* exists—ready to be used for planning enhancements and bug fixes.

## Why This Matters

Enterprises inherit legacy systems with incomplete or outdated documentation. To practice **Spec-Driven Development (SDD)**, you first need a reliable baseline.

1.  **Baseline:** Extract the current state into a PRD & Tech Spec.
2.  **Spec Phase:** Update the PRD to reflect the *new* feature (Target State).
3.  **Task Phase:** Generate tasks to bridge the gap between the Tech Spec (Current State) and the updated PRD (Target State).

## Workflow

```
┌─────────────────┐     ┌─────────────────┐
│  Existing Code  │────▶│  reverse-spec   │────▶ Baseline PRD
└─────────────────┘     └─────────────────┘           │
         │                                            │ (Add Feature)
         │                                            ▼
         │                                       Target PRD
         │                                            │
         │              ┌─────────────────┐           │
         └─────────────▶│  reverse-arch   │────▶ Baseline TSD
                        └─────────────────┘           │
                                                      │
                                                      ▼
                                             ┌──────────────────┐
                                             │   AI Dev Tasks   │
                                             │ (generate-tasks) │
                                             └──────────────────┘
                                                      │
                                                      ▼
                                               Actionable Tasks
```

## Files

| File | Purpose | Output |
|------|---------|--------|
| `reverse-spec.md` | Extract *functional* requirements | `PRD-[service].md` |
| `reverse-arch.md` | Extract *technical* architecture | `TSD-[service].md` |

---

## Usage

### Step 1: Generate Baseline PRD

Start with the PRD to document *what* the system does today.

**Prompt:**
```text
Use @reverse-spec.md

Analyze the codebase at @/path/to/your/service and generate a Product Requirements Document (PRD).

This service is: [brief description].
```

### Step 2: Generate Baseline TSD

Document *how* it is built today.

**Prompt:**
```text
Use @reverse-arch.md

Analyze the codebase at @/path/to/your/service and generate a Technical Specification Document (TSD).

The companion PRD is at @PRD-[service].md for functional context.
```

### Step 3: Update PRD (The Spec Phase)

**This is the most critical step for SDD.** Before generating tasks, update the PRD to reflect the *future* state.

**Prompt:**
```text
I need to update the PRD for a new feature.

Current PRD: @PRD-[service].md

New Feature Request:
[Describe feature, e.g., "Add support for partial refunds."]

Please update the PRD to include this new feature.
- Add it to Functional Requirements.
- Update Business Rules if applicable.
- Update API/Edge Cases if applicable.

Output the full updated PRD markdown.
```

*Save the output as the new version of `PRD-[service].md`.*

### Step 4: Generate Tasks (The Implementation Phase)

Now, use [AI Dev Tasks](https://github.com/snarktank/ai-dev-tasks) to bridge the gap between your **Target PRD** and your **Baseline TSD**.

**Prompt:**
```text
Use @generate-tasks.md

Context:
- Target PRD (What we want): @PRD-[service].md
- Baseline Tech Spec (What we have): @TSD-[service].md

Please generate a list of tasks and sub-tasks to implement the changes defined in the Target PRD, given the architectural constraints in the Baseline TSD.
```

---

## Recommended Workflow

| Step | Action | Output |
|------|--------|--------|
| 1 | Run `reverse-spec.md` | Baseline PRD |
| 2 | Run `reverse-arch.md` | Baseline TSD |
| 3 | **Prompt AI to update PRD** | **Target PRD** |
| 4 | Run `generate-tasks.md` | Task List |

---

## Contributing

Contributions are welcome. If you improve the prompts for specific frameworks, languages, or edge cases, please open a PR.

## License

Apache-2.0
