# Multi-Tenant Enterprise Platform – REST API Guide

## Overview

This document describes a production-ready, multi-tenant REST API that supports project and task management at scale. The platform enforces strong tenant isolation, role-based authorization (RBAC), and subscription-based usage limits to ensure security and scalability.

**Base API URL:** `http://localhost:5000/api`

## Contents

1. Authentication Services
2. Tenant Administration
3. User Management
4. Project Services
5. Task Services
6. Error Handling Standards
7. Sample Login Credentials

---

## Authentication Services

### 1. Tenant / Organization Signup

Creates a new tenant along with its primary administrator account.

**Endpoint:** `POST /api/auth/register-tenant`

**Request Body:**

```json
{
  "tenantName": "Acme Corp",
  "subdomain": "acme",
  "adminEmail": "admin@acme.com",
  "adminPassword": "SecurePassword123!",
  "adminFullName": "John Admin"
}
```

**Response – 201 Created:**

```json
{
  "success": true,
  "message": "Tenant created successfully",
  "data": {
    "tenantId": "uuid-here",
    "tenantName": "Acme Corp",
    "adminEmail": "admin@acme.com"
  }
}
```

**Failure Scenarios:**

* `400` – Invalid input or duplicate subdomain
* `500` – Server-side failure

---

### 2. User Login

Validates credentials and returns a signed JWT token.

**Endpoint:** `POST /api/auth/login`

**Request Body:**

```json
{
  "email": "admin@demo.com",
  "password": "demo123",
  "tenantSubdomain": "demo"
}
```

**Response – 200 OK:**

```json
{
  "success": true,
  "message": "Authentication successful",
  "data": {
    "token": "jwt-token-here",
    "user": {
      "id": "uuid-here",
      "email": "admin@demo.com",
      "fullName": "Demo Admin",
      "role": "admin",
      "tenantId": "uuid-here"
    }
  }
}
```

**Token Details:**

* Format: JWT (HS256)
* Validity: 24 hours
* Header: `Authorization: Bearer <token>`

**Errors:**

* `401` – Invalid credentials
* `404` – User does not exist

---

### 3. Fetch Logged-In User

Returns profile information of the authenticated user.

**Endpoint:** `GET /api/auth/me`

**Headers:**

```
Authorization: Bearer <token>
```

**Response – 200 OK:**

```json
{
  "success": true,
  "data": {
    "id": "uuid-here",
    "email": "admin@demo.com",
    "fullName": "Demo Admin",
    "role": "admin",
    "tenantId": "uuid-here"
  }
}
```

---

### 4. Logout

Ends the user session (client-side token removal).

**Endpoint:** `POST /api/auth/logout`

**Headers:**

```
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "message": "Logout completed"
}
```

---

## Tenant Administration

### 1. Retrieve All Tenants (Super Admin)

**Endpoint:** `GET /api/tenants`

**Headers:**

```
Authorization: Bearer <super_admin_token>
```

**Query Options:**

* `page` – default 1
* `limit` – default 10

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-here",
      "name": "Demo Tenant",
      "subdomain": "demo",
      "status": "active",
      "createdAt": "2024-01-01T00:00:00Z"
    }
  ]
}
```

---

### 2. Get Tenant Information

**Endpoint:** `GET /api/tenants/:tenantId`

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "uuid-here",
    "name": "Demo Tenant",
    "subdomain": "demo",
    "status": "active",
    "subscription": {
      "plan": "pro",
      "maxUsers": 50,
      "maxProjects": 10
    }
  }
}
```

---

### 3. Update Tenant Settings

**Endpoint:** `PUT /api/tenants/:tenantId`

**Request Body:**

```json
{
  "name": "Updated Tenant Name",
  "subscription": "enterprise"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Tenant details updated"
}
```

---

## User Management

### 1. Create Tenant User

**Endpoint:** `POST /api/tenants/:tenantId/users`

**Request Body:**

```json
{
  "email": "newuser@example.com",
  "password": "SecurePassword123!",
  "fullName": "New User",
  "role": "user"
}
```

**Response – 201 Created:**

```json
{
  "success": true,
  "message": "User created",
  "data": {
    "id": "uuid-here",
    "email": "newuser@example.com",
    "role": "user"
  }
}
```

---

### 2. List Tenant Users

**Endpoint:** `GET /api/tenants/:tenantId/users`

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "uuid-here",
      "email": "admin@demo.com",
      "role": "admin"
    }
  ]
}
```

---

### 3. Modify User

**Endpoint:** `PUT /api/users/:userId`

**Request Body:**

```json
{
  "fullName": "Updated Name",
  "role": "admin"
}
```

---

### 4. Remove User

**Endpoint:** `DELETE /api/users/:userId`

---

## Project Services

### 1. Add Project

**Endpoint:** `POST /api/tenants/:tenantId/projects`

---

### 2. View Projects

**Endpoint:** `GET /api/tenants/:tenantId/projects`

---

### 3. Edit Project

**Endpoint:** `PUT /api/projects/:projectId`

---

### 4. Delete Project

**Endpoint:** `DELETE /api/projects/:projectId`

---

## Task Services

### 1. Create Task

**Endpoint:** `POST /api/projects/:projectId/tasks`

---

### 2. View Tasks

**Endpoint:** `GET /api/projects/:projectId/tasks`

---

### 3. Update Task

**Endpoint:** `PUT /api/tasks/:taskId`

---

### 4. Delete Task

**Endpoint:** `DELETE /api/tasks/:taskId`

---

## Error Handling Guidelines

**Error Response Format:**

```json
{
  "success": false,
  "message": "Description of error",
  "code": "ERROR_CODE"
}
```

**Common Status Codes:**

* 400 – Invalid request
* 401 – Unauthorized
* 403 – Forbidden
* 404 – Not found
* 500 – Server error

---

## Demo Credentials

**Super Admin**

* Email: [super_admin@demo.com](mailto:super_admin@demo.com)
* Password: super_admin

**Tenant Admin**

* Email: [admin@demo.com](mailto:admin@demo.com)
* Password: demo123
* Subdomain: demo

**Tenant User**

* Email: [user@demo.com](mailto:user@demo.com)
* Password: demo123
* Subdomain: demo

---

## Token Usage Flow

1. Register tenant
2. Login and obtain token
3. Attach token to all requests
4. Re-authenticate after token expiry (24 hours)

---

## Audit & Compliance

All state-changing actions are logged with tenant ID, user ID, timestamp, and IP address for traceability.

---

## Subscription Limits

| Plan       | Users     | Projects  | Access         |
| ---------- | --------- | --------- | -------------- |
| Free       | 5         | 2         | Core features  |
| Pro        | 50        | 10        | Advanced tools |
| Enterprise | Unlimited | Unlimited | Full access    |

---

## Support Notes

For issues, inspect API responses, status codes, and server logs before raising support requests.
