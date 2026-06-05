# revsecureclouddb

Database schema, migrations, and seed data for [RevSecureCloud](https://revsecurecloud.com) — Revenue Leakage Detector.

Managed with [Prisma ORM](https://www.prisma.io/) against **PostgreSQL 16**.

---

## Contents

```
prisma/
  schema.prisma   ← Full data model (multi-tenant, RBAC, Stripe billing)
  seed.ts         ← Demo seed data (admin + analyst accounts)
docker-compose.yml  ← Standalone PostgreSQL 16 for local dev
```

---

## Quick Start (Local)

### 1 — Start PostgreSQL
```bash
docker compose up -d
```

### 2 — Install deps
```bash
npm install
```

### 3 — Configure env
```bash
cp .env.example .env
# Edit DATABASE_URL in .env
```

### 4 — Push schema to DB
```bash
npm run db:push
```

### 5 — Seed demo data
```bash
npm run db:seed
```

Demo accounts created:
| Email | Password | Role |
|---|---|---|
| admin@demo.com | password123 | COMPANY_ADMIN |
| analyst@demo.com | password123 | ANALYST |

---

## Scripts

| Command | Description |
|---|---|
| `npm run db:push` | Push schema changes (dev / no migration file) |
| `npm run db:migrate` | Deploy pending migration files (production) |
| `npm run db:seed` | Insert demo data |
| `npm run db:studio` | Open Prisma Studio GUI |
| `npm run db:generate` | Regenerate Prisma Client |
| `npm run db:reset` | Drop + recreate + seed (dev only) |

---

## Cloud (Production)

Set `DATABASE_URL` to your Supabase (or any PostgreSQL) connection string and run:

```bash
npm run db:migrate
npm run db:seed
```

Supabase connection string format:
```
postgresql://postgres:[password]@db.[ref].supabase.co:5432/postgres?schema=public
```

---

## Schema Overview

The schema is **multi-tenant** — every data row is scoped to a `company_id`.

Key models:
- `User` — auth, roles (SUPER_ADMIN / COMPANY_ADMIN / ANALYST)
- `Company` — tenant with Stripe subscription state
- `RevenueRecord` — raw revenue entries per period
- `Leakage` — detected revenue anomalies (status: OPEN / INVESTIGATING / RESOLVED / FALSE_POSITIVE)
- `Integration` — connected external services (Stripe, GitHub, etc.)
- `BillingInvoice` — Stripe invoice records per company

---

## Related Repos

| Repo | Purpose |
|---|---|
| [revsecurecloudbackend](https://github.com/jagadishsahuai2022/revsecurecloudbackend) | Fastify API + BullMQ workers |
| [revsecurecloudfrontend](https://github.com/jagadishsahuai2022/revsecurecloudfrontend) | Vue 3 + Vite dashboard |
