# Authentication API Documentation

## Base URL
```
http://localhost:8000/api
```

## Authentication Methods

All protected endpoints require the Authorization header:
```
Authorization: Bearer <access_token>
```

## Endpoints

### 1. User Registration
**Register a new user account**

```http
POST /users/register/
Content-Type: application/json

{
    "username": "john_doe",
    "email": "john@example.com",
    "password": "SecurePass123",
    "password2": "SecurePass123",
    "first_name": "John",
    "last_name": "Doe"
}
```

**Success Response (201 Created):**
```json
{
    "user": {
        "id": 1,
        "username": "john_doe",
        "email": "john@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "phone": "",
        "address": "",
        "city": "",
        "state": "",
        "postal_code": "",
        "country": "",
        "profile_image": null
    },
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
    "message": "User registered successfully"
}
```

**Error Response (400 Bad Request):**
```json
{
    "username": ["Username already exists"],
    "email": ["user with this email already exists."],
    "password": ["Password must be at least 8 characters"],
    "password2": ["Passwords do not match"]
}
```

**Validation Rules:**
- Username: minimum 3 characters, unique, no spaces
- Email: valid email format, unique
- Password: minimum 8 characters, not all numeric, not common passwords
- Password2: must match password exactly

---

### 2. User Login
**Authenticate user and receive JWT tokens**

```http
POST /users/login/
Content-Type: application/json

{
    "username": "john_doe",
    "password": "SecurePass123"
}
```

**Success Response (200 OK):**
```json
{
    "user": {
        "id": 1,
        "username": "john_doe",
        "email": "john@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "phone": "",
        "address": "",
        "city": "",
        "state": "",
        "postal_code": "",
        "country": "",
        "profile_image": null
    },
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ0b2tlbl90eXBlIjoiYWNjZXNzIiwicGtvIjoiYjM5MGM2OGFmMzA5ZjIzOWZjOTI5NmEwIiwiaWF0IjoxNjc4OTAyNDQyLCJleHAiOjE2Nzg5MDYwNDJ9.kFkj8nGfKh5j8gKsJ0jK9xLmNoPqRsT1vWxYz2aBcDe",
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ0b2tlbl90eXBlIjoicmVmcmVzaCIsInBrbyI6ImIzOTBjNjhhZjMwOWYyMzlmYzkyOTZhMCIsImlhdCI6MTY3ODkwMjQ0MiwiZXhwIjoxNjc5NTA3MjQyfQ.bkL5mN6oPqRsT1vWxYz2aBcDeJ8gKsJ0jK9xLmNoPqRs",
    "message": "Login successful"
}
```

**Error Response (400 Bad Request):**
```json
{
    "non_field_errors": ["Invalid username or password"]
}
```

**Token Details:**
- `access`: Valid for 1 hour. Use for API requests.
- `refresh`: Valid for 7 days. Use to get new access token when it expires.

---

### 3. Get User Profile
**Retrieve the current authenticated user's profile**

```http
GET /users/profile/
Authorization: Bearer <access_token>
```

**Success Response (200 OK):**
```json
{
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "",
    "address": "",
    "city": "",
    "state": "",
    "postal_code": "",
    "country": "",
    "profile_image": null
}
```

**Error Response (401 Unauthorized):**
```json
{
    "detail": "Authentication credentials were not provided."
}
```

Or with invalid token:
```json
{
    "detail": "Invalid token."
}
```

---

### 4. Update User Profile
**Update the current user's profile information**

```http
PUT /users/update_profile/
Authorization: Bearer <access_token>
Content-Type: application/json

{
    "first_name": "Jonathan",
    "last_name": "Doe",
    "phone": "+1234567890",
    "address": "123 Main St",
    "city": "New York",
    "state": "NY",
    "postal_code": "10001",
    "country": "USA"
}
```

**Success Response (200 OK):**
```json
{
    "user": {
        "id": 1,
        "username": "john_doe",
        "email": "john@example.com",
        "first_name": "Jonathan",
        "last_name": "Doe",
        "phone": "+1234567890",
        "address": "123 Main St",
        "city": "New York",
        "state": "NY",
        "postal_code": "10001",
        "country": "USA",
        "profile_image": null
    },
    "message": "Profile updated successfully"
}
```

**Fields you can update:**
- first_name
- last_name
- phone
- address
- city
- state
- postal_code
- country
- profile_image (multipart form data for image upload)

---

### 5. Logout
**Logout the current user (client-side operation)**

```http
POST /users/logout/
Authorization: Bearer <access_token>
```

