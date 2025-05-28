# Feature-Based Organization

## Directory Structure
- Each feature/domain is encapsulated in its own directory under `src/features/`
- Typical feature structure:
  - `components/`: UI components specific to the feature
  - `models.ts`: Data models and interfaces
  - `store.ts`: State management (Zustand store)
  - `schema.ts`: Database schema mapping (if applicable)
  - `index.ts`: Public API and meta-registry registration

## Feature Index Pattern
- Re-export model types, store, and components for external use
- Register table metadata with the meta-registry in `index.ts`
- Example registration:
```typescript
registerTable('clientes', {
  labelSingular: 'Cliente',
  labelPlural: 'Clientes',
  describe: '{$cliente.nombre} ({$cliente.email})',
  defaultColumns: ['nombre', 'direccion', 'telefono', 'email', 'IDtarifa'],
  components: {
    list: ClientesList,
    view: ClienteView,
    edit: ClienteEdit,
  },
});
```

## Benefits
- Keeps registration code and implementation together
- Promotes modularity and maintainability
- Enables dynamic component discovery and UI adaptation

## Best Practices
- Keep feature logic, models, and UI together
- Register all relevant metadata and components at feature initialization
- Use the meta-registry for dynamic UI and metadata management 