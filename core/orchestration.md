# Orchestration

## Purpose

The Orchestration layer coordinates the pipeline, expert modules, and engines into a coherent reasoning process for each request.

---

## Orchestration Responsibilities

1. Receive the request and trigger the pipeline
2. Determine complexity level
3. Select active modules based on domain detection
4. Sequence engine invocations
5. Merge module outputs into a unified response
6. Resolve conflicts via the Consensus Engine
7. Maintain session state

---

## Module Activation Logic

```
function selectModules(request, context):
  domains = detectDomains(request)
  modules = []

  for domain in domains:
    module = domainModuleMap[domain]
    if module and isRelevant(module, request):
      modules.append(module)

  # Always activate cross-cutting modules if relevant
  if hasCode(request):       modules += [coding, testing, debugging]
  if hasSecurity(request):   modules += [cybersecurity]
  if hasArchitecture(request): modules += [architecture, system-design]
  if hasUI(request):         modules += [uiux, accessibility]
  if hasWriting(request):    modules += [writing, documentation]

  return deduplicate(modules)
```

---

## Module Priority

When multiple modules are active, outputs are integrated in this order:

1. **Core domain module** — primary expert perspective
2. **Cross-cutting modules** — security, performance, accessibility
3. **Review modules** — testing, documentation
4. **Consensus Engine** — if module outputs conflict

---

## Conflict Resolution Protocol

When two modules produce conflicting recommendations:

1. Identify the specific point of conflict
2. Activate the Consensus Engine
3. The Consensus Engine analyses the trade-offs
4. Presents both options with pros and cons
5. Recommends the most suitable option for the user's context
6. Does not force artificial consensus — legitimate trade-offs are presented as trade-offs

---

## Engine Invocation Sequence

```
Pipeline Stage   │  Engines Invoked
─────────────────┼─────────────────────────────────────────
03 Complexity    │  Planning Engine (complexity assessment)
06 Expert Select │  Multi-Expert Engine
07 Planning      │  Planning Engine
09 Verification  │  Verification Engine
10 Security      │  Threat Model Engine
14 Quality       │  Quality Engine
15 Confidence    │  Confidence Engine
16 Optimisation  │  Optimisation Engine + Consistency Engine
```

---

## Session Management

The orchestrator maintains a lightweight session record:

- Active modules list
- Decisions made this session
- Conventions established this session
- Architecture choices recorded
- Pending items (unresolved decisions, open questions)

This is passed to `memory/` for persistence within the session.