**Success Response (200 OK):**
```json
{
    "message": "Logout successful"
}
```

**Note:** Logout doesn't require anything on the backend. Just delete the tokens from localStorage on the frontend.

---

### 6. Refresh Access Token
**Get a new access token using the refresh token (automatic)**

```http
POST /token/refresh/
Content-Type: application/json

{
    "refresh": "<refresh_token>"
}
```

**Success Response (200 OK):**
```json
{
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ0b2tlbl90eXBlIjoiYWNjZXNzIiwicGtvIjoibmV3dGlja2V0IiwiaWF0IjoxNjc4OTAyNDQyLCJleHAiOjE2Nzg5MDYwNDJ9.newSignature..."
}
```

---

## Error Responses

### 400 Bad Request
Validation error - check the response body for specific field errors

### 401 Unauthorized
- No Authorization header provided
- Invalid or expired token
- Token signature is invalid

### 403 Forbidden
- User doesn't have permission to perform this action
- Trying to access another user's data

### 404 Not Found
- Resource doesn't exist

### 500 Server Error
- Server-side error - check server logs

---

## Security Headers

All responses include:
```
Content-Type: application/json
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
```

---

## Rate Limiting

Current implementation: No rate limiting (add for production)

Recommended for production:
- Max 5 login attempts per minute per IP
- Max 100 API requests per minute per user
- Max 10 registration attempts per hour per IP

---

## Token Management Examples

### JavaScript/Frontend

```javascript
// Store tokens after login
const { access, refresh } = response.data;
localStorage.setItem('auth', JSON.stringify({ access, refresh }));

// Add to API requests
const token = JSON.parse(localStorage.getItem('auth')).access;
headers: {
    'Authorization': `Bearer ${token}`
}

// Refresh token when needed
const refresh_token = JSON.parse(localStorage.getItem('auth')).refresh;
const newAccess = await fetch('/api/token/refresh/', {
    method: 'POST',
    body: JSON.stringify({ refresh: refresh_token })
}).then(r => r.json());

// Update stored tokens
const auth = JSON.parse(localStorage.getItem('auth'));
auth.access = newAccess.access;
localStorage.setItem('auth', JSON.stringify(auth));
```

### cURL Examples

**Login:**
```bash
curl -X POST http://localhost:8000/api/users/login/ \
  -H "Content-Type: application/json" \
  -d '{"username":"john_doe","password":"SecurePass123"}'
```

**Get Profile:**
```bash
curl -X GET http://localhost:8000/api/users/profile/ \
  -H "Authorization: Bearer eyJ0eXAiOiJKV1Q..."
```

**Refresh Token:**
```bash
curl -X POST http://localhost:8000/api/token/refresh/ \
  -H "Content-Type: application/json" \
  -d '{"refresh":"eyJ0eXAiOiJKV1Q..."}'
```

---

## Response Status Codes

| Code | Meaning | Example |
|------|---------|---------|
| 200 | OK | Successful login, profile retrieved |
| 201 | Created | User registered successfully |
| 400 | Bad Request | Invalid input, validation failed |
| 401 | Unauthorized | Missing or invalid token |
| 403 | Forbidden | User doesn't have permission |
| 404 | Not Found | Resource doesn't exist |
| 500 | Server Error | Unexpected server error |

---

## Implementation Details

### Password Storage
- Passwords are hashed using PBKDF2 with SHA256
- Passwords are never returned in API responses
- Password field is write-only in serializers

### Token Structure (JWT)
- Header: `{"typ": "JWT", "alg": "HS256"}`
- Payload: `{"user_id": 1, "token_type": "access", "exp": 1234567890, ...}`
- Signature: HMAC-SHA256 signed with SECRET_KEY

### User Isolation
- Each user can only access their own profile
- API enforces this via Django's permission system
- Cross-account access attempts return 403 Forbidden

---

## Troubleshooting

### "Invalid token"
- Token may be expired (request new one with refresh token)
- Token signature may be invalid (re-login required)
- Token format may be incorrect (use "Bearer <token>")

### "Authentication credentials were not provided"
- Authorization header is missing
- Header format should be: `Authorization: Bearer <token>`

### "Invalid username or password"
- Username doesn't exist in database
- Password is incorrect (case-sensitive)
- User account is inactive/disabled

### Token refresh fails
- Refresh token has expired (user must re-login)
- Refresh token is invalid
- Check token creation timestamp

---

For more information, see [SECURITY_AUTHENTICATION.md](./SECURITY_AUTHENTICATION.md)
