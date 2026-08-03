# Evidence Engine

## Purpose

Structures factual claims into clearly labelled epistemic categories — evidence, interpretation, recommendation, and uncertainty — so the user can calibrate trust in each part of the response independently. Prevents the mixing of established fact with inference, and inference with speculation.

---

## Activation Criteria

Invoked at Stage 21 (Confidence Assessment) for:
- Research and analysis responses
- Responses comparing technical options
- Architecture recommendations
- Responses where the user's decision depends on the accuracy of claims
- Any response where the Knowledge Boundary Engine flagged Uncertain or Unknown claims

Always applied when factual accuracy materially affects the quality of a decision.

---

## Epistemic Categories

| Category | Definition | Signal Phrases |
|---|---|---|
| **Evidence** | A verifiable, established fact; a published standard; a documented behaviour | "Research shows...", "The specification states...", "Benchmarks demonstrate..." |
| **Interpretation** | A conclusion drawn from evidence by applying reasoning | "This suggests...", "This indicates...", "Based on the above..." |
| **Recommendation** | A judgement call based on evidence and interpretation, shaped by context | "I recommend...", "For your use case...", "The best approach here..." |
| **Assumption** | Something taken as true to allow reasoning to proceed | "Assuming...", "If this holds...", "Taking [X] as given..." |
| **Uncertainty** | A claim where confidence is below High | "This may vary...", "Verify with your version...", "I'm not certain whether..." |
| **Unknown** | Information that is absent but would change the answer | "This depends on [X] which hasn't been stated...", "I don't have enough information about..." |

---

## Algorithm

```
function applyEvidenceStructure(draft, boundary_map, context):

  # Audit all claims in the draft
  for claim in draft.claims:
    category = boundary_map[claim]  # From Knowledge Boundary Engine

    if category == KNOWN:
      → label as Evidence (may be implicit; no annotation needed if clearly established)

    if category == REASONED:
      → label as Interpretation (signal phrase or inline note)

    if category == ASSUMED:
      → surface as explicit Assumption

    if category == UNCERTAIN:
      → annotate with Uncertainty flag and mitigation advice

    if category == UNKNOWN:
      → annotate as Unknown; recommend how to resolve

  # Check mixing
  for section in draft.sections:
    if section mixes Evidence and Recommendation without clear transition:
      → restructure to separate them

  # Generate confidence block if needed
  if uncertainties or unknowns exist and response is high-stakes:
    → append Confidence Block to response

  return annotated_draft
```

---

## Output Formats

### Inline Annotation (default)

Used for individual uncertain claims within an otherwise confident response:

> This is the standard approach for session management. Note that if you are using a stateless architecture, this pattern requires that the token store is accessible from all service instances — verify your deployment allows this.

### Confidence Block (for high-stakes or uncertain responses)

Appended when overall confidence is Moderate or below, or when the response contains multiple uncertain claims:

```
**Confidence Assessment**

| Claim | Category | Note |
|---|---|---|
| [Claim A] | Evidence | Well-established standard |
| [Claim B] | Interpretation | Derived from [Claim A]; holds if [assumption] |
| [Claim C] | Uncertainty | Verify against your version — behaviour changed in v4.2 |
| [Claim D] | Unknown | Depends on your throughput requirements; see note below |

**Assumptions made:**
- [Assumption 1] — impact if incorrect: [consequence]
- [Assumption 2] — impact if incorrect: [consequence]
```

### Evidence Separation Block (for research and analytical responses)

Used when the response is primarily analytical and the user needs to understand the epistemic basis for the conclusions:

```
**Evidence**
[Established facts, documented standards, verified behaviours]

**Interpretation**
[What these facts mean in this context]

**Uncertainties**
[Where the evidence is absent, limited, or contested]

**Recommendation**
[What to do, given the above — and under what assumptions]
```

---

## Inputs

- Annotated draft (from Stage 20 — Quality Gate)
- Knowledge boundary map (from Stage 07 — Knowledge Boundary Engine)
- Complexity level and risk profile (Stages 05–06)

---

## Outputs

- Evidence-structured draft with appropriate annotations
- Confidence block (when needed)
- Explicit assumption list (when assumptions are load-bearing)

---

## Integration

- Invoked at Stage 21 alongside the Confidence Engine
- Output is the final draft sent to Stage 22 (Final Response)

---

## Anti-Patterns

| Anti-Pattern | Description | Correction |
|---|---|---|
| Evidence laundering | Presenting interpretation as established fact | Apply evidence category; use signal phrase |
| Recommendation masquerading as evidence | "Best practice is X" without basis | State the evidence base or label as recommendation |
| Excessive hedging | Labelling clearly established facts as uncertain | Apply KNOWN classification correctly |
| Buried assumptions | Assumptions embedded in prose without surfacing | Declare assumptions explicitly |
| Missing unknowns | Answering fully without acknowledging absent information | Apply Knowledge Boundary Engine output |

---

## Limitations

- The distinction between Interpretation and Recommendation is sometimes narrow; err toward surfacing both
- Evidence quality cannot always be assessed without access to the original source
- Does not replace rigorous academic citation for research contexts
