# 🚀 KEVO Shoes - Complete Setup Guide

## Prerequisites

Before you start, make sure you have:
- **Python 3.9+** - [Download here](https://www.python.org/)
- **Node.js 16+** - [Download here](https://nodejs.org/)
- **PostgreSQL 12+** - [Download here](https://www.postgresql.org/)
- **Git** - [Download here](https://git-scm.com/)

Verify installations:
```bash
python --version
node --version
npm --version
psql --version
```

---

## Step 1: Database Setup

### PostgreSQL Configuration

1. **Start PostgreSQL service** (if not already running)
   - **Windows**: PostgreSQL should start automatically
   - **macOS**: `brew services start postgresql`
   - **Linux**: `sudo systemctl start postgresql`

2. **Create database and user**
   ```bash
   psql -U postgres
   # In psql shell:
   CREATE DATABASE kevo_db;
   CREATE USER kevo_user WITH PASSWORD 'your_secure_password';
   ALTER ROLE kevo_user SET client_encoding TO 'utf8';
   ALTER ROLE kevo_user SET default_transaction_isolation TO 'read committed';
   ALTER ROLE kevo_user SET default_transaction_deferrable TO on;
   ALTER ROLE kevo_user SET timezone TO 'UTC';
   GRANT ALL PRIVILEGES ON DATABASE kevo_db TO kevo_user;
   \q
   ```

3. **Test connection**
   ```bash
   psql -U kevo_user -d kevo_db -h localhost
   ```

---

## Step 2: Backend Setup

### Navigate to Backend Directory
```bash
cd "c:\Users\KELVIN\Desktop\kevo project\backend"
```

### Create Virtual Environment
```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

You should see `(venv)` prefix in your terminal.

### Install Dependencies
```bash
pip install -r requirements.txt
```

This installs:
- Django 4.2
- Django REST Framework
- djangorestframework-simplejwt
- django-cors-headers
- psycopg2-binary
- Pillow
- python-decouple
- stripe

### Configure Environment Variables

Create a `.env` file in the `backend` folder:

```env
# Database
DATABASE_URL=postgresql://kevo_user:your_secure_password@localhost:5432/kevo_db

# Django
SECRET_KEY=your-super-secret-key-here-min-50-chars-recommended
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Stripe (Optional - for payment)
STRIPE_API_KEY=sk_test_your_test_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret

# CORS
CORS_ALLOWED_ORIGINS=http://localhost:3000,http://127.0.0.1:3000
```

**Generate SECRET_KEY:**
```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

### Run Database Migrations
```bash
python manage.py makemigrations
python manage.py migrate
```

Expected output:
```
Operations to perform:
  Apply all migrations: admin, auth, ...
Running migrations:
  Applying users.0001_initial... OK
  ...
```

### Create Superuser (Admin Account)
```bash
python manage.py createsuperuser
```

Follow the prompts:
```
Username: admin
Email: admin@kevoshoes.com
Password: ••••••••
Password (again): ••••••••
Superuser created successfully.
```

### Load Initial Data (Optional)
If you have a fixtures file:
```bash
python manage.py loaddata initial_data.json
```

### Start Backend Server
```bash
python manage.py runserver
```

Expected output:
```
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

✅ **Backend is ready at:** `http://localhost:8000`

---

## Step 3: Frontend Setup

Open a **new terminal** window/tab and navigate to frontend:

### Navigate to Frontend Directory
```bash
cd "c:\Users\KELVIN\Desktop\kevo project\frontend"
```

### Install Dependencies
```bash
npm install
```

This installs:
- React 18
- React Router v6
- Redux Toolkit
- Axios
- React Toastify

### Configure Environment Variables

Create a `.env` file in the `frontend` folder:

```env
REACT_APP_API_URL=http://localhost:8000/api
REACT_APP_API_TIMEOUT=5000
```

### Start Frontend Server
```bash
npm start
```

Expected output:
```
> kevo-frontend@1.0.0 start
> react-scripts start

Compiled successfully!

You can now view kevo-frontend in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://192.168.x.x:3000
```

✅ **Frontend is ready at:** `http://localhost:3000`

---

## Step 4: Verify Everything Works

### Backend Health Check
1. Open browser and visit: `http://localhost:8000/api/`
2. You should see DRF browsable API
3. Visit admin panel: `http://localhost:8000/admin/`
4. Log in with superuser credentials

### Frontend Health Check
1. Browser automatically opens `http://localhost:3000`
2. You should see KEVO Shoes home page
3. Navigation bar with cart icon visible
4. All pages accessible from navigation

### Test Cart Functionality
1. Click on a product
2. Select size and color
3. Add to cart
4. Click cart icon (should show count)
5. View cart items

---

## Step 5: Create Initial Products (Optional)

### Option A: Via Django Admin

1. Go to `http://localhost:8000/admin/`
2. Log in with superuser
3. Click **Products** → **Shoe Sizes**
4. Add shoe sizes (US 6-14)
5. Go to **Categories**
6. Add categories (Sneakers, Boots, Formal, Casual)
7. Go to **Products**
8. Create a product with images
9. Go to **Product Variants**
10. Add variants with sizes and colors

### Option B: Via Management Commands

Create `backend/fixtures/initial_data.json`:

```json
[
  {
    "model": "products.category",
    "pk": 1,
    "fields": {
      "name": "Sneakers",
      "image": ""
    }
  },
  {
    "model": "products.shoesize",
    "pk": 1,
    "fields": {
      "size": "10",
      "unit": "US"
    }
  }
]
```

Then load:
```bash
python manage.py loaddata initial_data.json
```

---

## Troubleshooting

### PostgreSQL Connection Error

**Error:** `could not connect to server: No such file or directory`

**Solution:**
1. Ensure PostgreSQL is running
2. Check DATABASE_URL is correct
3. Test connection: `psql -U kevo_user -d kevo_db`

### Django Migrations Error

**Error:** `django.db.utils.OperationalError: ... does not exist`

**Solution:**
```bash
python manage.py migrate --fake-initial
python manage.py migrate
```

### CORS Error

**Error:** `Access to XMLHttpRequest blocked by CORS`

**Solution:**
Check `CORS_ALLOWED_ORIGINS` in `settings.py` or `.env` file.

### Port Already in Use

**Error:** `Address already in use`

**Backend on different port:**
```bash
python manage.py runserver 8001
```

**Frontend on different port:**
```bash
PORT=3001 npm start
```

### Module Not Found

**Error:** `ModuleNotFoundError: No module named ...`

**Solution:**
```bash
# Backend
pip install -r requirements.txt

# Frontend
npm install
```

---

## Daily Workflow

### Opening the Project

**Terminal 1 - Backend:**
```bash
cd "c:\Users\KELVIN\Desktop\kevo project\backend"
venv\Scripts\activate  # Windows
source venv/bin/activate  # macOS/Linux
python manage.py runserver
```

**Terminal 2 - Frontend:**
```bash
cd "c:\Users\KELVIN\Desktop\kevo project\frontend"
npm start
```

Both will be ready in ~20 seconds.

### Making Changes

**Backend Changes:**
- Edit files in `backend/apps/`
- Server auto-reloads
- Test via `http://localhost:8000/admin/`

**Frontend Changes:**
- Edit files in `frontend/src/`
- Browser auto-refreshes
- Open browser DevTools (F12) to check console

### Database Changes

After modifying models:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Useful Commands

### Backend

```bash
# Create superuser
python manage.py createsuperuser

# Run tests
python manage.py test

# Create backup
pg_dump -U kevo_user kevo_db > backup.sql

# Shell/REPL
python manage.py shell

# Check installed packages
pip list

# Freeze dependencies
pip freeze > requirements.txt
```

### Frontend

```bash
# Install package
npm install package-name

# Remove package
npm uninstall package-name

# Update packages
npm update

# Build for production
npm run build

# Run tests
npm test

# List all installed packages
npm list
```

---

## Environment Variables Reference

### Backend (.env)

| Variable | Description | Example |
|----------|-------------|---------|
| DATABASE_URL | PostgreSQL connection | postgresql://user:pass@localhost:5432/db |
| SECRET_KEY | Django secret | Very long random string |
| DEBUG | Debug mode | True (development) |
| ALLOWED_HOSTS | Allowed domains | localhost,127.0.0.1 |
| STRIPE_API_KEY | Stripe test/live key | sk_test_... |
| CORS_ALLOWED_ORIGINS | Frontend URL | http://localhost:3000 |

### Frontend (.env)

| Variable | Description | Example |
|----------|-------------|---------|
| REACT_APP_API_URL | Backend API URL | http://localhost:8000/api |
| REACT_APP_API_TIMEOUT | Request timeout (ms) | 5000 |

---

## Next Steps

1. ✅ Backend running on `http://localhost:8000`
2. ✅ Frontend running on `http://localhost:3000`
3. ✅ Database connected with PostgreSQL
4. 📝 Create initial products in admin
5. 🧪 Test shopping flow
6. 🚀 Deploy to production (Heroku/Vercel)

---

## Support

If you encounter issues:
1. Check terminal for error messages
2. Review this guide's Troubleshooting section
3. Check browser console (F12) for frontend errors
4. Review Django admin to verify data

---

**Happy coding! 🚀**
