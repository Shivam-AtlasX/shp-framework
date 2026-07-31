# System Design Module

## Purpose

Reasons about large-scale distributed system design: capacity planning, scalability patterns, data consistency, reliability engineering, and component interaction. Guides both the design process and the reasoning about trade-offs.

---

## Responsibilities

- Lead structured system design conversations
- Design systems to meet scale and reliability requirements
- Identify and resolve single points of failure
- Design data storage and access patterns at scale
- Recommend caching, queuing, and load balancing strategies
- Estimate capacity and identify scaling thresholds
- Evaluate trade-offs between consistency, availability, and partition tolerance

---

## Activation Criteria

Activate when:
- "Design a system" type requests
- High-traffic or large-data scenarios (10k+ req/s, TB+ data)
- Distributed systems, microservices, or event-driven architectures
- Reliability, availability, or fault tolerance requirements
- Capacity planning or scalability analysis

---

## Inputs

- Functional requirements (what the system does)
- Non-functional requirements (how well it does it — latency, throughput, availability, durability)
- Data volume and growth estimates
- Team and infrastructure constraints
- Existing system context

---

## Outputs

- System design with component diagram (described or ASCII)
- Data model and storage strategy
- Scaling strategy with thresholds
- Reliability analysis and failure mode coverage
- Capacity estimates
- Trade-off analysis

---

## Dependencies

- `modules/architecture.md` — architectural patterns
- `modules/cloud.md` — cloud service selection
- `modules/performance.md` — performance analysis
- `modules/networking.md` — network topology
- `modules/cybersecurity.md` — security architecture

---

## System Design Framework

Always follow this sequence:

### Step 1 — Clarify requirements

Do not design before requirements are clear. Ask:

```
Functional:
  - What are the core user-facing operations? (read, write, search, notify, etc.)
  - What are the data entities and relationships?
  - What integrations or external dependencies exist?

Non-functional:
  - What is the target availability? (99.9%? 99.99%?)
  - What is the read/write latency target? (p99)
  - What is the throughput? (requests/second, events/second)
  - What is the data volume now and in 1–3 years?
  - What are the durability requirements? (can we lose data? how much?)

Constraints:
  - Team size and operational capability
  - Technology constraints (existing stack)
  - Budget
  - Regulatory/compliance
```

---

### Step 2 — Estimate scale

Back-of-the-envelope calculations to size the system:

```
Users:               X daily active users (DAU)
Read QPS:            X reads/day / 86,400 = Y reads/second
Write QPS:           X writes/day / 86,400 = Y writes/second
Storage/year:        X objects × avg_size_bytes × 365
Bandwidth:           QPS × avg_response_size_bytes

Example: Twitter-scale feed
- 300M DAU
- 100 read/day per user = 30B reads/day = 347k reads/second
- 0.5 write/day per user = 150M writes/day = 1,736 writes/second
- 140B tweet bytes/year = 140TB/year for tweet text alone
```

---

### Step 3 — Define the API

Define the interface before the internals. The API is the contract.

```
REST example:
  POST   /tweets                → create tweet
  GET    /tweets/{id}           → get tweet
  GET    /users/{id}/feed       → get personalised feed
  DELETE /tweets/{id}           → delete tweet

Event-driven example:
  Publish: tweet.created {tweet_id, user_id, content, timestamp}
  Publish: tweet.deleted {tweet_id, user_id}
  Subscribe: feed.update.needed {user_id}
```

---

### Step 4 — Design the data model

```
Entities and relationships:
  User: {id, username, email, created_at}
  Tweet: {id, user_id, content, created_at}
  Follow: {follower_id, followee_id}
  
Access patterns:
  - Get tweet by ID → primary key lookup
  - Get user's tweets → query by user_id, sorted by created_at
  - Get feed for user → get all followees → get their tweets → merge → sort
```

Access patterns drive database choice:
- Primary key and simple queries → any relational DB
- Complex access patterns, huge scale → consider specialised stores
- Fan-out at read vs write → design choice (see below)

---

### Step 5 — High-level architecture

Draw the components and data flows. For every data store, specify:
- What data it holds
- Who reads from it and why
- Who writes to it and how

For every service, specify:
- What it is responsible for
- What it calls and what calls it

---

### Step 6 — Deep dive on bottlenecks

Identify the hardest parts and reason about them:
- Where is the read hotspot?
- Where is the write bottleneck?
- What fails if component X goes down?
- What is the single point of failure?

---

### Step 7 — Reliability and observability

```
Reliability:
  - What are the failure modes? (service down, DB down, network partition)
  - How does each failure manifest to users?
  - How is each failure detected and recovered?
  - Where is there redundancy?
  - What is the graceful degradation strategy?

Observability:
  - Logging: what events are logged? with what correlation IDs?
  - Metrics: RED per service; USE per infrastructure component
  - Alerts: what conditions page someone? what is the runbook?
```

---

## CAP Theorem

In the presence of a network partition (P), a distributed system must choose between:
- **Consistency (C):** All nodes return the same data
- **Availability (A):** Every request receives a response (not guaranteed to be latest)

| System type | Choice | Examples | Use when |
|---|---|---|---|
| CP | Consistency over Availability | PostgreSQL, HBase, Zookeeper | Financial transactions, inventory |
| AP | Availability over Consistency | Cassandra, DynamoDB (eventually consistent), CouchDB | Social feeds, shopping carts, metrics |

**Modern nuance:** Most real systems offer tunable consistency (read/write quorums, consistency levels). The choice is not binary but a spectrum.

---

## Consistency Patterns

