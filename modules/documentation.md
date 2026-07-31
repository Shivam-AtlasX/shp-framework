# Documentation Module

## Purpose

Generates complete, accurate, and maintainable technical documentation for code, APIs, systems, and processes. Applies documentation standards that keep docs useful long-term, not just at time of writing.

---

## Responsibilities

- Generate README files, API references, and architecture documents
- Write code comments and docstrings
- Create runbooks, onboarding guides, and how-to guides
- Document decisions (ADRs), processes, and conventions
- Review existing documentation for accuracy and gaps
- Structure documentation systems (docs-as-code, wiki architecture)

---

## Activation Criteria

Activate when:
- "Document this", "write a README", "add comments" are requested
- API documentation is needed
- Code is generated and documentation should accompany it
- A runbook, guide, or process document is requested
- Documentation review or improvement is requested

---

## Inputs

- Code, API, system, or process to document
- Target audience (developers, operators, end users, executives)
- Documentation format (README, API ref, ADR, runbook, guide)
- Existing documentation (if reviewing or extending)
- Documentation tooling (Sphinx, Docusaurus, Confluence, plain Markdown)

---

## Outputs

- Complete, structured documentation
- Code comments and docstrings
- README files
- API reference documentation
- Runbooks and operational guides
- Architecture Decision Records (ADRs)

---

## Dependencies

- `modules/writing.md` — prose quality and structure
- `modules/api-design.md` — API documentation standards
- `modules/coding.md` — code comment standards

---

## Documentation Philosophy

### Docs-as-code

Documentation lives alongside the code it describes, in the same repository, versioned in source control, reviewed in pull requests.

Benefits: docs stay in sync with code; changes are tracked; the same review culture applies.

### Document why, not what

Code shows what it does. Documentation explains why.

```python
# ❌ Explains what — the code already says this
# Increment counter
counter += 1

# ✓ Explains why — the code does not say this
# We use a counter rather than a timestamp to handle cases where
# multiple events occur within the same millisecond (observed in
# high-traffic load tests at > 50k events/second).
counter += 1
```

### Keep docs close to what they describe

The further documentation is from the thing it documents, the more likely it is to drift. Inline docstrings stay accurate longer than a separate wiki page.

### Documentation that lies is worse than no documentation

Stale docs mislead. Delete or mark outdated docs rather than leaving them.

---

## README Standard

Every repository and major component must have a README. Use this structure:

```markdown
# [Project Name]

One-sentence description of what this does and who it is for.

## What it does

[2–3 paragraphs. What problem does it solve? What does it produce?
Not how it works internally — that goes in architecture docs.]

## Quick start

[The absolute minimum to get something working. Should take < 5 minutes.]

```bash
# Install
npm install project-name

# Run
npm start
```

## Installation

[Full installation instructions, including prerequisites, platform notes,
and configuration. Assume nothing is pre-installed.]

## Usage

[Common usage examples. Code blocks. Realistic inputs and outputs.]

## Configuration

[All configuration options, their defaults, and their effects.
Table format works well here:]

| Option | Default | Description |
|---|---|---|
| `PORT` | `3000` | HTTP port to listen on |
| `DATABASE_URL` | required | PostgreSQL connection string |

## API reference

[If applicable. Or link to separate API docs.]

## Architecture

[Brief overview of key design decisions. Link to ADRs for detail.
One paragraph + component list is often enough.]

## Contributing

[How to set up a dev environment, run tests, submit PRs.]

## License

[Licence name with link to LICENSE file.]
```

---

## Code Documentation

### Docstrings

Every public function, class, and module should have a docstring. Private functions and internal helpers: document when the purpose is non-obvious.

**Python (Google style):**
```python
def calculate_discount(price: float, rate: float) -> float:
    """Apply a percentage discount to a price.

    Args:
        price: The original price in the currency's base unit (e.g. cents).
        rate: Discount rate as a decimal between 0.0 and 1.0.
              A rate of 0.1 represents a 10% discount.

    Returns:
        Discounted price. Always non-negative.

    Raises:
        ValueError: If rate is outside the range [0.0, 1.0].

    Example:
        >>> calculate_discount(10000, 0.1)
        9000.0
    """
    if not 0.0 <= rate <= 1.0:
        raise ValueError(f"Discount rate must be between 0.0 and 1.0, got {rate}")
    return price * (1 - rate)
```

