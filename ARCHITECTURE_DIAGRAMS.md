# Authentication System Architecture

## System Overview Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     USER BROWSER (Frontend)                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌──────────────────────┐         ┌────────────────────────┐    │
│  │   Login Page         │         │  Redux Auth State      │    │
│  ├──────────────────────┤         ├────────────────────────┤    │
│  │ Username input       │         │ user: {...}            │    │
│  │ Password input       │         │ isAuthenticated: true  │    │
│  │ [Login Button]       │         │ error: null            │    │
│  │ [Show/Hide pwd]      │         └────────────────────────┘    │
│  └──────────────────────┘                    ▲                  │
│           │                                   │                  │
│           │ Validate                         │ Updates          │
│           ├──────────────────────────────────┤                  │
│           ▼                                   │                  │
│  ┌──────────────────────┐                    │                  │
│  │   API Client (axios) │                    │                  │
│  ├──────────────────────┤                    │                  │
│  │ Request interceptor: │                    │                  │
│  │ Add Bearer token     │                    │                  │
│  │                      │                    │                  │
│  │ Response interceptor:│                    │                  │
│  │ Auto-refresh token   │                    │                  │
│  └──────────────────────┘                    │                  │
│           │                                   │                  │
│           │ HTTP POST                        │                  │
│           │ /api/users/login/                │                  │
│           │ + Authorization header           │                  │
│           ▼                                   │                  │
│  ┌──────────────────────┐                    │                  │
│  │  Local Storage       │◄───────────────────┘                  │
│  ├──────────────────────┤                                       │
│  │ auth: {              │                                       │
│  │   access: "token1",  │                                       │
│  │   refresh: "token2"  │                                       │
│  │ }                    │                                       │
│  │ user: {              │                                       │
│  │   id: 1,             │                                       │
│  │   username: "john",  │                                       │
│  │   email: "j@..."     │                                       │
│  │ }                    │                                       │
│  └──────────────────────┘                                       │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
                          │
                          │ HTTPS/HTTP
                          │
┌─────────────────────────────────────────────────────────────────┐
│                  DJANGO BACKEND SERVER                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌────────────────────────┐                                     │
│  │  /api/users/login/     │                                     │
│  ├────────────────────────┤                                     │
│  │ 1. Receive credentials │                                     │
│  │ 2. Validate with DB    │                                     │
│  │ 3. Hash password       │                                     │
│  │ 4. Compare hashes      │                                     │
│  │ 5. Generate JWT        │                                     │
│  │ 6. Return tokens       │                                     │
│  └────────────────────────┘                                     │
│           │                                                      │
│           │ Queries                                             │
│           ▼                                                      │
│  ┌────────────────────────┐                                     │
│  │   Django ORM           │                                     │
│  │   Custom User Model    │                                     │
│  └────────────────────────┘                                     │
│           │                                                      │
│           ▼                                                      │
│  ┌────────────────────────┐                                     │
│  │    PostgreSQL DB       │                                     │
│  ├────────────────────────┤                                     │
│  │ users table:           │                                     │
│  │ - id                   │                                     │
│  │ - username             │                                     │
│  │ - email                │                                     │
│  │ - password_hash *** (never plain text)                       │
│  │ - first_name           │                                     │
│  │ - last_name            │                                     │
│  │ - created_at           │                                     │
│  │ - is_active            │                                     │
│  └────────────────────────┘                                     │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

## Login Sequence Diagram

