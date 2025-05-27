# Meta-Registry Usage Guide

## Overview

This guide explains how to use the meta-registry system in the Preventa application.

## Key Features

- **Centralized table metadata storage**
- **Dynamic component discovery**
- **UI for exploring registered metadata**
- **Hooks-based API for React components**

## Accessing the Meta-Registry Explorer

The Preventa application includes a built-in Metadata Explorer UI that allows you to browse and inspect all registered table metadata.

1. Open the Preventa application
2. Click the vertical dots menu (⋮) in the top-right corner of the app bar
3. Select "Metadata Explorer" from the menu
4. The explorer will open in a dialog, showing all registered tables and their metadata

## Registering Table Metadata with Feature-Based Organization

With our feature-based organization structure, each feature registers its own metadata in its `index.ts` file. This keeps the registration code alongside the feature implementation:

```typescript
// In src/features/clientes/index.ts
import { registerTable } from '../../lib/registry';
import { ClientesList, ClienteView, ClienteEdit } from './components';

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

## Feature-Based Organization Pattern

In the Preventa application, we follow a feature-based organization pattern where each functional domain (clientes, productos, etc.) is encapsulated in its own directory structure under `src/features/`. This approach integrates seamlessly with the meta-registry system::

### Complete Feature Structure

```
/features
  /[feature-name]
    /components/      # UI components specific to this feature
      Component1.tsx  # Individual components
      Component2.tsx
      index.ts        # Re-exports all components for external use
    models.ts        # Data models and interfaces
    store.ts         # State management with Zustand
    index.ts         # Public API and meta-registry registration
```

### Feature Index Pattern

The `index.ts` file in each feature follows this pattern:

```typescript
// 1. Re-export model types for external use
export type { IModel } from './models';

// 2. Re-export store for external use
export { useFeatureStore } from './store';

// 3. Re-export components that may be used directly by other features
export { FeatureList, FeatureView, FeatureEdit } from './components';

// 4. Register with meta-registry
import { registerTable } from '../../lib/registry';
import { FeatureList, FeatureView, FeatureEdit } from './components';

registerTable('feature-name', {
  labelSingular: 'Feature',
  labelPlural: 'Features',
  describe: '{$feature.field1} ({$feature.field2})',
  defaultColumns: ['field1', 'field2', 'field3'],
  components: {
    list: FeatureList,
    view: FeatureView,
    edit: FeatureEdit,
  },
});
```

For a complete explanation of the feature-based organization pattern, see the [Feature-Based Organization Guide](../../feature-based-organization.md).

## Using Components

The meta-registry provides dynamic component rendering based on table types and view modes:

```typescript
import { TableView, TableList } from "src/lib/registry";

// In a component:
return <TableList id="Customer" />;
// or 
return <TableView id="Customer" mode="edit" recordId={customerId} />;
```

## Using Hooks

Access table metadata in React components with the provided hooks:

```typescript
import { useTableMeta, useTableColumns } from "src/lib/registry";

function CustomerComponent() {
  const meta = useTableMeta("Customer");
  const columns = useTableColumns("Customer");
  
  // Use the metadata...
}
```

## Additional Hooks

- `useTableMeta(tableId)` - Get all metadata for a table
- `useTableComponent(tableId, mode)` - Get a specific component
- `useAllTableMeta()` - Get metadata for all registered tables
- `useTableLabels(tableId)` - Get label information
- `useTableColumns(tableId)` - Get column configuration

## Testing

The meta-registry includes validation utilities to ensure metadata consistency:

```typescript
import { validateMetadataConsistency } from "src/tests/lib/registry/tableRegistry.test";

// Check for metadata errors
const errors = validateMetadataConsistency();
if (errors.length > 0) {
  console.error("Metadata validation failed:", errors);
}
```