**TypeScript / JSDoc:**
```typescript
/**
 * Apply a percentage discount to a price.
 *
 * @param price - The original price in the currency's base unit (e.g. cents).
 * @param rate - Discount rate as a decimal between 0.0 and 1.0.
 * @returns Discounted price. Always non-negative.
 * @throws {RangeError} If rate is outside [0.0, 1.0].
 *
 * @example
 * calculateDiscount(10000, 0.1); // → 9000
 */
function calculateDiscount(price: number, rate: number): number {
```

**Go:**
```go
// CalculateDiscount applies a percentage discount to a price.
// The rate must be between 0.0 and 1.0, where 0.1 represents 10%.
// Returns the discounted price, always non-negative.
func CalculateDiscount(price float64, rate float64) (float64, error) {
```

### What to document inline

Document these with comments:
- **Non-obvious business logic:** why this rule exists, what regulation or requirement it satisfies
- **Performance choices:** why a less readable approach was chosen for speed
- **Bug fixes:** what the bug was and why the fix works (link to issue if possible)
- **Workarounds:** what is being worked around and when it can be removed
- **Magic numbers and constants:** what the value represents, where it comes from

```python
# Rate limit: 100 requests per 15 minutes per API key.
# This matches the upstream vendor's own rate limit (documented in their
# API docs v2.3, section 4.2). Exceeding this causes 429 errors that
# are not retried and surface as user-visible errors.
RATE_LIMIT_REQUESTS = 100
RATE_LIMIT_WINDOW_SECONDS = 900
```

---

## API Documentation

Every API endpoint must document:

```markdown
### POST /payments

Create a new payment.

**Authentication:** Bearer token (see [Authentication](#authentication))

**Request**

```json
{
  "amount": 5000,
  "currency": "usd",
  "customer_id": "cus_01J3K...",
  "description": "Invoice #1234"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `amount` | integer | Yes | Amount in smallest currency unit (cents for USD) |
| `currency` | string | Yes | ISO 4217 currency code, lowercase |
| `customer_id` | string | Yes | Customer ID from your account |
| `description` | string | No | Human-readable description, max 500 characters |

**Response: 201 Created**

```json
{
  "id": "pay_01J3K...",
  "amount": 5000,
  "currency": "usd",
  "status": "pending",
  "created_at": "2025-07-30T14:23:01Z"
}
```

**Error responses**

| Status | Code | When |
|---|---|---|
| 400 | `INVALID_AMOUNT` | Amount is zero, negative, or non-integer |
| 400 | `UNSUPPORTED_CURRENCY` | Currency code not supported |
| 404 | `CUSTOMER_NOT_FOUND` | customer_id does not exist |
| 402 | `PAYMENT_DECLINED` | Payment processor declined the charge |
```

---

## Architecture Decision Records

Use ADRs to document significant decisions. Store them in `docs/decisions/` or `adr/`.

```markdown
# ADR-003: Use PostgreSQL for primary data storage

**Status:** Accepted  
**Date:** 2025-07-30  
**Deciders:** [Team]

## Context

We need a primary data store for the application. Our data is relational
(users, orders, items, relationships between them), and we need ACID
transactions for payment processing.

## Decision

We will use PostgreSQL 15 as our primary relational database.

## Rationale

- ACID compliance is required for financial data
- The data model is relational — normalised tables with foreign keys
- The team has deep PostgreSQL operational experience
- PostgreSQL's JSONB support gives flexibility for semi-structured data
  without sacrificing relational capability

## Alternatives Considered

- **MySQL:** Similar capability but weaker JSONB support; no meaningful advantage given our team's PostgreSQL experience.
- **MongoDB:** Document model does not match our relational data well; consistency model requires careful design for financial data.
- **DynamoDB:** High operational simplicity; does not support the query patterns required (complex JOINs, ad hoc analytics).

## Consequences

- Requires a managed PostgreSQL service (RDS, Cloud SQL, or self-managed)
- All services must use PostgreSQL connection libraries
- Schema migrations require careful planning at scale
- Read scaling via read replicas; write scaling via sharding if needed (not anticipated for 2 years)

## Review

Revisit if write throughput exceeds 10k writes/second sustained.
```

---

## Runbook Template

```markdown
# Runbook: [Service or Alert Name]

**Severity:** P1 / P2 / P3  
**Owner:** [Team]  
**Last reviewed:** [Date]  
**On-call contact:** [Slack channel or PagerDuty]

## What is this?

[One paragraph. What service or component does this cover? What does it do?]

## Alert definition

[What condition triggers this runbook. What the alert fires on.]

## Impact

[What users or systems are affected. What they experience.]

## Immediate triage (first 5 minutes)

1. [First thing to check]
2. [Second thing to check]
3. [Escalate if not resolved in [N] minutes]

## Diagnostic steps

```bash
# Check pod health
kubectl get pods -n [namespace] -l app=[service]

# Check recent logs (last 5 minutes)
kubectl logs -n [namespace] deploy/[service] --since=5m | tail -100

# Check error rate
# [Link to dashboard]

# Check database connectivity
kubectl exec -n [namespace] deploy/[service] -- nc -zv [db-host] 5432
```

## Common causes and resolutions

| Symptom | Likely cause | Resolution |
|---|---|---|
| All pods in CrashLoopBackOff | Bad deployment | Roll back: `kubectl rollout undo deploy/[service] -n [namespace]` |
| Database connection errors | DB overloaded or unreachable | Check DB health; increase connection pool if needed |
| High latency, no errors | Slow dependency | Check upstream service; enable circuit breaker |

## Escalation

- Not resolved in 30 minutes → escalate to [team lead / secondary on-call]
- Data integrity concern → escalate to [data lead] immediately
- Security concern → escalate to [security team] immediately

## Post-incident

File a post-mortem for P1/P2 incidents within 48 hours.
Template: [link]
```

---

## Documentation Quality Checklist

Before finalising any documentation:

- [ ] Is the target audience clear? Is the content written for them?
- [ ] Does it answer: what is it, what does it do, how do I use it?
- [ ] Are all code examples correct and runnable?
- [ ] Are all commands copy-pasteable (no placeholders that don't obviously show where to substitute)?
- [ ] Is the information current? Are version numbers accurate?
- [ ] Are there any links that may break (deep links to specific lines of code)?
- [ ] Is the document scannable (headings, lists, tables) rather than walls of prose?
- [ ] Is there anything that will become outdated quickly? Add a "last reviewed" date.

---

## Examples

### Example 1 — Function docstring from code

**Request:** "Add documentation to this Python function."

```python
def retry(func, max_attempts=3, delay=1.0, backoff=2.0, exceptions=(Exception,)):
    """Retry a function call with exponential backoff on failure.

    Calls `func` up to `max_attempts` times. On each failure, waits
    `delay * backoff^attempt` seconds before retrying. Returns the
    result of the first successful call.

    Args:
        func: Callable to retry. Must be a zero-argument callable.
              Use functools.partial to bind arguments.
        max_attempts: Maximum number of attempts (including the first).
                      Must be >= 1. Default: 3.
        delay: Initial delay in seconds between retries. Default: 1.0.
        backoff: Multiplier applied to delay after each failure. Default: 2.0.
        exceptions: Tuple of exception types to catch and retry on.
                    Re-raises any exception not in this tuple immediately.
                    Default: (Exception,) — retries on all exceptions.

    Returns:
        The return value of the first successful call to `func`.

    Raises:
        The last exception raised by `func` if all attempts fail.

    Example:
        >>> import functools, requests
        >>> fetch = functools.partial(requests.get, "https://api.example.com/data")
        >>> response = retry(fetch, max_attempts=5, delay=0.5)
    """
```

---

## Best Practices

- Write documentation at the time of writing code — retrofitting is slower and less accurate
- Test that all examples in documentation actually work
- Link to related documentation rather than duplicating content
- Use a consistent voice throughout (second person "you" for guides; first person "we" for ADRs)
- Date documents that may become stale (runbooks, architecture docs, decision records)

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Documenting how, not why | Low-value docs that duplicate what the code already says |
| Examples that don't work | Distrust; users spend time debugging docs instead of building |
| Missing prerequisites | Users blocked at setup before they start |
| No "last updated" date | Readers can't tell if content is current |
| Wiki sprawl with no structure | Documentation that can't be found is the same as no documentation |
| Documenting every parameter for private functions | Over-engineering; focus on public API |

---

## Limitations

- Cannot verify that code examples in documentation actually run
- Cannot access existing codebases to auto-generate documentation from source
- Domain-specific conventions (OpenAPI extensions, company wiki templates) may differ from defaults
