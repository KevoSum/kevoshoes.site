# Quick Testing Guide

## Prerequisites - Do This First

### 1. Install JWT Package
```bash
cd backend
pip install -r requirements.txt
```

### 2. Verify Installation
```bash
python -c "import rest_framework_simplejwt; print('✅ JWT installed successfully')"
```

---

## Start the Servers

### Terminal 1 - Backend
```bash
cd backend
python manage.py runserver
```

**Expected output:**
```
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

### Terminal 2 - Frontend  
```bash
cd frontend
npm start
```

**Expected output:**
```
webpack compiled...
On Your Network: http://192.168.x.x:3000
```

Browser should open automatically to `http://localhost:3000`

---

## Test Scenarios

### Test 1: Register New Account ✅
1. Go to `http://localhost:3000/register`
2. Fill in:
   - Username: `testuser1`
   - Email: `test1@example.com`
   - Password: `TestPass123`
   - Repeat Password: `TestPass123`
3. Click **Register**
4. **Expected Result**: 
   - ✅ Account created
   - ✅ Auto-logged in
   - ✅ Redirected to home page
   - ✅ See success toast message

---

### Test 2: Login with Correct Credentials ✅
1. Go to `http://localhost:3000/login`
2. Enter:
   - Username: `testuser1`
   - Password: `TestPass123`
3. Click **Login**
4. **Expected Result**:
   - ✅ Login successful toast
   - ✅ Redirected to home page
   - ✅ Your profile name visible (if shown)

---

### Test 3: Login with Wrong Password ❌
1. Go to `http://localhost:3000/login`
2. Enter:
   - Username: `testuser1`
   - Password: `WrongPassword`
3. Click **Login**
4. **Expected Result**:
   - ✅ Error message appears: "Invalid username or password"
   - ✅ NOT redirected
   - ✅ Stay on login page

---

### Test 4: Weak Password Rejection ❌
1. Go to `http://localhost:3000/register`
2. Try:
   - Username: `testuser2`
   - Email: `test2@example.com`
   - Password: `short` (only 5 chars)
   - Repeat: `short`
3. Click **Register**
4. **Expected Result**:
   - ✅ Error message: "Password must be at least 8 characters"
   - ✅ Registration blocked

---

### Test 5: Verify Tokens in Browser ✅
1. Login successfully (Test 2)
2. Open DevTools: Press **F12**
3. Go to **Application** tab
4. Click **Local Storage** in left menu
5. Click `http://localhost:3000`
6. **Expected Result**:
   - ✅ See `auth` key with value: `{"access":"...", "refresh":"..."}`
   - ✅ See `user` key with your user data
   - ✅ Tokens are stored and accessible

**Screenshot of what you should see:**
```
Storage
├── Local Storage
    └── http://localhost:3000
        ├── auth: {"access":"eyJ0eXA...","refresh":"eyJ0eXA..."}
        └── user: {"id":1,"username":"testuser1","email":"test1@example.com"}
```

---

### Test 6: Protected Route Access ✅
1. **While logged in**: Visit `/profile` - should work ✅
2. **Logout**: Clear browser data or delete tokens
3. **Try again**: Visit `/profile` - should redirect to `/login` ✅

**Expected Result**:
- ✅ Logged in: Can access profile page
- ✅ Logged out: Redirected to login

---

### Test 7: API Call with Token ✅
1. Login successfully
2. Open DevTools: **F12 → Network**
3. Click any tab that makes an API call (Products, Cart, etc.)
4. Look at network request in the **Headers** tab
5. **Expected Result**:
   - ✅ See header: `Authorization: Bearer eyJ0eXA...`
   - ✅ Token automatically included in request

---

### Test 8: Token Auto-Refresh ✅
1. Login and note the time
2. Wait 1 hour, then make an API call
3. Check DevTools → Network → Headers
4. **Expected Result**:
   - ✅ Request succeeds (no login required)
   - ✅ New token generated automatically
   - ✅ You don't notice anything

