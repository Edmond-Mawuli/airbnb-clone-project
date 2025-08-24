# airbnb-clone-project
A comprehensive, real-world application that simulates building a scalable booking platform like Airbnb. The project emphasizes backend architecture, database design, API development, application security, CI/CD, and effective team collaboration.

---

##  Project Goals
- Build a robust backend and API layer for a booking platform.
- Practice clean architecture, modular design, and documentation.
- Apply security best practices across authentication, authorization, and data handling.
- Set up CI/CD for reliable builds, tests, and deployments.
- Collaborate using Git/GitHub with clear roles and workflows.

---

##  Learning Objectives
By completing this project i will:
- Master collaborative workflows using GitHub (branches, PRs, reviews).
- Deepen understanding of backend architecture & relational database design.
- Implement advanced API security (authN, authZ, rate limiting, input validation).
- Design and manage CI/CD pipelines for automated building, testing, and deployment.
- Strengthen planning and documentation for complex software projects.
- Integrate **Django**, **MySQL** (or PostgreSQL), and **GraphQL** within one ecosystem.

---

##  Requirements
- A GitHub account and basic Git proficiency.
- Comfortable with Markdown for `README.md`.
- Prior experience with a backend framework (Django recommended) and SQL databases (MySQL or PostgreSQL).
- Understanding of SDLC practices including security, CI/CD, and schema design.
- Familiarity with Docker and CI tools (e.g., GitHub Actions).


##  Team Roles

- **Product Manager (PM):** Owns scope and priorities, defines acceptance criteria, maintains roadmap, coordinates releases.
- **Tech Lead / Architect:** Defines architecture, chooses patterns/tools, ensures non-functional requirements (security, scalability, observability).
- **Backend Developer:** Implements APIs (REST/GraphQL), business logic, services, tests, and integrations; maintains code quality.
- **Database Administrator (DBA):** Designs schema/indexes, manages migrations, performance tuning, backup/restore policies, data security.
- **DevOps / Platform Engineer:** Owns CI/CD, infrastructure-as-code, containerization, monitoring/logging, secrets management.
- **QA / Test Engineer:** Designs test plans, builds automated tests, validates features, tracks defects, ensures release readiness.
- **Security Engineer (shared role in small teams):** Threat modeling, security reviews, secret rotation, incident response planning.
- **Technical Writer (shared role):** Maintains README, ADRs, API docs, runbooks, and onboarding materials.

##  Technology Stack
- **Language & Framework**
  - **Python + Django**: Primary web framework, ORM, admin, REST (via DRF) and GraphQL (via Graphene).
- **API Layers**
  - **Django REST Framework (DRF)**: RESTful endpoints for public/mobile clients.
  - **GraphQL (Graphene-Django)**: Flexible queries/mutations for web app and power users.
- **Database**
  - **MySQL** (or **PostgreSQL**): Relational data store for core entities (users, properties, bookings, payments, reviews).
- **Caching & Async**
  - **Redis**: Caching sessions, rate-limit counters, and hot queries.
  - **Celery**: Background jobs (emails, payment webhooks, cleanup).
- **Auth & Security**
  - **JWT/OAuth2** (e.g., `django-rest-framework-simplejwt`) and **Django auth**.
- **Containerization & Web**
  - **Docker / Docker Compose**: Local dev & prod parity.
  - **Nginx / Gunicorn**: Reverse proxy and WSGI app server.
- **CI/CD**
  - **GitHub Actions**: Lint, tests, security scans, build & deploy.
- **Observability**
  - **Sentry / OpenTelemetry / Prometheus + Grafana**: Error tracking & metrics (optional but recommended).


---

##  Database Design

### Key Entities & Fields
- **User**
  - `id (PK)`, `email (unique)`, `password_hash`, `full_name`, `phone`, `role (host|guest|admin)`, `created_at`
- **Property**
  - `id (PK)`, `host_id (FK->User)`, `title`, `description`, `type (apt, house, room)`, `address`, `city`, `country`, `lat`, `lng`, `nightly_price`, `max_guests`, `created_at`
- **PropertyImage**
  - `id (PK)`, `property_id (FK)`, `url`, `is_primary (bool)`, `created_at`
