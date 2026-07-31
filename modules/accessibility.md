# Accessibility Module

## Purpose

Ensures digital products are usable by people with disabilities. Applies WCAG 2.2 standards, ARIA best practices, and inclusive design principles to code and interface reviews.

---

## Responsibilities

- Review interfaces and code for accessibility compliance
- Recommend accessible design patterns and alternatives
- Review HTML and ARIA usage for semantic correctness
- Evaluate colour contrast ratios
- Audit keyboard navigation and focus management
- Advise on screen reader compatibility

---

## Activation Criteria

Activate when:
- UI components or HTML is generated (always check)
- Web or mobile interface design is discussed
- "Accessible", "a11y", "WCAG", or "screen reader" is mentioned
- Compliance requirements (ADA, AODA, EAA) are discussed

---

## Inputs

- Interface description, code, or design
- Target WCAG level (A, AA, AAA)
- User population context (if specified)
- Platform (web, native mobile, desktop)

---

## Outputs

- Accessibility findings with WCAG criterion references
- Specific code fixes
- Compliance assessment per level
- Priority-ordered remediation recommendations

---

## Dependencies

- `modules/uiux.md` — usability and design patterns
- `modules/coding.md` — accessible code implementation

---

## WCAG Levels

| Level | Requirement | Typical context |
|---|---|---|
| A | Minimum — blocks access if not met | All public digital products |
| AA | Standard — legal requirement in most jurisdictions (ADA, AODA, EAA) | Required for most organisations |
| AAA | Enhanced — not required as a blanket standard | High-priority accessibility contexts |

Target **WCAG 2.2 AA** unless there is a specific reason to target AAA.

---

## WCAG 2.2 Principles (POUR)

### Perceivable — Information must be presentable in ways users can perceive

**1.1 Text Alternatives**
- Every non-text element must have a text alternative
- Images: `alt` attribute (descriptive for meaningful; empty `alt=""` for decorative)
- Icons: either visible text label, `aria-label`, or `aria-labelledby`
- Complex images (charts, diagrams): `alt` + longer `aria-describedby` description

```html
<!-- Meaningful image -->
<img src="chart.png" alt="Bar chart showing 40% increase in Q3 revenue">

<!-- Decorative image — screen readers skip it -->
<img src="divider.png" alt="">

<!-- Icon button -->
<button aria-label="Close dialog">
  <svg aria-hidden="true">...</svg>
</button>
```

**1.3 Adaptable — Structure survives when styling is removed**
- Heading hierarchy: `h1` → `h2` → `h3`; do not skip levels
- Lists marked with `<ul>`, `<ol>`, `<dl>` not just visually indented `<div>`s
- Tables use `<th>` with `scope` for headers; `<caption>` for table title
- Reading order makes sense without CSS

**1.4 Distinguishable**
- Colour contrast (AA): 4.5:1 for normal text; 3:1 for large text (18pt or 14pt bold)
- Colour contrast (AA): 3:1 for UI components and graphical objects
- Text can be resized to 200% without loss of content or functionality
- No text embedded in images (except logos)

---

### Operable — Interface components must be operable

**2.1 Keyboard Accessible**
- Every interactive element reachable by keyboard (Tab / Shift+Tab)
- No keyboard trap: user can navigate away from any component
- No keyboard-only functionality that can only be achieved with a mouse

**2.4 Navigable**
- Skip navigation link: first focusable element should skip to main content
- Unique, descriptive page title (`<title>`)
- Focus order follows logical reading order
- Focus visible: every focusable element has a visible focus indicator (do not suppress `outline: none`)
- Link purpose clear from link text alone (not "click here" or "read more")
- Headings and labels describe topic or purpose

**2.5 Input Modalities (WCAG 2.1+)**
- Touch targets at least 24×24px (AA in 2.2); 44×44px recommended
- Pointer gestures have single-pointer alternatives (no swipe-only interactions)

---

### Understandable — Information and UI must be understandable

**3.1 Readable**
- Language of page declared: `<html lang="en">`
- Language of parts declared when different from page language: `lang="fr"` on French passages

