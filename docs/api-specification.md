# API Specification

## Overview

This document describes the API endpoints used by the Preventa application for server synchronization. It defines the request/response formats, authentication methods, and error handling procedures.

## Base URL

```
https://api.preventaapp.com/v1
```

## Authentication

The API uses JSON Web Tokens (JWT) for authentication.

### Authentication Flow

1. Client requests authentication token by providing credentials
2. Server validates credentials and returns a JWT
3. Client includes JWT in the Authorization header for all subsequent requests
4. Server validates JWT for each request
5. Client refreshes JWT when it's about to expire

### Authentication Endpoints

#### POST /auth/login

Obtain a JWT by providing valid credentials.

**Request:**

```json
{
  "username": "string",
  "password": "string"
}
```

**Response (200 OK):**

```json
{
  "token": "string", // JWT token
  "expires": "string", // ISO 8601 timestamp
  "refreshToken": "string"
}
```

**Error Responses:**

- 401 Unauthorized: Invalid credentials
- 429 Too Many Requests: Rate limit exceeded

#### POST /auth/refresh

Obtain a new JWT using a refresh token.

**Request:**

```json
{
  "refreshToken": "string"
}
```

**Response (200 OK):**

```json
{
  "token": "string", // New JWT token
  "expires": "string", // ISO 8601 timestamp
  "refreshToken": "string" // New refresh token
}
```

**Error Responses:**

- 401 Unauthorized: Invalid refresh token
- 403 Forbidden: Refresh token expired

## API Endpoints

### Synchronization

#### GET /sync/status

Get the current server timestamp and sync status.

**Request Headers:**

- Authorization: Bearer {JWT token}

**Response (200 OK):**

```json
{
  "serverTime": "string", // ISO 8601 timestamp
  "lastSync": "string", // ISO 8601 timestamp or null
  "version": "string" // API version
}
```

#### POST /sync/upload

Upload local changes to the server.

**Request Headers:**

- Authorization: Bearer {JWT token}

**Request:**

```json
{
  "lastSyncDate": "string", // ISO 8601 timestamp or null for first sync
  "clientVersion": "string",
  "changes": {
    "created": [
      {
        "entityType": "string", // e.g., "cliente", "producto", etc.
        "records": [/* Array of entity objects */]
      }
    ],
    "updated": [
      {
        "entityType": "string",
        "records": [/* Array of entity objects */]
      }
    ],
    "deleted": [
      {
        "entityType": "string",
        "recordIds": [/* Array of record IDs */]
      }
    ]
  }
}
```

**Response (200 OK):**

```json
{
  "serverTime": "string", // ISO 8601 timestamp
  "results": {
    "created": [
      {
        "entityType": "string",
        "records": [/* Array of records with server IDs */]
      }
    ],
    "updated": [
      {
        "entityType": "string",
        "records": [/* Array of updated records */]
      }
    ],
    "deleted": [
      {
        "entityType": "string",
        "recordIds": [/* Array of successfully deleted record IDs */]
      }
    ],
    "errors": [
      {
        "entityType": "string",
        "recordId": "string",
        "operation": "string", // "create", "update", or "delete"
        "code": "string",
        "message": "string"
      }
    ]
  }
}
```

#### GET /sync/download

Download changes from the server since the last sync.

**Request Headers:**

- Authorization: Bearer {JWT token}

**Query Parameters:**

- lastSyncDate: ISO 8601 timestamp of the last successful sync

**Response (200 OK):**

```json
{
  "serverTime": "string", // ISO 8601 timestamp
  "changes": {
    "created": [
      {
        "entityType": "string",
        "records": [/* Array of new records */]
      }
    ],
    "updated": [
      {
        "entityType": "string",
        "records": [/* Array of updated records */]
      }
    ],
    "deleted": [
      {
        "entityType": "string",
        "recordIds": [/* Array of deleted record IDs */]
      }
    ]
  }
}
```

### Entity Endpoints

#### GET /{entityType}

Get a list of entities of the specified type.

**Path Parameters:**

- entityType: "clientes", "productos", "tarifas", "pedidos"

**Request Headers:**

- Authorization: Bearer {JWT token}

**Query Parameters:**

- page: Page number (default: 1)
- limit: Items per page (default: 20, max: 100)
- sort: Sort field (default: entity-specific)
- order: Sort order ("asc" or "desc", default: "asc")
- filter: JSON-encoded filter criteria

**Response (200 OK):**

```json
{
  "data": [/* Array of entity objects */],
  "pagination": {
    "page": "number",
    "limit": "number",
    "total": "number",
    "pages": "number"
  }
}
```

