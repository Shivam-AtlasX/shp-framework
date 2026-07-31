# UI/UX Module

## Purpose

Evaluates and designs user interfaces and experiences. Applies usability principles, interaction design patterns, and user-centred design thinking. Identifies usability problems and recommends concrete improvements.

---

## Responsibilities

- Review UI designs and flows for usability problems
- Recommend interaction patterns for common UI challenges
- Design information architecture and navigation structures
- Evaluate and improve user flows and journeys
- Write UI microcopy (labels, messages, CTAs)
- Contribute to accessibility review (in coordination with `modules/accessibility.md`)

---

## Activation Criteria

Activate when:
- UI component or screen design is discussed or generated
- User flows or journeys are being designed
- "Is this good UX?", "how should this work?" type questions
- Interface copy or microcopy is needed
- Usability problems are described

---

## Inputs

- User goal and context (who is doing what, and why)
- Product context (B2B / B2C, domain, existing design system)
- Platform (web, mobile, desktop)
- Interface description, wireframe, or design to review
- Constraints (brand, accessibility, existing components)

---

## Outputs

- Usability analysis with severity-rated findings
- Redesign recommendations with rationale
- Interaction pattern suggestions
- Microcopy suggestions
- User flow improvements

---

## Dependencies

- `modules/accessibility.md` — accessibility audit and standards
- `modules/writing.md` — microcopy and interface language

---

## Design Principles

### 1. Design for the task, not the interface

Users come to complete a task. The interface should be invisible in the sense that it creates no friction between intention and completion. Ask: what is the user trying to do? Design the shortest, clearest path to that goal.

### 2. Make the next step obvious

At every point in a flow, there should be one clearly primary action. Users should never wonder "what do I do next?"

### 3. Recognition over recall

Users should not have to remember information from one part of the interface to use another. Show relevant context; do not require users to memorise it.

### 4. Prevent errors; recover gracefully

The best error handling is preventing the error. Design validation, constraints, and affordances that make wrong actions hard. When errors happen, explain them in plain language with actionable recovery steps.

### 5. Consistency creates predictability

If similar elements behave the same way throughout the product, users learn the pattern once and apply it everywhere. Inconsistency forces users to re-learn and creates distrust.

### 6. Feedback closes the loop

Every action should produce visible feedback. Users need to know the system received and processed their input. Silence is ambiguous.

---

## Nielsen's 10 Usability Heuristics

| # | Heuristic | Applied meaning |
|---|---|---|
| 1 | Visibility of system status | Loading states, progress indicators, success/error messages |
| 2 | Match between system and real world | Use user vocabulary; match mental models; avoid jargon |
| 3 | User control and freedom | Undo, cancel, back; don't trap users in flows |
| 4 | Consistency and standards | Same element = same behaviour throughout |
| 5 | Error prevention | Constraints, validation, confirmation for destructive actions |
| 6 | Recognition over recall | Show options; don't make users memorise |
| 7 | Flexibility and efficiency | Shortcuts for experts; simple for novices |
| 8 | Aesthetic and minimalist design | Remove content that competes with the primary message |
| 9 | Help users recognise, diagnose, and recover from errors | Plain language errors; actionable recovery |
| 10 | Help and documentation | Available when needed; task-focused; searchable |

---

## Common Interaction Patterns

### Form design

- Label above field (not placeholder-only — labels disappear on focus)
- Mark required fields consistently (asterisk with legend, or mark optional instead if most are required)
- Inline validation: validate on blur, not on each keystroke
- Error messages adjacent to the field they refer to
- Group related fields visually and logically
- One column layout unless fields are naturally parallel (city / state / zip)
- Primary CTA is the last action in the form flow

```
❌ Placeholder-only labels:
   [Enter your email address        ]

✓  Label above:
   Email address
   [                                ]
```

---

### Empty states

Design empty states as carefully as full ones. They are often the first thing a new user sees.

Components of a good empty state:
1. **Illustration or icon** — sets tone, confirms the right page
2. **Heading** — what this section is for
3. **Explanation** — why it's empty
4. **Primary action** — what to do about it

```
Example: Empty notifications page

[Bell illustration]
No notifications yet

When something happens that needs your attention,
it'll show up here.

[Explore what's new →]
```

---

### Loading states

Never leave users looking at a blank screen.

| Duration | Pattern |
|---|---|
| < 300ms | No indicator needed |
| 300ms – 1s | Spinner |
| 1s – 5s | Skeleton screen (placeholder shapes matching the content layout) |
| > 5s | Progress bar with estimated time; or background processing with notification |

---

### Destructive actions

For actions that cannot easily be undone (delete, cancel subscription, revoke access):

1. **Prefer undo over confirmation** when the action is reversible — confirmation dialogs train users to click through without reading
2. **Use confirmation dialogs** when the action is truly irreversible — but only then
3. **Label the confirmation button with the action**, not "OK" or "Yes"

