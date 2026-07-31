# Debugging Module

## Purpose

Diagnoses bugs, errors, and unexpected behaviour systematically using root cause analysis. Identifies the actual cause, not just the symptom, and recommends fixes that prevent recurrence.

---

## Responsibilities

- Identify the root cause of bugs and runtime errors
- Interpret error messages, stack traces, and logs
- Diagnose intermittent and race condition bugs
- Suggest diagnostic steps when the cause is unclear
- Recommend fixes that address the root cause, not the symptom
- Recommend how to prevent the class of bug from recurring

---

## Activation Criteria

Activate when:
- Error messages or stack traces are provided
- "It's not working", "it breaks", "unexpected behaviour" type requests
- Intermittent or environment-specific failures are described
- A fix is applied but the problem persists

---

## Inputs

- Error message or stack trace
- Relevant code
- Description of expected vs actual behaviour
- When it fails (always, intermittently, under specific conditions)
- What changed recently (if known)
- Environment details (language version, OS, dependencies)

---

## Outputs

- Root cause identification with explanation of why it occurs
- Evidence linking cause to symptom
- Specific fix
- Test to verify the fix
- Prevention recommendation

---

## Dependencies

- `modules/coding.md` — for fix implementation quality
- `modules/testing.md` — for regression test recommendations

---

## Debugging Process

### Stage 1 — Reproduce

Confirm the problem is reproducible. A bug you cannot reproduce cannot be reliably fixed.

```
Questions to establish reproducibility:
- Does it happen every time? Under what conditions?
- Does it happen in all environments? (local, staging, production)
- Does it happen for all users? Or specific ones?
- Did it ever work? What changed?
```

### Stage 2 — Isolate

Narrow the problem to the smallest possible failing case. Remove everything that is not relevant to the failure.

```
- Minimal reproduction: what is the smallest input / code path that triggers the bug?
- Binary search: does it fail before or after line N?
- Comment out: does it fail without feature X?
```

### Stage 3 — Hypothesise

Generate candidate root causes based on the evidence.

```
Evidence gathering:
- What does the stack trace tell us? (file, line, exception type)
- What state was the program in when it failed? (variable values, request data)
- What assumptions does the failing code make? Which might be violated?

Candidate hypotheses (ranked by likelihood):
1. [Most likely cause]
2. [Second candidate]
3. [Third candidate]
```

### Stage 4 — Test the Hypothesis

Check each hypothesis against the evidence. Do not fix until the cause is confirmed.

```
For each hypothesis:
- What would we expect to see if this is true?
- What diagnostic can confirm or refute it?
  (add logging, inspect state, trace execution, isolate the variable)
- Does the evidence match?
```

### Stage 5 — Fix

Address the confirmed root cause. Not the symptom.

```
Fix principles:
- Fix the cause, not the manifestation
- One change at a time — isolate the effect of the fix
- Understand why the fix works before applying it
- Consider side effects of the fix
```

### Stage 6 — Verify

Confirm the fix resolves the original issue without introducing new problems.

```
Verification steps:
1. Reproduce the original bug — it should no longer occur
2. Check adjacent behaviour — the fix should not break related paths
3. Check edge cases — especially any that the fix touches
```

### Stage 7 — Prevent

Add a regression test and address the underlying cause of the bug class.

```
Prevention:
- Write a test that would catch this bug if reintroduced
- Ask: why did this bug exist? (missing validation, wrong assumption, race condition)
- Ask: are there similar bugs elsewhere in the codebase?
```

---

## Error Pattern Reference

### Null / Undefined / None reference

**Signals:** `NullPointerException`, `TypeError: Cannot read property X of undefined`, `AttributeError: 'NoneType'`

**Root causes:**
- Variable not initialised before use
- Function returns None/null on failure path; caller does not check
- Concurrent modification leaves object in inconsistent state

**Fix pattern:**
```python
# Before: assumes result is never None
result = fetch_user(user_id)
name = result.name  # crashes if fetch returns None

# After: handle the None case explicitly
result = fetch_user(user_id)
if result is None:
    raise UserNotFoundError(f"User {user_id} not found")
name = result.name
```

---

### Off-by-one

**Signals:** Missing last element, processing element twice, loop runs one too many times, fence-post errors

**Root causes:** Confusion between zero-indexed and one-indexed; `<` vs `<=`; inclusive vs exclusive range boundaries

**Diagnostic:** Trace with a tiny input (n=1, n=2). Draw out the array with indices.

---

### Race condition

**Signals:** Intermittent failure, failure only under load, failure in concurrent tests, data corruption

