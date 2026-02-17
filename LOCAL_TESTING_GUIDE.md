# Local Testing Guide

## Quick Start: Test Backend + Frontend Together

### 1. Start Backend Services
```bash
cd tiny-school-hub-api-backend
make docker-up
```
This starts:
- PostgreSQL database on `localhost:5432`
- MinIO storage on `localhost:9000` (console on `localhost:9001`)
- Backend API on `localhost:8080`
- Swagger UI on `localhost:8081`
- ReDoc on `localhost:8082`

### 2. Start Frontend (Hot Reload)
```bash
cd ../tiny-school-hub-frontend
npm install  # if not done already
npm run dev
```
Frontend will be available at `http://localhost:5173`

### 3. Test Your Features
1. **Login**: Go to `http://localhost:5173` and login with teacher credentials
2. **Create Class**: Navigate to Classes page and create a new class
3. **Add Students**: Click on the class and add students
4. **Test CRUD**: Try updating and deleting classes/students

### 4. Check Backend Logs
```bash
cd tiny-school-hub-api-backend
docker-compose logs -f api
```

### 5. Stop Services
```bash
# Stop backend
cd tiny-school-hub-api-backend
make docker-down

# Frontend stops with Ctrl+C
```

---

## Option 2: Test Backend Only (API Testing)

If you just want to test backend endpoints:

### 1. Start Backend
```bash
cd tiny-school-hub-api-backend
make docker-up
```

### 2. Use Swagger UI
Open `http://localhost:8081` in your browser for interactive API testing.

### 3. Or Use curl/Postman
```bash
# Get auth token first
TOKEN=$(curl -X POST http://localhost:8080/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"teacher@example.com","password":"password123"}' \
  | jq -r '.access_token')

# Test: Create a class
curl -X POST http://localhost:8080/api/v1/classes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Math 101","grade_level":"5th"}'

# Test: List classes
curl http://localhost:8080/api/v1/classes \
  -H "Authorization: Bearer $TOKEN"

# Test: Add student to class
CLASS_ID="<your-class-id>"
curl -X POST http://localhost:8080/api/v1/classes/$CLASS_ID/profiles \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "child_name":"John Doe",
    "parent_name":"Jane Doe",
    "email":"jane.doe@example.com"
  }'

# Test: List students in class
curl http://localhost:8080/api/v1/classes/$CLASS_ID/profiles \
  -H "Authorization: Bearer $TOKEN"

# Test: Update class
curl -X PUT http://localhost:8080/api/v1/classes/$CLASS_ID \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Advanced Math 101","grade_level":"6th"}'

# Test: Delete class
curl -X DELETE http://localhost:8080/api/v1/classes/$CLASS_ID \
  -H "Authorization: Bearer $TOKEN"
```

---

## Option 3: Full Docker Stack (Both Services)

Run both frontend and backend in Docker:

### 1. Build and Start Everything
```bash
# Start backend services
cd tiny-school-hub-api-backend
make docker-up

# Start frontend (wait for backend to be ready)
cd ../tiny-school-hub-frontend
docker-compose -f docker-compose.dev.yml up -d
```

### 2. Access
- Frontend: `http://localhost:3000`
- Backend API: `http://localhost:8080`
- Swagger: `http://localhost:8081`
- MinIO Console: `http://localhost:9001` (minioadmin/minioadmin)

### 3. Stop Everything
```bash
cd tiny-school-hub-api-backend
make docker-down

cd ../tiny-school-hub-frontend
docker-compose -f docker-compose.dev.yml down
```

---

## Troubleshooting

### Backend won't start
```bash
# Check if ports are in use
lsof -i :8080  # API
lsof -i :5432  # Postgres

# View backend logs
cd tiny-school-hub-api-backend
docker-compose logs api
```

### Frontend can't connect to backend
1. Check backend is running: `curl http://localhost:8080/health`
2. Check CORS settings in backend
3. Verify `VITE_API_BASE_URL` in frontend `.env` file

### Database issues
```bash
# Reset database (WARNING: deletes all data)
cd tiny-school-hub-api-backend
docker-compose down -v  # -v removes volumes
docker-compose up -d
```

### Need test users?
Check if your backend has a seed script:
```bash
cd tiny-school-hub-api-backend
make seed
```

---

## Development Workflow

1. **Make backend changes** → Backend auto-rebuilds in Docker
2. **Make frontend changes** → Vite hot-reloads automatically
3. **Test immediately** in browser at `localhost:5173`
4. **Check API docs** at `localhost:8081` (Swagger)
5. **View logs** with `docker-compose logs -f api`

No need to push to Git or go through CI/CD!
