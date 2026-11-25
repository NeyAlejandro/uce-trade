# UCE Trade

**UCE Trade** is a multi-platform community marketplace designed for the Universidad Central del Ecuador (UCE). It connects student entrepreneurs (**UCE Students**) who publish ventures and services with buyers (**UCE Clients**) and platform moderators (**UCE Admins**).

---

## 🏗️ Architecture & Tech Stack

- **Frontend (`frontend/`):**
  - **Web Application (`frontend/web-app/`):** Responsive React PWA + TanStack Query, packaged for Web, Desktop (Tauri), and Mobile (Capacitor).
  - **Landing Page (`frontend/landing/`):** Static Jamstack informative portal built with Astro.
- **Backend (`backend/`):**
  - **API Gateway (`backend/api-gateway/`):** Single entry point handling routing, CORS, and rate limiting (`NFR-11`).
  - **Modular Monolith (`backend/backend/`):** Single Java 21 + Spring Boot application organized into domain modules (`M1`–`M13`) following Hexagonal Architecture (Ports & Adapters), exposing REST and GraphQL APIs and communicating asynchronously across Command/Query modules via an event bus (Kafka/RabbitMQ) (`TC-01`, `TC-03`, `NFR-07`, `NFR-08`, `NFR-09`).
- **Identity & Automation (`infra/`):**
  - **Keycloak:** Identity provider handling authentication and role assignment (`UCE Admin`, `UCE Student`, `UCE Client`).
  - **n8n:** Business process automation for transactional emails and auditing workflows.
- **Polyglot Persistence Layer (`NFR-10`):**
  - **PostgreSQL:** Core transactional data, Stripe billing records, and immutable audit logs.
  - **MongoDB:** Real-time chat history and user notifications inbox.
  - **Elasticsearch:** Sub-200ms predictive search and catalog indexing.
  - **Redis:** Distributed caching and real-time WebSocket/MQTT session management.

---

## 📂 Repository Structure

```text
uce-trade/
├── .github/
│   └── pull_request_template.md       # Standardized Pull Request checklist
├── frontend/
│   ├── landing/                       # Astro Jamstack landing page
│   └── web-app/                       # React PWA multi-platform client
├── backend/
│   ├── api-gateway/                   # API Gateway service
│   └── backend/                       # Spring Boot modular monolith (M1-M13)
├── infra/
│   ├── ci-cd-pipelines/               # GitHub Actions CI & CD workflow definitions
│   │   ├── ci.yml                     # Continuous Integration (Build, Lint, Test)
│   │   └── cd.yml                     # Continuous Deployment (Docker Registry & VPS Deploy)
│   ├── docker-compose.yml             # Base multi-container setup
│   └── keycloak/                      # Keycloak realm definitions
├── .gitignore                         # Ignore rules for Java, Node, Docker, and IDEs
└── README.md                          # Project overview and setup instructions
```

---

## 🚀 Getting Started

### Prerequisites

- **Docker** & **Docker Compose** (v2.20+)
- **Java JDK 21** & **Maven 3.9+**
- **Node.js 20 LTS** & **npm / pnpm**

### Running with Docker Compose

```bash
docker compose -f infra/docker-compose.yml up -d
```

---

## 🌿 Branching Strategy & CI/CD Workflow

1. **`main` (Production):** Protected branch containing production-ready code. Every weekly sprint reaches `main` via an approved Pull Request before Thursday at 6:00 PM on demo day.
2. **`develop` (Integration & QA):** Main integration branch deployed automatically to the QA/Staging environment.
3. **`feature/<UT-ID>-<description>`:** Feature branches created from `develop` linked to a Jira issue key (e.g., `feature/UT-8-cicd-setup`).

### Pull Request Process

1. Create a feature branch from `develop`:
   ```bash
   git checkout -b feature/UT-XX-short-description develop
   ```
2. Commit changes following Conventional Commits (e.g., `feat(UT-8): add initial CI/CD pipeline`).
3. Open a Pull Request targeting `develop` and complete the PR checklist.
4. For weekly sprint releases, open a Pull Request from `develop` to `main` for team review and approval.
