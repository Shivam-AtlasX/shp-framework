# API Design Module

## Purpose

Designs and reviews APIs that are consistent, intuitive, secure, and built for long-term evolution. Covers REST, GraphQL, gRPC, and webhook patterns.

---

## Responsibilities

- Design RESTful API contracts from requirements
- Review existing APIs for consistency, correctness, and usability
- Design GraphQL schemas and resolver strategies
- Define gRPC service definitions
- Recommend versioning, pagination, and error handling strategies
- Design authentication and authorisation flows
- Define webhook and event notification patterns

---

## Activation Criteria

Activate when:
- API design is requested from scratch
- Existing API is being reviewed or extended
- "Endpoint", "REST", "GraphQL", "gRPC", "webhook", or "API contract" is mentioned
- API documentation is being generated
- Client–server integration is being designed

---

## Inputs

- Domain model (entities and relationships)
- Operations required (what clients need to do)
- Clients (web, mobile, third-party, internal)
- Scale requirements
- Authentication model
- Versioning requirements

---

## Outputs

- Complete API contract (resources, operations, request/response shapes)
- Error response schema
- Versioning strategy
- Pagination pattern
- Authentication/authorisation specification
- OpenAPI spec (if requested)

---

## Dependencies

- `modules/cybersecurity.md` — API security patterns
- `modules/documentation.md` — API reference documentation
- `modules/system-design.md` — API gateway and backend architecture

---

## REST Design

### Resource naming

```
✓ Correct patterns:
  /users                      (collection)
  /users/{user_id}            (resource)
  /users/{user_id}/orders     (nested collection — owned resource)
  /users/{user_id}/orders/{order_id}

✗ Anti-patterns:
  /getUsers                   (verb in path)
  /user                       (singular collection)
  /Users                      (uppercase)
  /user_list                  (underscore; not kebab)
  /api/users?action=delete    (action as query param)
```

### HTTP method semantics

| Method | Semantics | Idempotent | Safe | Body |
|---|---|---|---|---|
| GET | Retrieve | Yes | Yes | No |
| POST | Create / trigger action | No | No | Yes |
| PUT | Full replace | Yes | No | Yes |
| PATCH | Partial update | No* | No | Yes |
| DELETE | Delete | Yes | No | No |
| HEAD | Retrieve headers only | Yes | Yes | No |
| OPTIONS | Supported methods | Yes | Yes | No |

*PATCH should be designed idempotently where possible.

**When to use POST vs PUT vs PATCH:**
- Create a new resource → POST to the collection
- Replace an entire resource → PUT to the resource
- Update specific fields → PATCH to the resource
- Trigger an action (not CRUD) → POST to an action resource (`/orders/{id}/cancel`)

### URL design for actions

When an operation doesn't map cleanly to CRUD:

```
✓ Action as sub-resource (recommended):
  POST /orders/{id}/cancel
  POST /users/{id}/verify-email
  POST /invoices/{id}/send

✗ Action as query param (avoid):
  POST /orders/{id}?action=cancel
```

---

## HTTP Status Codes

### Success codes

| Code | Meaning | When |
|---|---|---|
| 200 | OK | Successful GET, PATCH, PUT |
| 201 | Created | Successful POST that creates a resource |
| 202 | Accepted | Request accepted for async processing |
| 204 | No Content | Successful DELETE or action with no body |

### Client error codes (4xx)

| Code | Meaning | When |
|---|---|---|
| 400 | Bad Request | Malformed request; invalid syntax |
| 401 | Unauthorized | Not authenticated; valid credentials required |
| 403 | Forbidden | Authenticated but not authorised |
| 404 | Not Found | Resource does not exist |
| 405 | Method Not Allowed | HTTP method not supported on this endpoint |
| 409 | Conflict | State conflict (duplicate, version mismatch) |
| 410 | Gone | Resource permanently deleted |
| 422 | Unprocessable Entity | Semantically invalid (fails validation) |
| 429 | Too Many Requests | Rate limit exceeded |

