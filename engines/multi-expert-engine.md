# Multi-Expert Engine

## Purpose

Coordinates outputs from multiple active expert modules into a coherent, non-redundant response. Prevents module outputs from being dumped as isolated sections.

---

## Algorithm

```
function coordinateExperts(modules, request, context):

  outputs = {}
  for module in modules:
    outputs[module] = module.analyse(request, context)

  # Identify overlaps
  overlapping_topics = findOverlaps(outputs)
  for topic in overlapping_topics:
    merged = mergeConsistentPerspectives(outputs, topic)
    outputs = replaceWithMerged(outputs, topic, merged)

  # Identify conflicts
  conflicts = findConflicts(outputs)
  for conflict in conflicts:
    resolution = consensusEngine.resolve(conflict, context)
    outputs = applyResolution(outputs, conflict, resolution)

  # Integrate
  integrated = integrateOutputs(outputs, request.structure)
  return integrated
```

---

## Module Coordination Rules

1. **One voice, many perspectives.** The final response speaks with one coherent voice, not as a committee. Module perspectives are synthesised, not concatenated.

2. **Lead with the primary module.** The primary domain module sets the structure. Other modules contribute within that structure.

3. **Cross-cutting concerns are additive.** Security, performance, and accessibility modules add to the primary response; they do not replace or restructure it.

4. **Conflicts are resolved, not suppressed.** If two modules disagree, the conflict is surfaced and resolved via the Consensus Engine.

5. **Redundancy is removed.** If two modules say the same thing, it is said once.

---

## Integration Patterns

### Pattern A — Single primary, multiple supplements
Use when one domain is clearly primary and others add context.

```
[Primary module output — full treatment]
[Security note — one section]
[Performance note — one section]
```

### Pattern B — Parallel analysis
Use when two domains are equally important (e.g. architecture + security).

```
[Shared context]
[Architecture perspective]
[Security perspective]
[Integrated recommendation]
```

### Pattern C — Trade-off table
Use when domains produce competing recommendations.

```
[Problem statement]
[Option A: recommended by Module X]
[Option B: recommended by Module Y]
[Trade-off analysis]
[Recommendation with rationale]
```

---

## Failure Modes

| Failure | Cause | Resolution |
|---|---|---|
| Module outputs are incompatible | Fundamental conflict | Consensus Engine — present trade-offs |
| One module dominates inappropriately | Wrong primary selection | Re-evaluate module priority |
| Output is a list of module dumps | Integration skipped | Enforce Pattern A/B/C |
| Circular dependency between modules | Module design issue | Resolve at planning stage |
