# API Reference

Complete REST API documentation for testapp-v6.

## Base URL

```
# Local Development
http://localhost:

# Kubernetes (via Service)
http://testapp-v6.default.svc.cluster.local:

# Production (via Ingress)
http://testapp-v6.example.com
```

## Endpoints

### Health Endpoints

#### Liveness Probe

Check if the application is running.

```http
GET /actuator/health/liveness
```

**Response (200 OK)**:
```json
{
  "status": "UP"
}
```

#### Readiness Probe

Check if the application is ready to accept traffic.

```http
GET /actuator/health/readiness
```

**Response (200 OK)**:
```json
{
  "status": "UP"
}
```

#### Overall Health

Get comprehensive health information.

```http
GET /actuator/health
```

**Response (200 OK)**:
```json
{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 250790436864,
        "free": 100000000000,
        "threshold": 10485760
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

### Application Endpoints

#### Hello Endpoint

Simple greeting endpoint for testing.

```http
GET /api/hello?name={name}
```

**Query Parameters:**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `name` | string | No | "World" | Name to greet |

**Example Request:**

```bash
curl http://localhost:/api/hello?name=Developer
```

**Response (200 OK)**:
```
Hello, Developer!
```

**Example Request (no parameter):**

```bash
curl http://localhost:/api/hello
```

**Response (200 OK)**:
```
Hello, World!
```

## Error Responses

### 404 Not Found

```json
{
  "timestamp": "2025-11-27T12:00:00.000+00:00",
  "status": 404,
  "error": "Not Found",
  "path": "/api/unknown"
}
```

### 500 Internal Server Error

```json
{
  "timestamp": "2025-11-27T12:00:00.000+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

## Response Headers

All API responses include:

```
Content-Type: application/json
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```

## Rate Limiting

Currently no rate limiting is enforced.

**Planned**: 1000 requests per minute per IP.

## Authentication

Currently no authentication is required.

**Planned**: OAuth 2.0 / JWT token-based authentication.

## Testing with curl

```bash
# Health check
curl -v http://localhost:/actuator/health

# Hello endpoint
curl -X GET "http://localhost:/api/hello?name=Alice"

# With headers
curl -H "Accept: text/plain" http://localhost:/api/hello
```

## Testing with HTTPie

```bash
# Health check
http GET localhost:/actuator/health

# Hello endpoint
http GET localhost:/api/hello name==Bob
```

## OpenAPI Specification

OpenAPI spec is available in the [Backstage API catalog](http://localhost:7007/catalog/default/api/testapp-v6-api).

---

**Need more details?** Check the [source code](https://github.com/kisung-backstage-demo-org/testapp-v6/blob/main/src/main/java/com/example/service/Application.java)!
