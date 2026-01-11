# JWT Authentication Setup Instructions

## Quick Start

### Step 1: Install JWT Package
```bash
cd backend
pip install djangorestframework-simplejwt==5.3.2
```

Or install all requirements:
```bash
pip install -r requirements.txt
```

### Step 2: Run Migrations (if database schema changes)
```bash
python manage.py migrate
```

### Step 3: Restart Backend Server
```bash
python manage.py runserver
```

### Step 4: Test the Login

1. **Register a new account:**
   - Go to `http://localhost:3000/register`
   - Fill in username, email, password (min 8 chars)
   - Click Register
   - You'll be logged in automatically

2. **Login with credentials:**
   - Go to `http://localhost:3000/login`
   - Enter username and password
   - Click Login
   - Should redirect to home page

3. **Check tokens in browser:**
   - Open Chrome DevTools (F12)
   - Go to Application → Local Storage
   - Look for 'auth' key - it contains your JWT tokens
   - Look for 'user' key - it contains your user data

## What Changed

### Backend Changes
- ✅ Added JWT token generation in login endpoint
- ✅ All API endpoints now require JWT Bearer token
- ✅ Password validation enhanced (min 8 chars, not all numeric)
- ✅ Unique username and email validation

### Frontend Changes
- ✅ Login form now stores JWT tokens in localStorage
- ✅ All API requests automatically include Bearer token
- ✅ Tokens auto-refresh when expired
- ✅ Better error messages and validation
- ✅ Enhanced login page styling

## Security Features

1. **No Cross-Account Access**: Each JWT token is tied to a specific user
2. **Password Hashing**: Passwords are never stored in plain text
3. **Token Expiration**: Access tokens expire after 1 hour
4. **Automatic Refresh**: Refresh tokens get new access tokens automatically
5. **Bearer Authentication**: Standard OAuth 2.0 implementation

## API Endpoint Reference

### Login
```
POST /api/users/login/
Body: {
    "username": "your_username",
    "password": "your_password"
}
Response: {
    "user": { ... user data ... },
    "access": "eyJ0eXAiOiJKV1QiLCJhbGc...",  // Access token
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGc...", // Refresh token
    "message": "Login successful"
}
```

### Protected API Calls
```
GET /api/users/profile/
Headers: Authorization: Bearer <access_token>
```

### Logout
```
POST /api/users/logout/
Headers: Authorization: Bearer <access_token>
(Just discard tokens on frontend - no backend token blacklist needed)
```

## Troubleshooting

### Issue: "No module named 'rest_framework_simplejwt'"
**Solution**: 
```bash
pip install djangorestframework-simplejwt
```

### Issue: 401 Unauthorized on API calls
**Possible causes**:
- Token not being sent in Authorization header
- Token has expired (should auto-refresh)
- Token is invalid or tampered with

**Solution**:
- Clear localStorage and login again
- Check browser console for errors
- Verify backend is running with JWT configured

### Issue: CORS error when making API requests
**Solution**: Ensure `CORS_ALLOWED_ORIGINS` in settings.py includes your frontend URL:
```python
CORS_ALLOWED_ORIGINS = ['http://localhost:3000', 'http://127.0.0.1:3000']
```

## Testing with cURL

### Login
```bash
curl -X POST http://localhost:8000/api/users/login/ \
  -H "Content-Type: application/json" \
  -d '{"username":"your_user","password":"your_pass"}'
```

### Get Profile (with token)
```bash
curl -X GET http://localhost:8000/api/users/profile/ \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Database Considerations

The CustomUser model extends Django's AbstractUser, which means:
- Passwords are automatically hashed using PBKDF2
- No plain-text passwords ever stored in database
- Each user has a unique ID and email
- Account data is isolated per user

## Environment Setup

Create `.env` file in backend directory (keep SECRET_KEY secret):

```env
DJANGO_SECRET_KEY=your-unique-secret-key-here-change-in-production
DEBUG=True
DB_NAME=ecommerce_db
DB_USER=postgres
DB_PASSWORD=postgres
DB_HOST=localhost
DB_PORT=5432
ALLOWED_HOSTS=localhost,127.0.0.1
CORS_ALLOWED_ORIGINS=http://localhost:3000
```

## Production Deployment Checklist

- [ ] Change `DEBUG=False` in .env
- [ ] Generate strong `DJANGO_SECRET_KEY`
- [ ] Use HTTPS for all connections
- [ ] Update `ALLOWED_HOSTS` with production domain
- [ ] Update `CORS_ALLOWED_ORIGINS` with production frontend URL
- [ ] Use PostgreSQL instead of SQLite
- [ ] Set up proper database backups
- [ ] Monitor failed login attempts
- [ ] Keep Django and packages updated

---

Need help? Check the main [SECURITY_AUTHENTICATION.md](./SECURITY_AUTHENTICATION.md) guide for more details.
