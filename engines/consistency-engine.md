# Consistency Engine

## Purpose

Ensures that responses are consistent with prior session decisions, conventions, and architecture. Prevents contradictions that undermine trust and project coherence.

---

## Consistency Dimensions

| Dimension | What it checks |
|---|---|
| Architectural | Does this response contradict the established system architecture? |
| Naming | Does this response use established names consistently? |
| Style | Does code follow the established coding style and conventions? |
| Decision | Does this response contradict a prior explicit decision? |
| Terminology | Are technical terms used consistently with prior definitions? |

---

## Algorithm

```
function checkConsistency(draft, memory):

  violations = []

  # Architectural consistency
  if memory.architecture exists:
    for element in draft.architecturalElements:
      if conflicts(element, memory.architecture):
        violations.append(ArchitecturalViolation(element))

  # Decision consistency
  for decision in memory.decisions:
    if draft contradicts decision:
      violations.append(DecisionViolation(decision, draft))

  # Convention consistency
  for convention in memory.conventions:
    if draft violates convention:
      violations.append(ConventionViolation(convention))

  # Resolve violations
  for violation in violations:
    if violation is resolvable:
      draft = resolve(draft, violation, memory)
    else:
      flag violation for user

  return draft, flagged_violations
```

---

## Resolution Protocol

| Violation Type | Resolution |
|---|---|
| Name mismatch | Align to established name; note the correction |
| Style deviation | Apply established style automatically |
| Architectural conflict | Surface conflict; ask user to resolve if non-trivial |
| Decision contradiction | Align to prior decision; note the alignment |
| Updated requirement | If user has changed a requirement, update memory and proceed |

---

## Convention Tracking Format

Conventions are recorded in `memory/conventions.md`:

```markdown
### [Convention Name]
- **Type:** Naming / Style / Structure / Format
- **Rule:** [The convention]
- **Example:** [Correct usage]
- **Counter-example:** [Incorrect usage]
- **Established:** [When/why]
```

---

## When to Escalate

Some consistency conflicts cannot be resolved automatically:
- The user appears to have changed direction (new architecture conflicts with old)
- Two prior decisions now conflict with each other
- The user's current request requires violating an established constraint

In these cases:
1. Identify the conflict clearly
2. Present both the prior decision and the current requirement
3. Ask the user which takes precedence
4. Update memory with the new decision
