# SHP — Smart Human Protocol

**Version:** 4.0.0  
**Architecture:** Autonomous Reasoning  

---

## What is SHP?

SHP is an autonomous reasoning architecture for Claude. It transforms Claude from a prompt-responder into a structured reasoning system that understands, plans, reasons, verifies, improves, and then responds.

SHP does not answer first.

SHP understands first. Plans second. Reasons third. Verifies fourth. Improves fifth. Responds last.

---

## What SHP Provides

- **22-stage reasoning pipeline** — every non-trivial response passes through structured understanding, planning, expert review, verification, and quality gates before delivery
- **14 reasoning engines** — specialised cross-cutting reasoning for knowledge boundaries, strategy selection, threat modelling, consensus resolution, confidence assessment, and more
- **23 expert modules** — domain expertise across engineering, architecture, security, ML, research, writing, business, and design
- **Session memory** — decisions, conventions, and architecture tracked across the conversation for consistency
- **Token Intelligence** — response length, format, and delivery optimised to the minimum required for the highest quality output

---

## How to Use SHP

Upload the contents of this directory to Claude's context window. Once loaded, SHP governs all reasoning for the session. No additional prompting is required.

---

## Pipeline at a Glance

```
01 User Intent Detection        → What does the user actually want?
02 Context Analysis             → What is the full context?
03 Goal Identification          → What is the underlying goal?
04 Constraint Extraction        → What are the hard and soft limits?
05 Complexity Estimation        → How deeply should this be reasoned?
06 Risk Assessment              → What could go wrong?
07 Knowledge Boundary Detection → What is known vs assumed vs unknown?
08 Expert Selection             → Which domain modules apply?
09 Execution Strategy Selection → What type of reasoning fits this task?
10 Planning                     → What is the structured approach?
11 Initial Solution             → Generate complete first draft
12 Alternative Solution Gen.    → Is there a simpler, safer, better approach?
13 Expert Review                → Domain-specific correctness review
14 Consensus Resolution         → Resolve conflicts between expert views
15 Verification                 → Independent correctness and completeness check
16 Security Review              → Threat analysis and vulnerability check
17 Performance Review           → Efficiency and scalability check
18 Architecture Review          → System design check
19 Consistency Review           → Verify against session decisions
20 Quality Gate                 → Enforce all quality standards
21 Confidence Assessment        → Calibrate and communicate uncertainty
22 Final Response               → Deliver optimised response
```

---

## Directory Structure

```
shp/
├── SKILL.md                    ← Entry point; architecture overview
├── README.md                   ← This file
├── CHANGELOG.md                ← Version history
├── CONTRIBUTING.md             ← Plugin development standard
├── LICENSE
│
├── core/                       ← Reasoning infrastructure
│   ├── pipeline.md             ← 22-stage pipeline specification
│   ├── reasoning.md            ← Reasoning principles and philosophy
│   ├── orchestration.md        ← Module coordination and engine sequencing
│   ├── planning.md             ← Planning structures
│   ├── complexity.md           ← Complexity assessment
│   ├── verification.md         ← Verification checklist
│   ├── confidence.md           ← Confidence levels and communication
│   ├── quality.md              ← Quality gates
│   └── memory.md               ← Session memory categories
│
├── engines/                    ← Cross-cutting reasoning engines (14)
│   ├── knowledge-boundary-engine.md
│   ├── strategy-engine.md
│   ├── tool-strategy-engine.md
│   ├── reflection-engine.md
│   ├── multi-expert-engine.md
│   ├── consensus-engine.md
│   ├── verification-engine.md
│   ├── threat-model-engine.md
│   ├── planning-engine.md
│   ├── consistency-engine.md
│   ├── quality-engine.md
│   ├── confidence-engine.md
│   ├── evidence-engine.md
│   └── optimisation-engine.md
│
├── modules/                    ← Domain expert modules (23)
│   ├── token-intelligence.md   ← Always active
│   ├── coding.md
│   ├── debugging.md
│   ├── testing.md
│   ├── architecture.md
│   ├── system-design.md
│   ├── cybersecurity.md
│   ├── api-design.md
│   ├── devops.md
│   ├── cloud.md
│   ├── networking.md
│   ├── machine-learning.md
│   ├── ai.md
│   ├── mathematics.md
│   ├── research.md
│   ├── business.md
│   ├── writing.md
│   ├── documentation.md
│   ├── uiux.md
│   ├── accessibility.md
│   ├── performance.md
│   ├── education.md
│   └── innovation.md
│
├── memory/                     ← Session state
│   ├── context.md
│   ├── decisions.md
│   ├── conventions.md
│   ├── architecture.md
│   └── project-state.md
│
├── templates/                  ← Reusable output templates
│   ├── system-design.md
│   ├── architecture-decision-record.md
│   ├── security-review.md
│   └── api-endpoint.md
│
├── examples/                   ← Worked examples
│   ├── 01-authentication-system.md
│   ├── 02-code-review.md
│   ├── 03-system-design.md
│   ├── 04-debugging.md
│   └── 05-api-design.md
│
└── references/
    ├── standards.md            ← External standards and references
    └── developer-guide.md      ← Architecture and contribution guide
```

---

## Versioning

SHP follows [Semantic Versioning](https://semver.org/).  
See [CHANGELOG.md](CHANGELOG.md) for version history.

| Version | Release | Summary |
|---|---|---|
| 4.0.0 | 2025 | Autonomous reasoning architecture — 22-stage pipeline, 5 new engines, Token Intelligence module |
| 3.2.0 | 2025 | Innovation and Education modules |
| 3.1.0 | 2025 | Planning Engine and API Design example |
| 3.0.0 | 2025 | Initial public release |
