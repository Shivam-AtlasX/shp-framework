# Research Module

## Purpose

Synthesises information rigorously, evaluates evidence quality, and produces structured research outputs with clear epistemic labelling. Maintains the distinction between what is known, what is inferred, and what is assumed.

---

## Responsibilities

- Synthesise information across multiple sources or domains
- Evaluate evidence quality and identify limitations
- Produce structured research summaries with epistemic labels
- Distinguish facts, inferences, contested claims, and speculation
- Identify gaps in available evidence
- Support literature-style reviews and comparative analyses

---

## Activation Criteria

Activate when:
- Information synthesis across sources is needed
- "Research X", "what do we know about X", "compare X and Y" type requests
- Evidence-based recommendations are expected
- Literature or prior art review is requested
- Fact-checking or claim verification is needed

---

## Inputs

- Research question or topic
- Scope and boundaries (what to include / exclude)
- Knowledge cutoff awareness (what may have changed)
- Sources provided (if any)
- Desired output format

---

## Outputs

- Structured research with explicit epistemic labels
- Evidence quality assessment
- Identified gaps and limitations
- Recommendations (where applicable and clearly labelled as such)

---

## Dependencies

- `modules/writing.md` — structured prose output
- `core/confidence.md` — confidence and uncertainty communication
- `engines/verification-engine.md` — claim verification

---

## Epistemic Labelling System

Every factual claim in research output must carry one of these labels. This is non-negotiable — it enables the reader to calibrate their trust appropriately.

| Label | Meaning | Example |
|---|---|---|
| **[Established]** | Well-supported by strong, consistent evidence | "[Established] PostgreSQL supports ACID transactions" |
| **[Well-supported]** | Supported by good evidence; minor controversy or gaps | "[Well-supported] Transformer architectures outperform RNNs on most NLP benchmarks" |
| **[Contested]** | Evidence exists on multiple sides; genuine expert disagreement | "[Contested] Whether NoSQL databases are more scalable than relational databases for all use cases" |
| **[Reasonable inference]** | Follows logically from established facts; not directly evidenced | "[Inference] Given X and Y, Z is likely" |
| **[Assumption]** | Taken as given for the purpose of reasoning | "[Assumption] Team size remains constant" |
| **[Speculation]** | Plausible but not supported by current evidence | "[Speculation] This approach might improve latency at scale" |
| **[Outdated]** | Previously established but may have changed | "[Outdated as of 2022] Python 2 syntax" |

---

## Research Output Format

### Standard research report

```markdown
## [Topic] — Research Summary

### Overview
[2–3 sentence summary of the key findings and their confidence level]

### Key Findings

**[Finding 1 — most important]**
[Established] [Evidence and explanation]

**[Finding 2]**
[Well-supported] [Evidence and explanation]

**[Finding 3 — contested area]**
[Contested] [Description of the debate and positions]
- Position A: [Evidence]
- Position B: [Evidence]
- Current consensus leans toward: [assessment]

### Evidence Quality
[Assessment of the overall evidence base: strength, recency, diversity of sources]

### Gaps and Limitations
- [What is not known]
- [Where evidence is thin]
- [Where the research may be outdated]

### Recommendations
[If applicable — clearly labelled as recommendations, not established facts]

### Further Reading
[Directions for deeper investigation]
```

---

## Evidence Quality Assessment

Evaluate evidence on these dimensions:

### Source quality

| Tier | Description | Weight |
|---|---|---|
| Tier 1 | Peer-reviewed research, systematic reviews, meta-analyses | Highest |
| Tier 2 | Official documentation, standards bodies, primary data | High |
| Tier 3 | Expert practitioner accounts, conference talks, technical blogs from recognised experts | Medium |
| Tier 4 | General articles, second-hand reports, community discussion | Low |
| Tier 5 | Anecdotes, unverified claims | Very low |

### Evidence strength criteria

- **Reproducibility:** Has the result been reproduced by independent researchers or practitioners?
- **Sample size / scale:** Are the conclusions based on sufficient evidence?
- **Recency:** Is the evidence current for the domain? (Rapidly changing fields → recent matters more)
- **Applicability:** Does the evidence apply to the specific context being researched?
- **Independence:** Are sources independent, or do they all cite each other?

---

## Research Anti-Patterns

### Cherry-picking

Presenting only evidence that supports a predetermined conclusion. Counter with: explicitly seek disconfirming evidence.

### Equivocation

Treating contested claims as established. Counter with: always label claims with appropriate epistemic status.

### Recency bias

