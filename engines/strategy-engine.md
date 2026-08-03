# Strategy Engine

## Purpose

Selects the most appropriate reasoning strategy before execution begins. Prevents mismatched reasoning approaches — applying engineering thinking to a creative problem, or creative thinking to a formal derivation. The right strategy applied to the right problem produces materially better results.

---

## Activation Criteria

Invoked at Stage 09 of the pipeline for all requests at Complexity Level 1 or above.

---

## Strategy Catalogue

### Analytical Strategy

**Use when:** The request requires decomposing a problem, evaluating options, comparing approaches, or making a structured decision.

**Characteristics:**
- Decompose the problem into independent parts
- Evaluate each part systematically
- Apply criteria-based selection
- Present reasoning transparently

**Examples:**
- "Which database should I use for this workload?"
- "What are the trade-offs between Option A and Option B?"
- "Analyse this architecture for weaknesses"

---

### Creative Strategy

**Use when:** The request requires generating novel ideas, exploring a solution space, or producing something that does not have one correct answer.

**Characteristics:**
- Generate multiple distinct possibilities
- Evaluate by fit rather than correctness
- Allow for unconventional approaches
- Balance originality with practicality

**Examples:**
- "Suggest names for this product"
- "How might we approach this differently?"
- "Design an onboarding flow for a new user"

---

### Engineering Strategy

**Use when:** The request requires producing code, system designs, technical specifications, or concrete implementations.

**Characteristics:**
- Lead with the implementation
- Apply domain-specific standards (correctness, security, maintainability)
- Explain only trade-offs and non-obvious decisions
- Verify the implementation before delivery

**Examples:**
- "Write a rate limiter in Go"
- "Build a CI/CD pipeline for this service"
- "Design the schema for this application"

---

### Research Strategy

**Use when:** The request requires synthesising information from multiple sources, separating established evidence from interpretation, or explaining a contested or complex domain.

**Characteristics:**
- Separate evidence, interpretation, and recommendation
- Acknowledge limitations and knowledge boundaries
- Avoid presenting interpretation as established fact
- Apply the Evidence Engine for epistemic labelling

**Examples:**
- "What does the research say about X?"
- "Explain the state of the art in [domain]"
- "Compare approaches A and B across the literature"

---

### Educational Strategy

**Use when:** The request asks for explanation, teaching, or learning support at a stated or inferred level.

**Characteristics:**
- Calibrate depth and vocabulary to the learner
- Build from fundamentals to complexity
- Use analogies and examples generously
- Check for conceptual gaps before advancing

**Examples:**
- "Explain how JWT works"
- "Teach me about dependency injection"
- "I'm new to Rust — what should I understand about ownership?"

---

### Business Strategy

**Use when:** The request involves business decisions, trade-offs between organisational priorities, or stakeholder considerations.

**Characteristics:**
- Consider cost, risk, speed, and team capability
- Acknowledge organisational constraints explicitly
- Present options in terms of business outcomes
- Avoid purely technical framing for non-technical decisions

**Examples:**
- "Should we build or buy?"
- "How should we prioritise this quarter's roadmap?"
- "What is the business case for this refactor?"

---

### Security Strategy

**Use when:** The request involves threat analysis, vulnerability review, authentication, authorisation, secrets management, or trust boundary evaluation.

**Characteristics:**
- Lead with threat model — identify assets, threats, and trust boundaries
- Evaluate design before implementation details
- Prioritise findings by exploitability and impact
- Recommend mitigations proportional to risk

**Examples:**
- "Review this authentication design"
- "What are the security risks of this approach?"
- "Help me threat model this API"

---

### Mathematical Strategy

**Use when:** The request requires formal derivation, algorithm analysis, proof construction, or precise quantitative reasoning.

**Characteristics:**
- State assumptions formally
- Derive conclusions step by step
- Check derivations for errors before presenting
- Distinguish proven results from conjectures

**Examples:**
- "Prove the time complexity of this algorithm"
- "Derive the recurrence relation for this problem"
- "Is this probability calculation correct?"

---

### Architectural Strategy

**Use when:** The request involves system-level design, component structure, long-term consequence of design decisions, or scalability and operational thinking.

**Characteristics:**
- Reason about the system as a whole, not only individual components
- Evaluate coupling, cohesion, and operational complexity
- Consider what happens at 10x scale and 10x team size
- Document decisions as Architecture Decision Records (ADRs)

**Examples:**
- "Design the architecture for a real-time notification system"
- "Should we decompose this monolith into services?"
- "How should we structure this data pipeline?"

---

## Algorithm

```
function selectStrategy(intent, context, risk_profile, complexity):

  strategies = []

  # Primary strategy — determined by dominant task type
  primary = detectPrimaryStrategy(intent)
  strategies.append(primary)

  # Secondary strategies — triggered by cross-cutting concerns
  if hasSecurityConcerns(intent, risk_profile):
    strategies.append(SECURITY)

  if hasQuantitativeReasoning(intent):
    strategies.append(MATHEMATICAL)

  if hasSystemDesign(intent):
    strategies.append(ARCHITECTURAL)

  if hasPedagogicalSignals(intent, context):
    strategies.append(EDUCATIONAL)

  # Resolve conflicts
  if ENGINEERING in strategies and CREATIVE in strategies:
    → prioritise ENGINEERING; apply CREATIVE to option generation only

  if RESEARCH in strategies and EDUCATIONAL in strategies:
    → prioritise EDUCATIONAL; apply RESEARCH depth to content accuracy

  return StrategySet(primary=primary, supporting=strategies[1:])
```

---

## Inputs

- Intent statement (from Stage 01)
- Context summary (from Stage 02)
- Risk profile (from Stage 06)
- Complexity level (from Stage 05)

---

## Outputs

- Primary strategy
- Supporting strategies (if any)
- Strategy notes — specific guidance for how the primary strategy applies to this request

---

## Integration

- Invoked at Stage 09 of the pipeline
- Output governs how Stage 11 (Initial Solution) proceeds
- Strategy selection is passed to the Multi-Expert Engine to calibrate module contributions

---

## Failure Modes

| Failure | Prevention |
|---|---|
| Wrong strategy selected | Re-evaluate if draft quality is unexpectedly poor |
| Single-strategy rigidity | Always check for applicable supporting strategies |
| Strategy mismatch to audience | Incorporate context signals about user expertise level |

---

## Limitations

- Some requests span multiple strategies with no clear primary; apply judgement and prefer the strategy that best serves the user's underlying goal
- Strategy selection is not infallible; the Reflection Engine corrects mismatches during Stage 12
