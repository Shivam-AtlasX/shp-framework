# SHP Developer Guide

## Architecture Overview

SHP v4 is organised into five functional layers:

```
┌──────────────────────────────────────────────────────────────┐
│                        SKILL.md                              │
│         Entry point — skill definition and index             │
├──────────────────────────────────────────────────────────────┤
│                       core/                                  │
│                 Reasoning Infrastructure                     │
│                                                              │
│   pipeline       reasoning      orchestration                │
│   planning       complexity     verification                 │
│   confidence     quality        memory                       │
├──────────────────────────────────────────────────────────────┤
│                      engines/                                │
│             Cross-Cutting Reasoning Engines (14)             │
│                                                              │
│   knowledge-boundary   strategy        tool-strategy         │
│   reflection           multi-expert    consensus             │
│   verification         threat-model    planning              │
│   consistency          quality         confidence            │
│   evidence             optimisation                          │
├──────────────────────────────────────────────────────────────┤
│                      modules/                                │
│               Domain Expert Modules (23)                     │
│                                                              │
│   token-intelligence (always active)                         │
│   coding   debugging   testing   architecture   system-design│
│   cybersecurity   api-design   devops   cloud   networking   │
│   machine-learning   ai   mathematics   research   business  │
│   writing   documentation   uiux   accessibility   education │
│   innovation   performance                                   │
├──────────────────────────────────────────────────────────────┤
│                      memory/                                 │
│                    Session State                             │
│                                                              │
│   context   decisions   conventions   architecture           │
│   project-state                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## How Claude Uses SHP

SHP structures the reasoning process; it does not add new knowledge.

When SHP is active:

1. **Every non-trivial request passes through the 22-stage pipeline** (`core/pipeline.md`)
2. **Trivial requests take the fast path** (Stage 01 → 05 → 22)
3. **The pipeline activates modules** based on domain detection and risk profile (`core/orchestration.md`)
4. **Strategy is selected** before execution begins (`engines/strategy-engine.md`)
5. **Knowledge boundaries are mapped** before any claim is made (`engines/knowledge-boundary-engine.md`)
6. **Modules contribute expertise** — each applies its domain's standards
7. **The Reflection Engine challenges every solution** at Stage 12
8. **Engines process across concerns** — verification, quality, confidence, security
9. **Memory enforces consistency** — later outputs do not contradict earlier decisions
10. **Token Intelligence governs delivery** — every response is optimised for quality and efficiency

---

## The 22-Stage Pipeline — Stage Contracts

Each stage has a defined input, output, and responsible engine:

| Stage | Input | Output | Engine |
|---|---|---|---|
| 01 | Raw request | Intent + assumptions | — |
| 02 | Intent + memory | Context summary | Consistency Engine |
| 03 | Context | Goal hierarchy | — |
| 04 | Goals | Constraint list | — |
| 05 | Request + context | Complexity level | Planning Engine |
| 06 | Complexity + domains | Risk profile | — |
| 07 | Risk profile + claims | Boundary map | Knowledge Boundary Engine |
| 08 | Boundary map + domains | Active module list | Multi-Expert Engine |
| 09 | Active modules + intent | Strategy + tool plan | Strategy Engine + Tool Strategy Engine |
| 10 | Strategy | Reasoning plan | Planning Engine |
| 11 | Plan + modules | Initial draft | Active modules |
| 12 | Initial draft | Improved draft | Reflection Engine |
| 13 | Improved draft | Annotated draft + conflicts | Multi-Expert Engine |
| 14 | Conflicts | Resolved draft | Consensus Engine |
| 15 | Resolved draft | Verified draft | Verification Engine |
| 16 | Verified draft | Security-reviewed draft | Threat Model Engine |
| 17 | Draft | Performance-reviewed draft | Performance module |
| 18 | Draft | Architecture-reviewed draft | Architecture module |
| 19 | Draft | Consistency-checked draft | Consistency Engine |
| 20 | Draft | Quality-gated draft | Quality Engine |
| 21 | Draft + boundary map | Evidence-structured draft | Confidence Engine + Evidence Engine |
| 22 | Final draft | Delivered response | Optimisation Engine + Token Intelligence |

---

## Adding a New Module

### Step 1 — Create the file

`modules/your-domain.md` following the complete specification in `CONTRIBUTING.md`.

Required sections: Purpose, Responsibilities, Activation Criteria, Inputs, Outputs, Dependencies, Examples (minimum 2), Best Practices, Common Mistakes, Limitations.

### Step 2 — Register in SKILL.md

Add a row to the Module Index table under the appropriate category.

### Step 3 — Update orchestration mapping

In `core/orchestration.md`, add the domain-to-module mapping in the `selectModules()` function so the orchestrator activates your module correctly.

### Step 4 — Add an example

Create `examples/NN-your-domain-example.md` showing the module on a realistic, non-trivial request.

### Step 5 — Update CHANGELOG.md

Add a MINOR version entry describing the new module.

---

## Adding a New Engine

Engines are more structural than modules. An engine must:

1. Define a clear pipeline stage where it is invoked
2. Not break existing engine contracts (inputs, outputs)
3. Implement its algorithm as pseudocode or structured description
4. Define failure modes and how it handles them
5. Add its invocation to `core/orchestration.md` engine table
6. Register in the Engine Index in `SKILL.md`

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
- [ ] Every claim is accurate and verifiable (not aspirational)
- [ ] Examples are realistic and non-trivial
- [ ] Limitations are honest and specific
- [ ] Dependencies are correctly listed
- [ ] No jargon without definition in the module itself
- [ ] File follows naming conventions
- [ ] `SKILL.md` is updated
- [ ] `core/orchestration.md` is updated
- [ ] `CHANGELOG.md` is updated

---

## Versioning Policy

| Change type | Version bump | Examples |
|---|---|---|
| New module | MINOR | Add `modules/education.md` |
| New engine | MINOR | Add `engines/new-engine.md` |
| Breaking change to pipeline stage contracts | MAJOR | Change stage I/O contracts |
| Breaking change to module interface | MAJOR | Remove required module sections |
| Bug fix / clarification | PATCH | Fix incorrect advice in a module |
| Documentation only | PATCH | Improve examples, fix typos |

---

## Testing a Module

Before publishing a new module:

1. **Activation test:** Write a request that should activate the module. Confirm it produces better output with the module's guidance than without.
2. **Boundary test:** Write a request adjacent to but outside the module's scope. Confirm the module correctly defers.
3. **Conflict test:** Write a request that activates your module and an adjacent module. Confirm the Multi-Expert Engine integrates the outputs coherently.
4. **Limitation test:** Write a request that exceeds the module's stated limitations. Confirm the module acknowledges the limit honestly.

---

## Reference Architecture

Read these files before contributing:

1. `SKILL.md` — the skill's contract, module index, and engine index
2. `core/pipeline.md` — the 22-stage pipeline with stage contracts
3. `core/orchestration.md` — module selection and engine sequencing
4. `CONTRIBUTING.md` — the module and engine development standard
5. `modules/coding.md` — a full-depth module as a template

---

## Common Development Errors

| Error | Description | Fix |
|---|---|---|
| Fabricated standards | Citing non-existent RFC, standard, or library | Only cite real, verifiable references |
| Over-claiming | "Always" and "never" where exceptions exist | Use "typically", "in most cases"; list exceptions |
| Incomplete limitations | Module doesn't acknowledge its scope | Limitations section must be honest and specific |
| Missing examples | Module has fewer than two worked examples | Add realistic, non-trivial examples |
| Circular dependency | Module A → Module B → Module A | Extract shared concept to core/ |
| Missing orchestration registration | Module exists but orchestrator never activates it | Update `core/orchestration.md` activation logic |
| Bypassing pipeline stage contracts | Engine writes directly to output skipping quality gate | Respect stage I/O contracts; integrate via defined stages |
