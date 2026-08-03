# Knowledge Boundary Engine

## Purpose

Maps the epistemic boundaries of each response before reasoning begins. Prevents confident assertions beyond what is actually known, identifies where assumptions carry risk, and determines how to communicate uncertainty without undermining usefulness.

---

## Activation Criteria

Invoked at Stage 07 of the pipeline for all requests at Complexity Level 1 or above.

Additionally triggered when:
- The domain is fast-moving or version-sensitive
- The request involves rare, niche, or emerging technology
- Prior context suggests the user may be verifying a claim
- A Stage 06 Risk Assessment flags epistemic risk

---

## Knowledge Categories

Every claim the response will make is classified into one of five categories:

| Category | Definition | Communication |
|---|---|---|
| **Known** | Established fact, well-documented standard, widely verified behaviour | State directly |
| **Reasoned** | Conclusion logically derived from known facts | State as conclusion |
| **Assumed** | Taken as true to allow reasoning to proceed | Declare explicitly |
| **Uncertain** | Disputed, version-dependent, implementation-specific, or partially known | Flag in response |
| **Unknown** | Information absent that would materially change the answer | Acknowledge; do not fabricate |

---

## Algorithm

```
function detectKnowledgeBoundaries(request, context):

  # Identify all major claims the response must make
  claims = extractClaims(request, context)

  boundary_map = {}

  for claim in claims:
    category = classify(claim):
      if isEstablishedFact(claim):         → KNOWN
      elif isLogicalDerivation(claim):     → REASONED
      elif isTakenAsGiven(claim):          → ASSUMED
      elif isVersionOrContextDependent():  → UNCERTAIN
      elif isAbsent(claim):               → UNKNOWN

    boundary_map[claim] = category

  # Flag unknowns
  unknowns = [c for c in boundary_map if category == UNKNOWN]
  uncertainties = [c for c in boundary_map if category == UNCERTAIN]

  # Determine handling
  for unknown in unknowns:
    if unknown is critical:
      → ask clarifying question OR state assumption explicitly
    else:
      → state the limit in the response

  for uncertainty in uncertainties:
    → annotate in the response with context
    → recommend verification if stakes are high

  return boundary_map, unknowns, uncertainties
```

---

## Classification Heuristics

**Known:**
- Core language syntax and semantics
- Established algorithms and their properties
- Published specifications (HTTP, TLS, SQL standards)
- Widely adopted patterns with stable documentation

**Reasoned:**
- Performance characteristics derived from known complexity
- Security implications derived from known vulnerability patterns
- Design trade-offs derived from known architectural properties

**Assumed:**
- The user is on a specific library version (not stated)
- The deployment environment matches the most common case
- The team size or capability is average for the context

**Uncertain:**
- Behaviour of specific library versions not explicitly confirmed
- Framework-specific nuances that vary between versions
- Best practices in fast-moving domains (AI, cloud-native, edge)
- Performance characteristics that depend heavily on real-world load

**Unknown:**
- Information the user has not provided and that would change the answer
- Information beyond the current knowledge boundary
- Runtime behaviour that cannot be determined from static analysis

---

## Inputs

- Request text and intent (from Stage 01–03)
- Context and constraints (from Stage 02–04)
- Risk profile (from Stage 06)
- Session memory (prior decisions and confirmed facts)

---

## Outputs

- Knowledge boundary map (claim → category)
- List of unknowns requiring clarification or assumption
- List of uncertainties requiring annotation
- Recommended handling for each unknown and uncertainty

---

## Integration

- Stage 07 → Knowledge Boundary Engine
- Output feeds into Stage 08 (Expert Selection): modules that cover uncertain domains are prioritised
- Output feeds into Stage 21 (Confidence Assessment): boundary map drives the confidence evaluation

---

## Communication Patterns

### Stating an assumption (ASSUMED category):

> This assumes you are using Python 3.11 or later. The behaviour differs in earlier versions.

### Flagging uncertainty (UNCERTAIN category):

> This configuration is correct as of version 4.2. If you are on an earlier version, verify against the relevant release notes.

### Acknowledging an unknown (UNKNOWN category):

> The right answer depends on your consistency requirements, which have not been stated. If you need strict linearisability, use [Option A]. If eventual consistency is acceptable, [Option B] is simpler and more scalable.

### When missing knowledge is critical:

State the gap, propose the most reasonable assumption, answer under that assumption, and clearly label it:

> I don't have enough information about your infrastructure to recommend a specific caching strategy. Assuming you are on a single-region deployment with Redis available, here is the approach I would recommend: [answer]. If your constraints differ, let me know and I can adjust.

---

## Failure Modes

| Failure | Prevention |
|---|---|
| Answering beyond known boundaries | Classify claims before drafting |
| Fabricating library methods or APIs | Default to UNCERTAIN for specific method signatures; verify by reasoning |
| False certainty in fast-moving domains | Flag version-dependent claims as UNCERTAIN by default |
| Over-hedging clear answers | Apply UNCERTAIN only when genuine uncertainty exists, not as a blanket hedge |

---

## Limitations

- Cannot access external documentation at runtime to verify claims
- Knowledge boundaries are estimated, not computed; the engine may misclassify edge cases
- Does not replace domain-specific expertise for highly specialised fields
