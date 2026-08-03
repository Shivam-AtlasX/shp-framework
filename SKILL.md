---
name: shp
description: A modular AI reasoning framework for structured thinking, adaptive planning, expert orchestration, verification, and intelligent problem solving.
---

# SHP Framework

**Version:** 4.0.0
**Type:** Autonomous Reasoning Architecture
**Format:** Modular Markdown

---

## What is SHP?

SHP is a modular AI reasoning framework that guides Claude through structured analysis, planning, verification, and response generation. It improves the reasoning process while remaining transparent about uncertainty.

SHP understands first, plans second, reasons third, verifies fourth, improves fifth, and responds last.

---

## How to Activate SHP

Upload all files in this directory to Claude's context window. Once loaded, SHP governs every response for the session.

You do not need to prompt Claude to "use SHP" — once the files are in context, the reasoning architecture is active.

---

## Architecture

SHP is organised into five functional layers:

```
┌──────────────────────────────────────────────────────────────┐
│                        SKILL.md                              │
│                 Entry point — you are here                   │
├──────────────────────────────────────────────────────────────┤
│                     core/                                    │
│              Reasoning Infrastructure                        │
│                                                              │
│  pipeline.md     reasoning.md     orchestration.md           │
│  planning.md     complexity.md    verification.md            │
│  confidence.md   quality.md       memory.md                  │
├──────────────────────────────────────────────────────────────┤
│                    engines/                                  │
│             Cross-Cutting Reasoning Engines                  │
│                                                              │
│  multi-expert-engine.md    consensus-engine.md               │
│  confidence-engine.md      verification-engine.md            │
│  threat-model-engine.md    optimisation-engine.md            │
│  quality-engine.md         consistency-engine.md             │
│  planning-engine.md        knowledge-boundary-engine.md      │
│  reflection-engine.md      evidence-engine.md                │
│  strategy-engine.md        tool-strategy-engine.md           │
├──────────────────────────────────────────────────────────────┤
│                   modules/                                   │
│                  Domain Expert Modules                       │
│                                                              │
│  coding         debugging        testing                     │
│  architecture   system-design    performance                 │
│  cybersecurity  api-design       devops                      │
│  cloud          networking       machine-learning            │
│  ai             mathematics      research                    │
│  business       writing          documentation               │
│  uiux           accessibility    education                   │
│  innovation     token-intelligence                           │
├──────────────────────────────────────────────────────────────┤
│                   memory/                                    │
│                  Session State                               │
│                                                              │
│  context.md     decisions.md     conventions.md              │
│  architecture.md               project-state.md             │
└──────────────────────────────────────────────────────────────┘
```

---

## Pipeline Summary

SHP executes a 22-stage pipeline internally on every non-trivial request:

| Stage | Name | Purpose |
|---|---|---|
| 01 | User Intent Detection | Understand what the user actually wants |
| 02 | Context Analysis | Load session memory and infer context |
| 03 | Goal Identification | Distinguish immediate from underlying goal |
| 04 | Constraint Extraction | Extract hard and soft constraints |
| 05 | Complexity Estimation | Set reasoning depth |
| 06 | Risk Assessment | Identify what could go wrong |
| 07 | Knowledge Boundary Detection | Map what is known, inferred, assumed, unknown |
| 08 | Expert Selection | Activate relevant domain modules |
| 09 | Execution Strategy Selection | Choose the reasoning strategy |
| 10 | Planning | Structure the approach |
| 11 | Initial Solution | Generate first complete draft |
| 12 | Alternative Solution Generation | Challenge and improve the draft |
| 13 | Expert Review | Domain-specific review |
| 14 | Consensus Resolution | Resolve module conflicts |
| 15 | Verification | Independent correctness review |
| 16 | Security Review | Threat and vulnerability analysis |
| 17 | Performance Review | Efficiency and scalability review |
| 18 | Architecture Review | System design review |
| 19 | Consistency Review | Verify against session memory |
| 20 | Quality Gate | Enforce quality standards |
| 21 | Confidence Assessment | Calibrate and communicate uncertainty |
| 22 | Final Response | Deliver the optimised response |

---

## Module Index

### Core Modules (always active)

| Module | File | Purpose |
|---|---|---|
| Token Intelligence | `modules/token-intelligence.md` | Response length, format, and delivery optimisation |

### Engineering

| Module | File | Activation |
|---|---|---|
| Coding | `modules/coding.md` | Code generation, review, debugging, refactoring |
| Debugging | `modules/debugging.md` | Root cause analysis and defect resolution |
| Testing | `modules/testing.md` | Test strategy, test generation, coverage |
| Performance | `modules/performance.md` | Algorithmic efficiency, scalability |
| DevOps | `modules/devops.md` | CI/CD, deployment, observability |

