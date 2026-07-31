# Performance Module

## Purpose

Evaluates time complexity, space complexity, I/O efficiency, and scalability characteristics of algorithms, code, and system designs. Identifies bottlenecks and recommends optimisations with explicit trade-offs.

---

## Responsibilities

- Analyse algorithm time and space complexity
- Identify performance bottlenecks in code and designs
- Recommend optimisations with measurable trade-offs
- Evaluate scalability thresholds and failure points
- Advise on profiling and measurement strategies
- Review database query performance

---

## Activation Criteria

Activate when:
- Algorithms or data structures are involved
- Large data volumes or high throughput are mentioned
- "Slow", "latency", "performance", or "scale" is raised
- Performance requirements are stated (SLA, target latency, throughput)
- Database query optimisation is needed

---

## Inputs

- Code or design to evaluate
- Performance requirements (target latency, throughput, concurrent users)
- Data scale (rows, events/second, payload sizes)
- Infrastructure constraints
- Current measured performance (if known)

---

## Outputs

- Complexity analysis (time, space, I/O)
- Identified bottlenecks with severity
- Optimisation recommendations with trade-offs
- Measurement and profiling recommendations

---

## Dependencies

- `modules/coding.md` — optimised code implementation
- `modules/system-design.md` — scalability architecture
- `modules/cloud.md` — infrastructure scaling patterns

---

## Core Principle

> **Measure before optimising.** The bottleneck is rarely where intuition suggests. Profile first, optimise second.

Premature optimisation creates unreadable code for no gain. Late optimisation at the wrong place is the same. Measure → identify → optimise → measure again.

---

## Complexity Reference

### Time complexity

| Complexity | Name | Example | Max practical n |
|---|---|---|---|
| O(1) | Constant | Hash lookup, array index | Unlimited |
| O(log n) | Logarithmic | Binary search, balanced BST | Very large |
| O(n) | Linear | Single pass, linear scan | ~10⁸–10⁹ |
| O(n log n) | Linearithmic | Merge sort, heap sort | ~10⁷ |
| O(n²) | Quadratic | Bubble sort, naïve search | ~10⁴ |
| O(n³) | Cubic | Naïve matrix multiply | ~10³ |
| O(2ⁿ) | Exponential | Brute force subsets | n ≤ 25 |
| O(n!) | Factorial | Brute force permutations | n ≤ 12 |

### How to derive complexity

```
1. Identify all loops and recursive calls
2. Count iterations as a function of n
3. Nested loops multiply: O(n) inside O(n) = O(n²)
4. Sequential operations add: O(n) + O(n log n) = O(n log n) [dominant term]
5. Ignore constants: O(3n) = O(n)
6. Identify dominant term; drop lower-order terms
```

### Space complexity

Separate from time complexity. Includes:
- Input storage (usually excluded from analysis)
- Auxiliary space (extra allocations your algorithm makes)
- Call stack depth for recursion

---

## I/O Complexity

In real-world systems, I/O is almost always the dominant cost. CPU is fast; disk and network are slow.

| Operation | Approximate latency |
|---|---|
| L1 cache hit | 0.5 ns |
| L2 cache hit | 7 ns |
| RAM access | 100 ns |
| SSD random read | 100 µs |
| Database query (indexed) | 1–10 ms |
| Network round trip (same region) | 0.5–1 ms |
| Network round trip (cross-region) | 50–150 ms |
| HDD random read | 10 ms |

**Implication:** Reducing database queries from N to 1 is usually far more impactful than any algorithmic optimisation in application code.

---

## Common Performance Anti-Patterns

### N+1 Query Problem

**Pattern:** Load N items, then query the database once per item.

```python
# N+1: 1 query for orders + N queries for users
orders = db.query("SELECT * FROM orders")
for order in orders:
    user = db.query("SELECT * FROM users WHERE id = ?", order.user_id)
    print(f"{user.name}: {order.total}")

# Fixed: 1 query with JOIN
orders = db.query("""
    SELECT o.*, u.name
    FROM orders o
    JOIN users u ON u.id = o.user_id
""")
```

**Impact:** O(N) queries → O(1) query. For 1000 orders, this is 1001 queries → 1 query.

---

### Quadratic nested loop on large data

```python
# O(n²): check every pair
def has_duplicate(items):
    for i in range(len(items)):
        for j in range(i + 1, len(items)):
            if items[i] == items[j]:
                return True
    return False

# O(n): use a set for O(1) lookup
def has_duplicate(items):
    seen = set()
    for item in items:
        if item in seen:
            return True
        seen.add(item)
    return False
```

---

### Unbounded query / missing pagination

```sql
-- Returns all rows — fails at scale
SELECT * FROM events WHERE user_id = ?;

-- Paginated
SELECT * FROM events WHERE user_id = ? ORDER BY created_at DESC LIMIT 50 OFFSET ?;
```

---

### Synchronous blocking in async context

```javascript
// Blocks the event loop — all other requests wait
app.get('/report', async (req, res) => {
  const data = fs.readFileSync('large-file.csv');  // blocking!
  res.json(process(data));
});

// Non-blocking
app.get('/report', async (req, res) => {
  const data = await fs.promises.readFile('large-file.csv');
  res.json(process(data));
});
```

---

