# DetailsPlugin System

## Overview

The DetailsPlugin is a configurable and reusable component in the Preventa application's plugin architecture that displays related entity details in a tabular format. It follows a factory pattern that allows for type-safe creation of specialized detail views without code duplication.

## Key Features

- **Fully Configurable**: All aspects including titles, columns, relationships, and display logic can be configured
- **Type-Safe**: Uses TypeScript generics to ensure type safety between parent and detail entities
- **Flexible Relationship Mapping**: Define how to find related details through custom functions
- **Customizable Display**: Configure columns, alignment, formatting, and conditional coloring
- **Interactive Details**: Optionally make detail rows clickable to open related entities in view mode
- **Built-in Filtering**: Filter details based on any criteria (e.g., active status)

## Technical Implementation

### Core Components

#### 1. DetailsPluginConfig Interface

```typescript
export interface DetailsPluginConfig<TParentEntity, TDetailEntity> {
  // Core configuration properties
  id: string;                 // Unique identifier for this plugin instance
  title: string;              // Title displayed in the plugin header
  emptyMessage: string;       // Message shown when no related details are found
  icon?: React.ReactNode;     // Custom icon for the plugin header
  position?: 'before-form' | 'after-form'; // Position of the plugin
  priority?: number;          // Priority within the position
  
  // Entity and relationship configuration
  parentEntityType: string;   // The entity type this plugin works with
  detailsStoreHook: () => { entities: any[] }; // Hook to access the details store
  
  // Relationship mapping functions
  getRelatedDetails: (parentEntity: TParentEntity, detailEntities: any[]) => TDetailEntity[];
  isDetailActive?: (detail: TDetailEntity) => boolean; // Optional filter function
  
  // Interaction handlers
  onDetailClick?: (parentEntity: TParentEntity, detail: TDetailEntity) => void;
  
  // Table display configuration
  columns: Array<{
    id: string;               // Unique identifier for the column
    label: string;            // Column header label
    align?: 'left' | 'right' | 'center'; // Column alignment
    format: (parentEntity: TParentEntity, detailEntity: TDetailEntity) => {
      value: string | number; // The formatted value to display
      color?: string;         // Optional color for the cell
    };
  }>;
}
```

#### 2. Factory Functions

- `createDetailsComponent`: Creates a React component based on the configuration
- `createDetailsPlugin`: Creates a complete EntityPlugin that can be registered

### Plugin Creation Workflow

1. **Define Configuration**
   - Create a configuration object that specifies how to display and interact with details

2. **Generate Plugin**
   - Use the factory function to create a plugin from the configuration

3. **Register Plugin**
   - Register the plugin with the entity plugin system

## Usage Guide

### Basic Implementation

```typescript
import { createDetailsPlugin } from '../../../components/EntityForms/plugins/DetailsPlugin';
import { useMyDetailsStore } from '../store';
import { IParentEntity } from '../models';
import { IDetailEntity } from '../../details/models';

// Create a details plugin
const MyDetailsPlugin = createDetailsPlugin<IParentEntity, IDetailEntity>({
  id: 'my-details',
  title: 'Related Details',
  emptyMessage: 'No related details found',
  
  parentEntityType: 'parent-entity',
  detailsStoreHook: () => {
    const { details } = useMyDetailsStore();
    return { entities: details };
  },
  
  getRelatedDetails: (parent, allDetails) => {
    return allDetails.filter(detail => detail.parentId === parent.RecordIdent);
  },
  
  isDetailActive: (detail) => !detail.Inactivo,
  
  onDetailClick: (parent, detail) => {
    // Open the detail entity in view mode
    // Implementation depends on your application's navigation system
  },
  
  columns: [
    {
      id: 'name',
      label: 'Name',
      format: (parent, detail) => ({ value: detail.name })
    },
    {
      id: 'status',
      label: 'Status',
      format: (parent, detail) => ({
        value: detail.status,
        color: detail.status === 'active' ? 'success.main' : 'error.main'
      })
    }
  ]
});
```

### Registering the Plugin

```typescript
import { useEntityPlugins } from '../../../components/EntityForms/EntityPluginRegistry';
import { MyDetailsPlugin } from './plugins';

function MyEntityEdit() {
  const { registerPlugin } = useEntityPlugins('parent-entity');
  
  useEffect(() => {
    // Register along with other plugins
    registerPlugin(MyDetailsPlugin);
  }, [registerPlugin]);
  
  // Rest of component implementation
}
```

## Integration with Feature-Based Architecture

The DetailsPlugin aligns with Preventa's feature-based organization pattern by:

1. **Promoting Reusability**: Allowing the same pattern to be used across different entity relationships
2. **Encapsulating Related Logic**: Keeping relationship display logic together with the parent entity
3. **Maintaining Separation of Concerns**: Using store hooks and accessor functions to avoid direct dependencies

This approach ensures consistent display of related entities across the application while maintaining the modularity of the feature-based architecture.

## Examples

### TarifaLineasPlugin

The TarifaLineasPlugin uses the DetailsPlugin to show tarifa lines related to a producto:

```typescript
import { createDetailsPlugin } from './DetailsPlugin';
import { useTarifasStore } from '../../../features/tarifas/store';
import { IProducto } from '../../../features/productos/models';
import { ITarifa, ITarifaLinea } from '../../../features/tarifas/models';

type TarifaLineaDetail = { tarifa: ITarifa; linea: ITarifaLinea };

export const TarifaLineasPlugin = createDetailsPlugin<IProducto, TarifaLineaDetail>({
  id: 'tarifa-lineas',
  title: 'Tarifas Aplicables',
  // Configuration continues...
});
```

## Performance Considerations

- The plugin uses memoization techniques to prevent unnecessary re-renders
- Only details that pass the `isDetailActive` filter are rendered
- Rendering is optimized to only update when the parent entity or related details change
