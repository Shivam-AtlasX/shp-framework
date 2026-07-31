# Quality

## Purpose

Defines the quality standards that every SHP response must meet before delivery. Quality gates are enforced at Stage 14 of the pipeline.

---

## Quality Gates

### Gate 1 — Correctness
The response contains no factual errors, logical errors, or technical inaccuracies.

**Check:** Could a domain expert find an error in this response?

### Gate 2 — Completeness
All stated requirements are addressed. Implicit requirements are addressed or flagged.

**Check:** Is there anything the user asked for that is missing?

### Gate 3 — Consistency
The response does not contradict itself or prior session decisions.

**Check:** Are there any internal contradictions? Does this conflict with anything established earlier in the session?

### Gate 4 — Maintainability
Code and designs are structured for long-term maintainability, not just immediate function.

**Check:** Would a competent engineer find this maintainable in six months?

### Gate 5 — Security
No security vulnerabilities are introduced in code, designs, or recommendations.

**Check:** Is any user input unvalidated? Are any credentials exposed? Are any trust boundaries violated?

### Gate 6 — Performance
No obvious performance problems are introduced.

**Check:** Are there quadratic loops where linear would do? Are there N+1 query patterns? Are there unnecessary blocking operations?

### Gate 7 — Scalability
Designs scale appropriately for the stated or implied use case.

**Check:** Would this design break at 10x the stated load? If so, is that limitation acknowledged?

### Gate 8 — Accessibility
UI and UX outputs consider accessibility standards.

**Check:** Are colour contrast, keyboard navigation, and screen reader compatibility considered?

### Gate 9 — Professionalism
Tone, format, and language are appropriate for the audience and context.

**Check:** Is this response audience-appropriate? Is the format matched to the complexity?

### Gate 10 — Practical Usefulness
The response solves the actual problem, not just the stated question.

**Check:** Could the user act on this response immediately? Is there anything that would stop them?

---

## Gate Application by Complexity

| Gate | Trivial | Simple | Moderate | Complex | Expert |
|---|---|---|---|---|---|
| Correctness | ✓ | ✓ | ✓ | ✓ | ✓ |
| Completeness | — | ✓ | ✓ | ✓ | ✓ |
| Consistency | — | — | ✓ | ✓ | ✓ |
| Maintainability | — | ✓ (code) | ✓ | ✓ | ✓ |
| Security | — | ✓ (if code) | ✓ | ✓ | ✓ |
| Performance | — | — | ✓ (if code) | ✓ | ✓ |
| Scalability | — | — | — | ✓ | ✓ |
| Accessibility | — | ✓ (if UI) | ✓ (if UI) | ✓ | ✓ |
| Professionalism | ✓ | ✓ | ✓ | ✓ | ✓ |
| Practical usefulness | ✓ | ✓ | ✓ | ✓ | ✓ |

---

## Quality Improvement Protocol

If a gate is not passed:

1. Identify the specific failure
2. Determine the root cause
3. Revise the draft to address the failure
4. Re-check the gate
5. If the gate cannot be passed (e.g. insufficient information), state the limitation explicitly

Do not deliver a response that fails a gate without acknowledging the limitation.
