# API Documentation

## Base URL
```
Development: http://localhost:5000/api
Production: https://api.gamingwellness.com/api
```

## Authentication

All endpoints (except login/signup) require JWT token in Authorization header:
```
Authorization: Bearer <token>
```

### Token Format
JWT tokens are valid for 30 days and include:
- User information (id, email, role)
- Expiration time

## Response Format

### Success Response
```json
{
  "success": true,
  "message": "Operation successful",
  "data": {}
}
```

### Error Response
```json
{
  "success": false,
  "message": "Error description",
  "error": "Detailed error message"
}
```

## Status Codes
- `200` - OK
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `500` - Server Error

---

## Authentication Endpoints

### POST /auth/parent-signup
Register a new parent account.

**Request:**
```json
{
  "email": "parent@example.com",
  "password": "SecurePassword123",
  "firstName": "John",
  "lastName": "Doe",
  "phone": "+1 (555) 123-4567"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Parent registered successfully",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": 1,
    "email": "parent@example.com",
    "role": "parent"
  }
}
```

**Errors:**
- `400` - Email already registered
- `400` - Missing required fields

---

### POST /auth/parent-login
Login as parent.

**Request:**
```json
{
  "email": "parent@example.com",
  "password": "SecurePassword123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": 1,
    "email": "parent@example.com",
    "role": "parent"
  }
}
```

**Errors:**
- `401` - Invalid credentials
- `400` - Missing email or password

---

### POST /auth/child-signup
Register a new child (parent authenticated).

**Headers:**
```
Authorization: Bearer <parent_token>
```

**Request:**
```json
{
  "email": "child@example.com",
  "password": "ChildPassword123",
  "firstName": "Jane",
  "lastName": "Doe",
  "age": 10
}
```

**Response:**
```json
{
  "success": true,
  "message": "Child account created successfully",
  "child": {
    "id": 5,
    "email": "child@example.com",
    "firstName": "Jane",
    "lastName": "Doe",
    "age": 10
  }
}
```

---

### POST /auth/child-login
Login as child.

**Request:**
```json
{
  "email": "child@example.com",
  "password": "ChildPassword123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": 5,
    "email": "child@example.com",
    "role": "child"
  }
}
```

---

