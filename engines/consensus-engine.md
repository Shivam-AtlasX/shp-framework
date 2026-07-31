# Consensus Engine

## Purpose

Resolves conflicts between expert module recommendations. Does not force artificial agreement — legitimate trade-offs are presented honestly so the user can make an informed decision.

---

## When Conflicts Arise

Conflicts arise when two or more modules produce incompatible recommendations. Examples:

- Performance module recommends caching; Security module flags the caching layer as a trust boundary concern
- Architecture module recommends microservices; Business module notes the team is too small to operate them effectively
- Coding module uses a mutable data structure for performance; Testing module flags it as hard to test

These are genuine trade-offs. The Consensus Engine does not pretend they have a single correct answer.

---

## Algorithm

```
function resolveConflict(conflict, context):

  option_a = conflict.position_a  # from module A
  option_b = conflict.position_b  # from module B

  # Analyse the trade-off
  trade_off = TradeOff(
    option_a = option_a,
    a_advantages = analyseAdvantages(option_a, context),
    a_disadvantages = analyseDisadvantages(option_a, context),
    option_b = option_b,
    b_advantages = analyseAdvantages(option_b, context),
    b_disadvantages = analyseDisadvantages(option_b, context)
  )

  # Determine which factors are most important given the user's context
  priority_factors = extractPriorityFactors(context)

  # Recommend based on priorities
  if priority_factors align with option_a:
    recommendation = option_a, with acknowledged trade-offs
  elif priority_factors align with option_b:
    recommendation = option_b, with acknowledged trade-offs
  else:
    recommendation = PRESENT_BOTH, let user decide

  return ConflictResolution(trade_off, recommendation)
```

---

## Priority Factor Extraction

Context signals that influence the recommendation:

| Signal | Priority Factor |
|---|---|
| "production system", "security-critical" | Security, reliability |
| "startup", "MVP", "prototype" | Speed, simplicity |
| "team of 2", "small team" | Maintainability, low operational complexity |
| "10M users", "high traffic" | Scalability, performance |
| "regulated industry", "compliance" | Auditability, security |
| "internal tool" | Simplicity, speed |

---

## Output Format

```
**Trade-off: [Topic]**

Two approaches apply here and they have different strengths:

**Option A — [Name]** *(recommended by [Module])*
- Advantages: [list]
- Disadvantages: [list]
- Best when: [context]

**Option B — [Name]** *(recommended by [Module])*
- Advantages: [list]
- Disadvantages: [list]
- Best when: [context]

**Recommendation:** Given [context factors], Option [A/B] is more suitable because [rationale]. If [alternative condition] applies, reconsider Option [B/A].
```

---

## Non-Resolvable Conflicts

Some conflicts cannot be resolved without user input:
- Conflicts that depend on business requirements not yet stated
- Conflicts that depend on team capability not yet stated
- Conflicts that depend on regulatory requirements not yet stated

In these cases:
1. Present both options with trade-offs
2. Identify the specific information needed to make the decision
3. Ask for that information (one question only)

---

## Anti-Patterns

| Anti-Pattern | Problem |
|---|---|
| False consensus | Pretending there is one correct answer when there isn't |
| Opinion masquerading as consensus | Recommending a preference without surfacing the trade-offs |
| Analysis paralysis | Presenting so many options the user cannot decide |
| Missing context | Recommending without using the user's stated priorities |
