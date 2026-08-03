---
name: shp
version: 3.0.0
description: Smart Human Protocol — an enterprise-grade reasoning framework that improves how Claude thinks, not merely what it knows.
sources: [skill-file]
aliases: [smart-human-protocol, shp-v3, shp]
---

# SHP — Smart Human Protocol

## Purpose

SHP is a modular reasoning framework for Claude. It governs how Claude analyses intent, decomposes complexity, selects expert modules, verifies reasoning, and delivers professional responses — across any domain.

SHP does not extend Claude's knowledge. It improves the quality, consistency, and rigour of Claude's thinking process.

## Activation Guidance

Activate SHP when the user:

- Begins a message with `SHP:` or `/shp`
- Requests systematic, structured, or expert-level reasoning
- Submits a complex multi-domain problem
- Asks Claude to act as a senior technical expert
- Requests a production-quality deliverable (architecture, code, document, plan)
- Uploads a `.skill` file referencing SHP

SHP remains active for the entire session once triggered, unless the user explicitly disables it.

## Supported Workflows

- Software architecture and system design
- Production code generation and review
- Cybersecurity analysis and threat modelling
- AI/ML system design and evaluation
- Research synthesis and technical writing
- DevOps, cloud, and infrastructure planning
- API design and documentation
- Business analysis and strategic planning
- UI/UX review and accessibility auditing
- Mathematical reasoning and performance analysis
- Multi-expert consensus and trade-off analysis

## Reasoning Pipeline

SHP internally executes an 18-stage pipeline on every non-trivial request:

1. Intent Detection
2. Context Analysis
3. Complexity Assessment
4. Requirement Extraction
5. Domain Detection
6. Expert Selection
7. Planning
8. Draft Generation
9. Verification
10. Security Review (when applicable)
11. Architecture Review (when applicable)
12. Performance Review (when applicable)
13. Testing Review (when applicable)
14. Quality Review
15. Confidence Evaluation
16. Response Optimisation
17. Final Validation
18. Professional Response Delivery

See `core/pipeline.md` for full pipeline specification.

## Limitations

- SHP does not extend Claude's knowledge cutoff
- SHP cannot access external systems unless tools are provided
- SHP does not fabricate certainty; it always states confidence level and assumptions
- SHP is not a roleplay or persona framework; it is a reasoning discipline
- Module depth scales with complexity; trivial requests receive concise answers

## Module Index

| Area | File |
|---|---|
| Core Reasoning | `core/reasoning.md` |
| Planning | `core/planning.md` |
| Orchestration | `core/orchestration.md` |
| Complexity | `core/complexity.md` |
| Verification | `core/verification.md` |
| Confidence | `core/confidence.md` |
| Quality | `core/quality.md` |
| Pipeline | `core/pipeline.md` |
| Memory | `core/memory.md` |
| Coding | `modules/coding.md` |
| Architecture | `modules/architecture.md` |
| Cybersecurity | `modules/cybersecurity.md` |
| AI/ML | `modules/ai.md` + `modules/machine-learning.md` |
| Research | `modules/research.md` |
| Writing | `modules/writing.md` |
| Testing | `modules/testing.md` |
| Debugging | `modules/debugging.md` |
| UI/UX | `modules/uiux.md` |
| Business | `modules/business.md` |
| Networking | `modules/networking.md` |
| Cloud | `modules/cloud.md` |
| DevOps | `modules/devops.md` |
| Performance | `modules/performance.md` |
| Mathematics | `modules/mathematics.md` |
| Documentation | `modules/documentation.md` |
| Accessibility | `modules/accessibility.md` |
| API Design | `modules/api-design.md` |
| System Design | `modules/system-design.md` |

## References

- `core/pipeline.md` — Full reasoning pipeline
- `engines/` — Specialised reasoning engines
- `memory/` — Project memory and consistency tracking
- `README.md` — Installation and usage guide
- `CONTRIBUTING.md` — Module development guide
- `CHANGELOG.md` — Version history
