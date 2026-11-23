# Backend Layer

This directory contains the Java 21 + Spring Boot microservices for **UCE Trade** (`TC-01`, `NFR-08`, `NFR-09`):

- **`api-gateway/`**: Single entry point handling routing, CORS, and rate limiting (`NFR-11`).
- **`services/`**: Core domain microservices designed with Hexagonal Architecture (Ports and Adapters), communicating synchronously via REST/GraphQL/gRPC and asynchronously via Kafka/RabbitMQ.
