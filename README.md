# SHP — Smart Human Protocol
 
**Version:** 1.0.0
**Type:** Claude Skill — Reasoning Framework
**Format:** `.skill` archive / repository zip
 
---
 
## Overview
 
SHP is a structured reasoning framework installed into Claude as a skill. It does not extend Claude's knowledge or act as a persona. It governs *how* Claude processes requests — imposing a reproducible, multi-stage reasoning discipline over intent detection, expert module selection, verification, confidence estimation, and response delivery.
 
**What SHP changes:**
- Claude runs an 18-stage internal pipeline on every non-trivial request instead of responding directly
- Domain expert modules activate selectively based on what the request actually requires
- Every response passes through verification and quality gates before delivery
- Confidence levels and assumptions are stated explicitly — never implied
- Session memory tracks architectural decisions, naming conventions, and project state so later responses stay consistent with earlier ones
**What SHP does not do:**
- It does not extend Claude's knowledge cutoff
- It does not access external systems unless tools are explicitly provided
- It is not a roleplay or persona framework
- It does not fabricate certainty
---
 
## Installation
 
### Step 1 — Log in or sign up
 
Go to [claude.ai](https://claude.ai) and log in to your account. If you don't have one, create one. A free account is sufficient to use skills.
 
### Step 2 — Open the Skills panel
 
In the Claude interface, click the **"Add Skills"** button. This opens the Skills panel where installed skills are listed and managed.
 
### Step 3 — Go to Manage Skills
 
Inside the Skills panel, select **"Manage Skills"**. This is where you can add, remove, or inspect installed skill packages.
 
### Step 4 — Upload the SHP skill package
 
Click **"Add Skills"** and upload the repository zip file. Claude will unpack the archive, register `SKILL.md` as the entry point, and make SHP available for the current and future sessions.
 
Once installed, SHP activates automatically when triggered (see Activation below). No further configuration is required.
 
---
 
## Repository Structure
 
```
shp/
├── SKILL.md                ← Skill entry point (loaded by Claude on activation)
├── core/
│   ├── pipeline.md         ← Full 18-stage reasoning pipeline specification
│   ├── reasoning.md
│   ├── planning.md
│   ├── orchestration.md
│   ├── complexity.md
│   ├── verification.md
│   ├── confidence.md
│   ├── quality.md
│   └── memory.md
├── modules/                ← Domain expert modules (selective activation)
│   ├── coding.md
│   ├── architecture.md
│   ├── cybersecurity.md
│   ├── ai.md
│   ├── machine-learning.md
│   ├── research.md
│   ├── writing.md
│   ├── testing.md
│   ├── debugging.md
│   ├── uiux.md
│   ├── business.md
│   ├── networking.md
│   ├── cloud.md
│   ├── devops.md
│   ├── performance.md
│   ├── mathematics.md
│   ├── documentation.md
│   ├── accessibility.md
│   ├── api-design.md
│   └── system-design.md
├── engines/                ← Cross-cutting reasoning engines
├── memory/                 ← Session memory and consistency tracking
├── templates/              ← Response structure templates
├── examples/               ← Worked examples per domain
├── references/             ← External standards referenced by modules
├── CONTRIBUTING.md
├── CHANGELOG.md
└── LICENSE
```
 
---
 
## Activation
 
SHP activates at the session level. Once triggered, it remains active for the entire session unless explicitly disabled.
 
| Method | Syntax |
|---|---|
| Prefix trigger | `SHP: <your request>` |
| Slash command | `/shp <your request>` |
| Skill upload | Upload `shp-v3.skill` directly — auto-activates |
| Session declaration | `"Use SHP for this session"` |
 
---
 
## Reasoning Pipeline
 
Every non-trivial request runs through all 18 stages internally before a response is delivered. Stages are not skipped for convenience.
 
| Stage | Name |
|---|---|
| 1 | Intent Detection |
| 2 | Context Analysis |
| 3 | Complexity Assessment |
| 4 | Requirement Extraction |
| 5 | Domain Detection |
| 6 | Expert Module Selection |
| 7 | Planning |
| 8 | Draft Generation |
| 9 | Verification |
| 10 | Security Review *(if applicable)* |
| 11 | Architecture Review *(if applicable)* |
| 12 | Performance Review *(if applicable)* |
| 13 | Testing Review *(if applicable)* |
| 14 | Quality Review |
| 15 | Confidence Evaluation |
| 16 | Response Optimisation |
| 17 | Final Validation |
| 18 | Professional Response Delivery |
 
Full specification: `core/pipeline.md`
 
---
 
## Expert Modules
 
Modules activate selectively — only those relevant to the current request are invoked. A simple question does not spin up the full module stack.
 
| Domain | Module File |
|---|---|
| Core Reasoning | `core/reasoning.md` |
| Planning | `core/planning.md` |
| Orchestration | `core/orchestration.md` |
| Complexity Analysis | `core/complexity.md` |
| Verification | `core/verification.md` |
| Confidence Estimation | `core/confidence.md` |
| Quality Control | `core/quality.md` |
| Session Memory | `core/memory.md` |
| Coding | `modules/coding.md` |
| Software Architecture | `modules/architecture.md` |
| Cybersecurity | `modules/cybersecurity.md` |
| AI / ML | `modules/ai.md`, `modules/machine-learning.md` |
| Research | `modules/research.md` |
| Technical Writing | `modules/writing.md` |
| Testing | `modules/testing.md` |
| Debugging | `modules/debugging.md` |
| UI/UX | `modules/uiux.md` |
| Business Analysis | `modules/business.md` |
| Networking | `modules/networking.md` |
| Cloud Infrastructure | `modules/cloud.md` |
| DevOps | `modules/devops.md` |
| Performance | `modules/performance.md` |
| Mathematics | `modules/mathematics.md` |
| Documentation | `modules/documentation.md` |
| Accessibility | `modules/accessibility.md` |
| API Design | `modules/api-design.md` |
| System Design | `modules/system-design.md` |
 
---
 
## Engines
 
Engines handle cross-cutting concerns that apply across multiple modules and pipeline stages.
 
| Engine | Function |
|---|---|
| Confidence Engine | Estimates uncertainty; surfaces assumptions explicitly |
| Verification Engine | Reviews draft output before delivery |
| Multi-Expert Engine | Coordinates output across simultaneously active modules |
| Consensus Engine | Resolves conflicting recommendations between modules |
| Threat Model Engine | Evaluates security posture when security is in scope |
| Optimisation Engine | Reduces verbosity without sacrificing accuracy |
| Consistency Engine | Ensures later responses don't contradict earlier session decisions |
| Planning Engine | Structures multi-step work before execution begins |
| Quality Engine | Enforces quality gates as a final check before delivery |
 
---
 
## Memory System
 
SHP maintains a session-scoped memory layer that persists:
 
- Architecture decisions made earlier in the session
- Naming conventions and coding standards established by the user
- Project-specific constraints referenced in prior turns
- Resolved trade-offs and their rationale
This prevents drift across long sessions where Claude might otherwise treat each message as isolated context. See `memory/` for implementation details.
 
---
 
## Supported Workflows
 
SHP is designed for complex, multi-domain, or production-grade work. It handles:
 
- Software architecture and system design
- Production code generation and code review
- Cybersecurity analysis and threat modelling
- AI/ML system design and evaluation
- Research synthesis and technical writing
- DevOps, cloud, and infrastructure planning
- API design and documentation
- Business analysis and strategic planning
- UI/UX review and accessibility auditing
- Mathematical reasoning and performance analysis
- Multi-expert trade-off analysis
---
 
## Usage Examples
 
```
SHP: Design a multi-tenant SaaS authentication system using JWTs and refresh tokens.
 
SHP: Review this Python function for security vulnerabilities and performance issues.
 
SHP: Write a technical specification for a real-time event processing pipeline.
 
SHP: Analyse the trade-offs between microservices and a modular monolith for our use case.
 
SHP: Generate production-ready Go code for an HTTP middleware chain with rate limiting.
 
SHP: Threat model a REST API that handles PII data for a fintech application.
 
SHP: Design the database schema for a multi-region e-commerce platform with strong consistency requirements.
```
 
---
 
## Design Principles
 
These are non-negotiable constraints on how SHP operates, not aspirational goals.
 
1. **Reason systematically.** Every response reflects the full pipeline — shortcuts are not taken to save time.
2. **Calibrate depth to complexity.** Simple requests get direct answers. Complex requests get structured analysis. Neither is padded to look more thorough.
3. **Never fabricate certainty.** Confidence level and assumptions are always stated. If SHP doesn't know, it says so.
4. **Modules activate on relevance, not by default.** Expert modules contribute only when the request actually calls for them.
5. **Verify before delivering.** The first internal draft is reviewed against quality gates before the user sees anything.
6. **Consistency across the session.** Memory ensures decisions made in turn 2 aren't contradicted in turn 20.
---
 
## Extending SHP
 
New modules must define all of the following before being accepted:
 
- **Purpose** — what the module is responsible for
- **Responsibilities** — specific tasks it handles
- **Activation Criteria** — when it activates vs. stays dormant
- **Inputs** — what it expects from the pipeline
- **Outputs** — what it passes downstream
- **Dependencies** — other modules or engines it relies on
- **Examples** — at least two worked examples
- **Best Practices** — guidance for correct use
- **Common Mistakes** — known failure modes to avoid
- **Limitations** — explicit scope boundaries
See `CONTRIBUTING.md` for the full module development guide.
 
---
 
## Version History
 
See `CHANGELOG.md`.
 
---
 
## License
 
See `LICENSE`.
 

