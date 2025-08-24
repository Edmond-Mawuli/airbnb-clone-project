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







