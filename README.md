# 🚨 Emergency Response System — Production-Grade

A production-ready real-time emergency dispatch platform built with **Node.js · Express · MongoDB · Socket.IO · Redis**

[![Node.js](https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Express](https://img.shields.io/badge/Express.js-000000?style=for-the-badge&logo=express&logoColor=white)](https://expressjs.com)
[![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)](https://mongodb.com)
[![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)](https://redis.io)
[![Socket.IO](https://img.shields.io/badge/Socket.IO-010101?style=for-the-badge&logo=socket.io&logoColor=white)](https://socket.io)

---

## ✨ Production-Grade Features

### 🔧 Core Infrastructure

| Feature | Description |
|--------|-------------|
| **Advanced Rate Limiting** | Redis-backed tiered rate limiting with sliding windows |
| **Real Alert Delivery** | Slack, email, and webhook notifications for system alerts |
| **Health Checks** | Comprehensive `/health` endpoint with system diagnostics |
| **Metrics Dashboard** | Real-time visualization at `/metrics-dashboard` |
| **Load Testing** | 1000-user simulation with detailed performance analysis |

### 🛡️ Security & Observability

| Feature | Description |
|--------|-------------|
| **Enterprise Security** | Helmet.js, CORS, MongoDB sanitization, CSRF protection |
| **Advanced Monitoring** | Prometheus metrics export, structured logging |
| **Alert Management** | Multi-channel alerting with cooldown prevention |
| **Audit Compliance** | Immutable audit logs with tamper-proof records |

### ⚡ Performance & Scalability

| Feature | Description |
|--------|-------------|
| **Horizontal Scaling** | Multi-instance deployment with Redis session sharing |
| **Queue Management** | BullMQ with priority queues and retry logic |
| **Load Shedding** | Graceful degradation under extreme load (10k+ users) |
| **Database Optimization** | Connection pooling, indexing, and query optimization |

---

## 📁 Architecture

```
emergency-response-system/
├── index.js                    # Express server + MongoDB + Socket.IO bootstrap
├── controllers/
│   ├── authController.js
│   ├── emergencyController.js
│   ├── dispatchController.js
│   ├── ambulanceController.js
│   ├── adminController.js
│   ├── analyticsController.js
│   └── medicalController.js
├── middleware/
│   ├── auth.js                 # JWT verification + token blacklist (Redis/memory)
│   ├── role.js                 # Role-based access control
│   ├── validate.js             # Lightweight request validator
│   └── errorHandler.js        # Global Express error handler
├── models/
│   ├── User.js
│   ├── Ambulance.js
│   ├── EmergencyRequest.js
│   ├── DispatchLog.js
│   └── MedicalRecord.js
├── routes/                     # Express routers (one per domain)
├── utils/
│   ├── constants.js            # ALL enums - single source of truth
│   ├── AppError.js             # Custom operational error class
│   ├── haversine.js            # Great-circle distance calculation
│   ├── dispatchEngine.js       # Ambulance allocation logic
│   ├── etaCalculator.js        # OSRM routing with Haversine fallback
│   └── redisClient.js          # Redis initialisation with graceful fallback
└── tests/
    ├── system.test.js
    ├── auth.test.js
    ├── dispatch.test.js
    ├── ambulance.test.js
    ├── analytics-admin.test.js
    └── security.test.js
```

---

## 🚀 Quick Start

```bash
# 1. Clone and install
git clone https://github.com/Aartipaal/emergency-response-system.git
cd emergency-response-system
npm install

# 2. Configure environment
cp .env.example .env

# 3. Start development server
npm run dev

# 4. Run tests
npm test
```

---

## ⚙️ Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `MONGODB_URI` | ✅ | MongoDB connection string |
| `JWT_SECRET` | ✅ | 64-byte random hex string |
| `JWT_EXPIRE` | — | Token lifetime (default: `7d`) |
| `PORT` | — | HTTP port (default: `3000`) |
| `NODE_ENV` | — | `development` or `production` |
| `REDIS_URL` | — | Redis URL (required for multi-instance) |
| `CLOUDINARY_CLOUD_NAME` | — | Required for file uploads |
| `CLOUDINARY_API_KEY` | — | Required for file uploads |
| `CLOUDINARY_API_SECRET` | — | Required for file uploads |
| `OSRM_ROUTING_URL` | — | OSRM server URL (falls back to Haversine) |
| `FRONTEND_URL` | — | CORS origin (default: `http://localhost:3000`) |

> Generate JWT_SECRET: `node -e "console.log(require('crypto').randomBytes(64).toString('hex'))"`

---

## 📡 API Reference

### Auth `/api/auth`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| POST | `/register` | Public | Register new user |
| POST | `/login` | Public | Login (email or phone) |
| GET | `/me` | Private | Get current user |
| POST | `/logout` | Private | Logout + revoke token |
| POST | `/refresh` | Private | Refresh JWT |

### Emergency `/api/emergency`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| POST | `/` | Citizen | Create SOS request |
| GET | `/` | All roles | List requests (role-filtered) |
| GET | `/history` | Citizen | Own request history |
| GET | `/:id` | Related parties | Get single request |
| PUT | `/:id/accept` | Hospital | Accept request |
| PUT | `/:id/complete` | Driver | Mark completed |

### Dispatch `/api/dispatch`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| POST | `/request` | Citizen | New emergency (auto-dispatches) |
| GET | `/active` | Citizen | Current active request |
| GET | `/assignments` | Driver | Current assignments |
| PUT | `/:id/response` | Driver | Accept/reject assignment |
| PATCH | `/:id/track` | Driver | Update location/status |
| DELETE | `/:id` | Citizen | Cancel request |

### Ambulances `/api/ambulances`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| POST | `/` | Driver | Register or update ambulance |
| GET | `/` | Admin/Dispatcher/Driver | List ambulances |
| PATCH | `/:id/status` | Driver/Admin | Update status |
| PATCH | `/:id/location` | Driver | Update GPS location |

### Admin `/api/admin`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| GET | `/users` | Admin | List all users |
| PUT | `/users/:id/role` | Admin | Change user role |
| DELETE | `/users/:id` | Admin | Delete user |
| GET | `/stats` | Admin | System statistics |

### Analytics `/api/analytics`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| GET | `/latency` | Admin | Response latency metrics |
| GET | `/performance` | Admin | Daily performance stats |
| GET | `/export` | Admin | Export data (JSON/CSV, max 10k rows) |

### Medical Records `/api/medical`
| Method | Path | Access | Description |
|--------|------|--------|-------------|
| POST | `/upload` | Authenticated | Upload medical file |
| GET | `/:userId` | Owner/Hospital | Get user's records |
| DELETE | `/record/:id` | Owner | Delete record |
| POST | `/share/:recordId` | Owner | Share with hospital |

---

## 👥 Role System

| Role | Description |
|------|-------------|
| `CITIZEN` | Creates emergency requests, views own history, manages own medical records |
| `DRIVER` | Receives dispatch assignments, updates ambulance location/status |
| `HOSPITAL` | Accepts emergency requests, views patient medical records |
| `DISPATCHER` | Views all requests and ambulances; manages dispatch queue |
| `ADMIN` | Full access to all resources + user management + analytics |

> `DISPATCHER` and `ADMIN` roles must be assigned by an existing ADMIN.

---

## 🔐 Security Model

- **JWT authentication** with token blacklist (Redis in production, in-memory fallback)
- **httpOnly + SameSite:Strict cookies** — tokens never accessible from JavaScript
- **Token revocation on logout** — tokens invalidated immediately, not just at expiry
- **bcrypt** password hashing with work factor 12 (OWASP 2024 minimum)
- **Role-based access control** on every route
- **Input sanitisation** — pagination clamped, coordinates range-validated
- **Export DoS prevention** — analytics export hard-capped at 10,000 rows
- **Generic error messages** for auth failures — prevents user enumeration
- **Helmet** security headers on all responses
- **Rate limiting** — 200 requests per 15 minutes per IP

---

## 🧪 Running Tests

```bash
# All tests
npm test

# With coverage
npm run test:cov

# Single file
npx jest tests/auth.test.js
```

### Test Coverage

| File | Cases | Coverage Area |
|------|-------|---------------|
| `system.test.js` | 27 | Haversine, AppError, constants, role middleware |
| `auth.test.js` | 25 | Register, login, logout, JWT security |
| `dispatch.test.js` | 20 | Coordinates, priority/type fallbacks, ETA |
| `ambulance.test.js` | 20 | Status transitions, GPS threshold |
| `analytics-admin.test.js` | 25 | Export cap, CSV format, pagination |
| `security.test.js` | 20 | Token extraction, role injection, cookie config |
| **Total** | **137** | **6 test files** |

---

## 📊 Monitoring & Alerting

```bash
# Health check
curl http://localhost:3000/health

# Metrics dashboard
http://localhost:3000/metrics-dashboard
```

**Key Metrics:** Request rate · Error rates · Response time (P50/P95/P99) · Queue depth · Memory/CPU · DB health

**Alert Channels:** Slack · Email · Webhooks — with 5-minute cooldown protection

---

## 🔥 Load Testing

```bash
# Quick (100 users, 1 min)
npm run test:load:quick

# Full production (1000 users, 5 min)
npm run test:load:full

# Custom
node scripts/load-test.js [duration_seconds] [max_users]
```

---

## 🐳 Production Deployment

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

```bash
docker-compose -f docker-compose.prod.yml up -d
```

### Production Checklist

- [ ] Set `NODE_ENV=production`
- [ ] Generate strong `JWT_SECRET` (64 hex bytes)
- [ ] Provision Redis for token blacklist + Socket.IO adapter
- [ ] Use MongoDB Atlas (replica set)
- [ ] Set `CLOUDINARY_*` vars for medical file storage
- [ ] Configure `OSRM_ROUTING_URL`
- [ ] Place behind Nginx/ALB with SSL termination
- [ ] Enable MongoDB read replica for analytics
- [ ] Set up log aggregation (Datadog / CloudWatch / ELK)

---

## 🐛 Bug Fixes Applied

| # | Severity | Description |
|---|----------|-------------|
| 1 | 🔴 CRITICAL | `index.js` was a Cloudinary sample — no Express server existed |
| 2 | 🔴 CRITICAL | `DispatchLog` used in `emergencyController` without import |
| 3 | 🔴 CRITICAL | `mongoose` used for transactions without import |
| 4 | 🔴 CRITICAL | `REQUEST_STATUS.ACCEPTED` doesn't exist — saves `undefined` to DB |
| 5 | 🔴 CRITICAL | `REQUEST_PRIORITY.includes()` called on Object (not array) → TypeError |
| 6 | 🔴 CRITICAL | `.map(req => ...)` shadows outer Express `req` parameter |
| 7 | 🟠 SECURITY | Live MongoDB + Cloudinary credentials committed to `.env.example` |
| 8 | 🟠 SECURITY | Token accepted from `?token=` query param → exposed in access logs |
| 9 | 🟠 SECURITY | `startsWith('Bearer')` missing trailing space |
| 10 | 🟠 SECURITY | In-memory blacklist not shared across server instances |
| 11 | 🟡 SECURITY | bcrypt work factor 10 (OWASP minimum is 12) |
| 12 | 🟡 SECURITY | No row limit on analytics export → OOM / DoS |
| 13 | 🟡 LOGIC | `requireAllRoles` always fails for multiple required roles |
| 14 | 🟡 LOGIC | Ambulance freed before next-ambulance search — race condition |
| 15 | 🟡 PERF | N+1 query — 1 DB call per request document to fetch driver info |
| 16 | 🟡 LOGIC | Two conflicting compound indexes on `EmergencyRequest` |
| 17 | 🟡 LOGIC | Hardcoded status strings instead of constants |
| 18 | 🟡 LOGIC | Incorrect `$cond` pattern for null date check in analytics pipeline |
| 19 | 🟡 COMPAT | `$percentile` requires MongoDB 7.0+ — fails silently on older clusters |
| 20 | 🟡 LOGIC | `logs[]` array used in controllers but missing from `DispatchLog` schema |
| 21 | 🟡 LOGIC | Ambulance capacity default = 0, which is never valid |
| 22 | 🟡 DX | Manual `updatedAt` bookkeeping instead of `timestamps: true` |

---

## 🌐 Connect

**Aarti** — [Portfolio](https://aartiii.netlify.app) · [GitHub](https://github.com/Aartipaal) · [Gmail](mailto:aarti190920@gmail.com) · [Twitter](https://x.com/AartiP42890)
