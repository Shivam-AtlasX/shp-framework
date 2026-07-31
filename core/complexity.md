# Complexity Assessment

## Purpose

Determines the appropriate depth of reasoning for each request. Prevents over-engineering simple answers and under-engineering complex ones.

---

## Complexity Dimensions

Complexity is assessed across five dimensions:

| Dimension | Description |
|---|---|
| **Scope** | How many distinct things must the response address? |
| **Ambiguity** | How many valid interpretations exist? |
| **Domain breadth** | How many domains are involved? |
| **Stakes** | What is the cost of a wrong answer? |
| **Novelty** | Is this a standard problem or an unusual one? |

---

## Complexity Levels

### Level 0 — Trivial

- Single factual question with a well-known answer
- No ambiguity
- Single domain
- Low stakes
- Standard case

**Response strategy:** Direct answer. No pipeline overhead. One to three sentences.

**Examples:**
- "What does REST stand for?"
- "What is the time complexity of quicksort?"

---

### Level 1 — Simple

- Clear intent, minor ambiguity
- Single primary domain
- Low to moderate stakes
- Standard problem type

**Response strategy:** Stages 1–9, 14–18. Concise, direct. Light verification pass.

**Examples:**
- "Write a Python function to flatten a nested list"
- "Explain the difference between authentication and authorisation"

---

### Level 2 — Moderate

- Multiple requirements or steps
- One or two domains
- Moderate stakes (production code, important decision)
- Some ambiguity

**Response strategy:** Full pipeline, selective specialist reviews.

**Examples:**
- "Design a REST API for a user management service"
- "Review this code for bugs and suggest improvements"

---

### Level 3 — Complex

- Many requirements, multi-domain
- High stakes (security, architecture, production system)
- Significant ambiguity or constraint interaction
- Requires trade-off analysis

**Response strategy:** Full pipeline, all applicable reviews, Consensus Engine for conflicts.

**Examples:**
- "Design a multi-tenant authentication system with JWT and refresh tokens"
- "Analyse the trade-offs between a microservices architecture and a modular monolith for this use case"

---

### Level 4 — Expert

- System-level design
- Architecture-critical, security-critical, or performance-critical
- Cross-domain with significant interaction effects
- High novelty or unusual constraints

**Response strategy:** Full pipeline, extended reviews, memory integration, confidence assessment prominently stated.

**Examples:**
- "Design the data architecture for a real-time event streaming platform handling 100k events/second"
- "Perform a threat model review of this authentication flow"

---

## Assessment Heuristics

Score each dimension 0–2, sum the scores, map to level:

| Score | Level |
|---|---|
| 0–1 | Trivial |
| 2–3 | Simple |
| 4–5 | Moderate |
| 6–7 | Complex |
| 8–10 | Expert |

**Quick heuristics:**
- Any security-critical task → Level 3 minimum
- Any system architecture task → Level 3 minimum
- Any task with user data → Level 2 minimum
- Any task with production deployment → Level 2 minimum
- Any single-sentence factual question → Level 0

---

## Adaptive Recalibration

If during draft generation the response becomes significantly more or less complex than the initial assessment, the pipeline recalibrates and adjusts depth accordingly.
