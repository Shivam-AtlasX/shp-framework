# Planning

## Purpose

Structures the approach to complex requests before generating content. Planning prevents disorganised reasoning and ensures all requirements are addressed systematically.

---

## Planning Principle

For non-trivial requests, generate a plan before generating content. A plan is not visible to the user by default — it is an internal reasoning scaffold.

---

## Plan Structure

```
PLAN
────
Request:     [Restated in own words]
Goal:        [What success looks like]
Assumptions: [What is being taken as given]
Constraints: [What cannot be done or used]
Modules:     [Expert modules activated]
Steps:
  1. [First reasoning step]
  2. [Second reasoning step]
  ...
  N. [Final delivery step]
Output:      [Expected format and structure]
Risks:       [What could go wrong; how to address]
```

---

## Planning by Complexity Level

### Level 0–1
No explicit plan. Proceed directly to response.

### Level 2
Lightweight plan: goal, assumptions, 3–5 steps.

### Level 3
Full plan: goal, assumptions, constraints, modules, steps, output format, risks.

### Level 4
Full plan + dependency mapping + checkpoint verification at intermediate steps.

---

## Checkpoint Verification

For Level 4 tasks, verify reasoning at each major step before proceeding:

```
Step N complete.
Checkpoint: [What was produced]
Quality gate: [Does this satisfy the step requirement?]
Proceed: [Yes / No — if No, revise before continuing]
```

---

## Re-Planning

If mid-execution reveals that the plan is incorrect or insufficient:

1. Stop
2. Identify what changed the plan
3. Update the plan
4. Resume from the affected step

Do not continue executing a known-incorrect plan.

---

## Planning Anti-Patterns

| Anti-Pattern | Description |
|---|---|
| Phantom planning | Writing a plan that doesn't match the execution |
| Over-planning | Planning every micro-detail for a simple task |
| Premature commitment | Locking in an approach before exploring alternatives |
| Missing constraints | Planning without accounting for stated limitations |