```
❌ Dialog: "Are you sure?"  [Cancel] [OK]
✓  Dialog: "Delete project? This cannot be undone."  [Keep project] [Delete project]
```

---

### Navigation

| Context | Pattern |
|---|---|
| 5–7 top-level destinations | Top navigation bar |
| > 7 destinations or complex hierarchy | Side navigation |
| Deep hierarchy (3+ levels) | Breadcrumbs + side nav |
| Mobile, primary actions | Bottom tab bar |
| Secondary/contextual navigation | Dropdown, flyout, or drawer |

---

### Notifications and feedback

| Message type | Component | Duration |
|---|---|---|
| Success (action completed) | Toast / snackbar | 3–5 seconds, auto-dismiss |
| Error (action failed) | Inline error or modal | Persistent until dismissed |
| Warning (confirm or caution) | Banner or modal | Persistent |
| Information | Banner or toast | Depends on urgency |

Feedback should be:
- **Specific:** "Invoice #1234 sent to client@example.com" not "Success"
- **Actionable:** if there's a next step, offer it
- **Positioned near the action:** not in a remote corner

---

## Microcopy Standards

Microcopy is the small text in interfaces: button labels, error messages, help text, placeholder text, tooltips.

### Principles

- **Use verbs for buttons:** "Save changes", "Delete account", "Send message" — not "Submit" or "OK"
- **Plain language:** write at a reading level appropriate to the user; avoid technical or legalese
- **Specific errors:** "Password must be at least 8 characters" not "Invalid password"
- **Positive framing:** "Enter a valid email" not "Invalid email address entered"
- **Consistent terminology:** pick one word per concept and use it everywhere

### Button labels

| Context | Good label | Bad label |
|---|---|---|
| Saving a form | "Save changes" | "Submit" |
| Confirming delete | "Delete account" | "Yes" |
| Cancelling delete | "Keep account" | "No" |
| Sending a message | "Send message" | "OK" |
| Creating something | "Create project" | "Continue" |

### Error message format

```
[What went wrong] + [Why] + [How to fix it]

✓ "Your session has expired. Sign in again to continue."
✓ "This email is already registered. Sign in or use a different email."
✗ "Error 401: Unauthorized"
✗ "Something went wrong. Please try again."
```

---

## Usability Severity Ratings

When reviewing a design, rate each finding:

| Severity | Description | Priority |
|---|---|---|
| Critical | Prevents task completion; users cannot proceed | Fix before release |
| High | Major friction; significantly reduces task success | Fix in this sprint |
| Medium | Noticeable friction; reduces efficiency or satisfaction | Fix in next sprint |
| Low | Minor annoyance; does not impact task success | Fix when time allows |
| Informational | Observation or suggestion for enhancement | Backlog |

---

## Examples

### Example 1 — Login form review

**Design under review:**
- Email and password fields with placeholder text only
- "Submit" button
- Error appears as an alert at the top of the page on failed login
- No "Forgot password?" link visible

**Findings:**

| Severity | Finding | Recommendation |
|---|---|---|
| High | Placeholder-only labels disappear on focus, leaving users without field context | Add persistent labels above each field |
| Medium | "Submit" is a generic, unhelpful label | Change to "Sign in" |
| Medium | Error appears at top of page, far from the fields causing it | Move inline error near the relevant field; or make top-of-page error visually prominent and scroll into view |
| Medium | No "Forgot password?" link | Add below the password field |
| Low | No "Show password" toggle | Add password visibility toggle — reduces login friction |

---

### Example 2 — Redesigning an error state

**Before:**
```
Error: 500
```

**After:**
```
Something went wrong on our end

We're having trouble loading your account data.
This usually resolves itself quickly.

[Try again]   Need help? Contact support →
```

---

## Best Practices

- Design the empty and error states before the success state
- Test flows with real users when possible — even 5 users reveal 80% of usability problems
- Reduce cognitive load: fewer decisions, clearer hierarchy, shorter paths
- Never make users confirm an action they can undo — only when truly irreversible
- Design for the most common case; make edge cases accessible but not prominent

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Placeholder-only labels | Users lose context when typing |
| Generic button labels (OK, Submit, Yes) | Users unsure of the consequence |
| Error messages without instructions | Users cannot self-serve recovery |
| Multiple competing primary actions | Users paralysed or take the wrong action |
| No loading/empty states designed | Broken experience for the most common case (new user, slow network) |
| Confirmation for reversible actions | Dialog fatigue; users click through without reading |

---

## Limitations

- Cannot conduct user research or usability testing
- Visual and motion design (colours, typography, animation) require design tool review
- Platform-specific conventions (iOS Human Interface Guidelines, Material Design) vary
- Accessibility is covered in detail in `modules/accessibility.md`
