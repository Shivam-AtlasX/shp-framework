# Confidence Engine

## Purpose

Estimates uncertainty across claims and determines how to communicate it to the user. Never fabricates certainty. Never over-hedges clear answers.

---

## Algorithm

```
function evaluateConfidence(draft, context):

  overall_confidence = HIGH

  for each major_claim in draft:
    factors = [
      evidenceQuality(major_claim),
      contextCompleteness(context),
      ambiguityLevel(major_claim),
      domainStability(major_claim),
      complexityScore(major_claim)
    ]
    claim_confidence = aggregate(factors)
    overall_confidence = min(overall_confidence, claim_confidence)

    if claim_confidence <= MODERATE:
      annotate(major_claim, claim_confidence)

  if overall_confidence <= MODERATE:
    generate confidence_statement
    generate assumption_list

  return draft_with_confidence_annotations
```

---

## Confidence Factors

| Factor | Weight | High | Low |
|---|---|---|---|
| Evidence quality | High | Authoritative, verifiable | Absent, anecdotal |
| Context completeness | High | Full context provided | Key info missing |
| Domain stability | Medium | Established standards | Emerging/changing field |
| Ambiguity | Medium | Single interpretation | Multiple valid readings |
| Complexity | Low | Simple derivation | Many interacting factors |

---

## Calibration Examples

| Situation | Confidence Level |
|---|---|
| "What is the time complexity of merge sort?" | High — no annotation needed |
| "Which database should I choose?" (no context) | Low — depends on requirements |
| "Is this code secure?" | Moderate — depends on full deployment context |
| "What will happen to AI in 5 years?" | Insufficient — speculative |
| "Does this SQL query have an injection risk?" | High (if query is provided) |

---

## Output Format

When confidence annotation is needed:

**In-line (for specific claims):**
> This assumes your PostgreSQL version is 14 or later — behaviour differs in earlier versions.

**Summary block (for overall response):**
```
**Confidence note:**
This recommendation is based on [assumption]. If [alternative condition], the recommendation changes to [alternative]. Confidence: Moderate.
```

**Assumption list (for complex responses):**
```
**Assumptions:**
- [Assumption 1] — impact if wrong: [consequence]
- [Assumption 2] — impact if wrong: [consequence]
```

---

## Anti-Patterns

| Pattern | Problem | Correction |
|---|---|---|
| "I'm confident that..." (for all claims) | Noise; dilutes real confidence signals | Only state confidence when it's below High |
| No confidence annotation for uncertain claims | User cannot calibrate their trust | Annotate all Moderate/Low claims |
| "I think..." as a general hedge | Vague; unhelpful | State specific uncertainty and its source |
| Refusing to answer due to uncertainty | Over-cautious; unhelpful | Answer with stated uncertainty |
