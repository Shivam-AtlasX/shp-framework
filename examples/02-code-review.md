# Example: Code Security Review

**Trigger:** `SHP: Review this Python function for security vulnerabilities.`

```python
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    result = db.execute(query)
    return result.fetchone()
```

---

## Pipeline Execution

**Complexity:** Level 2 (Moderate) — code review + security

**Modules activated:** coding, cybersecurity

**Engines:** verification, threat-model, quality

---

## Response

### Findings

**[Critical] SQL Injection**

The `user_id` parameter is concatenated directly into the SQL query. An attacker can pass `1 OR 1=1` or `1; DROP TABLE users; --` to manipulate the query.

```python
# Vulnerable
query = f"SELECT * FROM users WHERE id = {user_id}"

# Fixed — parameterised query
def get_user(user_id: int) -> dict | None:
    query = "SELECT * FROM users WHERE id = %s"
    result = db.execute(query, (user_id,))
    return result.fetchone()
```

**[Medium] Missing Input Validation**

`user_id` has no type validation. Pass the expected type explicitly and validate at the trust boundary:

```python
def get_user(user_id: int) -> dict | None:
    if not isinstance(user_id, int) or user_id <= 0:
        raise ValueError(f"Invalid user_id: {user_id}")
    query = "SELECT * FROM users WHERE id = %s"
    result = db.execute(query, (user_id,))
    return result.fetchone()
```

**[Low] SELECT \* in production code**

`SELECT *` returns all columns including any sensitive ones added in future migrations. Use explicit column lists:

```python
query = "SELECT id, email, name, created_at FROM users WHERE id = %s"
```

### Fixed Version

```python
def get_user(user_id: int) -> dict | None:
    """
    Retrieve a user by ID.

    Args:
        user_id: Positive integer user ID.

    Returns:
        User record dict, or None if not found.

    Raises:
        ValueError: If user_id is invalid.
    """
    if not isinstance(user_id, int) or user_id <= 0:
        raise ValueError(f"Invalid user_id: {user_id!r}")

    query = "SELECT id, email, name, created_at FROM users WHERE id = %s"
    result = db.execute(query, (user_id,))
    return result.fetchone()
```

