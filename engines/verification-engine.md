# Verification Engine

## Purpose

Treats every draft as a candidate solution, not a final answer. Reviews, challenges, and improves drafts before they pass to specialist reviews.

---

## Algorithm

```
function verify(draft, requirements, context):

  # Step 1: Requirements trace
  for each requirement in requirements:
    if draft does not satisfy requirement:
      flag(requirement, "UNMET")

  # Step 2: Correctness probe
  for each claim in draft:
    if claim is technical:
      verify(claim) or flag(claim, "UNCERTAIN")
    if claim is factual:
      verify(claim) or flag(claim, "UNCERTAIN")

  # Step 3: Counter-example test
  generate counter_example for primary solution
  if counter_example breaks solution:
    record(weakness)

  # Step 4: Alternative generation
  if complexity >= MODERATE:
    generate alternative_approach
    compare(draft, alternative_approach)
    if alternative_approach is superior:
      replace or add as recommended alternative

  # Step 5: Weakness resolution
  for each weakness:
    fix inline if possible
    else acknowledge in response

  return improved_draft
```

---

## Trigger Conditions

Invoked at Stage 09 for all requests at Complexity Level 1 or above.

---

## Inputs

- Complete draft response
- Extracted requirements list
- Active session memory
- Complexity level

---

## Outputs

- Verified, improved draft
- List of acknowledged limitations (for confidence stage)
- List of alternatives considered (for response if relevant)

---

## Integration

Feeds into Stage 10 (Security Review) or Stage 14 (Quality Review) depending on which specialist reviews apply.

---

## Verification Depth

| Complexity | Depth |
|---|---|
| Simple | Requirements trace + correctness probe |
| Moderate | + Counter-example test |
| Complex | + Alternative generation + weakness resolution |
| Expert | + Full alternative analysis + adversarial probe |

---

## Failure Modes

| Failure | Response |
|---|---|
| Cannot verify a claim | Mark as uncertain; do not present as fact |
| No alternative found | Proceed with primary solution; note it was verified |
| Fundamental problem with approach | Return to Stage 07 (Planning) |
| Requirements cannot be fully met | State limitation; deliver best partial solution |
