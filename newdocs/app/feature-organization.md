# Feature-Based Organization

This section describes the feature-based organization pattern used in the Preventa application.

## Structure
- Each domain feature (clientes, productos, tarifas, pedidos, sync) is in its own folder under `/src/features/`
- Each feature contains:
  - `components/`: UI components for the feature
  - `models.ts`: TypeScript interfaces and types
  - `store.ts`: Zustand store for state and business logic
  - `index.ts`: Public API and meta-registry registration
  - `rules.ts`: Business rules (optional)

## Example Structure
```
/src/features
  /clientes
    /components
      ClientesList.tsx
      ClienteEdit.tsx
      ClientesConfirmDeletion.tsx
      ClienteSelectorEditor.tsx
      index.ts
    models.ts
    store.ts
    index.ts
```

## Best Practices
- Keep all code for a feature together
- Only import from other features via their `index.ts` (public API)
- Encapsulate internal logic; export only what is needed
- Use named exports, avoid default exports
- Register each feature with the meta-registry in `index.ts`

See also: [Registry System Integration](registry-system-integration.md) for feature registration details. 