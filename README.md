# Enterprise Multi-Tenant Task Management Platform

An enterprise-ready, multi-tenant application built to streamline project and task coordination across organizations. The platform leverages modern web technologies such as Node.js, Express, React, and PostgreSQL, and is fully containerized using Docker for simplified deployment and operations.

## 🌟 Key Capabilities

* **Strict Tenant Isolation** – Ensures complete separation of data between organizations
* **Role-Based Authorization** – Three-level access control: super_admin, tenant_admin, and user
* **Secure Authentication** – JWT-based access tokens with 24-hour validity and bcrypt-based password hashing
* **Extensive REST API** – 19 production-ready endpoints supporting full CRUD workflows
* **Subscription Enforcement** – Plan-driven limits on users and projects
* **Audit Logging** – Automatic recording of all critical system events
* **Modern Web Interface** – React-based UI with protected routes and centralized state handling
* **Automated Database Setup** – Schema migrations and seed data executed automatically
* **Docker-Native Design** – Multi-container orchestration with built-in health checks

## 🏗️ Platform Architecture

```
╔═════════════════════════════════════════════════════════╗
║                 Client Layer (React SPA)                ║
║               Access URL: localhost:3000               ║
╚═════════════════════════════════════════════════════════╝
                          ↓
╔═════════════════════════════════════════════════════════╗
║             Backend Layer (Node.js / Express)           ║
║              API Endpoint: localhost:5000/api           ║
║  → RESTful API with 19 endpoints                        ║
║  → JWT authentication middleware                        ║
║  → Zod-based request validation                         ║
║  → Role-based access enforcement                        ║
╚═════════════════════════════════════════════════════════╝
                          ↓
╔═════════════════════════════════════════════════════════╗
║              Persistence Layer (PostgreSQL)             ║
║                   Port: 5432                            ║
║  → Multi-tenant data model                              ║
║  → Type-safe queries via Prisma ORM                     ║
╚═════════════════════════════════════════════════════════╝
```

## 🚀 Getting Started

### Prerequisites

* Docker Engine and Docker Compose
* Node.js 18.x or later (for local, non-containerized development)

### Start the Application

```bash
docker-compose up -d
```

Once running, the services are available at:

* **Frontend:** [http://localhost:3000](http://localhost:3000)
* **Backend API:** [http://localhost:5000/api](http://localhost:5000/api)
* **Database:** localhost:5432

### Verify Service Status

```bash
docker-compose ps
```

All containers should report a healthy or running state.

### Stop the Platform

```bash
docker-compose down
```

## 🧭 Usage Guide

### 1. Login (Demo Accounts)

Access the application at **[http://localhost:3000](http://localhost:3000)** and sign in using one of the following demo credentials:

**Super Administrator**

* Email: `superadmin@system.com`
* Password: `Admin@123`

**Tenant Administrator (Demo Org)**

* Email: `admin@demo.com`
* Password: `Demo@123`
* Tenant: `demo`

**Standard Users (Demo Org)**

* Email: `user1@demo.com` / `user2@demo.com`
* Password: `User@123`
* Tenant: `demo`

### 2. Register a New Organization

Use the registration flow to onboard a new tenant along with its initial administrator account.

### 3. Manage Users

Within the Users section, tenant administrators can:

* View all users in the organization
* Add new users with assigned roles
* Update user profiles
* Remove users

### 4. Project Operations

From the Projects section:

* Create and edit projects
* Activate or archive projects
* Permanently delete projects

### 5. Task Management

Inside a project, users can:

* Create tasks and assign priorities
* Track task progress through defined statuses
* Update or remove tasks

## 📘 API Reference

Complete API documentation is available in `docs/API.md`.

### Sample API Requests

**Authenticate User:**

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@demo.com",
    "password": "demo123",
    "tenantSubdomain": "demo"
  }'
```

**Create a Project:**

```bash
curl -X POST http://localhost:5000/api/tenants/{tenantId}/projects \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Sample Project",
    "description": "Project description",
    "status": "active"
  }'
```

## 🔐 Authentication Model

### JWT Workflow

1. User logs in and receives a JWT valid for 24 hours
2. Token is sent in the `Authorization: Bearer <token>` header
3. Middleware validates the token on every request
4. Expired tokens require re-authentication

### Token Payload Example

```json
{
  "userId": "uuid",
  "tenantId": "uuid",
  "email": "user@example.com",
  "role": "admin",
  "iat": 1234567890,
  "exp": 1234654290
}
```

## 🗄️ Data Model Overview

### Tenant

* id (UUID)
* name
* subdomain (unique)
* status
* subscription plan
* maxUsers
* maxProjects

### User

* id (UUID)
* email
* hashed password
* fullName
* role
* tenantId
* createdAt

### Project

* id (UUID)
* name
* description
* status
* tenantId
* createdAt

### Task

* id (UUID)
* title
* description
* priority
* status
* projectId
* createdAt

### Audit Log

* id (UUID)
* userId
* tenantId
* action
* entityType
* entityId
* changes
* createdAt

## 🧪 Testing

### Integration Tests

```bash
node integration-test.js
```

This validates all API endpoints using realistic workflows.

### Manual Validation

Refer to `docs/API.md` for detailed cURL examples.

## 📁 Codebase Structure

The repository is organized into clearly separated frontend, backend, and documentation modules, with Docker configuration at the root for seamless orchestration.

## 🔧 Environment Configuration

### Backend

* DATABASE_URL
* JWT_SECRET
* NODE_ENV

### Frontend

* VITE_API_URL

All variables are automatically injected when running in Docker.

## 🔒 Security Measures

* bcrypt password hashing (cost factor 10)
* JWT authentication using HS256
* Zod-based input validation
* Role-based access checks
* Tenant-aware data filtering
* Comprehensive audit logging
* Controlled CORS configuration
* Containers run as non-root users

## 📊 Subscription Tiers

| Plan       | Users     | Projects  | Access             |
| ---------- | --------- | --------- | ------------------ |
| Free       | 5         | 2         | Core functionality |
| Pro        | 50        | 10        | Full feature set   |
| Enterprise | Unlimited | Unlimited | All capabilities   |

API enforces limits and rejects overages with appropriate error responses.

## 🐳 Docker Operations

```bash
# Build and launch services
docker-compose up -d --build

# Inspect logs
docker logs backend -f
docker logs frontend -f
docker logs database -f

# Stop services
docker-compose down

# Remove volumes (destructive)
docker-compose down -v

# Rebuild backend only
docker-compose build backend

# Run backend tests
docker-compose exec backend npm test
```

## 🛠️ Troubleshooting

* **Container startup issues:** Check logs and rebuild images
* **Database connectivity errors:** Ensure database readiness before API startup
* **Frontend load failures:** Verify API URL configuration
* **401 responses:** Token may have expired; re-authenticate

## 📌 Notes

* All timestamps use UTC
* Email uniqueness is enforced per tenant
* Super admin accounts are seeded, not self-registered
* Demo data is created automatically on first startup
* Tokens are stored in localStorage for demo purposes

## 🎯 Demonstrated Features

✔ Multi-tenant architecture with strict isolation
✔ Role-based authorization
✔ JWT-based authentication
✔ Input validation on all endpoints
✔ Subscription-aware limits
✔ Automated database migrations
✔ Audit logging

✔ Protected frontend routes
✔ Dockerized deployment
✔ Health monitoring
✔ Robust error handling
✔ Type-safe backend
✔ Automated testing

## 📄 License

Provided for demonstration and evaluation purposes only.

---

**Developed using Node.js, Express, React, PostgreSQL, and Docker by LAkshmi Deepthi Chittajallu**