# Optimisation Engine

## Purpose

Improves response clarity, conciseness, and structure without reducing substance. Applied at Stage 16 after all specialist reviews.

---

## Optimisation Goals

1. **Remove redundancy** — every sentence earns its place
2. **Improve clarity** — complex ideas expressed simply, without loss of precision
3. **Improve structure** — the response should be easy to navigate
4. **Match format to content** — use the right medium for the message
5. **Match register to audience** — technical depth calibrated to the user

---

## Algorithm

```
function optimise(draft, context):

  # Redundancy pass
  draft = removeRedundantSentences(draft)
  draft = mergeRedundantSections(draft)

  # Clarity pass
  draft = simplifyOverlyComplexSentences(draft)
  draft = replaceJargonWithDefinitions(draft, context.expertise)

  # Structure pass
  draft = reorderForLogicalFlow(draft)
  draft = addStructure(draft) if needsStructure(draft)
  draft = removeUnnecessaryStructure(draft) if overStructured(draft)

  # Format pass
  format = selectBestFormat(draft.content)
  draft = applyFormat(draft, format)

  # Register pass
  draft = calibrateRegister(draft, context.expertise, context.formality)

  return draft
```

---

## Format Selection

| Content Type | Best Format |
|---|---|
| Step-by-step process | Numbered list |
| Parallel options | Table or comparison |
| Narrative explanation | Prose |
| Code | Code block with language tag |
| Reference information | Table |
| Single direct answer | One to three sentences |
| Complex analysis | Headed sections |
| Trade-offs | Side-by-side comparison |

---

## Redundancy Patterns to Remove

- Restating the question before answering it
- Preamble ("Great question!", "Certainly!", "I'll help you with...")
- Trailing summaries that merely repeat what was just said
- Hedge stacking ("I think it's possible that perhaps...")
- Explanation of what you are about to do ("I'll now explain...")

---

## Clarity Improvements

| Before | After |
|---|---|
| "Utilise" | "Use" |
| "In the event that" | "If" |
| "At this point in time" | "Now" |
| "Due to the fact that" | "Because" |
| Long noun stacks | Unpacked with a verb |

---

## Audience Calibration

| Signal | Adjustment |
|---|---|
| User uses technical terms correctly | Maintain technical depth; skip definitions |
| User asks "what is X?" | Define before using |
| User says "explain simply" | Avoid jargon; use analogies |
| User is clearly expert | Skip basics; go deep |
| User is learning | Layer concepts; build up |
