# UCE Trade

**UCE Trade** is a multi-platform community marketplace designed for the Universidad Central del Ecuador (UCE). It connects student entrepreneurs (**UCE Students**) who publish ventures and services with buyers (**UCE Clients**) and platform moderators (**UCE Admins**).

---

## 🏗️ Architecture & Tech Stack

- **Frontend (`frontend/`) — Micro-Frontends (`NFR-20`), MVVM (`NFR-29`) & WebAssembly (`NFR-21`):**
  - **Landing Page (`frontend/landing/`):** Static Jamstack informative portal built with Astro and connected to **Contentful** as the Headless CMS (`NFR-06`).
  - **Micro-Frontend Application (`frontend/web-app/`):** Responsive React 19 PWA structured under the **MVVM (Model-View-ViewModel)** architectural pattern (`NFR-29`) and **Module Federation** (`NFR-20`), packaged for Web (1st), Desktop via Tauri (2nd), and Mobile via Capacitor (3rd) (`FR-36`, `TC-02`):
    - **`shell/`:** Host application managing global navigation, Keycloak authentication, layout, and Module Federation routing.
    - **`mf-marketplace/`:** Remote micro-frontend for Home categories, 20-item Explorer, `<200ms` predictive search, RAG semantic search, and Stripe Sandbox checkout.
    - **`mf-chat/`:** Remote micro-frontend for real-time buyer-seller chat (`WebSocket + STOMP /ws`), comments, star ratings, and notifications inbox.
    - **`mf-seller/`:** Remote micro-frontend for student entrepreneurs (`UCE_STUDENT`) managing venture CRUD, live commission/net-earnings preview (`FR-37`), and personal sales dashboard.
    - **`mf-admin/`:** Remote micro-frontend for platform administrators (`UCE_ADMIN`) managing pending venture moderation, commission percentage config (`0–100%`), global metrics, and immutable audit logs.
    - **`wasm-image-compressor/`:** Client-side **WebAssembly (`.wasm`)** module that compresses and resizes venture gallery images and user avatars directly in the browser before uploading to AWS S3 (`NFR-21`).
- **Edge, Load Balancing & High Availability (`backend/api-gateway/` & `infra/`):**
  - **Cloudflare WAF & Load Balancer (Nginx / AWS ELB):** Terminates HTTPS/TLS (`NFR-02`) and distributes traffic across **at least 2 instances** of the backend modular monolith with active `/actuator/health` checks to guarantee **≥ 99% availability** (`NFR-13`, `NFR-24`).
  - **API Gateway (`backend/api-gateway/`):** Single entry point handling routing, CORS, and Redis-backed rate limiting (`NFR-11`).
- **Backend Modular Monolith (`backend/uce-trade-api/`):**
  - Single Java 21 + Spring Boot 3 application organized into domain modules (`M1`–`M13`) following **Hexagonal Architecture (Ports & Adapters)** (`TC-01`, `NFR-08`, `NFR-09`, `NFR-29`).
  - Exposes OpenAPI/Swagger-documented REST (`/api/v1/...` — `NFR-27`), GraphQL (`/graphql` — `TC-03`), and **WebSocket + STOMP (`/ws` — `NFR-04`)** endpoints.
  - Integrates mandatory AI capabilities (`M13`): semantic search via **RAG** (`FR-33`) with **PostgreSQL `pgvector` embeddings** (`FR-39`), **LLM** assistant (`FR-34`), and runtime **MCP** server integration (`FR-35`).
- **Identity, Messaging, Automation & Observability (SDD & ODD — `NFR-28`):**
  - **Keycloak (`infra/keycloak/`):** OAuth 2.0 / OIDC identity provider managing `UCE_ADMIN`, `UCE_STUDENT` (`@uce.edu.ec`), and `UCE_CLIENT` roles (`FR-01`–`FR-03`, `NFR-01`).
  - **RabbitMQ:** Single AMQP event bus decoupling Command (write) and Query (read) modules (`NFR-07`, `NFR-08`).
  - **n8n:** Business process automation for asynchronous PDF invoice emails and auditing (`FR-17`, `FR-32`).
  - **Observability-Driven Development (ODD — `NFR-23`, `NFR-28`):** **Sentry** (real-time exception & performance tracking across all React micro-frontends and Spring Boot) + **Site24x7** (external 99% uptime monitoring and alerting in QA and Production).
