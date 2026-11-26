# UCE Trade

**UCE Trade** is a multi-platform community marketplace designed for the Universidad Central del Ecuador (UCE). It connects student entrepreneurs (**UCE Students**) who publish ventures and services with buyers (**UCE Clients**) and platform moderators (**UCE Admins**).

---

## 🏗️ Architecture & Tech Stack

- **Frontend (`frontend/`):**
  - **Web Application (`frontend/web-app/`):** Responsive React 19 PWA + TanStack Query + MUI + Tailwind CSS, packaged for Web, Desktop (Tauri), and Mobile (Capacitor), instrumented with **Sentry** for frontend error tracking.
  - **Landing Page (`frontend/landing/`):** Static Jamstack informative portal built with Astro and connected to **Contentful** as the Headless CMS (`NFR-06`).
- **Edge, Load Balancing & High Availability (`backend/api-gateway/` & `infra/`):**
  - **Cloudflare WAF & Load Balancer (Nginx / AWS ELB):** Terminates HTTPS/TLS (`NFR-02`) and distributes traffic across **at least 2 instances** of the backend modular monolith with active `/actuator/health` checks to guarantee **≥ 99% availability** (`NFR-13`).
  - **API Gateway (`backend/api-gateway/`):** Single entry point handling routing, CORS, and Redis-backed rate limiting (`NFR-11`).
- **Backend Modular Monolith (`backend/uce-trade-api/`):**
  - Single Java 21 + Spring Boot 3 application organized into domain modules (`M1`–`M13`) following **Hexagonal Architecture (Ports & Adapters)** (`TC-01`, `NFR-08`, `NFR-09`).
  - Exposes REST (`/api/v1/...`), GraphQL (`/graphql`), and **WebSocket + STOMP (`/ws`)** endpoints (`TC-03`, `NFR-04`).
  - Integrates mandatory AI capabilities (`M13`): semantic search via **RAG** (`FR-33`), **LLM** assistant (`FR-34`), and runtime **MCP** server integration (`FR-35`).
- **Identity, Messaging, Automation & Observability (ODD):**
  - **Keycloak (`infra/keycloak/`):** OAuth 2.0 / OIDC identity provider managing `UCE_ADMIN`, `UCE_STUDENT` (`@uce.edu.ec`), and `UCE_CLIENT` roles (`FR-01`–`FR-03`, `NFR-01`).
  - **RabbitMQ:** Single AMQP event bus decoupling Command (write) and Query (read) modules (`NFR-07`, `NFR-08`).
  - **n8n:** Business process automation for asynchronous PDF invoice emails and auditing (`FR-17`, `FR-32`).
  - **Observability-Driven Development (ODD):** **Sentry** (real-time exception & performance tracking in React and Spring Boot) + **Site24x7** (external 99% uptime monitoring and alerting).
- **Polyglot Persistence Layer (`NFR-10`) & External Storage:**
  - **PostgreSQL 16 + `pgvector`:** Core ACID transactional tables, Stripe billing records, append-only `audit_logs` (`NFR-05`), Medallion analytics layers (`Bronze`, `Silver`, `Gold` — `FR-27`), and `pgvector` embeddings for mandatory RAG semantic search (`FR-33`).
  - **MongoDB 7:** Real-time chat history (`conversations`, `chat_messages`) and user notifications inbox (`notifications`).
  - **Elasticsearch 8:** Sub-200ms predictive search and catalog indexing (`venture_search_index`).
  - **Redis 7:** Distributed caching (`catalog:featured`, `config:commission:current`), rate limiting, and WebSocket + STOMP session state.
  - **AWS S3 (Object Storage):** Stores binary files (venture gallery images, user avatars, and generated PDF invoices), while PostgreSQL stores only their URLs.

---

## 📂 Repository Structure

