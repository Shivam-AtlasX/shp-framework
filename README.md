# SHP — Smart Human Protocol

**Version:** 3.0.0  
**Type:** Claude Skill (Enterprise Reasoning Framework)  
**Format:** `.skill` archive

---

## What is SHP?

SHP is an enterprise-grade reasoning framework for Claude. It governs *how* Claude thinks — not what it knows.

Rather than adding domain knowledge, SHP installs a structured reasoning discipline:

- Systematic intent detection and complexity analysis
- Modular expert coordination across 20+ domains
- Built-in verification, confidence estimation, and quality gates
- Consistent memory and project-state tracking
- Professional, audience-appropriate response delivery

SHP is not a persona. It is not roleplay. It is a reproducible reasoning protocol.

---

## Installation

### As a Claude Skill

1. Upload `shp-v3.skill` to your Claude session via the paperclip / file attachment UI.
2. Claude will load the skill and activate SHP reasoning automatically.
3. Alternatively, prefix your first message with `SHP:` to activate manually.

### Repository Usage

Clone or download this repository. Reference `SKILL.md` as the skill entry point. All modules are self-contained Markdown files.

```
shp/
├── SKILL.md           ← Skill entry point
├── core/              ← Reasoning pipeline components
├── modules/           ← Domain expert modules
├── engines/           ← Specialised reasoning engines
├── memory/            ← Project memory system
├── templates/         ← Response templates
├── examples/          ← Worked examples
└── references/        ← External standards and references
```

---

## Activation

| Method | Syntax |
|---|---|
| Prefix trigger | `SHP: <your request>` |
| Slash command | `/shp <your request>` |
| Skill upload | Upload `shp-v3.skill` — auto-activates |
| Session declaration | "Use SHP for this session" |

Once activated, SHP remains active for the entire session.

---

## Usage Examples

```
SHP: Design a multi-tenant SaaS authentication system using JWTs and refresh tokens.

SHP: Review this Python function for security vulnerabilities and performance issues.

SHP: Write a technical specification for a real-time event processing pipeline.

SHP: Analyse the trade-offs between microservices and a modular monolith for our use case.

SHP: Generate production-ready Go code for an HTTP middleware chain with rate limiting.
```

---

## How It Works

### Reasoning Pipeline

Every non-trivial request passes through an 18-stage internal pipeline. See `core/pipeline.md`.

### Expert Modules

SHP automatically selects the most relevant expert modules for each request. Modules only activate when relevant — simple questions receive concise answers.

### Engines

Specialised engines handle cross-cutting concerns:

| Engine | Purpose |
|---|---|
| Confidence Engine | Estimates and communicates uncertainty |
| Verification Engine | Reviews drafts before delivery |
| Multi-Expert Engine | Coordinates expert module outputs |
| Consensus Engine | Resolves conflicts between expert recommendations |
| Threat Model Engine | Evaluates security posture |
| Optimisation Engine | Improves response quality and conciseness |
| Consistency Engine | Maintains project-wide coherence |
| Planning Engine | Structures complex multi-step work |
| Quality Engine | Enforces quality gates before delivery |

### Memory System

SHP tracks architecture decisions, naming conventions, coding standards, and project state across a session. See `memory/`.

---

## Design Principles

1. **Reason systematically, not superficially.** Every response reflects structured analysis.
2. **Calibrate to complexity.** Simple tasks get direct answers. Complex tasks get deep reasoning.
3. **Never fabricate certainty.** SHP always states confidence level and assumptions explicitly.
4. **Expert depth without expert ego.** Modules contribute only when relevant.
5. **Verify before delivering.** The first draft is always a draft.
6. **Maintain consistency.** Memory tracks decisions so later outputs don't contradict earlier ones.

---

## Extending SHP

See `CONTRIBUTING.md` for the module development guide.

New modules must define: Purpose, Responsibilities, Activation Criteria, Inputs, Outputs, Dependencies, Examples, Best Practices, Common Mistakes, and Limitations.

---

## Version History

See `CHANGELOG.md`.

---

## License

See `LICENSE`.
