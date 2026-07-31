# Planning Engine

## Purpose

Structures complex tasks into manageable, sequenced plans before execution. Prevents disorganised reasoning and missed requirements on multi-step requests.

---

## Planning Modes

| Mode | Trigger | Output |
|---|---|---|
| Micro | Complexity Level 0–1 | No plan; proceed directly |
| Outline | Complexity Level 2 | Goal + 3–5 steps |
| Full | Complexity Level 3 | Goal + requirements + steps + risks |
| Milestone | Complexity Level 4 | Full plan + checkpoints + dependencies |

---

## Milestone Planning Format

For large tasks, the engine produces a milestone plan:

```
MILESTONE PLAN
──────────────
Goal:        [What done looks like]
Inputs:      [What is available]
Constraints: [What limits the solution]
Risks:       [What could go wrong]

Milestones:
  M1: [Name] — [Deliverable]
    Steps: [list]
    Checkpoint: [How to verify M1 is complete]
    Dependencies: [What M1 requires]

  M2: [Name] — [Deliverable]
    Steps: [list]
    Checkpoint: [How to verify M2 is complete]
    Dependencies: [M1, plus any other inputs]

  ...

Final deliverable: [Description]
```

---

## Dependency Management

```
function buildDependencyGraph(milestones):
  graph = DirectedGraph()
  for milestone in milestones:
    graph.add(milestone)
    for dependency in milestone.dependencies:
      graph.edge(dependency, milestone)
  return topologicalSort(graph)
```

Execution follows topological order. Circular dependencies are flagged and require redesign.

---

## Re-Planning Triggers

Re-planning is triggered when:
- A requirement changes mid-execution
- A milestone cannot be completed as planned
- New information changes the approach
- A verification checkpoint fails

Re-planning is not a failure — it is correct behaviour in response to new information.
