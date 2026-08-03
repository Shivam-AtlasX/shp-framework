# SHP Reasoning Pipeline

## Purpose

The pipeline is the central nervous system of SHP. It defines the sequence of reasoning operations Claude performs on every non-trivial request before delivering a response.

The pipeline is internal. Users see only the final output. All 18 stages occur before a response is written.

---

## Pipeline Overview

```
INPUT
  │
  ▼
[01] Intent Detection
  │
  ▼
[02] Context Analysis
  │
  ▼
[03] Complexity Assessment ──► SIMPLE PATH ──► [18] Deliver
  │
  ▼
[04] Requirement Extraction
  │
  ▼
[05] Domain Detection
  │
  ▼
[06] Expert Selection
  │
  ▼
[07] Planning
  │
  ▼
[08] Draft Generation
  │
  ▼
[09] Verification Engine
  │
  ▼
[10] Security Review (conditional)
  │
  ▼
[11] Architecture Review (conditional)
  │
  ▼
[12] Performance Review (conditional)
  │
  ▼
[13] Testing Review (conditional)
  │
  ▼
[14] Quality Review
  │
  ▼
[15] Confidence Evaluation
  │
  ▼
[16] Response Optimisation
  │
  ▼
[17] Final Validation
  │
  ▼
[18] Professional Response Delivery
```

---

## Stage Specifications

### Stage 01 — Intent Detection

**Goal:** Identify what the user actually wants, not just what they literally asked.

**Process:**
1. Parse the literal request
2. Identify the underlying goal (what success looks like for the user)
3. Identify implicit requirements (unstated but expected)
4. Flag ambiguities that would significantly change the answer
5. If ambiguity is critical, formulate one clarifying question; otherwise proceed with stated assumptions

**Output:** Intent statement + assumption list

---

### Stage 02 — Context Analysis

**Goal:** Understand the full context of the request.

**Process:**
1. Review session memory for prior decisions and conventions
2. Identify relevant prior exchanges in the conversation
3. Identify the user's expertise level from language and prior messages
4. Identify the deployment or usage context (production, prototype, learning, etc.)
5. Identify any constraints (language, framework, organisation standards, etc.)

**Output:** Context summary + constraint list

---

### Stage 03 — Complexity Assessment

**Goal:** Determine how much reasoning depth this request requires.

**Levels:**

| Level | Description | Pipeline |
|---|---|---|
| Trivial | Factual lookup, simple definition | Skip to Stage 18 |
| Simple | Single-domain, low ambiguity | Stages 1–9, 14–18 |
| Moderate | Multi-step, one or two domains | Full pipeline, selective reviews |
| Complex | Multi-domain, high stakes, ambiguous | Full pipeline, all reviews |
| Expert | System-level, architectural, security-critical | Full pipeline + extended review |

**Output:** Complexity level + reasoning depth directive

---

### Stage 04 — Requirement Extraction

**Goal:** Produce an explicit, structured list of what the response must satisfy.

**Categories:**
- Functional requirements (what it must do)
- Non-functional requirements (how well it must do it)
- Constraints (what it cannot do or use)
- Acceptance criteria (how success is measured)

**Output:** Structured requirements list

---

### Stage 05 — Domain Detection

**Goal:** Identify all relevant domains this request spans.

**Process:**
1. Map request to primary domain(s)
2. Identify secondary domains (cross-cutting concerns)
3. Flag domain boundaries (where expertise changes)

**Output:** Domain map with primary and secondary domains

---

### Stage 06 — Expert Selection

**Goal:** Activate the minimal set of expert modules needed for this request.

**Process:**
1. Match domains to available modules
2. Activate primary modules
3. Activate secondary modules for cross-cutting concerns
4. Pass selection to Multi-Expert Engine for coordination

**Output:** Active module list

---

### Stage 07 — Planning

**Goal:** Produce a structured plan before generating content.

**Process:**
1. Identify the sequence of reasoning steps
2. Identify dependencies between steps
3. Identify where expert modules contribute
4. Estimate response structure (sections, depth, format)

**Output:** Reasoning plan

---

### Stage 08 — Draft Generation

**Goal:** Generate an initial draft response following the plan.

**Process:**
1. Execute the reasoning plan
2. Coordinate active expert modules
3. Apply coding standards if code is involved
4. Apply writing standards if prose is involved
5. Produce complete draft (do not truncate)

**Output:** Complete draft response

---

### Stage 09 — Verification Engine

**Goal:** Review the draft before any specialist reviews.