- **Polyglot Persistence Layer (`NFR-10`) & External Storage:**
  - **PostgreSQL 16 + `pgvector`:** Core ACID transactional tables, Stripe billing records, append-only `audit_logs` (`NFR-05`), Medallion analytics layers (`Bronze`, `Silver`, `Gold` — `FR-27`), and `pgvector` embeddings for mandatory RAG semantic search (`FR-33`, `FR-39`).
  - **MongoDB 7:** Real-time chat history (`conversations`, `chat_messages`) and user notifications inbox (`notifications`).
  - **Elasticsearch 8:** Sub-200ms predictive search and catalog indexing (`venture_search_index`).
  - **Redis 7:** Distributed caching (`catalog:featured`, `config:commission:current`), rate limiting, and WebSocket + STOMP session state.
  - **AWS S3 (Object Storage):** Stores WASM-compressed venture gallery images, user avatars, and generated PDF invoices, while PostgreSQL stores only their URLs.

---

## 📂 Repository Structure

```text
uce-trade/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                     # CI: Build MFEs/WASM/Monolith, Unit, Functional, Playwright E2E, k6 & OWASP ZAP
│   │   └── cd.yml                     # CD: Build MFE & Backend Images, Push to Docker Hub & HA VPS Rollout
│   └── pull_request_template.md       # Standardized Pull Request checklist
├── frontend/
│   ├── landing/                       # Astro Jamstack landing page (Contentful CMS)
│   └── web-app/                       # React 19 Micro-Frontends (Module Federation + MVVM + WebAssembly)
│       ├── shell/                     # Host application (Navbar, Keycloak Auth, Router)
│       ├── mf-marketplace/            # Marketplace remote (Home, Explorer, Search, Checkout)
│       ├── mf-chat/                   # Chat & Interactions remote (WebSocket+STOMP Chat, Reviews)
│       ├── mf-seller/                 # Seller Panel remote (Ventures CRUD, Commission Preview, Student KPIs)
│       ├── mf-admin/                  # Admin remote (Moderation, Commission %, Audit Logs, Global KPIs)
│       └── wasm-image-compressor/     # WebAssembly (.wasm) client-side image compression module (NFR-21)
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
docker compose -f infra/docker-compose.yml -f infra/docker-compose.dev.yml up -d
docker compose -f infra/docker-compose.yml ps
```

### 4. Run Backend & Micro-Frontend Applications (Development Mode)

```bash
# 1. Start Spring Boot Modular Monolith (backend/uce-trade-api)
cd backend/uce-trade-api
mvn spring-boot:run

# 2. Start Astro Landing Page (frontend/landing)
cd ../../frontend/landing
npm install && npm run dev

# 3. Start React 19 Micro-Frontends (Shell + Remotes in frontend/web-app)
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

### Conventional Commits Specification (`NFR-27`)

All commit messages and Pull Request titles must follow the **Conventional Commits** specification and reference the Jira ticket key (`UT-XX`):

```text
<type>(<UT-ID>): <short imperative summary in English>
```

| Commit Type | When to Use | Example |
| :--- | :--- | :--- |
| **`feat`** | New feature or user story implementation | `feat(UT-19): add venture creation form with commission preview` |
| **`fix`** | Bug fix in frontend, backend, or infrastructure | `fix(UT-20): resolve Stripe sandbox refund commission reversal` |
| **`docs`** | Documentation updates (`README.md`, OpenAPI, comments) | `docs(UT-8): document micro-frontend build and Conventional Commits` |
| **`ci`** | GitHub Actions workflows, Docker Hub, or CD scripts | `ci(UT-8): add Module Federation micro-frontend build and OWASP ZAP` |
| **`test`** | Adding or updating unit, functional, E2E, or k6 load tests | `test(UT-21): add Playwright E2E checkout test and k6 script` |
| **`refactor`** | Code restructuring without changing external behavior | `refactor(UT-8): organize web-app into shell and 4 micro-frontends` |
| **`chore`** | Build scripts, dependency bumps, or configuration files | `chore(UT-8): configure pgvector image in docker-compose.yml` |
