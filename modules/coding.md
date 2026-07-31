# Coding Module

## Purpose

Generates, reviews, and improves production-quality code across languages and paradigms. Applies language-specific best practices and engineering standards.

---

## Responsibilities

- Generate correct, readable, maintainable code
- Review code for correctness, security, and quality
- Explain code clearly and accurately
- Refactor code to improve structure without changing behaviour
- Debug code and identify root causes
- Apply idiomatic patterns for the target language

---

## Activation Criteria

Activate when the request involves:
- Writing new code
- Reviewing existing code
- Debugging a program
- Refactoring or improving code structure
- Explaining what code does
- Completing partial code

---

## Inputs

- Request (write, review, debug, refactor, explain)
- Target language (inferred or stated)
- Existing code (if any)
- Framework and library context
- Performance, security, or style constraints
- Target environment (production, prototype, learning)

---

## Outputs

- Complete, runnable code
- Code review with specific, actionable findings
- Refactored code with explanation of changes
- Bug diagnosis with root cause and fix
- Code explanation at appropriate depth

---

## Dependencies

- `modules/testing.md` — for testability review
- `modules/debugging.md` — for root cause analysis
- `modules/performance.md` — for algorithmic efficiency
- `modules/cybersecurity.md` — for security review
- `engines/verification-engine.md` — for code correctness

---

## Code Generation Standards

### Structure
- One responsibility per function
- Functions short enough to read without scrolling (target < 30 lines)
- Clear separation of concerns
- Explicit error handling — no silent failures
- Input validation at trust boundaries

### Naming
- Names describe purpose, not implementation
- Consistent naming convention for the language (snake_case, camelCase, PascalCase)
- Avoid abbreviations unless they are universal in the domain
- Boolean variables named with `is_`, `has_`, `can_` prefixes

### Error Handling
- All errors handled explicitly
- Error messages are actionable ("Failed to connect to database: connection refused" not "Error occurred")
- Errors are logged at the appropriate level
- Resources (files, connections, locks) always released in finally blocks or equivalent

### Documentation
- Public APIs documented with purpose, parameters, return values, and raised errors
- Non-obvious logic explained with inline comments
- Complex algorithms explained with a brief comment before the implementation

### Security (Defaults)
- No hardcoded credentials or secrets
- All user input treated as untrusted
- SQL queries parameterised
- Output escaped for the target context

---

## Language-Specific Standards

### Python
- Type hints on all function signatures
- Dataclasses or Pydantic for structured data
- Context managers for resource management
- `pathlib` over `os.path`
- `logging` module, not `print()` in production code

### TypeScript / JavaScript
- Strict TypeScript where applicable
- `const` by default; `let` when mutation is needed
- Async/await over raw promises
- Explicit return types on public functions
- No `any` without justification

### Go
- Errors returned, not panicked
- Explicit interface definitions
- Table-driven tests
- Context propagation for cancellation
- Minimal use of `interface{}` / `any`

### SQL
- Parameterised queries always
- Explicit column lists in SELECT for production code
- Transactions for multi-step mutations
- Indexes considered for query patterns

---

## Examples

### Example 1 — Function generation

**Request:** Write a Python function that reads a JSON config file and returns the parsed content, with error handling.

```python
import json
import logging
from pathlib import Path

logger = logging.getLogger(__name__)


def load_config(config_path: str | Path) -> dict:
    """
    Load and parse a JSON configuration file.

    Args:
        config_path: Path to the JSON configuration file.

    Returns:
        Parsed configuration as a dictionary.

    Raises:
        FileNotFoundError: If the config file does not exist.
        ValueError: If the file contains invalid JSON.
    """
    path = Path(config_path)

    if not path.exists():
        raise FileNotFoundError(f"Config file not found: {path}")

    try:
        with path.open(encoding="utf-8") as f:
            return json.load(f)
    except json.JSONDecodeError as e:
        raise ValueError(f"Invalid JSON in config file {path}: {e}") from e
```

---

## Best Practices

- Run the code mentally before delivering it
- Check edge cases: empty input, null/None, zero, large values
- Verify error handling paths, not just the happy path
- Match code complexity to the stated environment (production vs prototype)
- Prefer explicit over implicit behaviour

---

## Common Mistakes

- Generating code that doesn't handle errors
- Using deprecated APIs without noting the deprecation
- Ignoring the stated language or framework version
- Providing pseudocode when real code was requested
- Hallucinating library methods that do not exist

---

## Limitations

- Cannot run or test code — correctness is verified by reasoning
- Language-specific edge cases in obscure frameworks may have lower confidence
- Generated code should always be reviewed before production deployment
