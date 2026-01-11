# Security & Authentication Guide

## Overview
Your application now uses JWT (JSON Web Token) based authentication with industry-standard security practices to prevent unauthorized access and ensure only legitimate users can log into their accounts.

## Security Features Implemented

### 1. **JWT Token-Based Authentication**
- **Access Tokens**: Short-lived tokens (1 hour) used for API requests
- **Refresh Tokens**: Long-lived tokens (7 days) used to obtain new access tokens
- **Token Rotation**: Refresh tokens are rotated on each refresh for enhanced security
- Each user gets unique tokens that cannot be used by others

### 2. **Password Security**
- **Password Hashing**: Passwords are hashed using Django's PBKDF2 algorithm (not stored in plain text)
- **Password Validation**:
  - Minimum 8 characters required
  - Cannot be entirely numeric
  - Cannot match username or other user attributes
  - Common passwords are rejected
  
### 3. **User Account Protection**
- **Unique Credentials**: Each username and email is unique in the database
- **No Account Cross-Access**: JWT tokens are user-specific; tokens from User A cannot access User B's data
- **Automatic Token Validation**: Every API request validates the JWT token before processing
- **Token Expiration**: Tokens automatically expire after their lifetime

### 4. **API Security**
- **Authorization Headers**: All authenticated requests require a Bearer token in the Authorization header
- **Automatic Token Refresh**: When an access token expires, the system automatically uses the refresh token to get a new one
- **Logout Mechanism**: Tokens are discarded client-side; server doesn't need to maintain a blacklist for 1-hour tokens

## How the Login Process Works

```
1. User enters username and password on the Login page
2. Frontend validates the input (length, format, etc.)
3. Credentials are sent securely to backend `/api/users/login/`
4. Backend authenticates the user by comparing hashed passwords
5. If valid, JWT tokens (access + refresh) are generated
6. Tokens are stored in localStorage on the frontend
7. All subsequent API requests include the access token
8. User is logged in and redirected to home page
```

## User Authentication Flow

```
Login Page
    ↓
Submit Username/Password
    ↓
Backend validates credentials
    ↓
Generate JWT Tokens (Access + Refresh)
    ↓
Store tokens in localStorage
    ↓
Store user data in Redux + localStorage
    ↓
Protected API calls include Authorization header with Bearer token
```

## Technical Implementation

### Backend (Django)

**Authentication Endpoints:**
- `POST /api/users/register/` - Create new user account
- `POST /api/users/login/` - Authenticate and get JWT tokens
- `POST /api/users/logout/` - Logout (client-side token removal)
- `GET /api/users/profile/` - Get current user profile (requires token)
- `PUT /api/users/update_profile/` - Update user profile (requires token)

**Security Settings in Django:**
```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(hours=1),  # 1 hour
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),  # 7 days
    'ROTATE_REFRESH_TOKENS': True,                # New refresh token on each refresh
    'BLACKLIST_AFTER_ROTATION': True,             # Invalidate old token
}
```

### Frontend (React)

**Token Storage:**
- Access token and refresh token stored in localStorage (key: 'auth')
- User data stored in localStorage (key: 'user')
- Redux state manages auth state across the app

**API Client Configuration:**
```javascript
// Automatically adds Bearer token to every request
axiosInstance.interceptors.request.use(config => {
    const auth = localStorage.getItem('auth');
    if (auth) {
        const { access } = JSON.parse(auth);
        config.headers.Authorization = `Bearer ${access}`;
    }
    return config;
});

// Automatically refreshes token when expired
axiosInstance.interceptors.response.use(
    response => response,
    error => {
        if (error.response.status === 401) {
            // Get new access token using refresh token
        }
    }
);
```

## How to Test the Security

### 1. **Test Login with Correct Credentials**
- Go to `/login`
- Enter username and password
- Should login successfully and redirect to home page

### 2. **Test Login with Wrong Credentials**
- Go to `/login`
- Enter wrong password
- Should see error message "Invalid username or password"

### 3. **Test Account Isolation**
- Login as User A
- Note the access token in browser's localStorage
- Try to manually change the username in the token (won't work - token is signed)
- Logout and login as User B
- User A cannot access User B's data because they have different tokens

### 4. **Test Password Security**
- Try to register with password less than 8 characters
- Should be rejected with error message
- Try to register with all-numeric password
- Should be rejected

### 5. **Test Token Expiration**
- Login successfully
- Wait 1 hour (or check token with JWT decoder)
- Make an API call
- System will automatically refresh the token using the refresh token
- User stays logged in transparently

## Security Best Practices

### For Users:
1. **Use Strong Passwords**: Combine uppercase, lowercase, numbers, and special characters
2. **Never Share Credentials**: Your username/password should be kept secret
3. **Logout When Done**: Always logout when using public/shared computers
4. **Keep Browser Updated**: Security patches are released regularly

### For Developers:
1. **Environment Variables**: Store `SECRET_KEY` and sensitive data in `.env` file (not in code)
2. **HTTPS in Production**: Always use HTTPS to encrypt tokens in transit
3. **CORS Configuration**: Only allow requests from trusted domains
4. **Token Storage**: Current setup uses localStorage (acceptable for most apps; sessionStorage for higher security)

## Environment Configuration

Create a `.env` file in the backend directory:

```
DJANGO_SECRET_KEY=your-secret-key-change-in-production
DEBUG=False  # Set to False in production
DB_NAME=ecommerce_db
DB_USER=postgres
DB_PASSWORD=your-db-password
DB_HOST=localhost
DB_PORT=5432
ALLOWED_HOSTS=localhost,127.0.0.1,yourdomain.com
CORS_ALLOWED_ORIGINS=http://localhost:3000
```

## Troubleshooting

### User Cannot Login
- Check if the username exists in the database
- Verify the password is correct (case-sensitive)
- Check if the user account is active (not disabled)

### "Invalid Token" Error
- The token may have expired (refresh token is automatic)
- Try logging out and logging back in
- Clear browser localStorage and try again

### Protected Route Shows 401 Unauthorized
- User is not logged in (no valid token)
- Token has expired and refresh failed
- Token was tampered with (won't work)

## Installation & Setup

1. **Backend Setup:**
   ```bash
   cd backend
   pip install -r requirements.txt  # Installs djangorestframework-simplejwt
   python manage.py migrate
   python manage.py runserver
   ```

2. **Frontend Setup:**
   ```bash
   cd frontend
   npm install
   npm start
   ```

3. **First User:**
   ```bash
   # Create superuser for admin access
   python manage.py createsuperuser
   ```

## Related Files Modified

- `backend/requirements.txt` - Added djangorestframework-simplejwt
- `backend/config/settings.py` - JWT configuration
- `backend/apps/users/views.py` - JWT token generation in login
- `backend/apps/users/serializers.py` - Enhanced validation
- `frontend/src/api/api.js` - Axios interceptors for token handling
- `frontend/src/pages/Auth/Login.js` - Enhanced login form
- `frontend/src/redux/authSlice.js` - Auth state management
- `frontend/src/pages/Auth/Auth.css` - Improved styling

## Future Enhancements

1. **Email Verification**: Send verification email before account activation
2. **Two-Factor Authentication (2FA)**: Add additional security layer
3. **Password Reset**: Implement secure password reset via email
4. **Social Login**: Add Google, GitHub login options
5. **Account Lockout**: Lock account after N failed login attempts
6. **Audit Logging**: Log all login attempts and account changes

---

**Security Level**: ⭐⭐⭐⭐ (Production-Ready with JWT)
**Last Updated**: January 2026
