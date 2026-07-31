# Example: Multi-Tenant Authentication System

**Trigger:** `SHP: Design a multi-tenant SaaS authentication system using JWTs and refresh tokens.`

---

## Pipeline Execution

**Complexity:** Level 4 (Expert) — system design + security + architecture, high stakes

**Modules activated:** architecture, cybersecurity, system-design, api-design, coding

**Engines:** planning, multi-expert, threat-model, verification, quality, confidence

---

## Response

### Overview

A multi-tenant JWT authentication system has three primary concerns that must be designed together: **token issuance**, **tenant isolation**, and **token lifecycle management**.

---

### Architecture

```
[Client]
   │
   ▼
[API Gateway]  ← validates JWT on every request
   │
   ▼
[Auth Service]  ← issues and refreshes tokens
   │        │
   │        └──► [Token Store]  (Redis — refresh token storage)
   │
   └──► [User Store]  (PostgreSQL — users, credentials, tenant membership)
```

---

### Token Design

**Access Token (JWT)**
- Short-lived: 15 minutes
- Signed: RS256 (asymmetric — public key distributed to all services)
- Claims: `sub` (user ID), `tenant_id`, `roles`, `exp`, `iat`, `jti`

**Refresh Token**
- Long-lived: 7–30 days (configurable per tenant)
- Opaque random token (not JWT) — stored server-side in Redis
- One-use: rotation on every refresh

```json
{
  "sub": "usr_01J3K...",
  "tenant_id": "ten_01H9...",
  "roles": ["member"],
  "exp": 1722000000,
  "iat": 1721999100,
  "jti": "tok_01J3K..."
}
```

---

### Tenant Isolation

Each JWT carries `tenant_id`. Every service validates:
1. JWT signature (valid issuer)
2. Token not expired
3. `tenant_id` matches the resource being accessed

Database rows are partitioned by `tenant_id` with row-level policies (PostgreSQL RLS) as a defence-in-depth measure.

---

### API Contract

```
POST /auth/login
POST /auth/refresh
POST /auth/logout
POST /auth/logout-all   ← revoke all sessions for user
GET  /auth/me
```

---

### Token Lifecycle

```
Login → [Issue access token + refresh token]
           │
           ▼ (15 min later)
Access token expires → [Client calls /auth/refresh]
           │
           ▼
[Validate refresh token] → [Issue new access token + rotate refresh token]
           │
           ▼ (30 days later, or on logout)
Refresh token expired/revoked → [Force re-login]
```

---

### Security Review

| Severity | Finding | Mitigation |
|---|---|---|
| High | Refresh token theft | Rotation on use; revoke all on suspicious reuse |
| Medium | JWT leakage via logs | Never log token contents; only log `jti` |
| Medium | Clock skew | 30-second clock skew tolerance in validation |
| Low | Token enumeration | Use unpredictable IDs (ULID/UUID v7) |

**Refresh token reuse detection:** If a refresh token is used after rotation, revoke the entire token family for that user (all sessions). This detects theft.

---

### Confidence

High for the overall design pattern. Moderate for specific Redis TTL configuration — this depends on your security vs usability trade-off preferences.

**Assumptions:**
- Stateless access tokens are acceptable (token revocation within 15-minute window is tolerable)
- If immediate token revocation is required, shift to stateful short-lived tokens or a token denylist in Redis

