# Nexus IT Web

A starter implementation of the Nexus IT Web public site + secure admin dashboard, built as:

- **frontend/** — React app created with Create React App (react-scripts), not Vite.
- **backend/** — Node.js / Express / MongoDB (Mongoose) REST API.

This is a working foundation covering the core flows end-to-end (real code, real API calls,
real database models) — not a mockup. It is **not** the complete 41-part spec: pricing/testimonial/
settings admin management, notifications, and some polish items are intentionally left for a
follow-up phase so this first delivery stays reviewable and correct rather than sprawling.

## What's included

**Public site:** Home, About, Services, Projects, Project Details, Pricing, Testimonials, Contact,
Request a Quote, FAQ, Privacy Policy, Terms — all real pages, with Contact and Request a Quote
wired to the live API and stored in MongoDB.

**Admin dashboard:** `/admin/login` (JWT auth, bcrypt password hashing, protected routes),
`/admin/dashboard` (real counts from the database), `/admin/leads` (search/filter/sort, status
changes, full detail/edit view, archive, and confirmed permanent delete), `/admin/projects`
(same pattern, plus a project-creation form linked to a client/lead, progress bar, in-progress vs
completed views).

**Not yet built (left for a follow-up pass, by your choice):** pricing management, testimonial
approval workflow, contact-messages inbox UI, settings page, admin notifications UI. The backend
routes for contact messages already exist (`GET /api/admin/contact`); the rest would follow the
same pattern as Leads/Projects.

## Getting started

### 1. Backend

```
cd backend
cp .env.example .env
# edit .env: set MONGO_URI, JWT_SECRET, CLIENT_ORIGIN
npm install
SEED_ADMIN_PASSWORD=someStrongPassword npm run seed:admin   # creates your first admin login
npm run dev
```

The API runs on `http://localhost:5000` by default (see `PORT` in `.env`).

### 2. Frontend

```
cd frontend
cp .env.example .env
# edit .env if your API isn't on localhost:5000
npm install
npm start
```

The app runs on `http://localhost:3000`. Public pages need no login. Go to `/admin/login` and
sign in with the admin account you seeded above.

## Security notes

- Passwords are hashed with bcrypt; the password hash is never returned in API responses.
- Admin routes are protected by JWT middleware; the token is never hard-coded in frontend code.
- Login is rate-limited to slow down brute-force attempts.
- CORS is restricted to `CLIENT_ORIGIN`.
- Deleting a lead or project requires an explicit `?confirm=true` — the UI always offers Archive
  (soft delete) as the default, with a confirmation prompt before any permanent delete.
- No fabricated client results, testimonials, or statistics are included anywhere in this codebase.

## Project structure

```
nexus-it-web/
  backend/
    config/        MongoDB connection
    controllers/    Route handlers (auth, leads, projects, contact, dashboard)
    middleware/    JWT auth guard, error handler
    models/        Mongoose schemas (Admin, Lead, Project, ContactMessage)
    routes/        Express routers
    seed/          One-time admin account creation script
    server.js
  frontend/
    src/
      api/          Axios client with auth interceptor
      context/      AuthContext (login/logout/session)
      components/   Navbar, Footer, layouts, ProtectedRoute
      pages/public/  All public-facing pages
      pages/admin/   Login, Dashboard, Leads, LeadDetail, Projects, ProjectDetail
      styles/        Plain CSS, responsive from 320px up
```

## Next steps toward the full spec

- Pricing & Testimonial admin CRUD + public pages reading from those endpoints instead of static data
- Settings page (company info, socials, currency, timezone)
- Admin notifications for new leads/contact messages
- Sitemap.xml generation, structured data (schema.org) for SEO
- Automated tests