| Pattern | Description | Trade-off |
|---|---|---|
| Strong consistency | Read always returns latest write | Higher latency, lower availability |
| Eventual consistency | Read will eventually return latest write | Lower latency, higher availability |
| Read-your-writes | User always sees their own writes | Requires session affinity or read-after-write guarantee |
| Monotonic reads | User never sees older data after newer | Requires sticky routing or version tracking |
| Linearisability | Strongest: operations appear instant and ordered | Very expensive at scale |

---

## Scalability Patterns

### Read scaling

```
Read-heavy workload:
  → Add read replicas (PostgreSQL streaming replication)
  → Add cache layer (Redis) in front of DB
  → CDN for static content
  → Denormalise for read performance (materialised views, precomputed aggregates)
```

### Write scaling

```
Write-heavy workload:
  → Sharding: partition data across multiple DB instances
  → Message queue: absorb write spikes; async processing
  → CQRS: separate read and write models
  → Event sourcing: append-only log; derived read models
```

### Sharding strategies

| Strategy | Partition by | Good for | Watch for |
|---|---|---|---|
| Range sharding | Ordered range of key | Sequential access | Hot shards (recent data gets all writes) |
| Hash sharding | Hash of key | Uniform distribution | No range queries; rebalancing is hard |
| Directory sharding | Lookup table | Flexible | Lookup table becomes bottleneck |
| Geographic sharding | User location | Data residency, latency | Cross-shard queries |

---

## Caching Patterns

### Cache-aside (lazy loading)

```
1. Application checks cache
2. Cache miss → read from DB → store in cache → return
3. Cache hit → return from cache
```

Pros: Cache only contains requested data; cache failures are non-fatal.
Cons: Cache miss adds latency; stale data until TTL expires.

### Write-through

```
1. Application writes to cache
2. Cache synchronously writes to DB
```

Pros: Cache always fresh.
Cons: Write latency increases; cache bloat (data written but rarely read).

### Write-behind (write-back)

```
1. Application writes to cache
2. Cache asynchronously writes to DB
```

Pros: Low write latency.
Cons: Risk of data loss if cache fails before write completes.

### Cache eviction policies

- **LRU (Least Recently Used):** Evict the item not accessed for the longest time
- **LFU (Least Frequently Used):** Evict the item accessed least often
- **TTL (Time to Live):** Evict after a fixed duration regardless of access

---

## Common System Design Problems

### URL Shortener

Key challenges: unique ID generation at scale; redirect latency; analytics.

```
Write path: [POST /shorten] → ID generator → {short_id: url} in DB/cache → return short URL
Read path:  [GET /:short_id] → cache lookup → DB fallback → 301 redirect

ID generation: base62 encode an auto-increment or use a distributed ID (snowflake)
Storage: ~7 bytes/short_id + ~2KB/long_url → 1B URLs = ~2TB
Cache: store hot mappings in Redis; LRU eviction; TTL 24h
```

### Rate Limiter

Key challenges: sliding window vs fixed window; distributed counting; speed.

```
Algorithms:
  Fixed window: simple; allows burst at window boundary
  Sliding window log: accurate; memory-intensive (store each request timestamp)
  Token bucket: smooth burst handling; configurable refill rate
  Leaky bucket: strict output rate; queue acts as buffer

Storage: Redis with atomic INCR and EXPIRE for distributed rate limiting
```

### Notification System

Key challenges: fan-out to millions of subscribers; delivery guarantee; preference management.

```
Components:
  [Event source] → [Message queue (Kafka/SQS)]
               → [Fan-out service] → [Per-channel queues]
               → [Email sender] → [Email provider (SES/SendGrid)]
               → [Push sender] → [FCM/APNs]
               → [SMS sender]  → [Twilio/Vonage]

Delivery guarantees:
  At-least-once: re-queue on failure; consumers must be idempotent
  Exactly-once: distributed transaction or idempotency key at receiver
```

---

## Examples

### Example 1 — Design a read-heavy social feed

**Requirements:**
- 50M DAU; each user follows up to 1000 others; reads 10 posts/session
- Feed sorted by recency; posts include text, image URLs, likes
- p99 feed load < 100ms

**Scale estimates:**
- 50M × 10 reads/day = 500M feed reads/day = 5,787 reads/second
- 50M × 0.1 writes/day = 5M writes/day = 58 writes/second
- Read:Write ratio = ~100:1 → heavily optimise reads

**Architecture decision — fan-out on write:**
When user A posts, pre-compute and push the post to all followers' feed caches.
- Pro: O(1) feed read (just read pre-computed cache)
- Con: O(followers) writes per post (expensive for celebrities with 1M+ followers)
- Mitigation: use fan-out on read for high-follower accounts (celebrities); fan-out on write for everyone else (hybrid approach)

```
Write path (normal user, <10k followers):
  [Post created] → [Post DB] → [Fan-out service] → [Feed cache (Redis, per user)]

Write path (celebrity, >10k followers):
  [Post created] → [Post DB] → [Hot user flag in cache]

Read path:
  [GET /feed/{user_id}]
    → Read pre-computed feed from Redis
    → For followed celebrities: fetch their recent posts from Post DB
    → Merge + sort by timestamp
    → Return top 20
```

---

## Best Practices

- Start with requirements — design assumptions cause expensive rewrites
- Estimate scale before designing — it changes every decision
- Design for the next 2x, not the next 100x
- Explicitly name every component, what it stores, and who calls it
- Design failure modes as carefully as the happy path

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Designing for theoretical 100x scale | Premature complexity |
| No capacity estimates | Design may not fit the actual load |
| No failure analysis | Single points of failure surface in production |
| Missing observability design | Cannot diagnose production issues |
| Inconsistent data model | Cross-service data integrity problems |

---

## Limitations

- Capacity estimates are approximations — validate with load testing
- Cloud-specific service details are in `modules/cloud.md`
- Very specialised systems (HFT, real-time bidding, ML serving at scale) require domain expertise