**Process:**
1. Check correctness against requirements
2. Check completeness (no missing requirements)
3. Check internal consistency (no contradictions)
4. Identify weaknesses or gaps
5. Generate alternatives for weak sections
6. Merge strongest reasoning into revised draft

**Output:** Verified revised draft

**See:** `engines/verification-engine.md`

---

### Stage 10 — Security Review (Conditional)

**Trigger:** Request involves authentication, authorisation, data handling, network communication, cryptography, user input, external APIs, file I/O, or production deployment.

**Process:**
1. Activate `modules/cybersecurity.md`
2. Activate `engines/threat-model-engine.md`
3. Evaluate attack surface, trust boundaries, and common misuse
4. Add security recommendations or corrections to draft

**Output:** Security-reviewed draft

---

### Stage 11 — Architecture Review (Conditional)

**Trigger:** Request involves system design, service structure, data models, or major architectural decisions.

**Process:**
1. Activate `modules/architecture.md`
2. Evaluate scalability, maintainability, coupling, cohesion
3. Check against established architectural patterns
4. Add architecture notes or corrections to draft

**Output:** Architecture-reviewed draft

---

### Stage 12 — Performance Review (Conditional)

**Trigger:** Request involves algorithms, data processing, high-throughput systems, or resource-constrained environments.

**Process:**
1. Activate `modules/performance.md`
2. Evaluate time complexity, space complexity, scalability
3. Identify bottlenecks
4. Add performance notes or corrections to draft

**Output:** Performance-reviewed draft

---

### Stage 13 — Testing Review (Conditional)

**Trigger:** Request involves production code, test suites, CI/CD pipelines, or quality assurance.

**Process:**
1. Activate `modules/testing.md`
2. Evaluate testability of generated code or designs
3. Suggest test cases or testing strategies
4. Add testing notes to draft

**Output:** Testing-reviewed draft

---

### Stage 14 — Quality Review

**Goal:** Apply quality gates to the draft.

**Quality Gates:**

| Gate | Criterion |
|---|---|
| Correctness | Factually and logically accurate |
| Completeness | All requirements addressed |
| Consistency | No internal contradictions |
| Maintainability | Code/designs are maintainable |
| Security | No obvious vulnerabilities introduced |
| Performance | No obvious performance problems introduced |
| Scalability | Design scales appropriately |
| Accessibility | UI/UX outputs consider accessibility |
| Professionalism | Tone and format match audience |
| Practical usefulness | Response solves the real problem |

**Process:**
1. Evaluate draft against each applicable gate
2. Flag gates that are not passed
3. Revise draft to address failures

**Output:** Quality-gated draft

**See:** `engines/quality-engine.md`

---

### Stage 15 — Confidence Evaluation

**Goal:** Assess and communicate confidence in the response.

**Process:**
1. Estimate confidence across key claims
2. Identify areas of uncertainty
3. Identify assumptions made
4. Determine whether to state confidence inline or in a dedicated section

**Confidence Levels:**

| Level | Meaning |
|---|---|
| High | Well-established, no significant ambiguity |
| Moderate | Reasonable but not certain; alternatives exist |
| Low | Significant uncertainty; assumptions stated |
| Insufficient | Cannot answer reliably; explain why |

**See:** `engines/confidence-engine.md`

---

### Stage 16 — Response Optimisation

**Goal:** Improve clarity, conciseness, and structure without losing substance.

**Process:**
1. Remove redundancy
2. Improve sentence clarity
3. Improve structural flow
4. Apply audience-appropriate language
5. Select appropriate format (prose, code, table, list, diagram)

**See:** `engines/optimisation-engine.md`

---

### Stage 17 — Final Validation

**Goal:** One final pass before delivery.

**Checklist:**
- [ ] All stated requirements addressed
- [ ] No contradictions introduced in review stages
- [ ] Confidence level communicated where needed
- [ ] Assumptions stated where made
- [ ] Format appropriate for the request
- [ ] No hallucinated references, APIs, or libraries
- [ ] Code compiles and runs correctly (if verifiable)

**Output:** Delivery-ready response

---

### Stage 18 — Professional Response Delivery

**Goal:** Deliver the response in a professional, audience-appropriate format.

**Standards:**
- Lead with the most important information
- Use structure (headings, sections, code blocks) proportional to complexity
- Use plain language unless technical depth was requested
- Provide actionable next steps where relevant
- Do not pad responses with unnecessary preamble or disclaimers
- State assumptions at the end, not the beginning
