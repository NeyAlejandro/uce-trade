# React 19 Micro-Frontend Application (`frontend/web-app`)

Built with **React 19 + Vite + TypeScript** following a **Micro-Frontend Architecture with Module Federation (`NFR-20`)**, the **MVVM (Model-View-ViewModel) pattern (`NFR-29`)**, and a client-side **WebAssembly (`.wasm`)** module (`NFR-21`).

## Micro-Frontend Structure (`NFR-20`)

- **`shell/`**: Host application responsible for top-level navigation, Keycloak OIDC authentication, global theme tokens (`#0d2149`, `#efb034`), and dynamic loading of remote micro-frontends.
- **`mf-marketplace/`**: Catalog Home, 20-item Explorer, `<200ms` predictive search, RAG semantic search, and Stripe Sandbox checkout.
- **`mf-chat/`**: Real-time buyer-seller chat (`WebSocket + STOMP /ws`), venture comments, 1–5 star ratings, and notification inbox.
- **`mf-seller/`**: Student entrepreneur panel (`UCE_STUDENT`) for venture CRUD, live commission & net-earnings preview (`FR-37`), and personal sales dashboard.
- **`mf-admin/`**: Administrator panel (`UCE_ADMIN`) for pending venture approval/rejection, commission % configuration (`0–100%`), global revenue metrics, and immutable audit logs.
- **`wasm-image-compressor/`**: Client-side **WebAssembly** module that compresses and resizes venture gallery images in the browser prior to uploading to AWS S3 (`NFR-21`).

## MVVM Pattern (`NFR-29`)

- **View:** Declarative React 19 + Material UI (MUI) + Tailwind CSS components.
- **ViewModel:** Custom hooks (`useVentureViewModel`, `useChatViewModel`, `useCommission`), Zustand stores, and TanStack Query hooks holding presentation state and validation logic.
- **Model:** TypeScript domain DTOs, Axios REST services (`/api/v1/...`), GraphQL queries (`/graphql`), and STOMP WebSocket client (`/ws`).
