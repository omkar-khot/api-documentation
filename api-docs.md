# API Documentation

## Overview

This document provides detailed information about the RESTful API endpoints available in the Capstone DevOps Backend application.

- **Base URL:** `http://localhost:8080` (Development)
- **Production URL:** `https://<your-domain>/api`
- **API Version:** v1.0.0
- **Content Type:** `application/json`

---

## Table of Contents

- [Authentication](#authentication)
- [Endpoints](#endpoints)
  - [Health Check](#health-check)
  - [Tasks API](#tasks-api)
- [Error Handling](#error-handling)
- [Status Codes](#status-codes)
- [Request/Response Examples](#requestresponse-examples)
- [Data Models](#data-models)
- [Testing the API](#testing-the-api)
- [Rate Limiting](#rate-limiting)
- [CORS Configuration](#cors-configuration)
- [API Versioning](#api-versioning)
- [Monitoring & Observability](#monitoring--observability)
- [Performance Considerations](#performance-considerations)
- [Security Considerations](#security-considerations)
- [Best Practices](#best-practices)
- [Migration Guide](#migration-guide)
- [API Changelog](#api-changelog)
- [Support & Contact](#support--contact)
- [Additional Resources](#additional-resources)
- [Appendix](#appendix)
- [License](#license)
- [Document Version](#document-version)

---

## Authentication

Currently, the API does not require authentication.  
**Future:** JWT-based authentication.

**Planned Authentication Headers:**
```
Authorization: Bearer <jwt-token>
```

---

## Endpoints

### Health Check

#### Get Application Health

**Endpoint:** `GET /actuator/health`

**Response:**
```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "PostgreSQL",
        "validationQuery": "isValid()"
      }
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 499963174912,
        "free": 123456789012,
        "threshold": 10485760
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

#### Get Application Metrics

**Endpoint:** `GET /actuator/prometheus`  
**Response:** Prometheus-formatted metrics.

---

## Tasks API

### 1. Get All Tasks

**Endpoint:** `GET /api/tasks`

**Example Request:**
```http
GET /api/tasks HTTP/1.1
Host: localhost:8080
Accept: application/json
```

**Example Response:**
```json
[
  {
    "id": 1,
    "title": "Complete DevOps Project",
    "description": "Finish setting up the entire DevOps pipeline",
    "status": "IN_PROGRESS",
    "createdAt": "2025-11-08T10:30:00",
    "updatedAt": "2025-11-11T14:20:00"
  },
  {
    "id": 2,
    "title": "Deploy to Production",
    "description": "Deploy the application to AWS EKS production environment",
    "status": "TODO",
    "createdAt": "2025-11-09T09:15:00",
    "updatedAt": "2025-11-09T09:15:00"
  }
]
```

**Response Codes:**
- `200 OK` – Success
- `500 Internal Server Error` – Server error

---

### 2. Get Task by ID

**Endpoint:** `GET /api/tasks/{id}`

| Parameter | Type   | Required | Description            |
|-----------|--------|----------|------------------------|
| id        | Long   | Yes      | Unique task identifier |

**Example Request:**
```http
GET /api/tasks/1 HTTP/1.1
Host: localhost:8080
Accept: application/json
```

**Example Response:**
```json
{
  "id": 1,
  "title": "Complete DevOps Project",
  "description": "Finish setting up the entire DevOps pipeline",
  "status": "IN_PROGRESS",
  "createdAt": "2025-11-08T10:30:00",
  "updatedAt": "2025-11-11T14:20:00"
}
```

**Response Codes:**
- `200 OK` – Task found
- `404 Not Found` – Task does not exist
- `500 Internal Server Error` – Server error

---

### 3. Create New Task

**Endpoint:** `POST /api/tasks`

**Request Headers:**
```
Content-Type: application/json
```

**Request Body:**
```json
{
  "title": "Setup Monitoring",
  "description": "Configure Prometheus and Grafana for monitoring",
  "status": "TODO"
}
```

| Field       | Type   | Required | Constraints                       |
|-------------|--------|----------|-----------------------------------|
| title       | String | Yes      | 3-100 characters, not blank       |
| description | String | No       | Max 500 characters                |
| status      | String | Yes      | TODO, IN_PROGRESS, or DONE        |

**Example Response:**
```json
{
  "id": 3,
  "title": "Setup Monitoring",
  "description": "Configure Prometheus and Grafana for monitoring",
  "status": "TODO",
  "createdAt": "2025-11-11T18:00:00",
  "updatedAt": "2025-11-11T18:00:00"
}
```

**Response Codes:**
- `200 OK` – Task created
- `400 Bad Request` – Invalid input
- `500 Internal Server Error` – Server error

---

### 4. Update Task

**Endpoint:** `PUT /api/tasks/{id}`

| Parameter | Type   | Required | Description            |
|-----------|--------|----------|------------------------|
| id        | Long   | Yes      | Unique task identifier |

**Request Body:**
```json
{
  "title": "Complete DevOps Project",
  "description": "Finish setting up the entire DevOps pipeline including monitoring",
  "status": "DONE"
}
```

**Response Codes:**
- `200 OK` – Task updated
- `404 Not Found` – Task does not exist
- `400 Bad Request` – Invalid input
- `500 Internal Server Error` – Server error

---

### 5. Delete Task

**Endpoint:** `DELETE /api/tasks/{id}`

**Response:** `200 OK`
```json
{}
```

**Response Codes:**
- `200 OK` – Task deleted
- `404 Not Found` – Task does not exist
- `500 Internal Server Error` – Server error

---

## Error Handling

### Error Response Format

All error responses follow a consistent structure:

```json
{
  "timestamp": "2025-11-11T18:10:00",
  "status": 404,
  "error": "Not Found",
  "message": "Task with ID 999 not found",
  "path": "/api/tasks/999"
}
```

---

## Status Codes

| Status Code | Description                          |
|-------------|--------------------------------------|
| 200         | OK - Request successful              |
| 201         | Created - Resource created           |
| 400         | Bad Request - Invalid input          |
| 404         | Not Found - Resource not found       |
| 500         | Internal Server Error                |
| 503         | Service Unavailable                  |

---

## Request/Response Examples

### Using cURL

**Get All Tasks:**
```bash
curl -X GET http://localhost:8080/api/tasks
```

**Create Task:**
```bash
curl -X POST http://localhost:8080/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Task","description":"Testing API","status":"TODO"}'
```

**Update Task:**
```bash
curl -X PUT http://localhost:8080/api/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"title":"Updated Task","description":"Updated description","status":"DONE"}'
```

**Delete Task:**
```bash
curl -X DELETE http://localhost:8080/api/tasks/1
```

---

## Data Models

### Task Model

```json
{
  "id": "Long",
  "title": "String",
  "description": "String",
  "status": "String",
  "createdAt": "DateTime",
  "updatedAt": "DateTime"
}
```

### Task Status Values

| Value       | Description                     |
|-------------|---------------------------------|
| TODO        | Pending/not started             |
| IN_PROGRESS | Being worked on                 |
| DONE        | Completed                       |

---

## Testing the API

### Using Postman

1. Import Collection: Create a new collection named "Capstone API"
2. Set Base URL: Configure environment variable `{{base_url}}` = `http://localhost:8080`
3. Add Requests: Create requests for each endpoint
4. Save Tests: Add test scripts to validate responses

---

## Rate Limiting

Not yet implemented. Planned:
- 100 requests/min per IP
- Response headers will include:
  - `X-RateLimit-Limit`
  - `X-RateLimit-Remaining`
  - `X-RateLimit-Reset`

---

## CORS Configuration

- Accepts all origins:  
  ```java
  @CrossOrigin(origins = "*")
  ```
- For production, restrict:
  ```java
  @CrossOrigin(origins = {"https://your-frontend-domain.com"})
  ```

---

## API Versioning

- **Current:** v1 (no prefix)
- **Future:** `/api/v2/tasks` or via header `Accept: application/vnd.capstone.v2+json`

---

## Monitoring & Observability

### Actuator Endpoints

| Endpoint                | Description               |
|-------------------------|---------------------------|
| `/actuator/health`      | Health check              |
| `/actuator/info`        | App info                  |
| `/actuator/metrics`     | App metrics               |
| `/actuator/prometheus`  | Prometheus metrics        |

### Metrics Available

- JVM, Database, HTTP, Custom task metrics

### Example Metrics Query

```bash
curl http://localhost:8080/actuator/metrics
curl http://localhost:8080/actuator/metrics/jvm.memory.used
```

---

## Performance Considerations

### Response Times

| Endpoint               | Avg   | Max   |
|------------------------|-------|-------|
| GET /api/tasks         | 50ms  | 200ms |
| GET /api/tasks/{id}    | 30ms  | 100ms |
| POST /api/tasks        | 80ms  | 300ms |
| PUT /api/tasks/{id}    | 70ms  | 250ms |
| DELETE /api/tasks/{id} | 40ms  | 150ms |

---

## Security Considerations

**Warning:** Current API lacks authentication/authorization.

### Planned Features

1. **JWT Auth:** Tokens, refresh, expiry 1h
2. **RBAC:** Admin/User/Guest roles
3. **HTTPS only:** SSL/TLS
4. **Input Validation:** SQLi, XSS, request size
5. **API Keys:** For integrations, with rotation

---

## Best Practices

- Always include `Content-Type: application/json`
- Handle errors gracefully
- Use idempotent operations where appropriate
- Validate input data on client side

---

## Migration Guide

- v0.9 to v1.0: No breaking changes
- v2.0 (planned): Auth required, metadata wrappers, ISO8601 date+tz

---

## API Changelog

### 1.0.0 (Nov 2025)
- CRUD for Tasks, actuator, CORS, no auth/pagination, limited errors

### 0.9.0 (Nov 2025)
- Initial implementation, basic CRUD

---

## Support & Contact

- **Issues/Bugs:**  
  GitHub issues: `https://github.com/omkar-khot/capstone-devops-project/issues`
- **API questions:**  
  1. Check this doc  
  2. See `docs/runbook.md`  
  3. Open GitHub issue, use label: `api`
- **Architecture:**  
  See `docs/architecture.md`

---

## Additional Resources

- [Architecture Overview](./architecture.md)
- [Operational Runbook](./runbook.md)
- [README](../README.md)
- [Spring Boot Actuator Documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)
- [RESTful API Design Best Practices](https://restfulapi.net/)
- [HTTP Status Codes](https://httpstatuses.com/)

---

## Appendix

### API Endpoint Summary

| Method | Endpoint                | Description             | Auth Required |
|--------|-------------------------|-------------------------|---------------|
| GET    | /api/tasks              | Get all tasks           | No            |
| GET    | /api/tasks/{id}         | Get task by ID          | No            |
| POST   | /api/tasks              | Create new task         | No            |
| PUT    | /api/tasks/{id}         | Update task by ID       | No            |
| DELETE | /api/tasks/{id}         | Delete task by ID       | No            |
| GET    | /actuator/health        | Health check            | No            |
| GET    | /actuator/metrics       | Get metrics             | No            |
| GET    | /actuator/prometheus    | Prometheus metrics      | No            |

---

## License

This API is part of the Capstone DevOps Project.  
© 2025 DevOps Capstone Team. All rights reserved.

---

## Document Version

**Version:** 1.0.0  
**Last Updated:** November 15, 2025  
**Maintained By:** DevOps Team  
**Review Date:** December 2025

---

**End of API Documentation**