```
Browser              Django Backend         Database
  │                      │                     │
  │─ POST /login ───────▶│                     │
  │  username: john      │                     │
  │  password: pass123   │                     │
  │                      │                     │
  │                      │─ Query user ───────▶│
  │                      │                     │
  │                      │◀── user record ─────│
  │                      │  {id:1, pass_hash}  │
  │                      │                     │
  │                      │ (Hash input password)
  │                      │ (Compare hashes)    │
  │                      │ ✅ Match!           │
  │                      │                     │
  │                      │ (Generate JWT tokens)
  │                      │                     │
  │◀─ 200 OK ──────────  │                     │
  │  {                   │                     │
  │    access: "...",    │                     │
  │    refresh: "...",   │                     │
  │    user: {...}       │                     │
  │  }                   │                     │
  │                      │                     │
  │ (Store in localStorage)                    │
  │                      │                     │
  │─ GET /profile ──────▶│                     │
  │  auth: Bearer ...    │                     │
  │                      │ (Verify JWT)        │
  │                      │ (Extract user_id)   │
  │                      │                     │
  │                      │─ Query user(id=1) ─▶
  │                      │                     │
  │                      │◀─ user data ───────│
  │                      │                     │
  │◀─ 200 OK ──────────  │                     │
  │  {...profile...}     │                     │
  │                      │                     │
```

## Token Structure

```
JWT = Header.Payload.Signature

┌─────────────────────────────────────────────────────┐
│ HEADER                                              │
├─────────────────────────────────────────────────────┤
│ {                                                   │
│   "typ": "JWT",                                     │
│   "alg": "HS256"  (HMAC with SHA-256)              │
│ }                                                   │
└─────────────────────────────────────────────────────┘
                          │
                 Base64URL Encoded
                          │
                          ▼
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9


┌─────────────────────────────────────────────────────┐
│ PAYLOAD (Claims)                                    │
├─────────────────────────────────────────────────────┤
│ {                                                   │
│   "user_id": 1,         ← User identification      │
│   "username": "john",   ← Can be included          │
│   "token_type": "access",  ← Access or Refresh    │
│   "exp": 1678906042,    ← Expiration timestamp     │
│   "iat": 1678902442,    ← Issued at timestamp      │
│   "jti": "abc123def456"  ← Unique token ID        │
│ }                                                   │
└─────────────────────────────────────────────────────┘
                          │
                 Base64URL Encoded
                          │
                          ▼
eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6ImpvaG4iLCJ0b2tlbl90eXBlIjoiYWNjZXNzIi...


┌─────────────────────────────────────────────────────┐
│ SIGNATURE                                           │
├─────────────────────────────────────────────────────┤
│ HMACSHA256(                                         │
│   base64UrlEncode(header) + "." +                   │
│   base64UrlEncode(payload),                         │
│   SECRET_KEY  ← Only backend knows this!          │
│ )                                                   │
│                                                     │
│ Makes token tamper-proof                           │
│ If even 1 character changes → signature invalid     │
└─────────────────────────────────────────────────────┘
                          │
                 Base64URL Encoded
                          │
                          ▼
kFkj8nGfKh5j8gKsJ0jK9xLmNoPqRsT1vWxYz2aBcDe


┌──────────────────────────────────────────────────────────────────┐
│ COMPLETE JWT TOKEN                                               │
├──────────────────────────────────────────────────────────────────┤
│ eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ1c2   │
│ VybmFtZSI6ImpvaG4iLCJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiaWF0IjoxNjc   │
│ 4OTAyNDQyLCJleHAiOjE2Nzg5MDYwNDJ9.kFkj8nGfKh5j8gKsJ0jK9xLmNo   │
│ PqRsT1vWxYz2aBcDe                                                │
│                                                                   │
│ Used in: Authorization: Bearer <token>                            │
└──────────────────────────────────────────────────────────────────┘
```

## Access Control Flow

```
Browser Makes API Call
           │
           ▼
Check localStorage for 'auth'
           │
      ┌────┴────┐
      │          │
    Found?     Not Found?
      │          │
      ▼          ▼
Add Bearer    Redirect to
Token         /login
      │
      ▼
Include in request:
Authorization: Bearer <token>
      │
      ▼
Backend Receives Request
      │
      ▼
Extract token from header
      │
      ▼
Verify JWT signature
      │
   ┌──┴──┐
   │     │
 Valid? Invalid?
   │     │
   ▼     ▼
Extract Return 401
user_id Unauthorized
   │
   ▼
Check token not expired
   │
   ┌──┴──┐
   │     │
 Valid? Expired?
   │     │
   ▼     ▼
Lookup Try to
user  refresh
   │    │
   ▼    ▼
Query  Success? 
DB  ┌───┴───┐
   │       │
 Yes     No
   │       │
   ▼       ▼
Return  Return 401
user    Unauthorized
data    (re-login)
   │
   ▼
Process request
with user context
   │
   ▼
Return response
```

