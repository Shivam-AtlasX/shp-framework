# Architecture Module

## Purpose

Reasons about software architecture decisions: system structure, component design, data modelling, service decomposition, and architectural trade-offs.

---

## Responsibilities

- Evaluate and recommend system architectures
- Decompose systems into components and services
- Design data models and storage strategies
- Analyse architectural trade-offs
- Review existing architectures for weaknesses
- Document architecture decisions

---

## Activation Criteria

Activate when the request involves:
- System design or redesign
- Service decomposition decisions
- Data model design
- Architectural pattern selection (microservices, monolith, event-driven, etc.)
- Scalability or reliability design
- Integration strategy between systems

---

## Inputs

- System requirements (functional and non-functional)
- Team size and operational capability
- Scale requirements (users, throughput, data volume)
- Technology constraints (existing stack, cloud provider, etc.)
- Compliance or regulatory requirements
- Existing system context

---

## Outputs

- Architecture recommendation with rationale
- Component diagram (described or ASCII)
- Data model or storage strategy
- Trade-off analysis
- Architecture Decision Record (ADR) when requested

---

## Dependencies

- `modules/system-design.md` — detailed system design patterns
- `modules/cybersecurity.md` — security architecture
- `modules/performance.md` — scalability and performance design
- `modules/cloud.md` — cloud-specific architecture patterns
- `engines/consensus-engine.md` — resolving architecture trade-offs

---

## Architectural Patterns

The module evaluates these patterns for fit:

| Pattern | Best When |
|---|---|
| Modular Monolith | Small team, early stage, domain boundaries unclear |
| Microservices | Large team, independent scaling needs, proven domain boundaries |
| Event-Driven | Loose coupling, async workflows, audit trail required |
| CQRS + Event Sourcing | Complex domain, audit required, read/write ratio skewed |
| Serverless | Variable load, low operational overhead, stateless workloads |
| Layered (N-Tier) | CRUD applications, standard business apps |
| Hexagonal (Ports & Adapters) | Testability, domain isolation, multiple interfaces |

---

## Trade-Off Dimensions

Architecture decisions are evaluated across:

| Dimension | Questions |
|---|---|
| Complexity | How much operational and cognitive complexity does this add? |
| Coupling | How tightly coupled are the components? |
| Cohesion | How focused are the components? |
| Scalability | What are the scaling characteristics? |
| Reliability | What are the failure modes and recovery strategies? |
| Maintainability | How easy is this to change over time? |
| Testability | How easy is this to test in isolation? |
| Operability | How easy is this to deploy, monitor, and debug? |
| Cost | What is the infrastructure and operational cost? |

---

## Architecture Decision Record Format

```markdown
# ADR-[N]: [Decision Title]

**Status:** [Proposed / Accepted / Superseded]
**Date:** [Date]

## Context
[What situation or requirement drove this decision?]

## Decision
[What was decided?]

## Rationale
[Why this option over alternatives?]

## Alternatives Considered
- [Alternative 1] — rejected because [reason]
- [Alternative 2] — rejected because [reason]

## Consequences
- [Positive consequence]
- [Negative consequence / trade-off accepted]
```

---

## Examples

### Example 1 — Monolith vs Microservices

**Context:** 3-person team, early-stage SaaS product, domain boundaries not yet proven

**Recommendation:** Modular monolith

**Rationale:** Microservices introduce distributed systems complexity (network partitions, eventual consistency, service discovery, distributed tracing) that a 3-person team cannot effectively manage in addition to building the product. A well-structured modular monolith with clear internal boundaries preserves the option to extract services later once boundaries are proven and team grows.

---

## Best Practices

- Design for the current scale, not 100x future scale
- Establish clear boundaries between components before implementing them
- Prefer reversible decisions when uncertainty is high
- Document decisions as ADRs; they are as valuable as the code
- Consider operational complexity alongside technical elegance

---

## Common Mistakes

- Choosing microservices for a small team or young product
- Designing for theoretical scale that will never be reached
- Ignoring operational requirements (monitoring, deployment, rollback)
- Under-specifying data ownership and consistency boundaries
- Coupling components across module boundaries

---

## Limitations

- Architecture decisions depend heavily on team and organisational context — this module asks for it when missing
- Cannot model organisational dynamics and team capability without user input
- Does not replace domain expertise for specific industries (healthcare, finance, etc.)
