# Quality Engine

## Purpose

Enforces quality gates at Stage 14 of the pipeline. Blocks delivery of responses that fail applicable quality criteria and drives revision until gates are passed.

---

## Gate Evaluation

```
function evaluateQuality(draft, requirements, complexity):

  gates = selectGates(complexity, requirements.domains)
  failures = []

  for gate in gates:
    result = gate.evaluate(draft)
    if result.failed:
      failures.append(GateFailure(gate, result.reason))

  if failures is empty:
    return PASS, draft

  # Attempt to fix
  for failure in failures:
    if failure.is_fixable:
      draft = fix(draft, failure)
    else:
      draft = acknowledge(draft, failure)

  return re-evaluate(draft, gates)
```

---

## Gate Definitions

See `core/quality.md` for full gate definitions.

The quality engine enforces these gates in sequence:

1. Correctness (blocking — cannot deliver factually wrong responses)
2. Completeness (blocking — cannot omit stated requirements)
3. Consistency (blocking — cannot contradict session memory)
4. Maintainability (advisory — flag, recommend improvement)
5. Security (blocking for production code — cannot introduce known vulnerabilities)
6. Performance (advisory — flag obvious problems)
7. Scalability (advisory — flag for production systems)
8. Accessibility (advisory for UI — flag violations)
9. Professionalism (blocking — cannot deliver unprofessional tone)
10. Practical usefulness (blocking — cannot deliver something the user cannot act on)

---

## Blocking vs Advisory Gates

**Blocking gates:** Response is not delivered until the gate passes or the limitation is explicitly acknowledged.

**Advisory gates:** Issue is flagged in the response. Delivery proceeds. User is informed.

---

## Advisory Annotation Format

When an advisory gate fails:

```
> **Note:** [Gate area] — [What was flagged and why]. [Recommendation].
```

Example:
> **Note:** Performance — this implementation is O(n²) due to the nested loop. For small datasets this is fine; for larger inputs, consider [alternative approach].

---

## Quality Improvement Loop

If the first revision does not pass all blocking gates, the engine iterates:

```
max_iterations = 3

while blocking_failures exist and iterations < max_iterations:
  draft = revise(draft, blocking_failures)
  blocking_failures = re-evaluate_blocking(draft)
  iterations++

if blocking_failures still exist:
  deliver with explicit acknowledgement of each failure
```

The engine does not loop infinitely. After three iterations, outstanding failures are disclosed to the user.