### Repeated expensive computation (missing cache)

```python
# Called thousands of times per second — recalculates every time
def get_exchange_rates():
    return requests.get("https://api.rates.example.com/latest").json()

# Cached with TTL
from functools import lru_cache
import time

_cache = {}
_cache_ttl = 300  # 5 minutes

def get_exchange_rates():
    now = time.time()
    if "rates" not in _cache or now - _cache["timestamp"] > _cache_ttl:
        _cache["rates"] = requests.get("https://api.rates.example.com/latest").json()
        _cache["timestamp"] = now
    return _cache["rates"]
```

---

## Database Query Performance

### Index usage

```sql
-- Slow: full table scan (no index on email)
SELECT * FROM users WHERE email = 'alice@example.com';

-- Fast: index scan (index on email)
CREATE INDEX idx_users_email ON users(email);
SELECT * FROM users WHERE email = 'alice@example.com';
```

**Index guidelines:**
- Index columns used in WHERE, JOIN ON, and ORDER BY
- Composite index column order matters: most selective first
- Indexes slow writes — do not index every column
- Use `EXPLAIN` / `EXPLAIN ANALYZE` to verify index usage

### Query patterns to avoid

| Anti-pattern | Problem | Fix |
|---|---|---|
| `SELECT *` | Over-fetches columns | Select only needed columns |
| `WHERE LIKE '%term%'` | Cannot use index (leading wildcard) | Full-text search; or trailing wildcard only |
| `WHERE function(column) = ?` | Index on column unusable | Rewrite to avoid function on indexed column |
| `ORDER BY` on non-indexed column | Full sort | Add index; or sort in application for small sets |
| Multiple N+1 levels | Exponential queries | Batch load with JOINs or DataLoader pattern |

---

## Scalability Analysis

For any design, evaluate the scaling threshold:

```
Component        │ Scales horizontally? │ Bottleneck at scale
─────────────────┼──────────────────────┼─────────────────────
Stateless API    │ Yes                  │ Connection pool
Session state    │ No (without store)   │ Memory per instance
PostgreSQL       │ Read: yes (replicas) │ Write throughput
Redis            │ Yes (cluster)        │ Memory
Message queue    │ Yes                  │ Consumer throughput
Search index     │ Yes (sharding)       │ Write throughput, memory
```

---

## Profiling Recommendations

| Language | CPU profiler | Memory profiler | DB profiler |
|---|---|---|---|
| Python | `cProfile`, `py-spy` | `memory_profiler`, `tracemalloc` | SQLAlchemy echo, `EXPLAIN ANALYZE` |
| Node.js | V8 Profiler, `clinic.js` | `--inspect` heap snapshot | Query logging, `EXPLAIN` |
| Go | `pprof` | `pprof` (heap) | `pgx` logging |
| Java | JProfiler, async-profiler | `jmap`, JProfiler | Hibernate statistics |

**Always profile in an environment that mirrors production data volume.** Profiling on dev data of 100 rows tells you nothing about production behaviour at 10M rows.

---

## Optimisation Priority Order

1. **Algorithm** — O(n) instead of O(n²) is never overcome by hardware
2. **I/O reduction** — fewer queries, batched operations, caching
3. **Concurrency** — parallelise independent work
4. **Data structure** — right structure for the access pattern (hash map vs array vs tree)
5. **Code micro-optimisation** — last resort; profile first

---

## Examples

### Example 1 — Full analysis

**Request:** "This endpoint takes 3 seconds. Why?"

```python
@app.route("/dashboard/<user_id>")
def dashboard(user_id):
    user = User.query.get(user_id)
    orders = Order.query.filter_by(user_id=user_id).all()
    for order in orders:
        order.items = OrderItem.query.filter_by(order_id=order.id).all()
        for item in order.items:
            item.product = Product.query.get(item.product_id)
    return render_template("dashboard.html", user=user, orders=orders)
```

**Analysis:**
- 1 query for user
- 1 query for orders (say N=20)
- N queries for order items (20 queries)
- N×M queries for products (say 5 items/order × 20 = 100 queries)
- **Total: 1 + 1 + 20 + 100 = 122 queries per page load**

**Fix:** Eager load with JOINs, reduce to 2–3 queries total. Use SQLAlchemy `joinedload` / `selectinload`.

---

## Best Practices

- State complexity for every non-trivial algorithm you generate
- Flag N+1 patterns whenever ORM or query loops appear in code
- Recommend profiling before optimising — do not guess at the bottleneck
- Always state the trade-off: performance vs readability, memory vs speed
- Distinguish between latency (one request's time) and throughput (requests/second)

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Optimising without profiling | Fast code in the wrong place |
| Sacrificing readability for nanoseconds | Unmaintainable code, negligible gain |
| Ignoring I/O, focusing only on CPU | 1000x optimisation in the wrong tier |
| Over-engineering for scale not yet reached | Complexity cost without benefit |
| Caching without TTL or invalidation | Stale data; memory leaks |

---

## Limitations

- Cannot measure actual performance — analysis is by reasoning
- Real bottlenecks require profiling in production-like conditions
- Database query plans vary by data distribution; `EXPLAIN ANALYZE` is definitive
- Cloud infrastructure performance varies; capacity planning requires load testing