**401 vs 403:**
- 401: "Who are you? I need credentials." (no valid session)
- 403: "I know who you are. You're not allowed." (valid session, insufficient permission)

### Server error codes (5xx)

| Code | Meaning |
|---|---|
| 500 | Internal Server Error — unexpected failure |
| 502 | Bad Gateway — upstream service returned invalid response |
| 503 | Service Unavailable — overloaded or in maintenance |
| 504 | Gateway Timeout — upstream service too slow |

---

## Error Response Schema

Consistency in error responses is critical for API usability. Use this schema everywhere:

```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "The request body contains validation errors.",
    "details": [
      {
        "field": "email",
        "code": "INVALID_FORMAT",
        "message": "Must be a valid email address."
      },
      {
        "field": "amount",
        "code": "OUT_OF_RANGE",
        "message": "Must be greater than 0."
      }
    ],
    "request_id": "req_01J3K..."
  }
}
```

**Rules:**
- `code` is a machine-readable constant (SCREAMING_SNAKE_CASE)
- `message` is human-readable English
- `details` provides field-level errors for validation failures
- `request_id` enables log correlation

**Never expose:**
- Stack traces
- Internal error messages from databases or ORMs
- File paths or internal service names
- SQL queries

---

## Pagination

Choose one pattern and use it consistently.

### Cursor-based pagination (recommended for most APIs)

```
Request:
GET /orders?cursor=eyJpZCI6MTIzfQ&limit=20

Response:
{
  "data": [...],
  "pagination": {
    "next_cursor": "eyJpZCI6MTQzfQ",
    "has_more": true
  }
}
```

**Advantages:** Stable under insertions/deletions; efficient; works with non-integer IDs.
**Disadvantages:** Cannot jump to arbitrary pages; cursor is opaque.

### Offset-based pagination (simpler, but less stable)

```
Request:
GET /orders?offset=40&limit=20

Response:
{
  "data": [...],
  "pagination": {
    "total": 247,
    "offset": 40,
    "limit": 20,
    "has_more": true
  }
}
```

**Advantages:** Can jump to any page; total count available.
**Disadvantages:** Unstable under insertions (page drift); slow at large offsets.

### Page-based pagination

```
Request:
GET /orders?page=3&per_page=20

Response:
{
  "data": [...],
  "pagination": {
    "page": 3,
    "per_page": 20,
    "total_pages": 13,
    "total_count": 247
  }
}
```

**Advantages:** Intuitive; total count available.
**Disadvantages:** Same instability as offset; SQL OFFSET is slow at depth.

---

## Filtering and Sorting

```
# Simple equality filter
GET /orders?status=pending

# Range filter
GET /orders?created_after=2025-01-01&created_before=2025-07-01

# Multiple values
GET /orders?status=pending,processing

# Sorting (prefix with - for descending)
GET /orders?sort=-created_at           (newest first)
GET /orders?sort=created_at            (oldest first)
GET /orders?sort=status,-created_at    (by status, then newest)

# Field selection
GET /orders?fields=id,status,amount,created_at
```

Avoid complex query DSLs in query parameters. If queries are very complex, consider a POST endpoint with a query body.

---

## Versioning

**Version from day one.** Introducing versioning later is far more painful.

### URL versioning (recommended)

```
https://api.example.com/v1/users
https://api.example.com/v2/users
```

Advantages: explicit, cacheable, easy to test, easy to route.

### Header versioning

```
GET /users
API-Version: 2025-07-30
```

Advantages: clean URLs.
Disadvantages: harder to test, cache, and discover.

### Versioning strategy

```
v1 → maintained indefinitely while clients exist
v2 → launched when breaking change required
     → v1 deprecated with 12-month notice
     → v1 sunset date announced
     → v1 shut down after sunset

Breaking changes (require new version):
  - Remove a field
  - Rename a field
  - Change a field's type
  - Change status code semantics
  - Remove an endpoint

Non-breaking changes (safe in existing version):
  - Add an optional field to a response
  - Add an optional field to a request
  - Add a new endpoint
  - Add a new enum value (clients must handle unknown values)
```