#### GET /{entityType}/{id}

Get a specific entity by ID.

**Path Parameters:**

- entityType: "clientes", "productos", "tarifas", "pedidos"
- id: Entity ID

**Request Headers:**

- Authorization: Bearer {JWT token}

**Response (200 OK):**

```json
{
  "data": {/* Entity object */}
}
```

**Error Responses:**

- 404 Not Found: Entity not found

#### POST /{entityType}

Create a new entity.

**Path Parameters:**

- entityType: "clientes", "productos", "tarifas", "pedidos"

**Request Headers:**

- Authorization: Bearer {JWT token}

**Request:**

```json
{
  /* Entity object */
}
```

**Response (201 Created):**

```json
{
  "data": {/* Created entity with ID */}
}
```

#### PUT /{entityType}/{id}

Update an existing entity.

**Path Parameters:**

- entityType: "clientes", "productos", "tarifas", "pedidos"
- id: Entity ID

**Request Headers:**

- Authorization: Bearer {JWT token}
- If-Match: Entity version (for optimistic concurrency)

**Request:**

```json
{
  /* Updated entity object */
}
```

**Response (200 OK):**

```json
{
  "data": {/* Updated entity */}
}
```

**Error Responses:**

- 404 Not Found: Entity not found
- 409 Conflict: Entity version mismatch

#### DELETE /{entityType}/{id}

Delete an entity.

**Path Parameters:**

- entityType: "clientes", "productos", "tarifas", "pedidos"
- id: Entity ID

**Request Headers:**

- Authorization: Bearer {JWT token}
- If-Match: Entity version (for optimistic concurrency)

**Response (204 No Content)**

**Error Responses:**

- 404 Not Found: Entity not found
- 409 Conflict: Entity version mismatch

## Error Handling

### Error Response Format

All API errors follow a consistent format:

```json
{
  "error": {
    "code": "string",
    "message": "string",
    "details": [/* Optional array of error details */]
  }
}
```

### Common Error Codes

| HTTP Status | Error Code | Description |
|------------|------------|-------------|
| 400 | INVALID_REQUEST | The request is malformed or contains invalid parameters |
| 401 | UNAUTHORIZED | Authentication is required or has failed |
| 403 | FORBIDDEN | The authenticated user lacks necessary permissions |
| 404 | NOT_FOUND | The requested resource was not found |
| 409 | CONFLICT | The request conflicts with the current state of the resource |
| 422 | VALIDATION_ERROR | The request contains validation errors |
| 429 | RATE_LIMITED | Too many requests, rate limit exceeded |
| 500 | SERVER_ERROR | An unexpected server error occurred |

### Validation Errors

Validation errors include details about each validation failure:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains validation errors",
    "details": [
      {
        "field": "string", // Field name
        "code": "string", // Error code (e.g., "required", "invalid")
        "message": "string" // Human-readable error message
      }
    ]
  }
}
```

## Rate Limiting

The API implements rate limiting to prevent abuse:

- Rate limits are based on the client IP and/or API key
- Limits vary by endpoint and request type
- Rate limit headers are included in all responses

### Rate Limit Headers

| Header | Description |
|--------|-------------|
| X-RateLimit-Limit | The maximum number of requests allowed in the current period |
| X-RateLimit-Remaining | The number of requests remaining in the current period |
| X-RateLimit-Reset | The time at which the current rate limit period resets (Unix timestamp) |

### Rate Limit Exceeded

When a rate limit is exceeded, the API returns a 429 Too Many Requests response:

```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded. Please try again later.",
    "details": {
      "retryAfter": 30 // Seconds to wait before retrying
    }
  }
}
```

## Versioning

The API uses URL versioning (e.g., `/v1/`, `/v2/`).

- Major version changes may include breaking changes
- Multiple versions may be supported simultaneously
- Deprecation notices will be provided before removing older versions

## Pagination

List endpoints support pagination using the following parameters:

- `page`: Page number (1-based)
- `limit`: Results per page (default varies by endpoint)

Pagination information is included in the response:

```json
{
  "data": [/* Results */],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "pages": 3
  }
}
```

## Filtering

List endpoints support filtering using a `filter` parameter containing a JSON-encoded object with filter criteria:

```
GET /v1/clientes?filter={"Inactivo":false,"IDtarifa":"123"}
```

Complex filters are supported using operators:

```
GET /v1/pedidos?filter={"FechaHora":{"$gte":"2025-01-01","$lte":"2025-12-31"}}
```

## Conclusion

This API specification provides a comprehensive guide for interacting with the Preventa application server. By following these guidelines, developers can ensure reliable data synchronization between the client application and the server.
