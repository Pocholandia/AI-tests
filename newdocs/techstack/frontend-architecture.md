# Frontend Architecture & Patterns

## Framework & Core Technologies
- **React + TypeScript** as the UI foundation
- **Material UI** for visual components and theming
- **Zustand** for state management, organized by domain/feature
- **IndexedDB (Dexie.js)** for local persistence
- **Service Worker** for offline support and caching
- **Vite** as the build tool

## Folder Structure (Feature-Based)
- `src/components/` – Pure presentation components
- `src/containers/` – Components connected to logic or state
- `src/hooks/` – Reusable hooks (e.g., `useForm`, `useNotification`)
- `src/repositories/` – Data access layer (interfaces and implementations)
- `src/rules/` – Pure business logic and validation (must not import React/UI)
- `src/stores/` – Zustand stores, one per entity/domain
- `src/features/` – Each feature encapsulates its own models, store, components, and meta-registry registration

## Key Patterns
- **SOLID principles** applied to all layers
- **Separation of concerns**: Data, business logic, and UI are strictly separated
- **Container/Presentation pattern** for components
- **Single-responsibility**: Each component/module should have one clear purpose
- **Limit file size**: Max 300 lines per JSX/TSX file

## State Management
- One Zustand store per entity/domain (e.g., `useClientesStore`)
- Use selectors to minimize unnecessary renders
- Middleware `persist` for IndexedDB/localStorage
- Combine/decompose stores as needed

## Dependency Injection
- Pass repository services via React Context or props
- Use factory hooks (e.g., `createXService`) for configurable instantiation

## Offline-First & Synchronization
- Register Service Worker (Workbox/manual)
- Use Dexie to mark entity sync state (`new/updated/deleted`)
- `useSync()` hook for online detection, queue processing, and conflict resolution
- UI elements for sync status and manual/auto sync 