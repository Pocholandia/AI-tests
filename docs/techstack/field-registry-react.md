# Field Registry System with React

This document details the React-specific implementation aspects of the Field Registry System in Preventa App.

## React Components

### FieldTypeRegistry

Centralized implementation of the field type registry:

```typescript
// Implemented in /components/FieldRegistry/FieldTypeRegistry.tsx
export const FieldTypeRegistry = new FieldTypeRegistryClass();

// Registering a field type
FieldTypeRegistry.registerFieldType({
  id: 'text',
  name: 'Text',
  category: 'basic',
  createAdapter: () => new TextFieldAdapter()
});
```

### Field Adapters for React

Implementation of adapters for Material-UI components:

```typescript
// Implemented in /components/FieldRegistry/FieldAdapters.tsx
export class TextFieldAdapter extends BaseFieldAdapter<string> {
  renderEditor({ label, required, disabled, onChange, error, helperText, ...rest }: FieldRenderProps): React.ReactNode {
    return (
      <TextField
        label={label}
        value={this.value || ''}
        onChange={(e) => {
          this.setValue(e.target.value);
          onChange?.(e.target.value);
        }}
        required={required}
        disabled={disabled}
        error={error || this.errors.length > 0}
        helperText={helperText || this.errors[0]}
        fullWidth
        margin="normal"
        {...rest}
      />
    );
  }
}
```

### EntitySelectorAdapter

Implementation of entity selector for Material-UI Autocomplete:

```typescript
// Implemented in /components/FieldRegistry/EntitySelectorAdapter.tsx
export class EntitySelectorAdapter implements FieldAdapter<any, EntitySelectorOptions> {
  // Internal values and options
  private value: any = null;
  private options: EntitySelectorOptions;

  renderEditor(props: FieldRenderProps): React.ReactNode {
    const { 
      value = this.value, 
      onChange = (val: any) => this.setValue(val),
      disabled = this.options.disabled,
      // Otras props
    } = props;

    const [options, setOptions] = React.useState<Array<{ [key: string]: any }>>([]);
    const [loading, setLoading] = React.useState(false);

    // Load options when component mounts
    React.useEffect(() => {
      const loadOptions = async () => {
        setLoading(true);
        try {
          const data = await this.options.getOptions();
          setOptions(Array.isArray(data) ? data : []);
        } catch (error) {
          console.error('Error loading options:', error);
          setOptions([]);
        } finally {
          setLoading(false);
        }
      };
      
      loadOptions();
    }, [this.options.entityType]);

    // Implementation with Material-UI Autocomplete
    return (
      <Autocomplete
        value={displayValue}
        onChange={handleChange}
        options={options}
        getOptionLabel={this.options.getOptionLabel}
        loading={loading}
        renderInput={(params) => (
          <TextField
            {...params}
            label={label}
            error={error}
            helperText={helperText}
            InputProps={{
              ...params.InputProps,
              endAdornment: (
                <React.Fragment>
                  {loading ? <CircularProgress color="inherit" size={20} /> : null}
                  {params.InputProps.endAdornment}
                </React.Fragment>
              ),
            }}
          />
        )}
      />
    );
  }
}
```

## Integration with React Context

Providing registry system access through React Context:

```typescript
// Implemented in /lib/registry/RegistryProvider.tsx
const RegistryContext = createContext<RegistryContextType>({
  entityDatafetcherRegistry: entityDatafetcherRegistryFactory.getRegistry(),
  fieldTypeResolvers: fieldTypeRegistryFactory.getResolvers(),
  getRegisteredEntityTypes: () => entityDatafetcherRegistryFactory.getRegisteredEntityTypes()
});

export function RegistryProvider({ children }: RegistryProviderProps) {
  const value = {
    entityDatafetcherRegistry: entityDatafetcherRegistryFactory.getRegistry(),
    fieldTypeResolvers: fieldTypeRegistryFactory.getResolvers(),
    getRegisteredEntityTypes: () => entityDatafetcherRegistryFactory.getRegisteredEntityTypes()
  };

  return (
    <RegistryContext.Provider value={value}>
      {children}
    </RegistryContext.Provider>
  );
}

export function useRegistry() {
  const context = useContext(RegistryContext);
  if (!context) {
    throw new Error('useRegistry must be used within a RegistryProvider');
  }
  return context;
}
```

## Initialization in the React Application

Integrating the registry system during application initialization:

```typescript
// Implemented in /App.tsx
import React from "react";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import { initializeRegistry, RegistryProvider } from "./lib/registry";

// Initialize the registry system
initializeRegistry();

export default function App() {
  return (
    <RegistryProvider>
      <RouterProvider router={router} />
    </RegistryProvider>
  );
}
```

## Best Practices for React

1. **Controlled Components**: Use React controlled components to maintain state synchronization
2. **Memoization**: Use useMemo and useCallback to optimize performance
3. **React.lazy**: Consider lazy loading for complex field types
4. **Styled Components**: Use styled-components or Material-UI's styling API for styling
5. **Testing**: Use @testing-library/react for component testing

## Performance Notes

- Entity selectors may have performance issues with large datasets. Consider pagination or lazy loading.
- The resolver system can impact performance if many resolvers are registered. Consider memoizing frequent results.

## Version Compatibility

| Dependency | Minimum Version | Recommended Version |
|------------|----------------|-------------------|
| React      | 16.8.0         | 18.0.0            |
| Material-UI | 5.0.0         | 5.10.0+           |
| React Router | 6.0.0        | 6.4.0+            |
