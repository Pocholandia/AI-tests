# State Management

## Core Principles
- Use **Zustand** for global and domain-specific state
- One store per entity/domain (e.g., `useClientesStore`, `usePedidosStore`)
- Organize stores by feature/domain for clarity and scalability

## Store Patterns
- Define selectors within stores to minimize unnecessary renders
- Use Zustand middleware `persist` for IndexedDB or localStorage persistence
- Combine or decompose stores using Zustand's `combine` utility as needed

## Memoization & Performance
- Use `React.memo`, `useMemo`, and `useCallback` for expensive components/selectors
- Prefer selectors over direct state access to avoid mass rerenders

## Context Integration
- For cross-cutting state (e.g., notifications), use React Context
- Pass services and stores via Context or props for dependency injection

## Example Store Structure
```typescript
// src/stores/clientesStore.ts
import create from 'zustand';

export const useClientesStore = create((set, get) => ({
  clientes: [],
  fetchClientes: async () => { /* ... */ },
  // selectors and actions
}));
``` 