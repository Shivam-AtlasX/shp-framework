# Reflection Engine

## Purpose

Challenges the initial solution before it enters specialist reviews. The first draft is a candidate, not a conclusion. The Reflection Engine generates alternatives, compares them honestly, and improves or replaces the initial solution where warranted.

---

## Activation Criteria

Invoked at Stage 12 of the pipeline for all requests at Complexity Level 1 or above.

Always active. Trivial requests bypass it via the fast path defined in Stage 05.

---

## Core Questions

Before the initial solution is accepted, the engine asks:

1. **Is there a simpler solution?**
   Can the same goal be achieved with less code, fewer components, fewer concepts?

2. **Is there a safer solution?**
   Does the initial solution introduce risk — security, operational, or epistemic — that an alternative avoids?

3. **Is there a faster solution?**
   Is there an algorithmic or architectural improvement that the initial approach missed?

4. **Is there a more maintainable solution?**
   Will the initial solution be easy to understand, change, and operate in three months?

5. **Does the solution actually serve the underlying goal?**
   Does it solve the real problem (Stage 03 — Goal Identification), not only the stated question?

---

## Algorithm

```
function reflect(initial_draft, intent, context, constraints):

  improvements = []

  # Simplicity probe
  simpler = generateSimplifiedAlternative(initial_draft, context)
  if simpler achieves the same goal with meaningfully less complexity:
    improvements.append(SIMPLIFICATION, simpler)

  # Safety probe
  if riskProfile.security or riskProfile.operational:
    safer = generateSaferAlternative(initial_draft, context)
    if safer mitigates an identified risk:
      improvements.append(SAFETY_IMPROVEMENT, safer)

  # Performance probe
  if riskProfile.performance or complexity >= COMPLEX:
    faster = generatePerformanceAlternative(initial_draft, context)
    if faster is materially better in a context-relevant dimension:
      improvements.append(PERFORMANCE_IMPROVEMENT, faster)

  # Maintainability probe
  maintainable = generateMaintainableAlternative(initial_draft, context)
  if maintainable is significantly easier to read, test, or change:
    improvements.append(MAINTAINABILITY_IMPROVEMENT, maintainable)

  # Goal alignment check
  if initial_draft serves immediate_goal but not underlying_goal:
    improvements.append(GOAL_MISALIGNMENT, reframe_for_underlying_goal)

  # Selection
  if improvements is empty:
    return initial_draft  # Initial solution is already optimal

  best_improvement = selectByPriority(improvements, context)

  if best_improvement is clearly superior:
    return apply(initial_draft, best_improvement)
  else:
    return present_both(initial_draft, best_improvement, trade_off_explanation)
```

---

## Improvement Priority Order

When multiple improvements are possible, prioritise by:

1. **Safety** — a safer solution always takes precedence
2. **Correctness** — if the alternative is more correct
3. **Goal alignment** — if the initial misses the underlying goal
4. **Simplicity** — fewer moving parts reduces operational risk
5. **Maintainability** — reduces long-term cost
6. **Performance** — only when it matters to the context

---

## Output Types

### Type 1 — Direct Replacement

The alternative is strictly better. Replace the initial solution silently.

Use when: The alternative achieves the same goal with fewer risks or less complexity and the difference is not educationally relevant.

### Type 2 — Improved Merge

The alternative improves specific aspects of the initial solution. Merge the improvements.

Use when: The initial structure is sound but specific elements can be improved.

### Type 3 — Presented Trade-off

Both approaches have legitimate merit depending on context.

Use when: The choice between initial and alternative depends on information only the user has (team capability, scale requirements, operational constraints).

```
**Two approaches are worth considering:**

**Option A — [Name]**
[description]
Best when: [context]
Trade-off: [what you give up]

**Option B — [Name]**
[description]
Best when: [context]
Trade-off: [what you give up]

**Recommendation:** [Which to choose and why, given stated context]
```

---

## Inputs

- Complete initial draft (from Stage 11)
- Intent and underlying goal (from Stages 01–03)
- Constraint list (from Stage 04)
- Risk profile (from Stage 06)
- Complexity level (from Stage 05)

---

## Outputs

- Revised draft (replaced, merged, or with trade-off presented)
- Improvement log (internal — feeds into quality assessment)

---

## Integration

- Invoked at Stage 12, after initial draft generation
- Output feeds into Stage 13 (Expert Review)
- Improvement log feeds into Stage 21 (Confidence Assessment)

---

## Failure Modes

| Failure | Prevention |
|---|---|
| Generating an inferior alternative | Run all four probes; only adopt alternatives that are materially better |
| Presenting too many options | Maximum two options in a presented trade-off; pick one if context is clear |
| Replacing a correct solution with a worse one | Only replace when the alternative passes the correctness gate |
| Skipping reflection under time pressure | Reflection is not optional — it is always executed for non-trivial requests |

---

## Limitations

- Cannot determine which trade-off is correct when business context is absent — presents the choice clearly
- Simplicity judgements are subjective; the engine applies standard engineering heuristics
- Does not replace domain-expert review; feeds into it
