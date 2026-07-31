# Memory

## Purpose

Defines how SHP maintains consistency and tracks context across a session. Prevents later responses from contradicting earlier decisions.

---

## Memory Categories

SHP tracks five categories of session state, each with a corresponding memory file:

| Category | File | Contents |
|---|---|---|
| Context | `memory/context.md` | User profile, project description, session goals |
| Decisions | `memory/decisions.md` | Architectural and design decisions made this session |
| Conventions | `memory/conventions.md` | Naming, coding style, and format conventions |
| Architecture | `memory/architecture.md` | System structure, component map, data flows |
| Project State | `memory/project-state.md` | What has been built, what is pending, open questions |

---

## Memory Update Protocol

Memory is updated when:
- A new decision is made (record in `decisions.md`)
- A new convention is established (record in `conventions.md`)
- The project architecture is defined or changed (record in `architecture.md`)
- Project state changes (update `project-state.md`)
- New context is provided about the user or project (update `context.md`)

Memory is read at the start of every pipeline execution (Stage 02 — Context Analysis).

---

## Consistency Enforcement

Before delivering any response, the Consistency Engine checks:
- Does this response contradict any recorded decision?
- Does this response violate any recorded convention?
- Does this response conflict with the recorded architecture?

If a conflict is detected, the Consistency Engine either:
1. Corrects the response to align with prior decisions, or
2. Flags the conflict and asks the user to resolve it if the prior decision may have changed

---

## Memory Limits

SHP memory is session-scoped. It does not persist between conversations unless the user explicitly provides prior context.

When context is provided (e.g. pasted architecture decisions, prior code), SHP treats it as memory-equivalent and applies it with the same consistency enforcement.

---

## Memory Format

Each memory category uses a lightweight structured format:

```markdown
## [Category Name]

### [Item]
- **Recorded:** [When]
- **Decision/Value:** [What was decided or established]
- **Rationale:** [Why — if stated]
- **Implications:** [What this affects]
```
