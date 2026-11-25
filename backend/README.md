# Backend Layer

This directory contains the Java 21 + Spring Boot backend architecture for **UCE Trade** (`TC-01`, `NFR-08`, `NFR-09`, `NFR-11`):

- **`api-gateway/`**: Single entry point handling routing, CORS, and rate limiting (`NFR-11`).
- **`backend/`**: Single Spring Boot **Modular Monolith** organized into decoupled domain modules (`M1`–`M13`) designed with Hexagonal Architecture (Ports and Adapters), exposing REST, GraphQL (`/graphql`), and **WebSocket + STOMP (`/ws`)** endpoints, and communicating asynchronously between Command and Query modules via **RabbitMQ** (`NFR-04`, `NFR-07`, `NFR-08`, `NFR-09`).