### GET /auth/verify
Verify current token is valid.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "user": {
    "id": 1,
    "email": "parent@example.com",
    "role": "parent"
  }
}
```

---

### POST /auth/logout
Logout current user.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:**
```json
{
  "success": true,
  "message": "Logout successful"
}
```

---

## Games Endpoints

### GET /games
Get list of available games.

**Response:**
```json
{
  "success": true,
  "games": [
    {
      "id": 1,
      "name": "Memory Challenge",
      "description": "Test your memory with matching pairs",
      "type": "memory",
      "difficulty": "easy",
      "recommended_age": "6+"
    }
  ]
}
```

---

### POST /games/start-session
Start a new gaming session (child authenticated).

**Headers:**
```
Authorization: Bearer <child_token>
```

**Request:**
```json
{
  "gameType": "memory",
  "gameName": "Memory Challenge"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Gaming session started",
  "session": {
    "sessionId": 42,
    "gameType": "memory",
    "gameName": "Memory Challenge",
    "remainingTime": 90,
    "startTime": "2024-03-09T10:30:00Z"
  }
}
```

**Errors:**
- `400` - Daily gaming limit exceeded
- `400` - Game type not provided

---

### POST /games/end-session
End a gaming session (child authenticated).

**Headers:**
```
Authorization: Bearer <child_token>
```

**Request:**
```json
{
  "sessionId": 42,
  "score": 850,
  "wellnessScore": 0.85
}
```

**Response:**
```json
{
  "success": true,
  "message": "Session ended successfully",
  "session": {
    "durationMinutes": 25,
    "score": 850,
    "wellnessScore": 0.85,
    "alertTriggered": false
  }
}
```

---

### GET /games/my-sessions
Get child's recent gaming sessions (child authenticated).

**Response:**
```json
{
  "success": true,
  "sessions": [
    {
      "id": 42,
      "game_type": "memory",
      "duration_minutes": 25,
      "score": 850,
      "wellness_score": 0.85,
      "start_time": "2024-03-09T10:30:00Z",
      "alert_triggered": false
    }
  ]
}
```

---

## User Endpoints

### GET /users/parent/profile
Get parent profile and children list (parent authenticated).

**Response:**
```json
{
  "success": true,
  "parent": {
    "id": 1,
    "email": "parent@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "+1 (555) 123-4567"
  },
  "children": [
    {
      "id": 5,
      "first_name": "Jane",
      "last_name": "Doe",
      "age": 10,
      "email": "jane@example.com"
    }
  ]
}
```

---

### GET /users/child/profile
Get child profile (child authenticated).

**Response:**
```json
{
  "success": true,
  "child": {
    "id": 5,
    "email": "jane@example.com",
    "first_name": "Jane",
    "last_name": "Doe",
    "age": 10,
    "gaming_limit_minutes": 120,
    "todayGameTime": 45
  }
}
```

---

### GET /users/parent/children
Get all children with analytics (parent authenticated).

**Response:**
```json
{
  "success": true,
  "children": [
    {
      "id": 5,
      "first_name": "Jane",
      "last_name": "Doe",
      "age": 10,
      "email": "jane@example.com",
      "gaming_limit_minutes": 120,
      "total_gaming_minutes": 450,
      "total_score": 5200,
      "total_sessions": 18
    }
  ]
}
```

---

### PUT /users/child/{childId}/gaming-limit
Update child's gaming time limit (parent authenticated).

**Request:**
```json
{
  "gamingLimitMinutes": 90
}
```

**Response:**
```json
{
  "success": true,
  "message": "Gaming limit updated successfully"
}
```

---

## Alert Endpoints

### GET /alerts/parent/alerts
Get all alerts for parent (parent authenticated).

**Response:**
```json
{
  "success": true,
  "alerts": [
    {
      "id": 1,
      "child_id": 5,
      "alert_type": "HIGH_DURATION",
      "message": "Child played for 95 minutes today!",
      "severity": "HIGH",
      "is_read": false,
      "created_at": "2024-03-09T14:20:00Z",
      "first_name": "Jane",
      "last_name": "Doe"
    }
  ]
}
```

---

### PUT /alerts/alert/{alertId}/read
Mark alert as read (parent authenticated).

**Response:**
```json
{
  "success": true,
  "message": "Alert marked as read"
}
```

---

### GET /alerts/parent/unread-count
Get count of unread alerts (parent authenticated).

**Response:**
```json
{
  "success": true,
  "unreadCount": 3
}
```

---

## Analytics Endpoints

### GET /analytics/child/{childId}
Get detailed analytics for a child (parent authenticated).

**Response:**
```json
{
  "success": true,
  "child": {
    "id": 5,
    "name": "Jane",
    "age": 10
  },
  "analytics": {
    "id": 1,
    "child_id": 5,
    "total_sessions": 18,
    "total_gaming_minutes": 450,
    "average_session_duration": 25,
    "total_score": 5200,
    "wellness_trend": 0.75
  },
  "weeklyData": [
    {
      "date": "2024-03-09",
      "minutes": 120,
      "sessions": 3
    }
  ],
  "gameBreakdown": [
    {
      "game_type": "memory",
      "count": 8,
      "total_minutes": 200
    }
  ]
}
```

---

### GET /analytics/my-analytics
Get own analytics (child authenticated).

**Response:**
```json
{
  "success": true,
  "analytics": {
    "total_sessions": 18,
    "total_gaming_minutes": 450,
    "average_session_duration": 25,
    "total_score": 5200,
    "wellness_trend": 0.75
  },
  "today": {
    "total": 45,
    "count": 2
  },
  "recentSessions": [
    {
      "game_type": "memory",
      "duration_minutes": 25,
      "score": 850,
      "wellness_score": 0.85
    }
  ]
}
```

---

### GET /analytics/child/{childId}/wellness-prediction
Get AI-powered wellness predictions (parent authenticated).

**Response:**
```json
{
  "success": true,
  "predictions": {
    "nextDayDuration": 45,
    "wellnessRisk": "LOW",
    "recommendedLimit": 120,
    "trend": "IMPROVING"
  },
  "sessionCount": 18
}
```

---

## Example Usage

### Complete Parent Flow

**1. Register:**
```bash
curl -X POST http://localhost:5000/api/auth/parent-signup \
  -H "Content-Type: application/json" \
  -d '{
    "email": "parent@example.com",
    "password": "SecurePassword123",
    "firstName": "John",
    "lastName": "Doe"
  }'
```

**2. Create Child:**
```bash
curl -X POST http://localhost:5000/api/auth/child-signup \
  -H "Authorization: Bearer <parent_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "child@example.com",
    "password": "ChildPass123",
    "firstName": "Jane",
    "lastName": "Doe",
    "age": 10
  }'
```

**3. Get Children:**
```bash
curl -X GET http://localhost:5000/api/users/parent/children \
  -H "Authorization: Bearer <parent_token>"
```

**4. View Alerts:**
```bash
curl -X GET http://localhost:5000/api/alerts/parent/alerts \
  -H "Authorization: Bearer <parent_token>"
```

### Complete Child Flow

**1. Login:**
```bash
curl -X POST http://localhost:5000/api/auth/child-login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "child@example.com",
    "password": "ChildPass123"
  }'
```

**2. Get Profile:**
```bash
curl -X GET http://localhost:5000/api/users/child/profile \
  -H "Authorization: Bearer <child_token>"
```

**3. Start Gaming:**
```bash
curl -X POST http://localhost:5000/api/games/start-session \
  -H "Authorization: Bearer <child_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "gameType": "memory",
    "gameName": "Memory Challenge"
  }'
```

**4. End Session:**
```bash
curl -X POST http://localhost:5000/api/games/end-session \
  -H "Authorization: Bearer <child_token>" \
  -H "Content-Type: application/json" \
  -d '{
    "sessionId": 42,
    "score": 850,
    "wellnessScore": 0.85
  }'
```

---

## Rate Limiting

Current limits per user:
- Login attempts: 5 per minute
- API calls: 100 per minute
- Session creation: 20 per day

## CORS Headers

All responses include:
```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

## Error Messages

| Error | Meaning | Fix |
|-------|---------|-----|
| Invalid credentials | Wrong email/password | Check credentials |
| No token provided | Missing Authorization header | Add token to header |
| Invalid token | Expired or malformed token | Re-login to get new token |
| Daily gaming limit exceeded | Child has used all time | Update limit or wait for reset |
| Unauthorized | Insufficient permissions | Check user role |

---

For more information, refer to `README.md` or contact API support.
