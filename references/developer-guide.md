# SHP Developer Guide

## Architecture Overview

SHP is organised into four functional layers:

```
┌─────────────────────────────────────────────┐
│                  SKILL.md                    │  Entry point, skill definition
├─────────────────────────────────────────────┤
│              core/ (Pipeline)                │  Reasoning infrastructure
│  pipeline  reasoning  orchestration          │
│  planning  complexity  verification          │
│  confidence  quality  memory                 │
├─────────────────────────────────────────────┤
│            engines/ (Cross-cutting)          │  Specialised reasoning
│  verification  confidence  multi-expert      │
│  consensus  threat-model  optimisation       │
│  consistency  planning  quality              │
├─────────────────────────────────────────────┤
│            modules/ (Domain experts)         │  Domain knowledge
│  22 expert modules covering all domains      │
├─────────────────────────────────────────────┤
│         memory/ (Session state)              │  Consistency tracking
│  context  decisions  conventions             │
│  architecture  project-state                 │
└─────────────────────────────────────────────┘
```

---

## How Claude Uses SHP

SHP does not create new knowledge. It structures the reasoning process that Claude applies to any request.

When SHP is active:

1. **Every request passes through the pipeline** (`core/pipeline.md`)
2. **The pipeline activates modules** based on domain detection (`core/orchestration.md`)
3. **Modules contribute expertise** — each one applies its domain's standards
4. **Engines process across concerns** — verification, quality, confidence, security
5. **Memory enforces consistency** — later outputs don't contradict earlier decisions

---

## Adding a New Module

### Step 1 — Create the file

`modules/your-domain.md` following the specification in `CONTRIBUTING.md`.

Required sections:
- Purpose
- Responsibilities
- Activation Criteria
- Inputs
- Outputs
- Dependencies
- Examples (minimum 2)
- Best Practices
- Common Mistakes
- Limitations

### Step 2 — Register in SKILL.md

Add a row to the module index table in `SKILL.md`.

### Step 3 — Update orchestration mapping

In `core/orchestration.md`, add the domain-to-module mapping so the orchestrator activates your module correctly.

### Step 4 — Add an example

Create `examples/NN-your-domain-example.md` showing the module in action on a realistic request.

### Step 5 — Update CHANGELOG.md

Add an entry under `[Unreleased]`.

---

## Extending an Engine

Engines are more structural than modules. An engine extension should:

1. Not break existing engine contracts (inputs, outputs)
2. Add capability as an opt-in extension, not a replacement
3. Document the new algorithm section clearly
4. Add integration notes showing how it connects to the pipeline

---

## File Naming Conventions

| Type | Convention | Example |
|---|---|---|
| Modules | kebab-case noun | `machine-learning.md` |
| Engines | kebab-case with -engine suffix | `confidence-engine.md` |
| Core | kebab-case noun | `reasoning.md` |
| Memory | kebab-case noun | `project-state.md` |
| Examples | NN-descriptive-name | `03-system-design.md` |
| Templates | descriptive noun | `system-design.md` |

---

## Quality Bar for Contributions

A module is ready to merge when:

- [ ] All required sections are present and complete
- [ ] Every claim is accurate (not aspirational)
- [ ] Examples are realistic and non-trivial
- [ ] Limitations are honest and specific
- [ ] Dependencies are correctly listed
- [ ] No jargon without definition in the module itself
- [ ] File follows naming conventions
- [ ] SKILL.md is updated
- [ ] CHANGELOG.md is updated

---

## Versioning Policy

| Change type | Version bump | Examples |
|---|---|---|
| New module | MINOR | Add `modules/education.md` |
| New engine | MINOR | Add `engines/new-engine.md` |
| Breaking change to pipeline | MAJOR | Change stage count or stage contracts |
| Bug fix / clarification | PATCH | Fix incorrect advice in a module |
| Documentation only | PATCH | Improve examples, fix typos |

---

## Testing a Module

Before publishing a new module:

1. **Realistic test case:** Write a request that should activate the module. Does it produce better output with the module's guidance than without?
2. **Boundary test:** Write a request that is adjacent to but outside the module's scope. Does the module correctly defer?
3. **Conflict test:** Write a request that activates your module and an adjacent module (e.g., cybersecurity + networking). Does the Multi-Expert Engine integrate the outputs coherently?
4. **Limitation test:** Write a request that exceeds the module's limitations. Does the module honestly state the limit?

---

## Common Development Errors

| Error | Description | Fix |
|---|---|---|
| Fabricated standards | Citing non-existent RFC or standard | Only cite real, verifiable standards |
| Over-claiming | "Always" and "never" where exceptions exist | Use "typically", "in most cases", list exceptions |
| Incomplete limitations | Module doesn't acknowledge its scope | Limitations section must be honest and specific |
| Missing examples | Module has no worked examples | Add at least 2 realistic examples |
| Circular dependency | Module A depends on B depends on A | Redesign — extract shared concept to core/ |

---

## Reference Architecture

The most important files to understand before contributing:

1. `SKILL.md` — the skill's contract
2. `core/pipeline.md` — how requests flow through SHP
3. `core/orchestration.md` — how modules are selected and coordinated
4. `CONTRIBUTING.md` — the module development standard
5. One completed full-depth module (e.g., `modules/coding.md`) — as a template

Read these five files before writing a new module.
