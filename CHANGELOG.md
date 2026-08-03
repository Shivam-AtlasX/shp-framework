# Changelog

All notable changes to SHP are documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).  
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [4.0.0] — 2025

### Summary

SHP v4 is a major architectural upgrade. The framework evolves from a structured prompt library into a full autonomous reasoning architecture — one that dynamically selects strategies, coordinates expertise, validates conclusions, manages uncertainty, and maintains project consistency across complex sessions.

The pipeline expands from 18 to 22 stages. Five new reasoning engines are introduced. A new core philosophy governs the sequencing of all reasoning. The Token Intelligence module establishes a formal standard for response efficiency.

---

### Added

#### Core Philosophy
- Explicit reasoning philosophy: *understand first, plan second, reason third, verify fourth, improve fifth, respond last*
- Formal commitment to deliberate reasoning over immediate generation

#### Pipeline — 4 New Stages
- **Stage 03 — Goal Identification**: Distinguishes the immediate request from the underlying goal; ensures responses solve the real problem, not only the stated one
- **Stage 04 — Constraint Extraction**: Produces an explicit, classified constraint list (hard and soft) before planning
- **Stage 06 — Risk Assessment**: Evaluates correctness, security, architectural, operational, and epistemic risk before selecting modules or strategy
- **Stage 09 — Execution Strategy Selection**: Chooses the most appropriate reasoning strategy (Analytical, Creative, Engineering, Research, Educational, Business, Security, Mathematical, Architectural) before execution begins
- **Stage 12 — Alternative Solution Generation**: Challenges every non-trivial initial solution by generating and comparing an alternative; replaces or presents trade-offs where warranted

#### New Engines (5)
- **Knowledge Boundary Engine** (`engines/knowledge-boundary-engine.md`): Maps all claims into Known / Reasoned / Assumed / Uncertain / Unknown categories at Stage 07; feeds into confidence assessment and drives explicit uncertainty communication
- **Reflection Engine** (`engines/reflection-engine.md`): Challenges every initial solution at Stage 12 against four probes — simpler, safer, faster, more maintainable — and improves or replaces the draft accordingly
- **Evidence Engine** (`engines/evidence-engine.md`): Applied at Stage 21; structures factual claims into Evidence / Interpretation / Recommendation / Assumption / Uncertainty / Unknown categories; prevents mixing of evidence and speculation
- **Strategy Engine** (`engines/strategy-engine.md`): Selects the appropriate reasoning strategy at Stage 09; nine strategy types with formal activation criteria
- **Tool Strategy Engine** (`engines/tool-strategy-engine.md`): Determines at Stage 09 when tool use (web search, code execution, file analysis) would materially improve response quality; prevents both under-use and over-use of tools

#### New Module
- **Token Intelligence** (`modules/token-intelligence.md`): Governs response length, format, and delivery priority; defines Code-First Principle, Zero-Fluff Policy, Adaptive Response Length, Smart Explanation Policy, and Output Priority ranking; always active

#### Plugin Architecture
- Formal plugin standard in `SKILL.md`: every module must define Purpose, Activation Criteria, Inputs, Outputs, Responsibilities, Dependencies, Examples, and Limitations
- Modules integrate without modifying the core framework

---

### Changed

#### `core/pipeline.md`
- Expanded from 18 to 22 stages
- Added formal stage specifications with inputs, outputs, and engine invocations
- Added Trivial fast-path (Stage 01 → 05 → 22) for simple factual requests
- Consensus Resolution promoted to an explicit pipeline stage (Stage 14)
- Added pre-delivery checklist at Stage 22

#### `core/reasoning.md`
- Added Core Philosophy section with the six-step reasoning sequence
- Added Principles 9 (Knowledge Boundary Identification) and 10 (Reflect Before Finalising)
- Added Systems reasoning mode
- Added Knowledge Boundary Violation to Anti-Patterns table
- Added "Alternatives considered" to the Reasoning Trace Format

#### `core/orchestration.md`
- Updated engine invocation sequence for all 22 pipeline stages
- Added Tool Strategy Engine and Strategy Engine to invocation table
- Token Intelligence module added to always-active module set
- Added Adaptive Depth Control table
- Formalised mid-execution recalibration behaviour

#### `SKILL.md`
- Version updated to 4.0.0
- Architecture diagram updated with new engines and module
- Pipeline summary updated with all 22 stages
- Module Index updated with Token Intelligence
- Engine Index updated with 5 new engines
- Added Plugin Architecture section
- Added What SHP Changes comparison table

#### `CONTRIBUTING.md`
- Added Plugin Architecture standard requirements
- Added Engine Development Standard for v4 engines
- Updated versioning notes for v4 major release

#### `references/developer-guide.md`
- Updated architecture diagram to reflect 14 engines and 23 modules
- Added sections for new engines and Token Intelligence module
- Updated "Reference Architecture" file list

---

### Reasoning Depth — Before and After

| Capability | v3 | v4 |
|---|---|---|
| Pipeline stages | 18 | 22 |
| Reasoning engines | 9 | 14 |
| Expert modules | 22 | 23 |
| Goal identification | Implicit | Explicit (Stage 03) |
| Constraint extraction | Implicit | Explicit (Stage 04) |
| Risk assessment | None | Stage 06 |
| Knowledge boundary mapping | None | Stage 07 (Knowledge Boundary Engine) |
| Strategy selection | None | Stage 09 (Strategy Engine) |
| Alternative generation | Optional | Stage 12 (Reflection Engine) |
| Tool use strategy | None | Stage 09 (Tool Strategy Engine) |
| Evidence structuring | None | Stage 21 (Evidence Engine) |
| Response efficiency | Implicit | Always-active Token Intelligence module |

---

## [3.2.0] — 2025

### Added
- `modules/innovation.md` — Creative problem-solving, ideation, and first-principles thinking
- `modules/education.md` — Learning scaffolding, concept explanation, depth calibration

### Changed
- `SKILL.md` — Module index updated

---

## [3.1.0] — 2025

### Added
- `engines/planning-engine.md` — Milestone planning, dependency graphs, re-planning protocol
- `templates/api-endpoint.md` — API endpoint design template
- `examples/05-api-design.md` — Full worked example: REST API design

### Changed
- `core/planning.md` — Added checkpoint verification for Level 4 tasks
- `core/orchestration.md` — Planning Engine added to engine invocation sequence

---

## [3.0.0] — 2025

### Summary
Initial public release of SHP v3. Full modular architecture with 18-stage pipeline, 9 reasoning engines, 20 expert modules, memory system, and quality gates.

### Added
- 18-stage autonomous reasoning pipeline
- 9 cross-cutting reasoning engines
- 20 domain expert modules
- Session memory system (5 memory categories)
- Quality gate framework (10 gates)
- Confidence assessment and communication standard
- STRIDE-based threat modelling engine
- Full contributing guide and developer documentation
- 5 worked examples

---

## [2.0.0]

Internal release. Multi-expert coordination introduced.

## [1.0.0]

Internal release. Initial prompt framework.