- **Booking**
  - `id (PK)`, `guest_id (FK->User)`, `property_id (FK)`, `check_in (date)`, `check_out (date)`, `guests_count`, `status (pending|confirmed|cancelled|completed)`, `total_amount`, `created_at`
- **Payment**
  - `id (PK)`, `booking_id (FK)`, `provider (stripe, paystack, etc.)`, `amount`, `currency`, `status (authorized|captured|refunded|failed)`, `transaction_ref`, `created_at`
- **Review**
  - `id (PK)`, `booking_id (FK)`, `author_id (FK->User)`, `property_id (FK)`, `rating (1–5)`, `comment`, `created_at`
- **Availability (optional optimization)**
  - `id (PK)`, `property_id (FK)`, `date`, `is_available (bool)`, `price_override (nullable)`

### Relationships (high level)
- A **User (host)** can own many **Properties**.
- A **User (guest)** can make many **Bookings**.
- A **Booking** belongs to one **Property** and one **guest (User)**.
- A **Payment** belongs to one **Booking**.
- A **Review** is written by a **User** for a **Property**, typically after a completed **Booking**.
- **PropertyImage** belongs to a **Property**.
- Optional **Availability** rows help fast availability checks and dynamic pricing.

---

##  Feature Breakdown

- **User Management**
  - Sign up/login (JWT), profile management, roles (host/guest/admin), password reset, KYC (optional).
- **Property Management (Host)**
  - Create/update listings with photos, amenities, pricing, availability calendar, and house rules.
- **Search & Discovery**
  - Filter by location, date range, price, guests; map view; pagination and sorting; cached hot queries.
- **Booking System**
  - Real-time availability checks, create/cancel bookings, compute totals, enforce rules (min nights, max guests).
- **Payments**
  - Provider integration (e.g., Stripe/Paystack), intent/capture flow, webhooks, refunds, receipts.
- **Reviews & Ratings**
  - Post-stay reviews with star ratings and comments; aggregate scores per property.
- **Notifications**
  - Email/SMS for booking status, reminders, and payment outcomes (via Celery workers).
- **Admin & Moderation**
  - Manage users/listings, resolve disputes, handle refunds, and monitor platform health.

---

## API Security

- **Authentication**
  - JWT-based auth for APIs; refresh/token rotation; secure cookie (httpOnly) or Authorization header.
- **Authorization**
  - Role & object-level permissions (hosts only manage their properties; guests only their bookings).
- **Input Validation & Sanitization**
  - DRF serializers/validators; strict schema for GraphQL; reject unknown fields; enforce types & ranges.
- **Rate Limiting & Throttling**
  - IP/user-based limits for login, search, and booking endpoints (e.g., Django throttling + Redis counters).
- **Transport & Secrets**
  - Enforce HTTPS; HSTS; rotate API keys; store secrets in CI/CD vault or GitHub Encrypted Secrets.
- **Data Protection**
  - Hash passwords (PBKDF2/Argon2); encrypt sensitive fields at rest (where applicable); GDPR-style deletion.
- **Audit & Monitoring**
  - Structured logs, security event auditing, anomaly detection, alerting (Sentry/Prometheus).

Why it matters:
- **User data** must remain confidential and intact and **Payments** demand strong integrity, non-repudiation, and secure handling of webhooks and the **Platform trust** depends on preventing abuse (fraud, brute force, scraping).

---

##  CI/CD Pipeline

- **Continuous Integration (CI)**
  - On every PR/push: run linters (flake8/ruff), type checks (mypy), unit/integration tests, security scans (`bandit`, `pip-audit`), and build Docker image.
- **Continuous Delivery/Deployment (CD)**
  - On `main` merge: push image to registry, run DB migrations, deploy to staging/production (e.g., via GitHub Actions).
- **Suggested Tools**
  - **GitHub Actions** for workflows, **Docker** for images, **docker-compose** for local parity, **Sentry** for release health, **Terraform** (optional) for IaC.

Example workflow names:
- `ci.yml`: lint + test + build
- `deploy.yml`: build + push image + deploy + migrate

---