## User Isolation Example

```
User A (id=1)                       User B (id=2)
    │                                   │
    │ Login                             │ Login
    │                                   │
    ▼                                   ▼
Token A:                            Token B:
{                                   {
  user_id: 1,  ← Key!               user_id: 2,  ← Different!
  exp: ...,                         exp: ...,
  ...                               ...
}                                   }
    │                                   │
    │                                   │
    │ Try to use Token B                │ Try to use Token A
    ▼                                   ▼
Backend checks:                     Backend checks:
user_id in token = 2                user_id in token = 1
Request user = 2                    Request user = 2
2 == 2 ✓                           1 ≠ 2 ✗
Allow                              Reject (403 Forbidden)
    │                                   │
    ▼                                   ▼
Cannot access                      Cannot access
User B's data                      User A's data
```

## Token Refresh Flow

```
Frontend Calls API
         │
         ▼
Add Access Token to request
         │
         ▼
Backend validates token
         │
         ├─ Valid? ───────────────▶ Process request ✅
         │
         └─ Expired?
            │
            ▼
         Return 401
            │
            ▼
Frontend Interceptor catches 401
            │
            ▼
Check if we have Refresh Token
            │
         ┌──┴──┐
         │     │
       Yes    No
         │     │
         ▼     ▼
    POST to  Redirect
 /token/    to /login
 refresh/
         │
         ▼
    Backend validates
    Refresh Token
         │
         ├─ Valid? ────────────────▶ Generate new
         │                          Access Token
         │
         └─ Expired?
            │
            ▼
         Reject
            │
            ▼
         Redirect to
         /login
            │
            ▼
         Update localStorage
         with new token
            │
            ▼
         Retry original
         request
            │
            ▼
         Success ✅
         (User didn't notice!)
```

## Password Hashing Flow

```
User Input: "SecurePass123"
            │
            ▼
         ┌─────────────────────────────┐
         │   PBKDF2 Hash Function      │
         ├─────────────────────────────┤
         │ Input: plain password       │
         │ Algorithm: SHA256           │
         │ Iterations: 260000          │
         │ Salt: random bytes          │
         └─────────────────────────────┘
            │
            ▼
Stored: "pbkdf2_sha256$260000$xyz..."
        (Cannot reverse this!)
            │
        User logs in again
            │
            ▼
Input: "SecurePass123"
            │
            ▼
Hash it the same way
            │
            ▼
Compare hashes
            │
         ┌──┴──┐
         │     │
      Match? Mismatch?
         │     │
         ▼     ▼
      ✅      ❌
    Allow   Reject
    Login    "Invalid
             credentials"
```

## Security Summary

```
┌─────────────────────────────────────────────────────┐
│              SECURITY LAYERS                        │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Layer 1: Password Security                        │
│  ├─ Hashed with PBKDF2 (not plain text)           │
│  ├─ Validated (8+ chars, strong)                  │
│  └─ Never returned in API responses                │
│                                                     │
│  Layer 2: Token Security                          │
│  ├─ Cryptographically signed (JWT)                │
│  ├─ User-specific (contains user_id)              │
│  ├─ Short-lived (1 hour for access)               │
│  └─ Can't be modified without detection            │
│                                                     │
│  Layer 3: Request Security                        │
│  ├─ Requires Authorization header                  │
│  ├─ Validates token on every request              │
│  ├─ Checks user_id matches request                │
│  └─ Rejects invalid/expired tokens                │
│                                                     │
│  Layer 4: Account Security                        │
│  ├─ Unique username & email                       │
│  ├─ User can only access own data                 │
│  ├─ Admin can't see other user's passwords        │
│  └─ Each login generates new tokens               │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

**Visual Representation**: System Architecture  
**Security Levels**: 4 Layers  
**Status**: ✅ Production-Ready