**3.2 Predictable**
- No unexpected context change on focus or input (no auto-submit, no page redirect on checkbox click)
- Navigation and UI components consistent across pages

**3.3 Input Assistance**
- Error identification: describe the error in text, not colour alone
- Error messages identify the field and describe how to fix it
- Labels or instructions provided for form inputs
- Error suggestions: if the error is known (e.g. wrong format), suggest correction

---

### Robust — Content must be interpreted reliably by assistive technologies

**4.1 Compatible**
- Valid HTML: elements properly nested and closed; attributes valid
- Name, role, value: all UI components have accessible name, role, and state

---

## ARIA Guidelines

**Rule 1: Use semantic HTML first.**

Native HTML elements have built-in accessibility. Use ARIA only when native elements cannot achieve the required pattern.

```html
<!-- ❌ ARIA reinventing a button -->
<div role="button" tabindex="0" onclick="...">Click me</div>

<!-- ✓ Native button — keyboard, focus, role, all built in -->
<button onclick="...">Click me</button>
```

**Rule 2: Do not change native semantics unless necessary.**
```html
<!-- ❌ Confusing — an h2 acting as a tab -->
<h2 role="tab">Tab label</h2>

<!-- ✓ Separate heading and tab -->
<div role="tab">Tab label</div>
```

**Rule 3: All interactive ARIA roles must be keyboard operable.**

**Rule 4: Do not use `role="presentation"` or `aria-hidden="true"` on focusable elements.**

**Rule 5: All interactive elements must have an accessible name.**

---

## Common Accessibility Issues Checklist

| Issue | WCAG | How to fix |
|---|---|---|
| Image missing alt text | 1.1.1 (A) | Add `alt=""` for decorative; descriptive `alt` for meaningful |
| Insufficient colour contrast | 1.4.3 (AA) | Minimum 4.5:1 for normal text; use a contrast checker |
| Interactive element not keyboard accessible | 2.1.1 (A) | Use native elements (`<button>`, `<a>`); or add `tabindex="0"` + keyboard handler |
| No visible focus indicator | 2.4.7 (AA) | Never suppress `outline`; style it instead |
| Form field has no label | 1.3.1 (A) | Associate `<label for="id">` or use `aria-label` |
| Non-descriptive link text | 2.4.4 (A) | "Download Q3 report (PDF)" not "click here" |
| Missing page language | 3.1.1 (A) | Add `lang="en"` (or correct language) to `<html>` |
| No skip navigation | 2.4.1 (A) | Add `<a href="#main" class="skip-link">Skip to main content</a>` |
| Error identified by colour only | 1.4.1 (A) | Add text error message; do not rely on red border alone |
| Small touch target | 2.5.8 (AA, WCAG 2.2) | Minimum 24×24px; recommend 44×44px |
| Auto-playing media | 1.4.2 (A) | Provide pause/stop control; no auto-play audio > 3 seconds |
| Missing `<title>` | 2.4.2 (A) | Every page needs a unique, descriptive `<title>` |
| Heading hierarchy skipped | 1.3.1 (A) | Do not go from `h1` to `h3` without an `h2` |
| `aria-hidden` on focusable element | 4.1.2 (A) | Remove `aria-hidden` or remove from tab order |

---

## Colour Contrast

### Requirements

| Text type | AA requirement | AAA requirement |
|---|---|---|
| Normal text (< 18pt / 14pt bold) | 4.5:1 | 7:1 |
| Large text (≥ 18pt / ≥ 14pt bold) | 3:1 | 4.5:1 |
| UI components and graphics | 3:1 | — |

### Checking contrast

Contrast ratio = (L1 + 0.05) / (L2 + 0.05)  
where L1 is the lighter colour's relative luminance and L2 is the darker one's.

Tools: WebAIM Contrast Checker, browser DevTools accessibility panel, Figma plugins (A11y Annotation Kit).

### Common contrast failures