**Root causes:** Shared mutable state accessed without synchronisation; assumption that operation is atomic when it is not; TOCTOU (time-of-check to time-of-use) violations

**Fix pattern:**
```python
# Before: race condition — another thread may modify balance between check and deduction
if account.balance >= amount:
    account.balance -= amount  # not atomic with the check

# After: use atomic operation or lock
with account.lock:
    if account.balance >= amount:
        account.balance -= amount
```

---

### Resource leak

**Signals:** Slow performance degradation, OOM errors, "too many open file descriptors", database connection exhaustion

**Root causes:** Resource opened but not closed; error path skips cleanup; resource acquired in loop without release

**Fix pattern:**
```python
# Before: file not closed if exception occurs
f = open("data.txt")
data = f.read()  # if this raises, f is never closed
f.close()

# After: context manager guarantees cleanup
with open("data.txt") as f:
    data = f.read()
```

---

### Environment / Configuration mismatch

**Signals:** Works locally, fails in CI or production; works for one developer, not another

**Root causes:** Hardcoded local paths; missing environment variables; different dependency versions; different OS behaviour

**Diagnostic:** Compare the exact environment (language version, dependency versions, env vars, OS) between working and failing environments.

---

### Import / Module not found

**Signals:** `ModuleNotFoundError`, `ImportError`, `Cannot find module`

**Root causes:** Package not installed; wrong virtual environment; circular import; wrong import path

**Diagnostic:**
```bash
# Python: verify the environment
python -m pip list | grep package-name
which python  # confirm you are in the right environment

# Node: verify installation
cat node_modules/package-name/package.json | grep version
```

---

### Type / Encoding errors

**Signals:** `UnicodeDecodeError`, `TypeError`, unexpected values from JSON parsing

**Root causes:** String/bytes confusion; incorrect encoding assumption; API response format different from expected; JSON field type different from assumed

---

## Stack Trace Reading Guide

```
Traceback (most recent call last):           ← read bottom-up
  File "app.py", line 42, in process_order   ← outermost call frame
    result = calculate_total(items)
  File "billing.py", line 17, in calculate_total  ← intermediate frame
    return sum(item.price for item in items)
  File "billing.py", line 17, in <genexpr>   ← innermost call (the actual crash)
    return sum(item.price for item in items)
AttributeError: 'NoneType' object has no attribute 'price'
                                              ← root error: item is None
```

**Reading strategy:**
1. Start at the bottom — this is where execution failed
2. Identify the exception type and message
3. Trace upward to find where invalid data entered the system
4. The root cause is usually where invalid data was created or allowed, not where it crashed

---

## Examples

### Example 1 — Intermittent failure diagnosis

**Symptom:** "Our order processing job fails maybe once a day with a KeyError on 'user_id'. It doesn't always fail."

**Analysis:**
- `KeyError: 'user_id'` means a dict does not contain the key `'user_id'`
- Intermittent suggests the dict format varies — not all inputs have this key
- The fix is to handle the missing key case, but the root cause is: why is `user_id` sometimes absent?

**Diagnostic questions:**
1. What is the source of the dict? (queue message, API response, database record?)
2. Are there any code paths that create the dict without `user_id`?
3. Is there a race condition where the user record is created asynchronously?

**Likely fix:**
```python
# Before
user_id = event['user_id']

# After — with explicit error for investigation
user_id = event.get('user_id')
if user_id is None:
    logger.error("Event missing user_id: %s", event)
    raise ValueError(f"Event missing required field user_id: {event}")
```

This surfaces the root cause rather than silently failing.

---

## Best Practices

- Diagnose before fixing — understanding the cause prevents wrong fixes
- Change one thing at a time when testing hypotheses
- Add logging before adding a fix — you need to see the state
- Fix the root cause, not the symptom (catching and ignoring exceptions is a symptom fix)
- Write a test that catches the bug before fixing it — this proves the test is meaningful

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Fixing without understanding | The symptom disappears; the cause returns in a different form |
| Treating symptoms | `try/except: pass` masks the real problem |
| Not verifying the fix | Believing a fix works without reproducing the failure |
| One-line diagnosis | Complex bugs need systematic investigation |
| Skipping the reproduce step | Fixes that cannot be verified cannot be trusted |

---

## Limitations

- Cannot run code — correctness is by reasoning from provided evidence
- Without environment details, some diagnoses are speculative; confidence is stated accordingly
- Intermittent bugs may require production observability tooling to confirm
- Concurrency bugs are difficult to reason about without seeing all interacting code paths
