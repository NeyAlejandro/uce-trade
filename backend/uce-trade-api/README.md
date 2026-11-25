# Spring Boot Modular Monolith (`backend/uce-trade-api`)

Single Java 21 + Spring Boot **Modular Monolith** application organized into domain modules (`M1`–`M13`) following Hexagonal Architecture (`TC-01`, `NFR-08`, `NFR-09`):

- **M1 – Identity & Access** (`FR-01` – `FR-04`)
- **M2 – Product Command / Ventures Management** (`FR-05` – `FR-07`, `FR-37`, `FR-38`)
- **M3 – Catalog Query & M4 – Search** (`FR-08` – `FR-11`)
- **M5 – Interactions, Chat & Reviews** (`FR-12` – `FR-14`)
- **M6 – Payments & M7 – Billing** (`FR-15` – `FR-19`)
- **M8 – Real-Time Notifications** (`FR-20` – `FR-23`)
- **M9 – Analytics & Dashboards** (`FR-24` – `FR-27`)
- **M10 – Audit & Traceability** (`FR-28` – `FR-30`)
- **M12 – Automation Integration (n8n)** (`FR-32`)
- **M13 – AI Integration (RAG / LLM / MCP — To be confirmed)** (`FR-33` – `FR-35`)
