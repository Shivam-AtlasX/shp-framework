# Testing Module

## Purpose

Evaluates testability of code and designs. Recommends testing strategies proportional to risk. Generates test cases with clear structure. Reviews test suites for coverage gaps and anti-patterns.

---

## Responsibilities

- Assess testability of generated code and designs
- Generate unit, integration, and end-to-end test cases
- Recommend testing strategy and pyramid proportions
- Identify hard-to-test patterns and recommend refactoring
- Review existing tests for coverage gaps, duplication, and anti-patterns
- Design test data strategies

---

## Activation Criteria

Activate when:
- Production code is generated (always add testing consideration)
- Test cases are explicitly requested
- CI/CD pipeline is discussed
- "How do I test this?" type questions
- Code review includes testability assessment

---

## Inputs

- Code or design to evaluate
- Existing test suite (if any)
- Testing framework preference (pytest, Jest, JUnit, etc.)
- Coverage requirements
- Environment constraints (can tests use real DB? real network?)

---

## Outputs

- Test cases with AAA structure
- Testing strategy recommendation
- Coverage gap analysis
- Testability improvement suggestions

---

## Dependencies

- `modules/coding.md` — test code quality standards
- `modules/devops.md` — CI/CD integration
- `modules/debugging.md` — test failure diagnosis

---

## Testing Pyramid

```
              ╱╲
             ╱E2E╲          ← Few. Slow. Cover critical user journeys.
            ╱──────╲
           ╱  Integ  ╲      ← Some. Verify component collaboration.
          ╱────────────╲
         ╱  Unit Tests  ╲   ← Many. Fast. Cheap. Cover logic.
        ╱────────────────╲
```

**Invest at the base.** A pyramid with too many E2E tests is slow, fragile, and expensive to maintain.

---

## Test Types

### Unit Tests

**What:** Test a single unit of behaviour in isolation.  
**Dependencies:** Mocked or stubbed.  
**Speed:** Milliseconds.  
**Scope:** One function, method, or class.

```python
# Unit test: pure function — no mocking needed
def test_calculate_discount_applies_ten_percent():
    # Arrange
    price = 100.0
    discount_rate = 0.10

    # Act
    result = calculate_discount(price, discount_rate)

    # Assert
    assert result == 90.0
```

---

### Integration Tests

**What:** Test that components work correctly together.  
**Dependencies:** Real (or test doubles for external services).  
**Speed:** Seconds.  
**Scope:** Multiple components, a service + its database, an API + its handlers.

```python
# Integration test: tests the full repository layer with a real test DB
def test_create_user_persists_to_database(db_session):
    # Arrange
    repo = UserRepository(db_session)
    user_data = {"email": "test@example.com", "name": "Alice"}

    # Act
    created = repo.create(user_data)

    # Assert
    found = repo.find_by_id(created.id)
    assert found.email == "test@example.com"
```

---

### End-to-End Tests

**What:** Test complete user journeys through the deployed system.  
**Dependencies:** Real system, real infrastructure.  
**Speed:** Seconds to minutes.  
**Scope:** Full stack — UI/API → service → database.

Use sparingly. Cover critical paths only: login, checkout, core workflow.

---

### Contract Tests

**What:** Verify that a service fulfils the contract expected by its consumers.  
**When:** Microservices or API-first development.  
**Tool:** Pact, Spring Cloud Contract.

---

## AAA Structure

Every test follows Arrange → Act → Assert:

```python
def test_[behaviour]_when_[condition]_then_[expectation]():
    # Arrange — set up the state needed for the test
    user = User(email="alice@example.com", role="admin")
    resource = Resource(owner_id=999)  # different owner

    # Act — perform the action being tested
    result = can_user_access(user, resource)

    # Assert — verify the outcome
    assert result is False
```

### Naming convention

`test_[behaviour]_when_[condition]_then_[expectation]`

Good names:
- `test_login_when_credentials_invalid_returns_401`
- `test_order_total_when_discount_applied_reduces_price`
- `test_send_email_when_smtp_fails_raises_notification_error`

Bad names:
- `test_login` — says nothing about what is tested
- `test1` — meaningless
- `test_the_function_works` — tautological

---

## Test Data Strategy

### Builders / Factories

Use factory functions or builder objects for test data. Never duplicate large data literals.

```python
# Factory function
def make_user(**overrides):
    defaults = {
        "id": "usr_test_001",
        "email": "default@example.com",
        "role": "member",
        "active": True
    }
    return User(**{**defaults, **overrides})

# Usage
admin = make_user(role="admin")
inactive = make_user(active=False)
```

### Fixtures vs helpers

- **Fixtures** (pytest/Jest): use for shared setup/teardown — database connections, server instances
- **Helper functions**: use for test data creation

### Test database strategy

| Approach | Speed | Isolation | Accuracy |
|---|---|---|---|
| In-memory SQLite | Fast | High | Lower (dialect differences) |
| Docker test DB | Moderate | High | High (matches production) |
| Shared test DB | Fast | Low (requires cleanup) | High |
| Transaction rollback | Fast | High | High |

