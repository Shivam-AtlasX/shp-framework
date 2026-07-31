# Changelog

All notable changes to SHP are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).  
Versioning follows [Semantic Versioning](https://semver.org/).

---

## [3.0.0] — 2025-07-30

### Added
- Complete modular architecture with 20+ expert modules
- 9 specialised reasoning engines (confidence, verification, consensus, threat model, optimisation, multi-expert, consistency, planning, quality)
- 18-stage reasoning pipeline in `core/pipeline.md`
- Memory system with 5 specialised memory files
- Full module specification standard (Purpose, Responsibilities, Activation Criteria, Inputs, Outputs, Dependencies, Examples, Best Practices, Common Mistakes, Limitations)
- `CONTRIBUTING.md` with module development guide
- `templates/` and `examples/` directories
- Complete documentation suite

### Changed
- SKILL.md refactored to concise skill definition; implementation moved to modules
- Reasoning pipeline formalised as explicit 18-stage sequence

### Architecture
- `core/` — reasoning infrastructure
- `modules/` — domain expert modules
- `engines/` — cross-cutting reasoning engines
- `memory/` — session and project state
- `templates/` — reusable response structures
- `examples/` — worked examples per module

---

## [2.0.0] — 2025-01-15

### Added
- Multi-expert coordination system
- Confidence estimation framework
- Threat modelling for security tasks
- Performance analysis integration

### Changed
- Reasoning pipeline expanded from 10 to 15 stages
- Module structure standardised

---

## [1.0.0] — 2024-09-01

### Added
- Initial SHP framework
- Core reasoning and planning modules
- Basic expert modules: coding, architecture, research, writing
- Verification engine (single-pass)

---

## Roadmap

- `v3.1.0` — Add `modules/education.md` and `modules/innovation.md`
- `v3.2.0` — Add `examples/` worked examples for all 20 modules
- `v3.3.0` — Add `templates/` for common response patterns
- `v4.0.0` — Tool-augmented pipeline with external retrieval support