### Architecture & Design

| Module | File | Activation |
|---|---|---|
| Architecture | `modules/architecture.md` | System architecture and design decisions |
| System Design | `modules/system-design.md` | Distributed systems, data flows |
| API Design | `modules/api-design.md` | REST, GraphQL, gRPC API design |
| Cloud | `modules/cloud.md` | Cloud-native patterns, AWS/GCP/Azure |
| Networking | `modules/networking.md` | Network protocols, DNS, TLS |

### Security

| Module | File | Activation |
|---|---|---|
| Cybersecurity | `modules/cybersecurity.md` | Security review, threat analysis, vulnerabilities |

### Intelligence & Data

| Module | File | Activation |
|---|---|---|
| Machine Learning | `modules/machine-learning.md` | ML models, training, evaluation |
| AI | `modules/ai.md` | AI systems, prompt engineering, LLM integration |
| Mathematics | `modules/mathematics.md` | Formal reasoning, algorithms, proofs |
| Research | `modules/research.md` | Evidence synthesis, academic reasoning |

### Communication & Presentation

| Module | File | Activation |
|---|---|---|
| Writing | `modules/writing.md` | Technical and professional writing |
| Documentation | `modules/documentation.md` | Developer docs, API docs, READMEs |
| UI/UX | `modules/uiux.md` | Interface design, user experience |
| Accessibility | `modules/accessibility.md` | WCAG, inclusive design |
| Education | `modules/education.md` | Teaching, explanation, learning scaffolding |

### Business & Strategy

| Module | File | Activation |
|---|---|---|
| Business | `modules/business.md` | Strategy, trade-offs, stakeholder reasoning |
| Innovation | `modules/innovation.md` | Creative problem solving, ideation |

---

## Engine Index

| Engine | File | Pipeline Stage |
|---|---|---|
| Knowledge Boundary | `engines/knowledge-boundary-engine.md` | Stage 07 |
| Multi-Expert | `engines/multi-expert-engine.md` | Stages 08, 13 |
| Strategy | `engines/strategy-engine.md` | Stage 09 |
| Tool Strategy | `engines/tool-strategy-engine.md` | Stage 09 |
| Planning | `engines/planning-engine.md` | Stages 05, 10 |
| Reflection | `engines/reflection-engine.md` | Stage 12 |
| Consensus | `engines/consensus-engine.md` | Stage 14 |
| Verification | `engines/verification-engine.md` | Stage 15 |
| Threat Model | `engines/threat-model-engine.md` | Stage 16 |
| Consistency | `engines/consistency-engine.md` | Stages 02, 19 |
| Quality | `engines/quality-engine.md` | Stage 20 |
| Confidence | `engines/confidence-engine.md` | Stage 21 |
| Evidence | `engines/evidence-engine.md` | Stage 21 |
| Optimisation | `engines/optimisation-engine.md` | Stage 22 |

---

## What SHP Changes

SHP changes the *process* of reasoning, not the *knowledge* base.

| Without SHP | With SHP |
|---|---|
| Immediate generation from prompt | 22-stage pipeline before response |
| Single perspective | Coordinated expert modules |
| Confidence unstated | Confidence assessed and communicated |
| Fixed response depth | Depth calibrated to complexity |
| No alternative consideration | Reflection Engine challenges every solution |
| Knowledge limits invisible | Knowledge Boundary Engine maps them explicitly |
| Inconsistency possible | Consistency Engine enforces session memory |
| No strategy selection | Strategy Engine matches approach to task type |
| Response length arbitrary | Token Intelligence Engine optimises delivery |

---

## Plugin Architecture

SHP is extensible. New modules can be added without modifying the core framework.

Every plugin module must define:

- **Purpose** — what reasoning capability it adds
- **Activation Criteria** — when the orchestrator activates it
- **Inputs** — what context it requires
- **Outputs** — what it produces
- **Responsibilities** — what specific tasks it performs
- **Dependencies** — other modules or engines it relies on
- **Examples** — at least two worked examples
- **Limitations** — where its expertise ends

Modules are placed in `modules/` and registered in the Module Index above.

See `CONTRIBUTING.md` for the full development standard.

---

## Quality Commitment

Every SHP response is held to these standards:

- Correct — no factual or logical errors
- Complete — all requirements addressed
- Consistent — no contradictions with session decisions
- Secure — no vulnerabilities introduced
- Honest — uncertainty communicated clearly
- Efficient — minimum tokens for maximum quality
- Professional — appropriate tone and format for the audience

If meaningful weaknesses remain after all pipeline stages, they are disclosed rather than concealed.
