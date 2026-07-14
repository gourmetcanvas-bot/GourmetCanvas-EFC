# GourmetCanvas Elite Fitness Club — Platform

Full-stack member platform for GourmetCanvas Elite: web app, mobile app
(iOS/Android), backend API, Shopify-linked store, payment gateway,
membership & renewals, class booking, geo-tagged club locator, community
chat, and a news-feed/promotions marketplace.

## Monorepo layout

```
gourmetcanvas-elite/
├── backend/            Node.js + Express + MongoDB API
├── frontend-web/        React (Vite) web app
├── mobile/               React Native (Expo) iOS/Android app
├── infra/
│   ├── docker/            docker-compose for local full-stack dev
│   └── k8s/production/    Kubernetes manifests (reference)
├── .github/workflows/     CI + CD pipelines (GitHub Actions)
└── docs/                  Architecture, API reference, deployment guide
```

## Feature map

| Area | Where |
|---|---|
| Auth & new customer registration | `backend/src/routes/auth.routes.js`, `frontend-web/src/pages/Register.jsx` |
| Member dashboard | `backend/src/controllers/dashboard.controller.js`, `.../pages/Dashboard.jsx` |
| Geo-tagged club locator ("near me") | `backend/src/services/geo.service.js`, `.../pages/Clubs.jsx`, `mobile/screens/ClubLocatorScreen.js` |
| Membership plans, subscribe, renew | `backend/src/controllers/membership.controller.js`, `.../pages/Membership.jsx` |
| Payment gateway (Razorpay + Stripe) | `backend/src/services/payment.service.js` |
| Shopify-linked store | `backend/src/services/shopify.service.js`, `.../pages/Store.jsx` |
| Class schedule & booking | `backend/src/controllers/class.controller.js`, `.../pages/ClassSchedule.jsx` |
| Class reminders & renewal notifications | `backend/src/jobs/*.job.js`, `backend/src/services/notification.service.js` |
| Diet plans & workout schedules | `backend/src/models/DietPlan.js`, `WorkoutSchedule.js` (surfaced on Dashboard) |
| Community chat (per club) | `backend/src/controllers/community.controller.js` + Socket.io, `.../pages/Community.jsx` |
| News feed + partner promotions tab | `backend/src/controllers/newsfeed.controller.js`, `.../pages/NewsFeed.jsx` |

## Quick start (local)

```bash
# 1. Backend
cd backend
cp .env.example .env         # fill in Mongo URI, JWT secret, Shopify/Razorpay keys
npm install
node src/seed/seed.js        # loads membership plans + 2 pilot clubs
npm run dev                  # http://localhost:5000

# 2. Web app
cd ../frontend-web
cp .env.example .env
npm install
npm run dev                  # http://localhost:5173

# 3. Mobile app
cd ../mobile
cp .env.example .env
npm install
npx expo start                # scan the QR with Expo Go
```

Or run backend + web together with Docker:

```bash
cd infra/docker
docker compose up --build
```

## Tech stack

- **Backend**: Node.js, Express, MongoDB/Mongoose, JWT auth, Socket.io, node-cron, Joi validation
- **Web**: React 18, Vite, React Router, Leaflet (maps), Axios
- **Mobile**: React Native (Expo), React Navigation, react-native-maps, Expo Notifications
- **Payments**: Razorpay (primary, India), Stripe (fallback)
- **Commerce**: Shopify Admin API — product catalog synced into the app, checkout via Shopify draft orders
- **DevOps**: Docker, docker-compose, GitHub Actions (CI per app + staging/production CD), Kubernetes manifests, GitHub Container Registry

## CI/CD

- `backend-ci.yml` / `frontend-ci.yml` / `mobile-ci.yml` — lint, test, build on every PR
- `deploy-staging.yml` — auto-deploys `develop` branch to staging (Docker images → GHCR)
- `deploy-production.yml` — deploys `main` to production + triggers an EAS build for the mobile app store release

See `docs/DEPLOYMENT.md` for the full pipeline walkthrough and `docs/ARCHITECTURE.md` for the system design.
