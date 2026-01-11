# ⚡ KEVO Shoes - Quick Start Guide

## 🏃 Get Running in 5 Minutes

### Prerequisites
- Python 3.9+ installed
- Node.js 16+ installed  
- PostgreSQL installed and running
- Terminal/Command Prompt

---

## 🔥 Ultra-Quick Setup

### Step 1: Backend (2 minutes)
```bash
cd backend
python -m venv venv

# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

✅ Backend ready at: `http://localhost:8000`

### Step 2: Frontend (2 minutes)

**Open NEW terminal tab/window:**
```bash
cd frontend
npm install
npm start
```

✅ Frontend ready at: `http://localhost:3000`

### Step 3: Create Sample Data (1 minute)

1. Go to: `http://localhost:8000/admin`
2. Login with superuser
3. Add Categories: Sneakers, Boots, Formal
4. Add Shoe Sizes: US 6-14
5. Add 2-3 sample products

---

## 🎮 Test It Out

1. **Home page**: See categories and products
2. **Click a product**: View details
3. **Select size**: Choose your size
4. **Add to cart**: Click button
5. **View cart**: Click cart icon
6. **Checkout**: Select country, see shipping
7. **Order**: Place your order
8. **History**: View order in Order History

---

## 📁 Project Structure

```
kevo project/
├── backend/            ← Django API server
│   ├── apps/           ← User, Product, Order, Cart apps
│   ├── manage.py
│   └── requirements.txt
├── frontend/           ← React app
│   ├── src/            ← All components and pages
│   └── package.json
└── README.md           ← Full documentation
```

---

## 🔗 Important URLs

| Purpose | URL |
|---------|-----|
| Frontend | `http://localhost:3000` |
| Backend API | `http://localhost:8000/api` |
| Admin Panel | `http://localhost:8000/admin` |

---

## 🛠️ Common Commands

### Backend
```bash
# Activate virtual environment (Windows)
venv\Scripts\activate

# Activate (macOS/Linux)
source venv/bin/activate

# Run migrations after model changes
python manage.py makemigrations
python manage.py migrate

# Access Django shell
python manage.py shell
```

### Frontend
```bash
# Install new package
npm install package-name

# Run tests
npm test

# Build for production
npm run build
```

---

## ⚠️ Troubleshooting

### Port 8000 already in use
```bash
python manage.py runserver 8001
```

### Port 3000 already in use
```bash
PORT=3001 npm start
```

### Database connection error
1. Ensure PostgreSQL is running
2. Check DATABASE_URL in settings.py
3. Verify credentials

### Virtual environment not activating
```bash
# Delete and recreate
rm -rf venv  # macOS/Linux: use this
rmdir venv /s  # Windows: use this

python -m venv venv
venv\Scripts\activate
```

---

## 📖 Documentation

- **SETUP_GUIDE.md**: Detailed setup instructions
- **README.md**: Complete project documentation
- **PROJECT_CHECKLIST.md**: Implementation status

---

## 🎯 Next Steps

1. ✅ Get both servers running
2. ✅ Create test products
3. ✅ Test shopping flow
4. ⏭️ Deploy to production
5. ⏭️ Go live!

---

## 💬 Need Help?

1. Check **SETUP_GUIDE.md** for detailed troubleshooting
2. Read **README.md** for full documentation
3. Review **PROJECT_CHECKLIST.md** for feature status

---

**You're all set! Happy coding! 🚀**
