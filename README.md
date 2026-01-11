# 👟 KEVO Shoes - Premium E-Commerce Platform

A modern, full-stack e-commerce platform specialized for selling premium shoes with variant management, dynamic shipping calculation, and hiistyle.com-inspired premium design.

## 📋 Project Overview

KEVO Shoes is a complete e-commerce solution built with:
- **Backend**: Django 4.2 + Django REST Framework
- **Frontend**: React 18 + Redux Toolkit
- **Database**: PostgreSQL
- **Design**: Premium black/white aesthetic with modern animations

### Key Features

✅ **User Authentication**
- User registration and login
- Session-based authentication
- User profile management
- Address book management

✅ **Product Management**
- Shoe product catalog with multiple images
- Product variants (size/color combinations)
- Multiple shoe size systems (US/EU/UK)
- Stock tracking by variant
- Product reviews and ratings
- Advanced filtering and search

✅ **Shopping Cart**
- Add/remove items with variants
- Quantity management
- Real-time cart updates
- Cart persistence via Redux

✅ **Checkout & Shipping**
- Shipping address management
- Dynamic shipping cost calculation based on country
- Multiple country support
- Order placement and tracking
- Order history with detailed items

✅ **Premium UI/UX**
- Modern hero banner with animations
- Responsive grid layouts
- Card-based product display
- Floating animations for shoes
- Mobile-first responsive design
- Professional color scheme (black #000, white #fff)

---

## 🚀 Quick Start

### Backend Setup (5 minutes)

```bash
# Navigate to backend
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Configure database
# Update DATABASE_URL in settings.py or create .env file

# Run migrations
python manage.py makemigrations
python manage.py migrate

# Create admin user
python manage.py createsuperuser

# Start server
python manage.py runserver
```
Backend runs on: **http://localhost:8000**

### Frontend Setup (5 minutes)

```bash
# Navigate to frontend
cd frontend

# Install dependencies
npm install

# Start development server
npm start
```
Frontend runs on: **http://localhost:3000**

---

## 🗂️ Project Structure

```
kevo project/
├── backend/
│   ├── config/                 # Django settings
│   │   ├── settings.py         # Main configuration
│   │   ├── urls.py             # URL routing
│   │   └── wsgi.py
│   │
│   ├── apps/
│   │   ├── users/              # Authentication & profiles
│   │   │   ├── models.py       # CustomUser
│   │   │   └── ...
│   │   ├── products/           # Product catalog
│   │   │   ├── models.py       # Product, Variant, ShoeSize
│   │   │   └── ...
│   │   ├── orders/             # Order management
│   │   │   ├── models.py       # Order, OrderItem
│   │   │   └── ...
│   │   └── cart/               # Shopping cart
│   │       ├── models.py       # Cart, CartItem
│   │       └── ...
│   │
│   └── requirements.txt
│
└── frontend/
    ├── public/
    ├── src/
    │   ├── api/                # API client
    │   │   └── api.js
    │   ├── components/         # Reusable components
    │   │   ├── Navigation.js   # Header with cart
    │   │   └── Footer.js
    │   ├── pages/              # Page components
    │   │   ├── Home.js         # Hero & categories
    │   │   ├── Products.js     # Catalog with filters
    │   │   ├── ProductDetail.js # Variant selection
    │   │   ├── Cart.js         # Shopping cart
    │   │   ├── Checkout.js     # Shipping & order
    │   │   ├── Orders.js       # Order history
    │   │   ├── Profile.js      # User profile
    │   │   └── Auth/
    │   ├── redux/              # State management
    │   │   ├── authSlice.js
    │   │   ├── cartSlice.js
    │   │   └── store.js
    │   ├── App.js
    │   └── index.css           # Global styles
    │
    └── package.json
```

---

## 🔌 API Endpoints

### Authentication
```
POST   /api/users/register/           Register new user
POST   /api/users/login/              Login user
POST   /api/users/logout/             Logout user
GET    /api/users/profile/            Get user profile
PUT    /api/users/update-profile/     Update profile
```

### Products
```
GET    /api/products/products/        List all products
GET    /api/products/products/{id}/   Get product details
POST   /api/products/{id}/add-review/ Add review
GET    /api/products/sizes/           Get shoe sizes
GET    /api/products/variants/        Get product variants
```

### Shipping
```
POST   /api/products/shipping/calculate/
Body: { "country": "US", "num_items": 2 }
Response: { "total_shipping": 20.00, ... }
```

### Cart
```
GET    /api/cart/my-cart/             Get cart
POST   /api/cart/add-item/            Add item
PUT    /api/cart/update-item/         Update quantity
DELETE /api/cart/remove-item/         Remove item
```

### Orders
```
GET    /api/orders/                   List orders
POST   /api/orders/                   Create order
GET    /api/orders/{id}/              Get order details
```

---

## 🎨 Design System

### Color Palette
- **Primary Black**: #000 (text, buttons, borders)
- **Secondary White**: #fff (background, light text)
- **Gray**: #e0e0e0 (borders, dividers)
- **Status Colors**:
  - ✓ Delivered: #2e7d32 (Green)
  - ⏳ Pending: #ff9800 (Orange)
  - ⚙️ Processing: #2196f3 (Blue)
  - ✕ Cancelled: #f44336 (Red)

### Typography
- **Font**: System sans-serif
- **Weights**: 400, 500, 600, 700
- **Headings**: 36px (H1), 18px (H2), 16px (H3)
- **Body**: 14px, **Small**: 12px

### Components
- **Buttons**: Black bg, white text, uppercase labels
- **Cards**: White bg, light border, subtle shadow
- **Inputs**: Light gray borders, focus on black
- **Responsive**: Mobile-first design, 768px+ breakpoint

---

## 📦 Key Models

### CustomUser
```python
- username (unique)
- email (unique)
- first_name, last_name
- phone, address
- city, state, postal_code, country
- profile_image
```

### Product
```python
- name, description, price
- brand, material, color
- image, image2, image3 (multiple images)
- category (FK)
- created_at, updated_at
```

### ProductVariant
```python
- product (FK)
- shoe_size (FK)  # US/EU/UK sizes
- color
- stock
- is_available
```

### Shipping
```python
- country
- base_rate
- per_item_rate
+ calculate_shipping(quantity) method
```

### Order
```python
- user (FK)
- order_number
- total_price, shipping_cost
- status (pending/processing/shipped/delivered/cancelled)
- shipping_address
- created_at, updated_at
```

---

## 🛠️ Development

### Running Both Servers

**Terminal 1 - Backend:**
```bash
cd backend
source venv/bin/activate
python manage.py runserver
```

**Terminal 2 - Frontend:**
```bash
cd frontend
npm start
```

Both will be ready in ~15 seconds.

### Database Migrations

After model changes:
```bash
cd backend
python manage.py makemigrations
python manage.py migrate
```

### Admin Panel

Access at: **http://localhost:8000/admin/**

Log in with superuser credentials to manage:
- Products & variants
- Categories & sizes
- Orders & customers
- Shipping rates

---

## 🧪 Testing

### Backend
```bash
cd backend
python manage.py test
```

### Frontend
```bash
cd frontend
npm test
npm run build
```

---

## 📱 Responsive Design

All pages are fully responsive:
- **Mobile**: < 768px (single column)
- **Tablet**: 768px - 1024px (adjusted layout)
- **Desktop**: > 1024px (full multi-column)

Key responsive features:
- Hamburger menu on mobile
- Flexible grids and flexbox
- Touch-friendly buttons (44px+ minimum)
- Optimized images for mobile

---

## 🔐 Security

- Password hashing with Django utilities
- CORS protection
- CSRF tokens for forms
- Session-based authentication
- SQL injection protection (ORM)
- Environment variables for secrets
- Input validation frontend & backend

---

## 📊 Performance

- Redux state management reduces re-renders
- Lazy image loading
- API request caching
- Responsive image sizes
- CSS Grid for efficient layouts
- Production-optimized builds

---

## 🚀 Deployment

### Backend (Heroku)
```bash
pip install gunicorn
heroku create app-name
git push heroku main
heroku run python manage.py migrate
```

### Frontend (Vercel/Netlify)
```bash
npm run build
vercel deploy --prod
# or
netlify deploy --prod --dir=build
```

---

## 🔄 Workflow

### Adding a New Product

1. Go to **http://localhost:8000/admin/**
2. Click **Products** → **Add Product**
3. Fill in name, price, description
4. Upload images (image, image2, image3)
5. Click **Product Variants** → **Add Variant**
6. Select size, color, enter stock
7. Save and refresh frontend

### Customer Checkout Flow

1. Browse products on home/products pages
2. Click product to view details
3. Select size and color
4. Add to cart
5. Click cart icon in nav
6. Click "Proceed to Checkout"
7. Select country for shipping cost
8. Review order and place order
9. View in order history

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Database connection error | Check PostgreSQL is running, verify DATABASE_URL |
| CORS error | Update CORS_ALLOWED_ORIGINS in settings.py |
| Images not loading | Configure MEDIA_URL/MEDIA_ROOT in Django |
| Cart not persisting | Ensure Redux store is initialized |
| API 404 errors | Check backend URL in frontend/src/api/api.js |

---

## 📚 Technologies

### Backend Stack
- Django 4.2
- Django REST Framework
- PostgreSQL
- Stripe (payment)
- Pillow (images)

### Frontend Stack
- React 18
- Redux Toolkit
- Axios
- React Router v6
- React Toastify

---

## 📄 License

MIT License - feel free to use this project!

---

## 🎯 Future Enhancements

- [ ] Payment processing (Stripe)
- [ ] Wishlist feature
- [ ] Product recommendations
- [ ] Live chat support
- [ ] Mobile app
- [ ] Advanced analytics
- [ ] Multi-currency support
- [ ] Loyalty program

---

## 👨‍💻 Built By

**KEVO Shoes Team** - Passionate about e-commerce ❤️

---

**Need help?** Check the docs or create an issue on GitHub!

