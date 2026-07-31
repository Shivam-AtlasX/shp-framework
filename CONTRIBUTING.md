# Contributing to SHP

Thank you for contributing to the Smart Human Protocol framework.

This guide covers how to develop new modules, extend existing engines, and maintain quality standards across the project.

---

## Module Development Standard

Every SHP module must follow this specification. Incomplete modules will not be merged.

### Required Sections

```markdown
# Module Name

## Purpose
One to three sentences. What reasoning capability does this module add?

## Responsibilities
Bullet list. What specific tasks does this module perform?

## Activation Criteria
When should the orchestrator activate this module?
List the conditions (keywords, domains, task types).

## Inputs
What context does this module require to operate?
- Request text
- Relevant prior context
- Session memory (if applicable)
- Other module outputs (if applicable)

## Outputs
What does this module produce?
- Analysis, recommendations, code, review, etc.

## Dependencies
Which other modules or engines does this module rely on?

## Examples
At least two worked examples showing the module in action.

## Best Practices
What should this module always do?

## Common Mistakes
What should this module never do?

## Limitations
Where does this module's expertise end?
What should it defer to other modules for?
```

---

## Module Placement

| Type | Directory |
|---|---|
| Reasoning infrastructure | `core/` |
| Domain expert module | `modules/` |
| Cross-cutting reasoning engine | `engines/` |
| Memory tracking | `memory/` |
| Response patterns | `templates/` |
| Worked examples | `examples/` |

---

## Quality Standards

All contributions must meet these standards:

### Accuracy
- No fabricated standards, specifications, or best practices
- Claims must be verifiable against authoritative references
- Cite references where appropriate

### Clarity
- Plain English
- No jargon without definition
- Technical terms used precisely

### Completeness
- All required sections present
- Examples must be realistic and non-trivial
- Limitations must be honest and specific

### Consistency
- Follow existing file naming conventions (kebab-case)
- Follow existing heading structure
- Do not contradict other modules without explicit resolution in the Consensus Engine

---

## Engine Development Standard

Engines differ from modules: they operate across domains rather than within one.

An engine must define:

- **Purpose** — what reasoning function it provides
- **Trigger conditions** — when the pipeline invokes it
- **Algorithm** — step-by-step reasoning process
- **Inputs** — what it consumes
- **Outputs** — what it produces
- **Integration** — how it connects to the pipeline and other engines
- **Failure modes** — what happens when it cannot operate

---

## Naming Conventions

- Files: `kebab-case.md`
- Headings: Title Case for H1 and H2, Sentence case for H3+
- Module names in references: use the file stem (e.g. `modules/coding.md` → `coding`)

---

## Pull Request Process

1. Fork the repository
2. Create a branch: `feature/module-name` or `fix/issue-description`
3. Write the module following the specification above
4. Add a worked example in `examples/`
5. Update `SKILL.md` module index if adding a new module
6. Update `CHANGELOG.md` under `[Unreleased]`
7. Submit pull request with a clear description

---

## Versioning

SHP follows Semantic Versioning:

- **MAJOR** — breaking changes to the pipeline or module interface
- **MINOR** — new modules, engines, or capabilities (backward compatible)
- **PATCH** — corrections, clarifications, documentation improvements

---

## Questions

Open an issue with the label `question` for design discussions.  
Open an issue with the label `bug` for incorrect reasoning or module behaviour.  
Open an issue with the label `enhancement` to propose new modules or engines.