*For testing: Can't easily wait 1 hour, but the interceptor code is in place*

---

### Test 9: Password Toggle ✅
1. Go to `/login`
2. Type in password field
3. Click the eye icon button 👁️
4. **Expected Result**:
   - ✅ Password becomes visible
   - ✅ Click again to hide

---

### Test 10: Form Validation Feedback ✅
1. Go to `/login`
2. Leave username empty, click password field
3. **Expected Result**:
   - ✅ See error: "Username is required"

---

## Success Checklist

| Test | Status | Notes |
|------|--------|-------|
| Register account | ✅ | Auto-logged in |
| Login with valid creds | ✅ | Redirects to home |
| Login with wrong password | ✅ | Shows error, stays on page |
| Weak password rejected | ✅ | Shows validation error |
| Tokens in localStorage | ✅ | Both access and refresh |
| Protected route access | ✅ | Only when logged in |
| Authorization header | ✅ | Bearer token included |
| Password toggle | ✅ | Show/hide works |
| Form validation | ✅ | Error messages appear |

---

## Verification Commands

### Check Backend is Working
```bash
curl http://localhost:8000/api/users/login/ -X OPTIONS
# Should return 200 OK
```

### Check CORS is Working
```bash
curl -H "Origin: http://localhost:3000" \
  -H "Access-Control-Request-Method: POST" \
  -H "Access-Control-Request-Headers: content-type" \
  -X OPTIONS http://localhost:8000/api/users/login/
# Should include Access-Control headers
```

### Test Login API Directly
```bash
curl -X POST http://localhost:8000/api/users/login/ \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser1","password":"TestPass123"}'

# Expected response:
# {
#   "access": "eyJ0eXA...",
#   "refresh": "eyJ0eXA...",
#   "user": {...},
#   "message": "Login successful"
# }
```

---

## Browser DevTools Inspection

### Console Tab (F12 → Console)
- No red errors should appear
- Look for any network errors
- Check for auth messages

### Network Tab (F12 → Network)
- Look for API calls to `/api/users/`
- Check request headers for `Authorization: Bearer ...`
- Check response status (200, 201, 400, 401)

### Application Tab (F12 → Application → Local Storage)
- Should see `auth` with JWT tokens
- Should see `user` with user data
- Storage updates after each login/logout

---

## Common Test Issues

### Issue: JWT package not found
```bash
pip install djangorestframework-simplejwt
```

### Issue: Backend shows error
- Check terminal where `python manage.py runserver` runs
- Look for error messages
- Make sure on the right directory

### Issue: Frontend won't start
```bash
cd frontend
npm install  # Install dependencies if needed
npm start
```

### Issue: CORS error in browser
- Make sure backend is running
- Check it's at `http://localhost:8000`
- Restart both servers

### Issue: Login button doesn't work
- Open DevTools console (F12)
- Look for red error messages
- Check backend is running
- Try in incognito window (clear cache)

---

## Test Results

After running all tests, you should have:

✅ Working login system
✅ Passwords validated and hashed
✅ JWT tokens generated and stored
✅ Tokens auto-included in API calls
✅ Protected routes only accessible when logged in
✅ Modern UI with validation feedback
✅ Error handling working correctly

---

## Next Steps After Testing

1. ✅ Run all 10 tests above
2. ✅ Verify browser DevTools show tokens
3. ✅ Try registering multiple users (test isolation)
4. ✅ Try using one user's token as another user (should fail)
5. ✅ Read [START_HERE.md](./START_HERE.md) for full overview
6. ✅ Follow [DEPLOYMENT_CHECKLIST.md](./DEPLOYMENT_CHECKLIST.md) for production

---

**Total test time**: ~15 minutes
**Difficulty**: ⭐ Very Easy
**Expected result**: All tests pass ✅
