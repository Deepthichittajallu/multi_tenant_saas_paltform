# Enterprise Task Platform – Quick Reference

## 🚀 Startup Instructions

```bash
cd "d:\GPP\Multi-Tenant SaaS Platform with Project & Task Management"
docker-compose up -d
```

Please wait approximately 10–15 seconds for all services to initialize completely.

---

## 🌐 Available Services

| Service         | URL                                                                  | Status    |
| --------------- | -------------------------------------------------------------------- | --------- |
| Web Application | [http://localhost:3000](http://localhost:3000)                       | ✅ Running |
| Backend API     | [http://localhost:5000/api](http://localhost:5000/api)               | ✅ Running |
| Health Endpoint | [http://localhost:5000/api/health](http://localhost:5000/api/health) | ✅ Running |
| PostgreSQL DB   | localhost:5432                                                       | ✅ Running |

---

## 🔐 Demo Login Credentials

### Platform Super Admin (Global Access)

```
Email: super_admin@demo.com
Password: super_admin
```

### Tenant Admin (Demo Organization)

```
Email: admin@demo.com
Password: demo123
Tenant: demo
```

### Regular User

```
Email: user@demo.com
Password: demo123
Tenant: demo
```

---

## 📱 Web Application Pages

1. **Login Page** ([http://localhost:3000](http://localhost:3000))

   * Secure authentication screen
   * Demo credentials visible
   * Option to register new organization

2. **Organization Registration** ([http://localhost:3000/register](http://localhost:3000/register))

   * Create a new tenant
   * Set administrator details
   * Configure basic organization profile

3. **Dashboard** ([http://localhost:3000/dashboard](http://localhost:3000/dashboard))

   * Overview of organization statistics
   * Quick navigation links
   * User account details

4. **User Management** ([http://localhost:3000/users](http://localhost:3000/users))

   * View all users within the tenant
   * Add new users
   * Update roles and permissions
   * Delete user accounts

5. **Project Management** ([http://localhost:3000/projects](http://localhost:3000/projects))

   * Create and edit projects
   * Change project status
   * Remove projects when required

6. **Task Management** ([http://localhost:3000/tasks](http://localhost:3000/tasks))

   * Add tasks to projects
   * Assign priority levels
   * Update task status
   * Delete tasks

---

## 📚 API Usage Samples

### Authenticate User

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@demo.com",
    "password": "demo123",
    "tenantSubdomain": "demo"
  }'
```

Use the returned token in subsequent API calls:

```
Authorization: Bearer <token>
```

### Fetch Current User

```bash
curl -X GET http://localhost:5000/api/auth/me \
  -H "Authorization: Bearer <token>"
```

### Retrieve Users

```bash
curl -X GET http://localhost:5000/api/tenants/{tenantId}/users \
  -H "Authorization: Bearer <token>"
```

### Create a Project

```bash
curl -X POST http://localhost:5000/api/tenants/{tenantId}/projects \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "My Project",
    "description": "Project description",
    "status": "active"
  }'
```

---

## 🛠️ Common Commands

### Check Container Status

```bash
docker-compose ps
```

### View Container Logs

```bash
# Backend
docker logs backend -f

# Frontend
docker logs frontend -f

# Database
docker logs database -f
```

### Execute Integration Tests

```bash
node integration-test.js
```

### Stop the Platform

```bash
docker-compose down
```

### Clean Restart

```bash
docker-compose down -v
docker-compose up -d
```

---

## 📖 Documentation Links

* **API Documentation:** docs/API.md (all 19 endpoints)
* **Setup Instructions:** README.md
* **Project Overview:** COMPLETION_SUMMARY.md
* **Submission Files:** DELIVERABLES.md
* **Metadata:** submission.json

---

## 🔑 API Overview (19 Endpoints)

### Authentication (4)

* POST /api/auth/register-tenant
* POST /api/auth/login
* GET /api/auth/me
* POST /api/auth/logout

### Tenant Management (3)

* GET /api/tenants
* GET /api/tenants/:tenantId
* PUT /api/tenants/:tenantId

### User Management (4)

* POST /api/tenants/:tenantId/users
* GET /api/tenants/:tenantId/users
* PUT /api/users/:userId
* DELETE /api/users/:userId

### Project Management (4)

* POST /api/tenants/:tenantId/projects
* GET /api/tenants/:tenantId/projects
* PUT /api/projects/:projectId
* DELETE /api/projects/:projectId

### Task Management (4)

* POST /api/projects/:projectId/tasks
* GET /api/projects/:projectId/tasks
* PUT /api/tasks/:taskId
* DELETE /api/tasks/:taskId

---

## ✨ Highlights

* ✔ 19 operational REST APIs
* ✔ Fully isolated multi-tenant design
* ✔ JWT-based authentication (24-hour expiry)
* ✔ Role-based authorization
* ✔ Request validation using Zod
* ✔ Complete audit logging
* ✔ React UI with six functional pages
* ✔ Docker-based deployment
* ✔ Automatic database migrations and seeding
* ✔ Detailed documentation

---

## 🐛 Troubleshooting Tips

### Port Conflict

```bash
# Example for port 3000
lsof -ti:3000 | xargs kill -9   # macOS/Linux
# Windows users: use Task Manager or
netstat -ano | findstr :3000
```

### Container Startup Failure

```bash
docker-compose down
docker-compose up -d --build
```

### Database Connectivity Issues

```bash
# Inspect database logs
docker-compose logs database | tail -20

# Restart services
docker-compose down
docker-compose up -d
```

### Authentication Errors (401)

* JWT token may have expired
* Re-authenticate to obtain a new token
* Confirm correct Authorization header format

---

## 📊 High-Level Architecture

```
┌───────────────┐
│ React (3000)  │
└───────┬───────┘
        │
        ↓
┌───────────────┐
│ Express (5000)│
└───────┬───────┘
        │
        ↓
┌───────────────┐
│ PostgreSQL    │
│ (5432)        │
└───────────────┘
```

---

## 📋 Repository Layout

```
.
├── frontend/
├── backend/
├── docs/
├── docker-compose.yml
├── README.md
├── submission.json
└── integration-test.js
```

---

## 🎯 How to Proceed

1. Start containers using `docker-compose up -d`

2. Open [http://localhost:3000](http://localhost:3000)
3. Login using demo credentials
4. Explore available features
5. Refer to docs/API.md for API details

---

## 📝 Additional Notes

* PostgreSQL is used for all data storage

* Authentication tokens are stored in localStorage

* Passwords are secured using bcrypt hashing

* All inputs are validated via Zod

* Audit logs record all data changes

* Super admins have global visibility

* Tenant admins are restricted to their organization

* Regular users have limited permissions

---

**The platform is fully set up and ready for use 🚀**


Start by running: `docker-compose up -d`
