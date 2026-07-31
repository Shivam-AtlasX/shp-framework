# Example: Production Bug Diagnosis

**Trigger:** `SHP: Our API returns 500 errors intermittently under load. Stack trace below.`

```
Traceback (most recent call last):
  File "/app/api/handlers/order.py", line 47, in create_order
    conn = db_pool.getconn()
  File "/usr/local/lib/python3.12/site-packages/psycopg2/pool.py", line 195, in getconn
    raise PoolError("connection pool exhausted")
psycopg2.pool.PoolError: connection pool exhausted
```

---

## Pipeline Execution

**Complexity:** Level 3 (Complex) — production incident, root cause analysis, performance + infrastructure

**Modules activated:** debugging, performance, coding, devops

**Engines:** verification, quality, confidence

---

## Response

### Root cause

The database connection pool is being exhausted under load. This is **not** a database capacity problem — it is a connection management problem in the application.

**What's happening:**

```
Connection pool has max_connections = N (e.g., 10)
Under load, N+1 concurrent requests each try to acquire a connection
The pool has none left → raises PoolError → 500 error
```

---

### Diagnosis checklist

**1. Is the pool size too small?**

Find your current pool configuration:

```python
# psycopg2 SimpleConnectionPool / ThreadedConnectionPool
pool = psycopg2.pool.ThreadedConnectionPool(
    minconn=1,
    maxconn=10   # ← this is the limit
)
```

Compare against: (concurrent requests) × (DB calls per request). If you have 50 concurrent requests each making 1 DB call, you need 50 connections minimum.

**2. Are connections being returned to the pool?**

The most common cause: connections are acquired but never returned because of an uncaught exception.

```python
# ❌ Bug: if an exception occurs, conn is never returned
def create_order(data):
    conn = db_pool.getconn()
    cursor = conn.cursor()
    cursor.execute("INSERT INTO orders ...")  # if this raises, conn leaks
    conn.commit()
    db_pool.putconn(conn)   # never reached on exception

# ✓ Fix: context manager guarantees return
from contextlib import contextmanager

@contextmanager
def get_db_connection():
    conn = db_pool.getconn()
    try:
        yield conn
    finally:
        db_pool.putconn(conn)  # always runs, even on exception

def create_order(data):
    with get_db_connection() as conn:
        cursor = conn.cursor()
        cursor.execute("INSERT INTO orders ...")
        conn.commit()
```

**3. Are transactions being committed or rolled back?**

Long-running open transactions hold connections. Every transaction must end with commit or rollback.

**4. Is connection pool size appropriate for your PostgreSQL max_connections?**

PostgreSQL has its own limit (`max_connections`, default 100). Every app instance's pool contributes:

```
Total connections = (app instances) × (pool max_connections)
Must be < PostgreSQL's max_connections

Example: 5 app instances × 20 pool connections = 100 total
         PostgreSQL max_connections = 100 → at the limit
```

---

### Immediate mitigation

1. **Increase pool size** (short-term):

```python
pool = psycopg2.pool.ThreadedConnectionPool(minconn=5, maxconn=50)
```

2. **Add connection timeout** (fail fast rather than hang):

```python
conn = db_pool.getconn(key=None)  # psycopg2 doesn't support timeout natively
# Use SQLAlchemy's pool_timeout or pg8000 which supports it
```

3. **Deploy PgBouncer** (medium-term):

PgBouncer is a connection pooler that sits between your app and PostgreSQL. It multiplexes thousands of app connections onto a small number of actual PostgreSQL connections.

```
App instances (500 connections) → PgBouncer → PostgreSQL (20 connections)
```

---

### Permanent fix

1. Fix the connection leak: wrap all DB operations in context managers
2. Add connection pool metrics to your monitoring (active connections, wait time, timeout count)
3. Alert on pool exhaustion before users see 500s
4. Use PgBouncer in transaction mode for PostgreSQL at scale

---

### Verification

After deploying the fix:

```python
# Add metrics to verify
import prometheus_client

pool_size = prometheus_client.Gauge('db_pool_size', 'Connection pool size')
pool_checked_out = prometheus_client.Gauge('db_pool_checked_out', 'Connections in use')

# In your connection context manager:
pool_checked_out.inc()
try:
    yield conn
finally:
    pool_checked_out.dec()
    db_pool.putconn(conn)
```

Monitor the graph: if `pool_checked_out` reaches `pool_size` before the errors stop, the pool size is still too small.

---

### Confidence

High. Connection pool exhaustion from this error pattern is well-established. The connection leak is the most likely cause in Python applications using psycopg2 without context managers. Moderate confidence on the exact pool sizing — this depends on your actual concurrent request count, which should be measured.