```text
uce-trade/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                     # CI: Build, Unit, Functional, Playwright E2E, k6 Load & OWASP ZAP
│   │   └── cd.yml                     # CD: Multi-Stage Docker Build, Push to Docker Hub & VPS Rollout
│   └── pull_request_template.md       # Standardized Pull Request checklist
├── frontend/
│   ├── landing/                       # Astro Jamstack landing page (Contentful CMS)
│   └── web-app/                       # React 19 PWA multi-platform client
├── backend/
│   ├── api-gateway/                   # API Gateway service
│   └── uce-trade-api/                 # Spring Boot modular monolith (M1-M13)
├── infra/
│   ├── docker-compose.yml             # Base multi-container setup (PostgreSQL+pgvector, Mongo, Redis, RabbitMQ, Keycloak)
│   ├── docker-compose.dev.yml         # Local development overrides
│   └── keycloak/                      # Keycloak realm definitions (realm-export.json)
├── .gitignore                         # Ignore rules for Java, Node, Docker, and IDEs
└── README.md                          # Project overview, setup guide, and Conventional Commits
```

---

## 🚀 How to Run the Project Locally

### 1. Prerequisites

- **Docker** & **Docker Compose** (v2.20+)
- **Java JDK 21** & **Maven 3.9+**
- **Node.js 20 LTS** & **npm / pnpm**

### 2. Clone the Repository

```bash
git clone https://github.com/NeyAlejandro/uce-trade.git
cd uce-trade
```

### 3. Start the Infrastructure Containers (Docker Compose)

Start PostgreSQL (`pgvector`), MongoDB, Redis, RabbitMQ, and Keycloak (with automatic realm import):

```bash
# Start local development stack with dev overrides
docker compose -f infra/docker-compose.yml -f infra/docker-compose.dev.yml up -d

# Check container status
docker compose -f infra/docker-compose.yml ps
```

- **PostgreSQL:** `localhost:5432` (DB: `ucetrade_dev`, User: `ucetrade`)
- **MongoDB:** `localhost:27017`
- **Redis:** `localhost:6379`
- **RabbitMQ Management UI:** `http://localhost:15672` (`guest` / `guest`)
- **Keycloak Admin Console:** `http://localhost:8080` (`admin` / `admin`, Realm: `uce-trade`)

### 4. Run Backend & Frontend Applications (Development Mode)

```bash
# 1. Start Spring Boot Modular Monolith (backend/uce-trade-api)
cd backend/uce-trade-api
mvn spring-boot:run

# 2. Start Astro Landing Page (frontend/landing)
cd ../../frontend/landing
npm install && npm run dev

# 3. Start React 19 PWA Web App (frontend/web-app)
cd ../web-app
npm install && npm run dev
```

---

## 🌿 Branching Strategy & Conventional Commits

### Branching Model (Simplified GitFlow)

1. **`main` (Production):** Protected branch containing production-ready releases. Every weekly sprint reaches `main` via an approved Pull Request before Thursday at 6:00 PM on demo day (`NFR-18`).
2. **`develop` (Integration & QA):** Primary integration branch deployed automatically to the QA/Staging environment.
3. **`feature/<UT-ID>-<short-description>`:** Feature branches created from `develop` linked to a Jira issue key (e.g., `feature/UT-8-cicd-setup`).
4. **`hotfix/<UT-ID>-<short-description>`:** Emergency production fixes branched from `main` and merged into both `main` and `develop`.

### Conventional Commits Specification

All commit messages and Pull Request titles must follow the **Conventional Commits** specification and reference the Jira ticket key (`UT-XX`):

```text
<type>(<UT-ID>): <short imperative summary in English>
```

| Commit Type | When to Use | Example |
| :--- | :--- | :--- |
| **`feat`** | New feature or user story implementation | `feat(UT-19): add venture creation form with commission preview` |
| **`fix`** | Bug fix in frontend, backend, or infrastructure | `fix(UT-20): resolve Stripe sandbox refund commission reversal` |
| **`docs`** | Documentation updates (`README.md`, comments, API docs) | `docs(UT-8): document Conventional Commits and Docker Hub rollout` |
| **`ci`** | GitHub Actions workflows, Docker Hub, or CD scripts | `ci(UT-8): add k6 load testing, Playwright E2E and OWASP ZAP stages` |
| **`test`** | Adding or updating unit, functional, E2E, or k6 load tests | `test(UT-21): add Playwright E2E checkout test and k6 script` |
| **`refactor`** | Code restructuring without changing external behavior | `refactor(UT-8): rename monolith module folder to uce-trade-api` |
| **`chore`** | Build scripts, dependency bumps, or configuration files | `chore(UT-8): configure pgvector image in docker-compose.yml` |
