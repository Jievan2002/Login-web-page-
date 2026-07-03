# UserHub — Full-Stack User Management System

A production-ready, secure, full-stack web application built with **React 18**, **Node.js / Express**, **MySQL** and **JWT Authentication**.

---

## ✨ Features

| Feature | Details |
|---|---|
| 🔐 Authentication | JWT access + refresh tokens, stored in HttpOnly cookies |
| 📝 Registration | Full validation, email availability check, password strength meter |
| 🔑 Login | Remember Me, show/hide password, Forgot Password |
| 🔄 Forgot Password | Two-step email verify → reset flow |
| 🏠 Dashboard | Profile overview with glassmorphism cards |
| ✏️ Edit Profile | Update all fields except email |
| 🌙 Dark Mode | System-aware, persisted in localStorage |
| 🔒 Security | Helmet, CORS, bcrypt, rate limiting, SQL injection protection |
| 📱 Responsive | Mobile-first Bootstrap 5 layout |
| ⚡ Performance | React lazy loading, code splitting |

---

## 📁 Folder Structure

```
UserManagement/
├── client/               # React 18 + Vite frontend
│   └── src/
│       ├── components/   # Navbar, Footer, Loader, ProtectedRoute, PasswordStrength, ErrorBoundary
│       ├── context/      # AuthContext (global auth state)
│       ├── hooks/        # useTheme
│       ├── pages/        # Login, Register, Dashboard, ForgotPassword, EditProfile
│       ├── services/     # Axios API layer with interceptors
│       ├── styles/       # global.css (design tokens, animations)
│       └── utils/        # Yup schemas, SweetAlert2 toasts
│
├── server/               # Node.js + Express backend
│   ├── config/           # Sequelize DB connection
│   ├── controllers/      # authController, userController
│   ├── middleware/       # authMiddleware, validationMiddleware
│   ├── models/           # User, RefreshToken (Sequelize)
│   ├── routes/           # authRoutes, userRoutes
│   ├── services/         # authService, userService
│   └── utils/            # jwt.js, response.js
│
└── database/
    └── user_management.sql
```

---

## ⚙️ Prerequisites

- Node.js ≥ 18
- MySQL 8.x
- npm ≥ 9

---

## 🚀 Installation

### 1 — Clone / extract the project

```bash
cd UserManagement
```

### 2 — Install all dependencies

```bash
npm run install:all
```

---

## 🗄️ MySQL Configuration

### Create the database and tables

```bash
mysql -u root -p < database/user_management.sql
```

This script creates:
- Database `user_management`
- Table `users` with all required columns and indexes
- Table `refresh_tokens` for token persistence
- 3 sample users (password for all: `Admin@1234`)

---

## 🔧 Environment Variables

### Backend — `server/.env`

```env
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=user_management
DB_PORT=3306

JWT_SECRET=your_super_secure_secret_key_change_in_production
JWT_REFRESH_SECRET=your_refresh_secret_change_in_production
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_EXPIRES_IN=7d

NODE_ENV=development
CLIENT_ORIGIN=http://localhost:5173
```

> ⚠️ **Never commit `.env` to version control.**

### Frontend — `client/.env`

```env
VITE_API_URL=http://localhost:5000/api
```

---

## ▶️ Running the Application

### Start the backend (terminal 1)

```bash
npm run dev:server
# Server starts at http://localhost:5000
```

### Start the frontend (terminal 2)

```bash
npm run dev:client
# App opens at http://localhost:5173
```

---

## 🏗️ Production Build

```bash
npm run build:client
# Output: client/dist/
```

Point your web server (Nginx / Apache) to `client/dist`.

---

## 📡 API Reference

### Authentication

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/api/auth/register` | Register new user | ❌ |
| POST | `/api/auth/login` | Login, returns cookies | ❌ |
| POST | `/api/auth/logout` | Clear tokens | ❌ |
| POST | `/api/auth/refresh` | Refresh access token | ❌ |
| POST | `/api/auth/forgot-password` | Verify email | ❌ |
| POST | `/api/auth/reset-password` | Reset password | ❌ |

### Users

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/api/users/profile` | Get own profile | ✅ |
| PUT | `/api/users/profile` | Update own profile | ✅ |
| GET | `/api/users` | List all users (admin) | ✅ |
| GET | `/api/users/check-email?email=` | Email availability | ❌ |

### Example: Register

```bash
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Alice Johnson",
    "gender": "Female",
    "mobile": "9876543210",
    "email": "alice@example.com",
    "password": "Admin@1234",
    "confirmPassword": "Admin@1234",
    "address": "123 Main Street, Springfield",
    "pincode": "560001"
  }'
```

### Example: Login

```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -c cookies.txt \
  -d '{"email":"alice@example.com","password":"Admin@1234"}'
```

---

## 🔐 Security Implementation

- **Helmet** — secure HTTP headers
- **CORS** — restricted to configured origins
- **bcrypt** (rounds=12) — password hashing
- **HttpOnly cookies** — JWT tokens never exposed to JS
- **express-rate-limit** — 20 auth requests / 15 min per IP
- **express-validator** — server-side input validation
- **Sequelize ORM** — parameterised queries prevent SQL injection
- **Body size limit** — 10 KB max to prevent payload attacks
- **Refresh token rotation** — tokens invalidated on logout / password reset

---

## 🎨 UI Design

- Glassmorphism auth cards with backdrop blur
- Gradient background (`#2563EB` blue tones)
- `Plus Jakarta Sans` display font + `Inter` body
- Smooth `fadeInUp` page transitions
- Bootstrap 5 responsive grid
- Dark / Light theme toggle (persisted)
- SweetAlert2 toast notifications
- Password strength meter (5 levels)

---

## 🛠️ Troubleshooting

| Problem | Solution |
|---------|---------|
| `ECONNREFUSED` on port 5000 | Backend not running — `npm run dev:server` |
| `Access denied` MySQL | Check `DB_USER` / `DB_PASSWORD` in `.env` |
| CORS errors | Confirm `CLIENT_ORIGIN` matches your frontend URL |
| Token 401 loop | Clear browser cookies and log in again |
| `alter: false` warning | Safe to ignore — use migration tools for schema changes in production |

---

## 🚀 Future Enhancements

- [ ] Email verification on registration
- [ ] OAuth 2.0 (Google / GitHub login)
- [ ] Admin panel with user management table
- [ ] Two-factor authentication (TOTP)
- [ ] Profile photo upload
- [ ] Activity / audit log
- [ ] Docker + docker-compose setup

---

## 📄 License

MIT — free to use and modify.
