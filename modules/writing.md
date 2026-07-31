# Writing Module

## Purpose

Produces professional, clear, technically accurate written content. Calibrates style, depth, structure, and tone to the audience and purpose of every piece.

---

## Responsibilities

- Write technical documentation, reports, proposals, and explanations
- Write long-form content: articles, guides, white papers, blog posts
- Edit and improve existing prose for clarity, structure, and accuracy
- Adapt register and style to the target audience
- Structure content for readability and navigability

---

## Activation Criteria

Activate when:
- Written content is the primary deliverable
- Documentation, reports, or proposals are requested
- Communication tasks are requested ("write an email", "draft a proposal")
- Existing prose needs editing or restructuring

---

## Inputs

- Topic or subject
- Target audience (technical / non-technical / mixed)
- Purpose (inform, persuade, instruct, document)
- Format (article, report, email, README, white paper)
- Length constraints
- Tone requirements (formal, conversational, authoritative)
- Existing draft (if editing)

---

## Outputs

- Well-structured prose
- Audience-appropriate language and depth
- Clear headings and logical flow
- Examples and analogies where appropriate

---

## Dependencies

- `modules/documentation.md` — for technical documentation specifics
- `modules/research.md` — for evidence-backed content
- `engines/optimisation-engine.md` — for conciseness pass

---

## Writing Standards

### Lead with the point
State the conclusion or key message before the supporting reasoning. Readers should know what they are reading before they read it, not after.

```
❌ "There are many considerations when choosing a database. First, you 
    must consider data model, then consistency requirements... 
    Therefore, PostgreSQL is the right choice."

✓  "PostgreSQL is the right choice for this use case. The primary 
    reasons are its ACID guarantees, mature JSON support, and 
    ecosystem maturity. Here is why each matters..."
```

### One idea per paragraph
Each paragraph has a topic sentence that states the idea. Supporting sentences explain, evidence, or illustrate it. No paragraph should try to do two things.

### Active voice by default
Active voice is shorter, clearer, and more direct.

```
❌ "The request is processed by the server."
✓  "The server processes the request."
```

Passive voice is acceptable when the actor is unknown, unimportant, or deliberately de-emphasised.

### Concrete over abstract
Abstract concepts become clear through examples, analogies, and specifics. Every abstract claim should be followed by a concrete illustration.

```
❌ "The system is designed for high availability."
✓  "The system is designed for high availability — it can lose an 
    entire availability zone and continue serving requests within 
    30 seconds."
```

### Appropriate length
As long as it needs to be. No longer. Every sentence that does not add information should be cut.

### Consistent terminology
Pick a name for each concept and use it consistently. Synonyms create ambiguity in technical writing.

---

## Audience Calibration

| Audience | Approach |
|---|---|
| Technical peers | Full technical depth; skip definitions of common terms; precise vocabulary |
| Technical in a different domain | Technical depth but define domain-specific terms; use analogies from their domain |
| Non-technical management | Outcomes, impact, and risk; minimal implementation detail; lead with "what it means for us" |
| Mixed | Executive summary at the top; technical detail in appendices or expandable sections |
| Students or learners | Build concepts progressively; use familiar analogies; anticipate common misconceptions |

---

## Document Structure Patterns

### Informational document (report, article, guide)
```
1. Title — specific and descriptive
2. Summary / TL;DR — the whole point in 2–3 sentences
3. Context — why this matters
4. Body — main content, logically ordered
5. Conclusion — what follows from the above
6. Next steps — if applicable
```

### Proposal
```
1. Problem statement — what is wrong or missing
2. Proposed solution — specific and concrete
3. Benefits — what improves and by how much
4. Trade-offs — what is given up or risked
5. Implementation plan — how and when
6. Cost and resource requirements
7. Decision request — specific ask
```

### Technical explanation
```
1. What it is — one-sentence definition
2. Why it matters — motivation
3. How it works — mechanism, with example
4. When to use it — context and criteria
5. When not to use it — limitations
6. Related concepts — for further learning
```

---

## Editing Checklist

When editing existing prose:

- [ ] Does every paragraph have a clear topic sentence?
- [ ] Is the key message stated early, not buried at the end?
- [ ] Are there any sentences longer than 30 words? Can they be split?
- [ ] Are there passive constructions that should be active?
- [ ] Is every technical term defined on first use?
- [ ] Is the terminology consistent throughout?
- [ ] Are there any paragraphs that try to do two things?
- [ ] Is there any content that adds no information?
- [ ] Does the conclusion follow from what was said?

---

## Common Mistakes

| Mistake | Example | Fix |
|---|---|---|
| Burying the point | Long preamble before the key message | Lead with the conclusion |
| Jargon without definition | "We use an LSM-tree based storage engine" | Define or link on first use |
| Passive overuse | "Errors are handled by the system" | "The system handles errors" |
| Hedge stacking | "It might possibly be somewhat faster" | State the claim or don't |
| Padding | "In conclusion, as we have seen above..." | Cut entirely |
| Inconsistent names | "The service / the API / the backend" for one thing | Pick one name |

---

## Examples

### Example 1 — Rewriting a buried-point paragraph

**Before:**
> Given the various factors at play, including the team's current experience with relational databases, the existing infrastructure, and the nature of the data which is primarily structured and relational, and taking into account that the consistency requirements are strict, we have come to the conclusion that PostgreSQL is the most appropriate choice.

**After:**
> PostgreSQL is the right choice. The data is relational, the consistency requirements are strict, and the team already has deep PostgreSQL experience. Switching to a different store would add learning cost without architectural benefit.

---

## Best Practices

- Know what you want the reader to know or do when they finish — write toward that
- Structure before drafting — an outline prevents structural rewrites
- Read aloud during editing — awkward sentences are audible before they are visible
- Cut the last paragraph of any first draft — it is usually a restatement

---

## Limitations

- Cannot know the specific organisational style guide without it being provided
- Legal, regulatory, and compliance writing requires specialist review
- Domain-specific technical accuracy depends on the accuracy of inputs
