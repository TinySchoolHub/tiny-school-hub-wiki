# Local Testing Guide

This guide explains how to test the full TinySchoolHub stack on your local machine.

## 🚀 Recommended: Centralized Tooling

The easiest way to test the full stack (Backend + Frontend + Infrastructure) is to use the **[tiny-school-hub-tooling](../tiny-school-hub-tooling)** repository.

### 1. Prerequisites
Ensure all repositories are cloned as siblings:
- `tiny-school-hub-tooling`
- `tiny-school-hub-api-backend`
- `tiny-school-hub-frontend`

### 2. Start the Stack
```bash
cd tiny-school-hub-tooling
make up
```

This starts:
- **Frontend**: http://localhost:3000
- **API**: http://localhost:8080 (Health: `/healthz`)
- **PostgreSQL**: `localhost:5432`
- **MinIO Console**: http://localhost:9001 (minioadmin/minioadmin)
- **API Documentation**: http://localhost:8082

### 3. Seed Test Data
To populate the database with default accounts:
```bash
make seed
```

---

## 🛠️ Specialized Testing

### Test Backend Only
If you only need to work on the API:
```bash
cd tiny-school-hub-api-backend
make docker-up   # Starts DB + MinIO + API
```
Documentation and Swagger UI will be available at `localhost:8081` / `localhost:8082`.

### Test Frontend Only (with Local API)
If you want hot-reloading for frontend development while the API is running in Docker:
```bash
# 1. Start API via tooling
cd tiny-school-hub-tooling
make up

# 2. Run frontend locally
cd ../tiny-school-hub-frontend
npm install
npm run dev
```
The frontend will be available at `http://localhost:5173`.

---

## 🔍 Troubleshooting

- **Port Conflicts**: Ensure ports 3000, 8080, 5432, 9000/9001 are not occupied.
- **Logs**: Use `make logs` in the `tiny-school-hub-tooling` directory to see all service output.
- **Cleanup**: If things get messy, use `make clean` in the tooling repo to reset everything.

> [!IMPORTANT]
> Always refer to the [tiny-school-hub-tooling](../tiny-school-hub-tooling/README.md) repository for the most up-to-date orchestration commands.
