# State Management with Zustand

## Overview

This document outlines the state management strategy for the Preventa application using [Zustand](https://zustand-demo.pmnd.rs/), a lightweight, fast, and scalable state management solution for React applications.

## Core Concepts

### Entity Store Pattern

We use a generic entity store pattern to manage different types of entities (Pedidos, Clientes, Productos, etc.) with consistent CRUD operations and state management.

### Key Features

- **Type Safety**: Full TypeScript support for all stores
- **Centralized State**: Single source of truth for application state
- **Reactivity**: Components only re-render when their subscribed state changes
- **Middleware Support**: Easy integration with devtools and other middleware
- **Minimal Boilerplate**: Less code compared to other state management solutions

## Implementation

### Base Entity Store

The foundation of our state management is the `createEntityStoreHook` function, which provides common functionality:

```typescript
// Example of base store usage
const useEntityStore = createEntityStoreHook<EntityType, FilterType>({
  filterFunction: customFilter,
  initialState: {
    // Initial state overrides
  },
  fetchItems: async (state) => {
    // Custom data fetching logic
  }
});
```

### Store Structure

Each store follows this structure:

1. **State**: The current state of the store
2. **Actions**: Functions to update the state
3. **Selectors**: Functions to select specific pieces of state

### Example: Pedidos Store

```typescript
// Creating a pedidos store with custom functionality
const usePedidosStoreBase = createEntityStoreHook<IPedido, IPedidosFiltros>({
  filterFunction: filterPedidos,
  initialState: {
    filteredItems: [],
    filtros: initialPedidosFiltros,
  },
  fetchItems: async (state) => {
    const pedidos = await fetchPedidos();
    return pedidos;
  }
});

// Extending the base store with custom functionality
export const usePedidosStore = (): PedidosStoreState => {
  const store = usePedidosStoreBase();
  
  const fetchPedidosHandler = async () => {
    // Custom fetch implementation
  };
  
  return {
    ...store,
    fetchPedidos: fetchPedidosHandler,
  };
};
```

## Advantages

1. **Simplicity**: Minimal boilerplate code
2. **Performance**: Optimized re-renders with selector functions
3. **Flexibility**: Easy to extend with custom functionality
4. **Type Safety**: Full TypeScript support
5. **DevTools**: Built-in support for Redux DevTools
6. **Middleware**: Easy to add persistence, logging, etc.

## Common Patterns

### Accessing State

```typescript
// In a component
const { items, loading } = usePedidosStore(state => ({
  items: state.items,
  loading: state.loading
}));
```

### Updating State

```typescript
// Using actions
const { addItem, updateItem } = usePedidosStore();

// Add new item
addItem(newItem);

// Update existing item
updateItem(updatedItem);
```

### Using Selectors

```typescript
// Memoized selector
const activeItems = usePedidosStore(
  useCallback(state => state.items.filter(item => item.isActive), [])
);
```

## Best Practices

1. **Keep Stores Focused**: Each store should manage a specific domain
2. **Use Selectors**: For derived state to prevent unnecessary re-renders
3. **Keep Actions Simple**: Move complex logic to separate functions
4. **Test Stores**: Write unit tests for store actions and selectors
5. **Use DevTools**: For debugging state changes

## Related Documentation

- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [React Context vs Zustand](https://docs.pmnd.rs/zustand/getting-started/comparison)
- [State Management Patterns](https://docs.pmnd.rs/zustand/guides/practice-with-no-store-actions)

## See Also

- [Feature Organization](../feature-based-organization.md)
- [TypeScript Guide](../techstack/typescript.md)
- [Testing Guide](../testing-strategy.md)
