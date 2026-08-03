# Orchestration

## Purpose

The Orchestration layer coordinates the pipeline, expert modules, and engines into a coherent reasoning process for each request.

---

## Orchestration Responsibilities

1. Receive the request and trigger the pipeline
2. Determine complexity level and risk profile
3. Select the execution strategy
4. Select active modules based on domain detection
5. Sequence engine invocations
6. Merge module outputs into a unified response
7. Resolve conflicts via the Consensus Engine
8. Enforce consistency against session memory
9. Maintain session state

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
  if hasCode(request):         modules += [coding, testing, debugging]
  if hasSecurity(request):     modules += [cybersecurity]
  if hasArchitecture(request): modules += [architecture, system-design]
  if hasUI(request):           modules += [uiux, accessibility]
  if hasWriting(request):      modules += [writing, documentation]
  if hasResearch(request):     modules += [research]
  if hasML(request):           modules += [machine-learning, ai]

  # Always active
  modules += [token-intelligence]

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
Pipeline Stage              │  Engines Invoked
────────────────────────────┼──────────────────────────────────────────────
02 Context Analysis         │  Consistency Engine (reads session memory)
05 Complexity Estimation    │  Planning Engine (complexity assessment)
07 Knowledge Boundary       │  Knowledge Boundary Engine
08 Expert Selection         │  Multi-Expert Engine
09 Strategy Selection       │  Strategy Engine
09 Tool Requirement         │  Tool Strategy Engine
10 Planning                 │  Planning Engine
12 Alternative Generation   │  Reflection Engine
13 Expert Review            │  Multi-Expert Engine
14 Consensus Resolution     │  Consensus Engine
15 Verification             │  Verification Engine
16 Security Review          │  Threat Model Engine
20 Quality Gate             │  Quality Engine
19 Consistency Review       │  Consistency Engine
21 Confidence Assessment    │  Confidence Engine + Evidence Engine
22 Final Response           │  Optimisation Engine + Token Intelligence
```

---

## Session Management

The orchestrator maintains a lightweight session record:

- Active modules list
- Execution strategy selected
- Decisions made this session
- Conventions established this session
- Architecture choices recorded
- Knowledge boundaries identified
- Pending items (unresolved decisions, open questions)

This is passed to `memory/` for persistence within the session.

---

## Adaptive Depth Control

The orchestrator does not apply the same depth to every request.

| Request Type | Pipeline Depth |
|---|---|
| Trivial (factual lookup) | Stage 01, 05, 22 only |
| Simple (single task, clear intent) | Stages 01–13, 15, 19–22 |
| Moderate (multi-step, important decision) | Full pipeline, selective specialist reviews |
| Complex (multi-domain, high stakes) | Full pipeline, all applicable reviews |
| Expert (system-level, security-critical) | Full pipeline + extended review + memory |

The orchestrator recalibrates mid-execution if discovered complexity differs from the initial estimate.
