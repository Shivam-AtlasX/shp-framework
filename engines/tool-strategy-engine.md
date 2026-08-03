# Tool Strategy Engine

## Purpose

Determines when external tool use — web search, code execution, file analysis, or other capabilities — would materially improve the quality of a response. Prevents both under-use (answering from stale or uncertain knowledge when a tool would give a reliable answer) and over-use (invoking tools that add latency without adding value).

---

## Activation Criteria

Invoked at Stage 09 of the pipeline alongside the Strategy Engine.

Evaluated for every request. The default is to reason without tools unless one of the activation conditions below applies.

---

## Tool Categories

| Tool Type | What It Provides | When to Use |
|---|---|---|
| Web search | Current information, recent events, live documentation | Knowledge is time-sensitive or version-specific |
| Code execution | Verified computational output | Correctness depends on runtime behaviour |
| File analysis | Content from uploaded documents or data | User has provided a file for processing |
| External API | Real-time data (weather, prices, repositories) | Response requires live external data |

---

## Decision Algorithm

```
function determineToolStrategy(request, context, boundary_map, knowledge_cutoff):

  tools_needed = []

  # --- Web Search ---
  if any of the following:
    - request involves events, versions, or documentation after knowledge_cutoff
    - Knowledge Boundary Engine flagged key claims as UNCERTAIN due to recency
    - request explicitly references "current", "latest", "today", "now"
    - request involves a rapidly-changing domain (frameworks, cloud services, AI models)
    - confidence in a key claim is Low due to version or time sensitivity
  then:
    tools_needed.append(WEB_SEARCH)

  # --- Code Execution ---
  if any of the following:
    - response involves a computation that cannot be verified by reasoning alone
    - request explicitly asks to run or test code
    - numerical result is precision-sensitive and the formula is non-trivial
    - data transformation result depends on input data that has been provided
  then:
    tools_needed.append(CODE_EXECUTION)

  # --- File Analysis ---
  if user has uploaded a file relevant to the request:
    tools_needed.append(FILE_ANALYSIS)

  # --- Suppression checks ---
  for tool in tools_needed:
    if tool.result_already_in_context:
      tools_needed.remove(tool)
    if tool.adds_no_material_value:
      tools_needed.remove(tool)

  return ToolStrategy(tools=tools_needed, rationale=reason_for_each)
```

---

## When NOT to Use Tools

Tools must not be invoked when:

- The answer is well within current reliable knowledge (timeless standards, established algorithms, language semantics for stable versions)
- The tool would be invoked for appearance rather than substance
- The user has already provided the information the tool would retrieve
- Tool latency would materially degrade the user's experience for a simple, fast request
- Reasoning alone is sufficient to produce a high-confidence answer

---

## Sufficiency Test

Before invoking a tool, ask:

> Would the absence of this tool result in a materially worse or less reliable answer?

If yes → invoke the tool.
If no → reason directly.

---

## File Analysis Strategy

When the user uploads a file:

1. Identify the file type
2. Determine which parts of the file are relevant to the request
3. Extract only what is needed — do not process or summarise the entire file unless asked
4. Treat extracted content as Evidence-class knowledge (high confidence, user-provided)

---

## Web Search Strategy

When web search is needed:

1. Formulate a precise query targeting the specific gap in knowledge
2. Evaluate results for source quality (official docs, RFC, authoritative references preferred)
3. Extract only the relevant claim — do not paraphrase the entire source
4. Label the claim with its source and apply the Evidence Engine categories
5. Acknowledge if search results are unavailable or inconclusive

---

## Code Execution Strategy

When code execution is needed:

1. Write the code to be executed for correctness first
2. Identify what the execution verifies (output value, data shape, error handling)
3. Execute and incorporate result
4. If execution fails, diagnose before retry — do not blindly rerun
5. Clearly distinguish between executed-and-verified output and reasoned output

---

## Inputs

- Request intent and knowledge boundary map (from Stages 01–07)
- Complexity and risk profile (from Stages 05–06)
- Uploaded file list (if any)
- Session context (what has already been retrieved or computed)

---

## Outputs

- Tool selection list (empty if no tools needed)
- Rationale for each selected tool
- Query or extraction plan for each selected tool

---

## Integration

- Invoked at Stage 09 alongside the Strategy Engine
- Selected tools are invoked at their relevant pipeline stages (before the stage that requires their output)
- Tool results feed into the draft generation at Stage 11

---

## Failure Modes

| Failure | Prevention |
|---|---|
| Tool invoked unnecessarily | Apply Sufficiency Test before every invocation |
| Tool result accepted uncritically | Apply Evidence Engine to classify tool-retrieved claims |
| Tool invoked but result not used | If the result changes nothing, remove the invocation |
| Failing to invoke when needed | Epistemic risk rating from Stage 06 should trigger review |

---

## Limitations

- Tool availability depends on the deployment context; SHP cannot guarantee any tool is available
- Web search results may be incomplete or inaccurate; treat them as Evidence-class input, not ground truth
- Code execution is not available in all environments; fall back to reasoned verification
