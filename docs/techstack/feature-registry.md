# Feature Registry

## Overview
The Feature Registry is a centralized system for managing and accessing features and their UI components throughout the application. It provides a type-safe way to register, retrieve, and manage features with their associated metadata, components, and launch functions.

## Key Concepts

### Feature
A feature is a self-contained unit of functionality that can be registered and launched. Each feature has:

- **ID**: Unique identifier (kebab-case)
- **Name**: Human-readable name
- **Description**: What the feature does
- **Icon** (optional): Visual representation (can be a string, React node, or component)
- **Component** (optional): The main React component for the feature
- **Launch function** (optional): Code to execute when the feature is activated

### Registry
A singleton registry that maintains a collection of features and provides methods to interact with them, including component management for UI features.

## Usage

### Importing

```typescript
import { featureRegistry } from '@preventa/core';
import type { FeatureConfig } from '@preventa/core';
```

### Registering a Basic Feature

```typescript
import { featureRegistry } from '@preventa/core';

// Basic registration with launch function
featureRegistry.register('demo-data', {
  name: 'Demo Data',
  description: 'Generate sample data for testing',
  icon: 'DataIcon', // Can be a string, React node, or component
  launch: () => {
    // Your feature's launch logic here
    generateDemoData();
  }
});
```

### Registering a Feature with a Component

```typescript
import { featureRegistry } from '@preventa/core';
import { MyFeatureComponent } from './MyFeature';

// Register with a component
featureRegistry.registerWithComponent(
  'my-feature',
  MyFeatureComponent, // The React component
  {
    name: 'My Feature',
    description: 'A feature with a UI component',
    icon: 'Star',
    // launch is optional when using components
  }
);

// With error handling
try {
  featureRegistry.registerWithComponent('my-feature', MyFeatureComponent, { /* ... */ });
} catch (error) {
  if (error instanceof DuplicateFeatureError) {
    console.error('This feature is already registered');
  }
}
```

### Retrieving Features

```typescript
// Get a single feature
const feature = featureRegistry.getFeature('my-feature');

// Access feature properties
console.log(feature.name); // 'My Feature'
console.log(feature.description); // 'A feature with a UI component'

// Get the component (if registered)
const FeatureComponent = feature.component;
if (FeatureComponent) {
  // Use the component
  return <FeatureComponent />;
}

// Check if a feature exists
if (featureRegistry.hasFeature('my-feature')) {
  // Feature exists
}

// Get all features
const allFeatures = featureRegistry.getAllFeatures();

// Get only features that have components
const featuresWithComponents = featureRegistry.getAllFeaturesWithComponents();

// Example usage in routing
const featureRoutes = featuresWithComponents.map((feature) => (
  <Route 
    key={feature.id}
    path={`/${feature.id}`}
    element={React.createElement(feature.component!)}
  />
));
```

### Error Handling

```typescript
try {
  const feature = featureRegistry.getFeature('non-existent-feature');
} catch (error) {
  if (error instanceof FeatureNotFoundError) {
    console.error('Feature not found');
  }
}
```

## Best Practices

1. **Feature IDs**: Use kebab-case and be consistent with naming
2. **Error Handling**: Always handle potential errors when getting features
3. **Type Safety**: Leverage TypeScript for type safety
4. **Documentation**: Document your features with clear descriptions
5. **Separation of Concerns**: Keep feature launch logic minimal and delegate to other modules

## API Reference

### `featureRegistry.register(id: string, config: FeatureConfig): void`
Registers a new feature.

- **Parameters**:
  - `id`: Unique identifier for the feature (kebab-case)
  - `config`: Feature configuration
- **Throws**:
  - `DuplicateFeatureError` if a feature with the same ID is already registered

### `featureRegistry.registerWithComponent<T = any>(id: string, component: ComponentType<T>, config: Omit<FeatureConfig, 'component'>): void`
Registers a new feature with its React component.

- **Type Parameters**:
  - `T`: Props type for the component
- **Parameters**:
  - `id`: Unique identifier for the feature (kebab-case)
  - `component`: The React component to associate with this feature
  - `config`: Feature configuration (excluding the component property)
- **Throws**:
  - `DuplicateFeatureError` if a feature with the same ID is already registered

### `featureRegistry.getFeature(id: string): Feature`
Retrieves a feature by its ID.

- **Parameters**:
  - `id`: The ID of the feature to retrieve
- **Returns**: The feature object
- **Throws**:
  - `FeatureNotFoundError` if no feature is found with the given ID

### `featureRegistry.hasFeature(id: string): boolean`
Checks if a feature with the given ID exists.

- **Parameters**:
  - `id`: The ID to check
- **Returns**: `true` if the feature exists, `false` otherwise

### `featureRegistry.getAllFeatures(): Feature[]`
Gets all registered features.

- **Returns**: An array of all registered features

### `featureRegistry.getAllFeaturesWithComponents(): Feature[]`
Gets all features that have a component registered.

- **Returns**: An array of features that have a component defined

### `featureRegistry.unregister(id: string): boolean`
Unregisters a feature.

- **Parameters**:
  - `id`: The ID of the feature to unregister
- **Returns**: `true` if the feature was found and removed, `false` otherwise

### `featureRegistry.clear(): void`
Clears all registered features.

## Example Use Cases

1. **Feature Toggles**: Enable/disable features at runtime
2. **Plugin System**: Allow dynamic registration of features with UI components
3. **Analytics**: Track feature usage and component rendering
4. **Access Control**: Control feature visibility based on permissions
5. **Progressive Enhancement**: Load features and their components on demand
6. **Dynamic Routing**: Automatically generate routes for features with components
7. **Feature Discovery**: Build UIs that automatically adapt to available features
8. **Modular Architecture**: Decouple feature implementation from feature usage
