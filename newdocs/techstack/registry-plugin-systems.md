# Registry & Plugin Systems

## Field Registry (React)
- Centralized `FieldTypeRegistry` for registering and resolving field types
- Field adapters wrap Material-UI components for editing and viewing
- Register new field types with `registerFieldType({ id, name, category, createAdapter })`
- Use React Context to provide registry access throughout the app

## Entity Plugin System
- Plugins extend entity forms without modifying core functionality
- Register plugins per entity type using `useEntityPlugins('entityType')`
- Plugins can be positioned: `before-form`, `after-form`, `sidebar`, `footer`
- Plugins receive entity data, form state, and custom props
- Priority and condition functions control rendering order and visibility

## Pluggable Editor Registry
- Register arbitrary editor components by string key (namespaced, e.g., `cliente.selector`)
- All editors must accept a generic `EntityEditorProps` interface
- Editors can be overridden or unregistered at runtime
- Use `getEditorOrDefault` to provide fallback editors
- Registry is discoverable for debugging and dynamic UI

## Feature Registry
- Central registry for features and their UI components
- Register features with `featureRegistry.register(id, config)` or `registerWithComponent`
- Retrieve features, check existence, and list all features/components
- Enables feature toggles, plugin systems, analytics, access control, and dynamic routing

## Best Practices
- Register all field types, plugins, and editors at app startup
- Use clear, namespaced keys for organization
- Document new registry entries and their props
- Leverage TypeScript for type safety throughout 