---

## Authentication Patterns

### Bearer token (JWT or opaque)

```
Authorization: Bearer eyJhbGciOiJSUzI1NiJ9...
```

**For machine-to-machine:** Use opaque API keys with scopes. Rotate frequently.
**For user sessions:** Use short-lived JWTs (15 min) with refresh tokens (7–30 days).

### API key in header

```
X-API-Key: sk_live_...
```

Use for: public APIs, third-party integrations, webhook delivery.

**Security requirements:**
- Keys must be long and random (min 32 bytes of entropy)
- Store hashed (bcrypt or SHA-256), not plaintext
- Scope keys to minimum required permissions
- Log all key usage for audit
- Support key rotation and immediate revocation

### OAuth 2.0 patterns

| Flow | Use case |
|---|---|
| Authorization Code + PKCE | Web and mobile apps acting on behalf of a user |
| Client Credentials | Machine-to-machine (service-to-service) |
| Device Authorization | TVs, CLI tools without a browser |

---

## Webhooks

Webhooks notify external systems of events. Design them carefully — they are fire-and-forget from your side.

### Webhook payload format

```json
{
  "id": "evt_01J3K...",
  "type": "payment.completed",
  "created_at": "2025-07-30T14:23:01.123Z",
  "data": {
    "payment_id": "pay_01J3K...",
    "amount": 5000,
    "currency": "usd",
    "status": "completed"
  }
}
```

**Fields:**
- `id` — unique event ID (enables idempotency)
- `type` — dot-namespaced event type
- `created_at` — when the event occurred
- `data` — full resource snapshot (not just the delta)

### Webhook security

```python
# Verify webhook signature
import hmac
import hashlib

def verify_webhook(payload: bytes, signature: str, secret: str) -> bool:
    expected = hmac.new(
        secret.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature)
```

Delivery requirements:
- Sign payloads with HMAC-SHA256
- Include timestamp in signature to prevent replay attacks
- Retry with exponential backoff on non-2xx responses
- Expose a delivery log and manual retry for debugging

### Webhook consumer requirements (tell your users)

- Respond 2xx quickly (< 5 seconds); process async if needed
- Validate the signature before processing
- Handle events idempotently (same event may be delivered more than once)
- Handle events out of order

---

## GraphQL Design

### Schema design principles

```graphql
# Nouns, not verbs in type names
type Order {
  id: ID!
  status: OrderStatus!
  items: [OrderItem!]!
  customer: Customer!
  createdAt: DateTime!
}

enum OrderStatus {
  PENDING
  CONFIRMED
  SHIPPED
  DELIVERED
  CANCELLED
}

# Mutations use verb-noun pattern
type Mutation {
  createOrder(input: CreateOrderInput!): CreateOrderPayload!
  cancelOrder(id: ID!): CancelOrderPayload!
}

# Payloads include the mutated object and errors
type CreateOrderPayload {
  order: Order
  errors: [UserError!]!
}

type UserError {
  field: [String!]
  message: String!
}
```

### N+1 problem in GraphQL

GraphQL's nested resolution naturally produces N+1 queries. Use DataLoader pattern:

```javascript
// DataLoader batches requests in the same tick
const userLoader = new DataLoader(async (userIds) => {
  const users = await db.query(
    'SELECT * FROM users WHERE id = ANY($1)',
    [userIds]
  );
  return userIds.map(id => users.find(u => u.id === id));
});

// Resolver — looks up one user, but DataLoader batches all concurrent lookups
const resolvers = {
  Order: {
    customer: (order) => userLoader.load(order.customerId),
  },
};
```

---

## gRPC Design