Assuming the most recent findings supersede all prior work. Counter with: assess recency relative to the rate of change in the field.

### False balance

Presenting minority and majority positions as equally supported. Counter with: acknowledge the weight of evidence, not just the existence of disagreement.

### Correlation-causation error

Inferring causal relationships from correlational data. Counter with: explicitly label causal claims and identify whether the evidence is correlational or experimental.

---

## Comparative Research

When comparing two or more options (A vs B):

```markdown
## [Option A] vs [Option B]: Comparative Analysis

### Evaluation Criteria
| Criterion | Weight | Option A | Option B |
|---|---|---|---|
| [Criterion 1] | High | [Assessment] | [Assessment] |
| [Criterion 2] | Medium | [Assessment] | [Assessment] |

### Detailed Comparison

**[Criterion 1]**
[Established] [Analysis of how each option performs on this criterion, with evidence]

**[Criterion 2]**
[Well-supported / Contested] [Analysis]

### Summary

| Dimension | Better Option | Notes |
|---|---|---|
| [Dimension 1] | A / B / Draw | [Brief reason] |
| [Dimension 2] | A / B / Draw | [Brief reason] |

### Recommendation
[Given the criteria weights and evidence, Option X is better when [conditions].
Option Y is better when [different conditions].]

### Limitations of This Comparison
[What factors were not evaluated; what context might change the conclusion]
```

---

## Claim Verification Process

When verifying a specific claim:

```
1. State the claim precisely
   ("PostgreSQL is faster than MySQL for analytical queries")

2. Identify what evidence would confirm or refute it
   ("Benchmark results comparing the two on analytical workloads")

3. Assess available evidence
   ("Multiple benchmarks exist; results vary significantly by workload type")

4. Reach a calibrated conclusion
   ("[Contested] Performance depends heavily on query type, indexing strategy, 
    and hardware. Neither is universally faster for analytics.")

5. State what evidence would change the conclusion
   ("A controlled benchmark on the specific workload in question would be
    more informative than general comparisons")
```

---

## Examples

### Example 1 — Technology comparison research

**Question:** Should we use Kafka or RabbitMQ for our event streaming system?

```
## Kafka vs RabbitMQ: Message Broker Comparison

### Overview
[Established] Both are mature, widely-deployed message brokers. They differ
primarily in design philosophy: Kafka is a distributed log optimised for high-
throughput streaming; RabbitMQ is a traditional message queue with flexible routing.
The right choice depends heavily on your access patterns and operational context.

### Key Findings

**Throughput**
[Established] Kafka achieves significantly higher throughput (millions of messages/
second) than RabbitMQ (tens of thousands) in standard configurations.
[Inference] This difference matters only above ~10k messages/second in practice.

**Message retention**
[Established] Kafka retains messages on disk for a configurable period (days/weeks),
allowing consumers to replay history. RabbitMQ deletes messages after consumption.
[Well-supported] This makes Kafka preferable when event replay, audit, or multiple
independent consumers reading the same events are required.

**Operational complexity**
[Well-supported] Kafka requires Zookeeper or KRaft coordination and significant
operational knowledge to run correctly. RabbitMQ is simpler to operate for small
deployments.
[Contested] The complexity gap narrows with managed services (Confluent, MSK,
CloudAMQP) — practitioner experience varies.

**Routing flexibility**
[Established] RabbitMQ supports more flexible routing (topic, fanout, direct,
headers exchanges). Kafka's routing is partition-based and less flexible.

### Recommendation
Given [event replay requirement + throughput > 10k/s]: Kafka.
Given [complex routing + small team + throughput < 10k/s]: RabbitMQ.

### Confidence: Moderate
Operational complexity assessments vary by team experience and managed service usage.
```

---

## Best Practices

- Label every claim — never present inference as established fact
- Seek disconfirming evidence before concluding
- State the evidence quality, not just the conclusion
- Distinguish between "no evidence for X" and "evidence against X"
- Acknowledge when the domain is changing rapidly and conclusions may become outdated

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Presenting contested findings as established | Misleads the reader |
| Omitting contradictory evidence | Biased and incomplete analysis |
| Extrapolating beyond the evidence base | Over-confident conclusions |
| Confusing correlation and causation | Wrong causal model |
| Not acknowledging what is unknown | False completeness |

---

## Limitations

- Knowledge is bounded by training data and cutoff date
- Cannot access paywalled research or primary data
- Fast-moving fields (AI, cloud services, security) may have stale information — note this explicitly
- Cannot conduct original research, surveys, or experiments
