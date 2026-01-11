# Netlify Deployment Guide

## ⚠️ Important: Backend Hosting

**Netlify cannot host Python/Django backends.** Only the React frontend can be deployed to Netlify.

### Your Project Structure
- **Frontend (React)** → Deploy to Netlify ✅
- **Backend (Django)** → Deploy to: Render, Railway, Heroku, or similar ⚠️

---

## Step 1: Deploy Frontend to Netlify

### Option A: Drag & Drop (Easy)
1. Open [Netlify.com](https://netlify.com)
2. Sign up/Login with GitHub
3. Drag the entire `frontend` folder to Netlify
4. Wait for build to complete

### Option B: Connect GitHub (Recommended)
1. Push project to GitHub
2. On Netlify, click "New site from Git"
3. Select your repository
4. Build command: `npm install && npm run build`
5. Publish directory: `build`
6. Deploy

---

## Step 2: Deploy Backend (Choose One)

### Option 1: Render.com (Recommended)
1. Go to [render.com](https://render.com)
2. Create account → New Web Service
3. Connect your GitHub repo
4. Runtime: Python 3.9+
5. Build Command: `pip install -r backend/requirements.txt`
6. Start Command: `gunicorn config.wsgi:application`
7. Set Environment Variables:
   - `PYTHONUNBUFFERED=true`
   - `SECRET_KEY=your-secret-key`
   - `DEBUG=False`
   - Database URL if using PostgreSQL

### Option 2: Railway.app
1. Go to [railway.app](https://railway.app)
2. Create project → GitHub
3. Select your repo
4. Auto-detects Django
5. Configure environment variables
6. Deploy

### Option 3: Heroku (Paid after free tier)
1. Create `Procfile` in backend root:
   ```
   web: gunicorn config.wsgi:application
   ```
2. Deploy via Heroku CLI or GitHub

---

## Step 3: Connect Frontend to Backend

### Update Frontend API URL
1. Create `frontend/.env.production` (or set in Netlify):
   ```
   REACT_APP_API_URL=https://your-backend-url.render.com
   ```

2. Update `frontend/src/api/api.js`:
   ```javascript
   const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:8000';
   ```

3. Redeploy frontend on Netlify

---

## Step 4: Fix CORS

Update `backend/config/settings.py`:
```python
CORS_ALLOWED_ORIGINS = [
    "https://your-netlify-domain.netlify.app",
    "https://your-custom-domain.com",
]
```

---

## Summary
- **Frontend**: Drag to Netlify or connect GitHub
- **Backend**: Deploy to Render/Railway separately
- **Connect them** via environment variables
