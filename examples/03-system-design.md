# Example: URL Shortener System Design

**Trigger:** `SHP: Design a URL shortener service like bit.ly.`

---

## Pipeline Execution

**Complexity:** Level 4 (Expert) — system design, scale, distributed ID generation, storage, caching

**Modules activated:** system-design, architecture, performance, cloud, api-design

**Engines:** planning, multi-expert, verification, quality, confidence

---

## Response

### Step 1 — Requirements clarification

**Functional:**
- Shorten a long URL to a short code
- Redirect short URL to original
- Optional: custom aliases; link analytics (click count, referrer)

**Non-functional (assumed, state if different):**
- 100M URLs shortened per day (write-heavy assumption)
- 10:1 read:write ratio → 1B redirects per day
- URL availability: 5 years minimum
- Latency: redirect < 10ms at p99
- Availability: 99.99% (< 1hr downtime/year)

---

### Step 2 — Scale estimates

```
Writes: 100M/day = 1,157/second
Reads:  1B/day   = 11,574/second

Storage per URL: 7 bytes (short code) + ~2KB (long URL) + metadata ≈ 2.5KB
5-year storage: 100M × 365 × 5 × 2.5KB = ~456TB
Peak read QPS: 11,574 × 3 (peak factor) ≈ 35,000 reads/second
```

---

### Step 3 — API design

```
POST /shorten
  Body: { "url": "https://...", "alias": "optional-custom" }
  Response 201: { "short_url": "https://short.ly/abc123", "expires_at": "..." }

GET /{short_code}
  Response 301: Location: https://original-url...
  (301 = permanent redirect; browser caches it — reduces load)
  (302 = temporary; use if analytics matter for every hit)
```

---

### Step 4 — Short code generation

**Options:**

| Approach | Pros | Cons |
|---|---|---|
| MD5/hash of URL | Deterministic (same URL → same code) | Collisions; not short enough |
| Auto-increment base62 | Simple; sequential | Predictable; single point of failure |
| Distributed ID (Snowflake) | Unique; time-ordered; distributed | More complex |
| Random + collision check | Simple | Contention at scale |

**Recommendation:** Snowflake-style 64-bit ID, base62 encoded (10 chars → 62¹⁰ = 839 trillion unique codes).

```python
# base62 encoding of a 64-bit integer
ALPHABET = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"

def encode(num: int) -> str:
    result = []
    while num:
        num, rem = divmod(num, 62)
        result.append(ALPHABET[rem])
    return ''.join(reversed(result)) or ALPHABET[0]
```

---

### Step 5 — Architecture

```
[Client]
   │
   ▼
[CDN] — cache redirects at edge (TTL 1hr for non-custom, 10min for custom)
   │
   ▼
[Load Balancer]
   │
   ├─► [Redirect Service] (stateless, horizontally scalable)
   │         │
   │         ├─► [Redis Cluster] (hot URL cache, TTL 24h)
   │         │       Cache miss ↓
   │         └─► [PostgreSQL] (source of truth)
   │
   └─► [Shortener Service] (handles POST /shorten)
             │
             ├─► [ID Generator Service] (Snowflake IDs, distributed)
             └─► [PostgreSQL] (write new mappings)
             └─► [Analytics Queue] (async click tracking)

[Analytics Worker]
   └─► [ClickHouse / TimescaleDB] (analytics store)
```

---

### Step 6 — Data model

```sql
CREATE TABLE url_mappings (
    short_code  VARCHAR(12)   PRIMARY KEY,
    long_url    TEXT          NOT NULL,
    owner_id    UUID,
    created_at  TIMESTAMPTZ   NOT NULL DEFAULT NOW(),
    expires_at  TIMESTAMPTZ,
    click_count BIGINT        NOT NULL DEFAULT 0
);

CREATE INDEX idx_url_mappings_owner ON url_mappings(owner_id);
```

---

### Step 7 — Reliability and caching

**Cache strategy:** Cache-aside.
- Redirect service checks Redis first (O(1) lookup)
- On miss: query PostgreSQL, populate cache (TTL 24h)
- 80/20 rule: 20% of URLs get 80% of traffic → cache effectively covers most reads

**CDN caching:** Cache 301 redirects at edge. This handles the very hot URLs without hitting origin at all.

**Database:** PostgreSQL Multi-AZ (RDS), read replicas for redirect service. Writes go to primary only.

**Failure modes:**
| Failure | Impact | Mitigation |
|---|---|---|
| Redis down | All reads hit DB | DB read replicas absorb load; auto-restart Redis |
| DB primary down | Writes fail | Multi-AZ automatic failover (30–60s) |
| ID generator down | Creates fail | Multiple ID generator instances; pre-allocate ID blocks |

---

### Confidence

High for the core design. Moderate for analytics architecture — TimescaleDB vs ClickHouse selection depends on query patterns and team familiarity.

**Assumptions:**
- 301 redirect is acceptable (analytics will undercount; browser caches the redirect)
- URL expiry is needed (5-year default)
- Custom aliases are a small fraction of total volume (< 5%)

