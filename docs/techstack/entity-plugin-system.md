# Entity Plugin System

## Architecture Overview

The Entity Plugin System extends the core form editing capabilities of the Preventa application by providing a flexible, React-based plugin architecture that allows dynamic enhancement of entity forms without modifying their core functionality.

### Core Components

#### 1. EntityPluginRegistry

The central registry that manages plugins and provides subscription mechanisms for reactive UI updates.

```typescript
// Key interfaces
export interface EntityPluginProps {
  entity: any;
  entityType: string;
  formState: any; // Access to the form state
  isReadOnly: boolean;
  onEntityChange?: (updatedEntity: any) => void;
  [key: string]: any; // Allow for custom plugin-specific props
}

export interface EntityPlugin {
  id: string;
  name: string;
  position: 'before-form' | 'after-form' | 'sidebar' | 'footer';
  priority: number; // For ordering multiple plugins in the same position
  component: React.ComponentType<EntityPluginProps>;
  condition?: (entity: any, entityType: string) => boolean;
}
```

**Implementation Details:**
- Uses React Context for global access to plugin registry
- Maintains plugins by entity type in memory store
- Provides subscription mechanism for real-time UI updates when plugins are registered
- Implements priority-based rendering order within position groups

#### 2. EntityExtendedEdit Component

An enhanced version of the EntityEdit component that supports plugins and manages their layout.

```typescript
export interface EntityExtendedEditProps<T> extends Omit<EntityEditProps<T>, 'onFormStateChange'> {
  entityType: string; // The type of entity being edited
  pluginProps?: Record<string, any>; // Additional props to pass to plugins
}
```

**Implementation Details:**
- Wraps the standard EntityEdit component
- Manages form state and propagates it to plugins
- Handles dynamic plugin layout based on position properties
- Conditionally renders plugins based on their condition functions

#### 3. Form State Propagation

Real-time form state is propagated to plugins using React Hook Form's watch API:

```typescript
// In EntityEdit component
useEffect(() => {
  if (onFormStateChange) {
    const subscription = watch((formValues) => {
      onFormStateChange({
        values: formValues,
        errors,
        isDirty,
        isValid: Object.keys(errors).length === 0
      });
    });
    
    return () => subscription.unsubscribe();
  }
}, [watch, errors, isDirty, onFormStateChange]);
```

## Technical Implementation

### Plugin Registration Workflow

1. **Global Provider Setup**
   - The `EntityPluginProvider` is added at the application root to make the plugin system available throughout the app.

2. **Entity-Specific Plugin Registration**
   - Plugins are registered for specific entity types using the `useEntityPlugins` hook:
   ```typescript
   const { registerPlugin } = useEntityPlugins('cliente');
   useEffect(() => {
     registerPlugin(SyncInfoPlugin);
     registerPlugin(RelatedEntitiesPlugin);
   }, [registerPlugin]);
   ```

3. **Subscription System**
   - Components subscribe to plugin changes using the context's subscription mechanism
   - When plugins are registered or updated, subscribed components are notified and re-render

### Plugin Rendering Process

1. The `EntityExtendedEdit` component retrieves plugins for the specified entity type
2. Plugins are filtered based on their condition functions
3. Remaining plugins are grouped by position ('before-form', 'after-form', etc.)
4. Within each position group, plugins are sorted by priority
5. Each plugin receives the current entity data, form state, and any additional props

### Form State Integration

The plugin system integrates with React Hook Form to provide real-time form state to plugins:

1. `EntityEdit` uses the `watch` API to observe form changes
2. Form values and validation state are passed to the parent `EntityExtendedEdit` component
3. This state is then distributed to all rendered plugins
4. Plugins can react to form state changes without triggering unnecessary re-renders

## Plugin Development Guide

### Creating a New Plugin

1. **Define Your Plugin Component**

```typescript
import React from 'react';
import { EntityPlugin, EntityPluginProps } from '../EntityPluginRegistry';

export function MyCustomPlugin({ entity, entityType, formState, isReadOnly }: EntityPluginProps) {
  // Plugin UI implementation
  return (
    <div>
      {/* Plugin content */}
    </div>
  );
}

export const MyCustomPluginConfig: EntityPlugin = {
  id: 'my-custom-plugin',
  name: 'My Custom Plugin',
  position: 'sidebar',
  priority: 30,
  component: MyCustomPlugin,
  condition: (entity) => true // Optional condition
};
```

2. **Register Your Plugin**

```typescript
import { MyCustomPluginConfig } from './plugins/MyCustomPlugin';
import { useEntityPlugins } from '../EntityPluginRegistry';

function MyEntityEditor() {
  const { registerPlugin } = useEntityPlugins('entityType');
  
  useEffect(() => {
    registerPlugin(MyCustomPluginConfig);
  }, [registerPlugin]);
  
  // ...
}
```

### Plugin Positioning

Plugins can be positioned in four distinct areas:

- `before-form`: Rendered above the main form
- `after-form`: Rendered below the main form
- `sidebar`: Rendered in a sidebar column next to the form
- `footer`: Rendered at the bottom of the form container

### Conditional Rendering

Plugins can include a condition function that determines whether they should be rendered:

```typescript
condition: (entity, entityType) => {
  // Only show for entities that have sync info
  return entity && (entity.syncStatus !== undefined);
}
```

## Extensibility Features

### 1. Type Safety

The plugin system is built with TypeScript generics to provide type safety:

```typescript
// Type-safe entity plugin implementation
export function EntityExtendedEdit<T extends FieldValues = FieldValues>({
  entity,
  entityType,
  pluginProps,
  ...entityEditProps
}: EntityExtendedEditProps<T>) {
  // Implementation
}
```

### 2. Custom Props

Plugins can receive additional custom props through the `pluginProps` parameter:

```typescript
<EntityExtendedEdit
  entity={cliente}
  entityType="cliente"
  pluginProps={{
    customSetting: true,
    apiEndpoint: '/api/special'
  }}
  // other props
/>
```

### 3. Performance Considerations

- The subscription system ensures plugins only re-render when relevant data changes
- Plugins are mounted/unmounted based on their condition functions
- Form state is efficiently shared between plugins to minimize redundant processing

## Integration with Feature-Based Architecture

The plugin system integrates with Preventa's feature-based architecture by:

1. Extending entity components within each feature module
2. Allowing feature-specific plugins to be registered
3. Maintaining separation of concerns between core form functionality and feature-specific enhancements

This approach ensures that features can extend entity forms without tight coupling to the core implementation.