```protobuf
// payment_service.proto
syntax = "proto3";

package payments.v1;

service PaymentService {
  rpc CreatePayment (CreatePaymentRequest) returns (Payment);
  rpc GetPayment (GetPaymentRequest) returns (Payment);
  rpc ListPayments (ListPaymentsRequest) returns (ListPaymentsResponse);
  rpc CancelPayment (CancelPaymentRequest) returns (Payment);
  
  // Server-side streaming for real-time status updates
  rpc WatchPaymentStatus (WatchPaymentStatusRequest) 
    returns (stream PaymentStatusUpdate);
}

message Payment {
  string id = 1;
  int64 amount = 2;       // always use int64 for money; avoid floats
  string currency = 3;
  PaymentStatus status = 4;
  google.protobuf.Timestamp created_at = 5;
}

enum PaymentStatus {
  PAYMENT_STATUS_UNSPECIFIED = 0;   // always define 0 as UNSPECIFIED
  PAYMENT_STATUS_PENDING = 1;
  PAYMENT_STATUS_COMPLETED = 2;
  PAYMENT_STATUS_FAILED = 3;
}
```

**gRPC guidelines:**
- Always define the 0 value of enums as `UNSPECIFIED` or `UNKNOWN` — proto3 uses 0 as default
- Use `int64` for monetary amounts (cents/minor units); never `float` or `double`
- Use `google.protobuf.Timestamp` for timestamps, not strings
- Use `google.protobuf.FieldMask` for partial updates
- Define error details using `google.rpc.Status` with `details`

---

## Examples

### Example 1 — Designing a task management API

**Requirements:** Users can create projects and tasks; tasks belong to projects; tasks have status, assignee, and due date.

```
Resources:
  /projects
  /projects/{project_id}
  /projects/{project_id}/tasks
  /projects/{project_id}/tasks/{task_id}

Operations:
  POST   /projects                           → 201 { project }
  GET    /projects                           → 200 { data: [project], pagination }
  GET    /projects/{project_id}              → 200 { project } | 404
  PATCH  /projects/{project_id}              → 200 { project } | 404 | 422
  DELETE /projects/{project_id}              → 204 | 404

  POST   /projects/{project_id}/tasks        → 201 { task }
  GET    /projects/{project_id}/tasks        → 200 { data: [task], pagination }
  GET    /projects/{project_id}/tasks/{id}   → 200 { task } | 404
  PATCH  /projects/{project_id}/tasks/{id}   → 200 { task } | 404 | 422
  DELETE /projects/{project_id}/tasks/{id}   → 204 | 404
  POST   /projects/{project_id}/tasks/{id}/complete  → 200 { task }

Task shape:
  {
    "id": "tsk_01J3K...",
    "project_id": "prj_01H9...",
    "title": "Design the API",
    "status": "in_progress",
    "assignee_id": "usr_01J3K...",
    "due_at": "2025-08-15T00:00:00Z",
    "created_at": "2025-07-30T14:23:01Z",
    "updated_at": "2025-07-30T14:23:01Z"
  }
```

---

## Best Practices

- Design APIs for the client, not the implementation
- Consistent naming across all resources — enforce a style guide
- Version from day one — `/v1/` in the URL
- Return the created/updated resource in mutation responses
- Pagination is not optional for collection endpoints
- Error messages must be actionable — "Email is required" not "Bad Request"

---

## Common Mistakes

| Mistake | Consequence |
|---|---|
| Exposing internal IDs (auto-increment integers) | Enumeration attacks; leaks row count |
| Returning 200 with an error body | Clients cannot distinguish success from failure |
| Missing pagination | Collection endpoints fail at scale |
| Inconsistent naming (`userId` vs `user_id`) | Every client handles two conventions |
| Undocumented status codes | Clients cannot handle errors gracefully |
| No rate limiting | Vulnerable to abuse and DoS |
| Versioning as afterthought | Breaking changes affect all clients simultaneously |

---

## Limitations

- GraphQL and gRPC patterns are at introductory-to-intermediate depth
- Schema design for complex GraphQL relay-style APIs requires additional guidance
- Protocol Buffer schema design for complex systems benefits from specialist review