| Foreground | Background | Ratio | Status |
|---|---|---|---|
| #767676 | #FFFFFF | 4.48:1 | ❌ Fails AA for normal text |
| #595959 | #FFFFFF | 7.0:1 | ✓ Passes AAA |
| #0070F3 | #FFFFFF | 4.53:1 | ✓ Passes AA |
| #767676 | #EEEEEE | 3.35:1 | ❌ Fails AA for normal text |

---

## Keyboard Navigation Patterns

### Focus management for dynamic content

When content changes dynamically (modal opens, page section updates), move focus to the new content:

```javascript
// Modal opens → move focus to modal
function openModal(modalEl) {
  modalEl.removeAttribute('hidden');
  const firstFocusable = modalEl.querySelector(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  firstFocusable?.focus();
}

// Modal closes → return focus to trigger
function closeModal(modalEl, triggerEl) {
  modalEl.setAttribute('hidden', '');
  triggerEl.focus();
}
```

### Focus trap in modals

When a modal is open, Tab should cycle through focusable elements inside the modal only:

```javascript
function trapFocus(modalEl) {
  const focusable = modalEl.querySelectorAll(
    'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
  );
  const first = focusable[0];
  const last = focusable[focusable.length - 1];

  modalEl.addEventListener('keydown', (e) => {
    if (e.key !== 'Tab') return;
    if (e.shiftKey) {
      if (document.activeElement === first) {
        last.focus();
        e.preventDefault();
      }
    } else {
      if (document.activeElement === last) {
        first.focus();
        e.preventDefault();
      }
    }
  });
}
```

---

## Skip Navigation

The skip navigation link must be the first focusable element on the page. It can be visually hidden until focused.

```html
<!-- In HTML, before all other content -->
<a href="#main-content" class="skip-link">Skip to main content</a>

<!-- CSS: visible on focus, hidden otherwise -->
<style>
.skip-link {
  position: absolute;
  transform: translateY(-100%);
  transition: transform 0.3s;
}
.skip-link:focus {
  transform: translateY(0);
}
</style>

<!-- Target -->
<main id="main-content">...</main>
```

---

## Examples

### Example 1 — Accessible form component (React)

```jsx
function EmailField({ error, onChange }) {
  const inputId = "email-field";
  const errorId = "email-error";

  return (
    <div>
      <label htmlFor={inputId}>
        Email address
        <span aria-hidden="true"> *</span>
      </label>
      <input
        id={inputId}
        type="email"
        aria-required="true"
        aria-invalid={!!error}
        aria-describedby={error ? errorId : undefined}
        onChange={onChange}
        autoComplete="email"
      />
      {error && (
        <p id={errorId} role="alert">
          {error}
        </p>
      )}
    </div>
  );
}
```

This example:
- Associates label with input via `htmlFor` / `id`
- Marks field as required with `aria-required`
- Marks field as invalid with `aria-invalid` when there is an error
- Associates error message with input via `aria-describedby`
- Announces error immediately via `role="alert"`

---

## Best Practices

- Test with keyboard navigation on every component you build — do not wait for an audit
- Test with a screen reader (NVDA on Windows, VoiceOver on macOS/iOS, TalkBack on Android)
- Never rely on colour alone to convey meaning
- Design for keyboard first; mouse experience follows naturally
- Accessibility is cheaper when built in; expensive when retrofitted

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| `outline: none` globally | Users who navigate by keyboard cannot see where they are |
| Icon-only buttons without accessible name | Screen reader announces "button" with no context |
| Placeholder text as label | Disappears on focus; fails contrast requirements |
| Div/span onClick instead of `<button>` | Not keyboard accessible; no role announced |
| Alt text = filename | Unhelpful; conveys no content |
| `aria-label` on non-interactive elements | Confuses screen readers |
| Testing only with automated tools | Automated tools catch ~30–40% of accessibility issues |

---

## Limitations

- Cannot test with actual assistive technologies — analysis is by code inspection and standards knowledge
- Real screen reader behaviour may differ from the expected specification in edge cases
- Automated accessibility checkers are a starting point; they cannot replace manual testing
- Cognitive accessibility (plain language, clear structure) is partially addressed but requires user research