Prefer: Docker test DB with transaction rollback per test.

---

## Mocking Strategy

### Mock external dependencies, not internal ones

```python
# Good: mock the external email service, not your own code
def test_sends_welcome_email_on_registration(mock_email_service):
    mock_email_service.send.return_value = True
    register_user("alice@example.com", "password123")
    mock_email_service.send.assert_called_once_with(
        to="alice@example.com",
        template="welcome"
    )
```

### Mock at the boundary

Mock at the seam between your code and the external world (HTTP client, DB driver, filesystem). Do not mock internal functions — that couples tests to implementation.

### Test doubles taxonomy

| Type | Description | Use When |
|---|---|---|
| Stub | Returns preset values | You need controlled responses |
| Mock | Records calls for assertion | You need to verify side effects |
| Fake | Simplified working implementation | You need real behaviour cheaply (in-memory store) |
| Spy | Wraps real object, records calls | You need real behaviour + call verification |

---

## Coverage

### What coverage tells you

Line/branch coverage measures which code was executed during tests. It does **not** measure whether the tests are meaningful.

- 100% coverage ≠ no bugs
- Low coverage is a red flag; high coverage is a necessary but not sufficient condition

### Coverage targets (practical)

| Code type | Target |
|---|---|
| Business logic / domain | 90–100% |
| Service layer | 80–90% |
| Infrastructure / adapters | 70–80% |
| Generated code | Exempt or minimal |
| UI components | Vary by risk |

### Coverage blind spots

- Tests that cover code without meaningful assertions (assertion-free tests)
- Mocking everything so the real code never runs
- Missing negative test cases
- Missing edge cases (empty input, max value, concurrent access)

---

## Testability Design

Code is easier to test when it:

- Has **pure functions** for business logic (no side effects → no mocking needed)
- Uses **dependency injection** (dependencies provided, not created internally)
- Has **small, focused units** (one responsibility → one test scope)
- **Separates I/O from logic** (validate data, then save — not both in one function)

### Testability smell: hard-coded dependency

```python
# Hard to test — creates its own dependency
class OrderService:
    def __init__(self):
        self.db = DatabaseConnection("postgresql://...")  # can't swap this in tests

# Easy to test — dependency injected
class OrderService:
    def __init__(self, db: DatabaseConnection):
        self.db = db  # inject a test double in tests
```

---

## Test Anti-Patterns

| Anti-Pattern | Problem | Fix |
|---|---|---|
| Testing implementation | Breaks on refactoring | Test behaviour and outcomes |
| Assertion-free test | No verification — always passes | Every test must assert something meaningful |
| Testing the mock | Mock returns X, test asserts X | Test that your code does the right thing with X |
| Flaky tests | Intermittent failures → ignored | Fix non-determinism; use fixed seeds, fixed times |
| God test | One test covers 20 scenarios | One test per scenario |
| Mystery guest | Test depends on data set up elsewhere | Self-contained Arrange in every test |
| Excessive setup | 50 lines of Arrange | Use factories; test smaller units |

---

## Examples

### Example 1 — Testing a validation function

```python
import pytest

# Code under test
def validate_email(email: str) -> bool:
    return "@" in email and "." in email.split("@")[-1]

# Tests
class TestValidateEmail:
    def test_valid_email_returns_true(self):
        assert validate_email("alice@example.com") is True

    def test_missing_at_sign_returns_false(self):
        assert validate_email("aliceexample.com") is False

    def test_missing_domain_dot_returns_false(self):
        assert validate_email("alice@examplecom") is False

    def test_empty_string_returns_false(self):
        assert validate_email("") is False

    def test_at_sign_only_returns_false(self):
        assert validate_email("@") is False

    def test_multiple_at_signs_uses_last_domain(self):
        # Edge case: "a@b@example.com" — spec says split on last @
        assert validate_email("a@b@example.com") is True
```

---

### Example 2 — Integration test with fixture

```python
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

@pytest.fixture
def db_session():
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    session = Session()
    yield session
    session.rollback()
    session.close()

def test_user_repository_find_by_email(db_session):
    # Arrange
    repo = UserRepository(db_session)
    repo.create({"email": "alice@example.com", "name": "Alice"})

    # Act
    result = repo.find_by_email("alice@example.com")

    # Assert
    assert result is not None
    assert result.name == "Alice"

def test_user_repository_returns_none_for_missing_email(db_session):
    repo = UserRepository(db_session)

    result = repo.find_by_email("nobody@example.com")

    assert result is None
```

---

## Best Practices

- Write the test before fixing a bug — it proves the test is meaningful
- Test the unhappy path as carefully as the happy path
- Keep tests independent — no shared mutable state between tests
- Make test failures diagnostic — the failure message should tell you what went wrong
- Run tests in CI on every commit — untested commits are unreviewed commits

---

## Limitations

- Cannot run tests or measure actual coverage
- Test quality analysis is by inspection, not execution
- Framework-specific patterns (React Testing Library, Playwright) vary in detail
