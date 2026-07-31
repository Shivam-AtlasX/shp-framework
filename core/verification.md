# Verification

## Purpose

Defines the verification standard applied to all draft responses before specialist reviews. The first draft is always a draft.

---

## Verification Principle

Every response is treated as a draft until it passes verification. Verification is not proofreading — it is structured critical review of the reasoning, solution, and output.

---

## Verification Checklist

### Correctness
- [ ] Is every factual claim accurate?
- [ ] Is every technical claim accurate?
- [ ] Is the logic internally consistent?
- [ ] Are there any contradictions?
- [ ] Are any assumptions stated but not justified?

### Completeness
- [ ] Does the response address every stated requirement?
- [ ] Does the response address likely implicit requirements?
- [ ] Are edge cases considered?
- [ ] Are failure modes considered?

### Approach
- [ ] Is this the best approach to the problem, or just a working one?
- [ ] Have alternatives been considered?
- [ ] Is there a simpler approach that achieves the same goal?
- [ ] Is there a more robust approach worth recommending?

### Code (if applicable)
- [ ] Does the code compile / run correctly?
- [ ] Are there off-by-one errors?
- [ ] Are there null/undefined handling gaps?
- [ ] Are there resource leaks?
- [ ] Is error handling present and appropriate?
- [ ] Are inputs validated?
- [ ] Are there hardcoded values that should be configuration?

### Reasoning
- [ ] Is the reasoning free from the anti-patterns in `core/reasoning.md`?
- [ ] Has confirmation bias been checked?
- [ ] Has the answer been tested against at least one counter-example?

---

## Verification Actions

| Finding | Action |
|---|---|
| Minor error | Correct inline |
| Logic gap | Fill with explicit reasoning |
| Missing requirement | Add before proceeding |
| Better alternative exists | Present as recommended alternative or replace |
| Fundamental approach problem | Revise from Stage 07 |
| Cannot verify a claim | State uncertainty, do not present as fact |

---

## Verification Depth by Complexity

| Complexity Level | Verification Depth |
|---|---|
| Trivial | Skip |
| Simple | Correctness + Completeness only |
| Moderate | Full checklist |
| Complex | Full checklist + alternatives considered |
| Expert | Full checklist + alternatives + counter-argument review |
