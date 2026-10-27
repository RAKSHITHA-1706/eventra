# Eventra — Multi-Tenant Event & Ticket Reservation System

A deployment-ready starter project for event discovery, organizer event management, temporary ticket holds, mock asynchronous payments, analytics, Redis caching, and expiry jobs.

## Stack
- Frontend: React + Vite
- API: Node.js + Express
- Database: PostgreSQL (inventory updates use transactions and `SELECT ... FOR UPDATE`)
- Cache / delayed jobs: Redis + BullMQ
- API testing: Postman collection included

## Quick start (Docker Compose)
Prerequisites: Docker Desktop and Docker Compose.

1. Copy `.env.example` to `.env` and set `JWT_SECRET` to a long random value.
2. From this folder run `docker compose up --build`.
3. Open the frontend at http://localhost:5173 and API docs/health at http://localhost:4000/api/health.
4. Demo login: `attendee@eventra.demo` / `DemoPass123!` (Organizer and Admin accounts are listed below).

Demo accounts (created on first API start):
- Admin: `admin@eventra.demo` / `DemoPass123!`
- Organizer: `organizer@eventra.demo` / `DemoPass123!`
- Attendee: `attendee@eventra.demo` / `DemoPass123!`

## Run without Docker
- Start PostgreSQL and Redis, create a database, then set `DATABASE_URL` and `REDIS_URL`.
- API: `cd backend && npm install && npm run dev`
- Frontend: `cd frontend && npm install && npm run dev`

## Main API routes
- `POST /api/auth/login`
- `GET /api/events`
- `POST /api/events` (Organizer/Admin)
- `POST /api/reservations` (Attendee)
- `POST /api/reservations/:id/pay` (mock asynchronous payment)
- `GET /api/reservations/:id`
- `GET /api/analytics` (Organizer/Admin)

## Concurrency and hold expiry
Reservation creation locks the relevant ticket-tier row in a PostgreSQL transaction and checks `available_quantity` before decrementing it. Each hold has an explicit `expires_at` and a BullMQ delayed job. Expiry jobs re-check status inside a transaction before returning inventory, preventing a stale expiry job from releasing a paid ticket. Payment is intentionally simulated; do not use this as a production payment processor without a real provider, webhook signature verification, idempotency controls, monitoring, and security review.

## Deploy
Deploy the API and worker as a persistent Node service, provision PostgreSQL and Redis, and deploy the frontend as a static Vite site. Set `VITE_API_URL` to the public API base URL (for example `https://your-api.example.com/api`). Set `DATABASE_URL`, `REDIS_URL`, `JWT_SECRET`, and `FRONTEND_URL` in the API environment. Configure the frontend host's build command as `npm run build` and output directory as `dist`. Configure the backend start command as `npm start`.

No public URL is embedded because a real deployment requires access to a hosting account and provisioned services.
