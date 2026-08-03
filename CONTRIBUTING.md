# Contributing to SHP

This guide covers how to develop new modules and engines, extend existing components, and maintain quality standards across the project.

---

## Plugin Architecture

SHP v4 uses a formal plugin architecture. New capabilities are added as self-contained modules or engines that integrate with the core framework without modifying it.

The core pipeline, orchestration logic, and memory system are stable contracts. Plugins extend SHP by registering with them, not by changing them.

---

## Module Development Standard

Every SHP module must implement this full specification. Incomplete modules will not be merged.

### Required Sections

```markdown
# Module Name

## Purpose
One to three sentences. What reasoning capability does this module add?

## Responsibilities
Bullet list. What specific tasks does this module perform?

## Activation Criteria
When should the orchestrator activate this module?
List the conditions — keywords, domains, task types, risk signals.

## Inputs
What context does this module require to operate?
- Request text and intent
- Relevant prior context
- Session memory (if applicable)
- Other module outputs (if applicable)

## Outputs
What does this module produce?
- Analysis, recommendations, code, review, etc.

## Dependencies
Which other modules or engines does this module rely on?

## Examples
Minimum two worked examples showing the module in active use.
Examples must be realistic, non-trivial, and demonstrate the module's specific value.

## Best Practices
What should this module always do?

## Common Mistakes
What should this module never do?

## Limitations
Where does this module's expertise end?
What should it defer to other modules for?
What does it not replace?
```

---

## Engine Development Standard

Engines differ from modules: they operate across domains rather than within one.

An engine must define:

- **Purpose** — what reasoning function it provides
- **Activation Criteria** — when the pipeline invokes it
- **Algorithm** — step-by-step reasoning process (pseudocode or structured description)
- **Inputs** — what it consumes
- **Outputs** — what it produces
- **Integration** — which pipeline stage(s) invoke it and what feeds into it and out of it
- **Failure Modes** — what happens when it cannot operate correctly
- **Limitations** — where the engine's capability ends

---

## Module Placement

| Type | Directory |
|---|---|
| Reasoning infrastructure | `core/` |
| Domain expert module | `modules/` |
| Cross-cutting reasoning engine | `engines/` |
| Session state tracking | `memory/` |
| Response patterns | `templates/` |
| Worked examples | `examples/` |

---

## Plugin Registration

When adding a new module:

### Step 1 — Create the file

`modules/your-domain.md` following the specification above.

### Step 2 — Register in SKILL.md

Add a row to the Module Index table in `SKILL.md` under the appropriate category.

### Step 3 — Update orchestration mapping

In `core/orchestration.md`, add the domain-to-module mapping so the orchestrator activates your module for the correct request types.

### Step 4 — Add an example

Create `examples/NN-your-domain-example.md` showing the module on a realistic request that demonstrates its specific reasoning contribution.

### Step 5 — Update CHANGELOG.md

Add an entry under the appropriate version or `[Unreleased]`.

---

## Quality Standards

All contributions must meet these standards:

### Accuracy
- No fabricated standards, specifications, library methods, or best practices
- Claims must be verifiable against authoritative references
- Do not cite non-existent RFCs, papers, or standards

### Clarity
- Plain English
- No jargon without definition
- Technical terms used precisely and consistently

### Completeness
- All required sections present and substantive (not placeholder text)
- Examples must be realistic and non-trivial
- Limitations must be honest and specific — not generic disclaimers

### Consistency
- Follow existing file naming conventions (kebab-case)
- Follow existing heading structure
- Do not contradict other modules without explicit resolution in the Consensus Engine

---

## Naming Conventions

- Files: `kebab-case.md`
- Engines: `kebab-case-engine.md`
- Headings: Title Case for H1 and H2; Sentence case for H3+
- Module names in references: use the file stem (`modules/coding.md` → `coding`)

---

## Pull Request Process

1. Fork the repository
2. Create a branch: `feature/module-name` or `fix/issue-description`
3. Write the module or engine following the specification above
4. Add a worked example in `examples/`
5. Update `SKILL.md` module or engine index
6. Update `core/orchestration.md` activation mapping
7. Update `CHANGELOG.md`
8. Submit pull request with a clear description of what the plugin adds and why

---

## Versioning

SHP follows Semantic Versioning:

- **MAJOR** — breaking changes to the pipeline contract, stage numbering, or module interface
- **MINOR** — new modules, engines, or capabilities (backward compatible)
- **PATCH** — corrections, clarifications, documentation improvements

---

## Quality Bar for Contributions

A module is ready to merge when:

- [ ] All required sections are present and complete
- [ ] Every claim is accurate and verifiable
- [ ] Examples are realistic, non-trivial, and show the module's specific value
- [ ] Limitations are honest and specific
- [ ] Dependencies are correctly listed
- [ ] No jargon without definition in the module itself
- [ ] File follows naming conventions
- [ ] `SKILL.md` module index updated
- [ ] `core/orchestration.md` activation mapping updated
- [ ] `CHANGELOG.md` updated

---

## Questions

Open an issue with the label `question` for design discussions.  
Open an issue with the label `bug` for incorrect reasoning or module behaviour.  
Open an issue with the label `enhancement` to propose new modules or engines.
