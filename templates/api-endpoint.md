# [Resource Name] API

## Overview
[What this API provides]

## Authentication
[Authentication method required]

## Endpoints

### [HTTP Method] [Path]

**Purpose:** [What this endpoint does]

**Request:**
```
Headers:
  Authorization: Bearer <token>
  Content-Type: application/json

Body:
{
  "field": "type — description",
  "field2": "type — description (optional)"
}
```

**Response:**
```
Status: 201 Created

Body:
{
  "id": "string",
  "field": "value"
}
```

**Error Responses:**
| Status | Code | When |
|---|---|---|
| 400 | VALIDATION_FAILED | Invalid input |
| 401 | UNAUTHORIZED | Missing or invalid token |
| 404 | NOT_FOUND | Resource does not exist |
| 409 | CONFLICT | State conflict |
