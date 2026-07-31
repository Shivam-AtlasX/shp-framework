# Example: REST API Design — E-commerce Order Management

**Trigger:** `SHP: Design a REST API for an e-commerce order management system. Customers can browse products, create orders, and track their status. Admins can update order status and manage inventory.`

---

## Pipeline Execution

**Complexity:** Level 3 (Complex) — API design, multi-actor system, state machine, security

**Modules activated:** api-design, cybersecurity, system-design, documentation

**Engines:** planning, multi-expert, verification, quality

---

## Response

### Resource model

```
Products       /v1/products
Orders         /v1/orders
Order Items    /v1/orders/{id}/items
Inventory      /v1/inventory  (admin only)
```

---

### Authentication and authorisation

- **Customers:** Bearer JWT token after login
- **Admins:** Bearer JWT token with `role: admin` claim
- Enforce at API gateway or middleware — never in individual route handlers

---

### Product endpoints

```
GET  /v1/products
  Query params: category, min_price, max_price, in_stock, sort, cursor, limit
  Auth: None (public)
  Response 200:
  {
    "data": [
      {
        "id": "prod_01J3K...",
        "name": "Wireless Headphones",
        "price_cents": 9999,
        "currency": "usd",
        "in_stock": true,
        "stock_count": 43,
        "images": ["https://cdn.example.com/..."],
        "categories": ["electronics", "audio"]
      }
    ],
    "pagination": { "next_cursor": "...", "has_more": true }
  }

GET  /v1/products/{product_id}
  Auth: None
  Response 200: { product object } | 404
```

---

### Order endpoints — Customer

```
POST /v1/orders
  Auth: Customer
  Body:
  {
    "items": [
      { "product_id": "prod_01J3K...", "quantity": 2 }
    ],
    "shipping_address": {
      "line1": "123 Main St",
      "city": "New York",
      "state": "NY",
      "postal_code": "10001",
      "country": "US"
    }
  }
  Response 201: { order object }
  Errors: 400 INVALID_QUANTITY, 404 PRODUCT_NOT_FOUND, 409 INSUFFICIENT_STOCK

GET /v1/orders
  Auth: Customer (own orders only)
  Query: status, cursor, limit
  Response 200: { data: [order], pagination }

GET /v1/orders/{order_id}
  Auth: Customer (own orders only)
  Response 200: { order } | 403 | 404

POST /v1/orders/{order_id}/cancel
  Auth: Customer (own orders only)
  Allowed when: status is pending or confirmed
  Response 200: { order with status: cancelled }
  Errors: 409 CANNOT_CANCEL (order already shipped)
```

---

### Order endpoints — Admin

```
GET  /v1/admin/orders
  Auth: Admin
  Query: status, customer_id, date_from, date_to, cursor, limit
  Response 200: { data: [order], pagination }

PATCH /v1/admin/orders/{order_id}
  Auth: Admin
  Body: { "status": "confirmed" | "shipped" | "delivered" | "refunded" }
  Response 200: { order }
  Errors: 409 INVALID_STATUS_TRANSITION

PATCH /v1/admin/inventory/{product_id}
  Auth: Admin
  Body: { "adjustment": 50 }  // positive = add stock, negative = remove
  Response 200: { product_id, stock_count }
```

---

### Order status state machine

```
PENDING → CONFIRMED → SHIPPED → DELIVERED
    │           │
    └───────────┴────────────→ CANCELLED
                               
    DELIVERED ──────────────→ REFUNDED

Valid transitions:
  pending    → confirmed, cancelled
  confirmed  → shipped, cancelled
  shipped    → delivered
  delivered  → refunded
```

Return 409 `INVALID_STATUS_TRANSITION` with the valid transitions in the error:

```json
{
  "error": {
    "code": "INVALID_STATUS_TRANSITION",
    "message": "Cannot transition from 'shipped' to 'cancelled'.",
    "details": {
      "current_status": "shipped",
      "attempted_status": "cancelled",
      "valid_transitions": ["delivered"]
    }
  }
}
```

---

### Order response shape

```json
{
  "id": "ord_01J3K...",
  "status": "confirmed",
  "customer_id": "cus_01H9...",
  "items": [
    {
      "id": "item_01J3K...",
      "product_id": "prod_01J3K...",
      "product_name": "Wireless Headphones",
      "quantity": 2,
      "unit_price_cents": 9999,
      "total_cents": 19998
    }
  ],
  "subtotal_cents": 19998,
  "shipping_cents": 499,
  "tax_cents": 1750,
  "total_cents": 22247,
  "currency": "usd",
  "shipping_address": { ... },
  "created_at": "2025-07-30T14:23:01Z",
  "updated_at": "2025-07-30T14:25:00Z"
}
```

---

### Security review

| Finding | Severity | Mitigation |
|---|---|---|
| Customer can access other customers' orders | Critical | Filter all queries by `customer_id` from JWT claim; never trust customer-provided customer_id |
| Customer can modify their own order to shipped | High | Admin-only status transitions enforced in middleware, not just in docs |
| Inventory adjustment is a delta, not an absolute | Medium | Use delta (not set) to prevent race conditions; wrap in DB transaction |
| Product prices could change between cart and order | Medium | Snapshot price into order item at creation time; never re-fetch live price |

---

### Idempotency for POST /v1/orders

Order creation should be idempotent to handle retries:

```
Client sends: POST /v1/orders
  Header: Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000

Server: 
  Check if idempotency key was seen before
  If yes: return cached response (don't create another order)
  If no:  create order; store key → response for 24h
```

---

### Webhook events

```
order.created      { order_id, customer_id, total_cents, status }
order.status_changed { order_id, previous_status, new_status }
order.cancelled    { order_id, reason }
inventory.low      { product_id, stock_count, threshold }
```

---

### Confidence

High. Standard e-commerce patterns. Moderate confidence on tax calculation — jurisdiction-specific rules require a dedicated tax service (Avalara, TaxJar) for production.

**Assumptions:**
- Single currency per order
- Flat-rate shipping (shipping calculation engine would change the POST body)
- Product prices are in cents to avoid floating-point money bugs

