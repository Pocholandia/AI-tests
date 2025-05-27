# Registry System Integration Guide

This guide explains how to integrate new features with the Registry System in Preventa App, enabling entity self-registration and the definition of custom field type resolvers.

## Basic Integration

To integrate a new feature with the Registry System, follow these steps:

### 1. Register Entity Types

```typescript
// En tu archivo feature/index.ts
import { entityDatafetcherRegistryFactory } from '../../lib/registry';
import { fetchEntidades, getEntidadLabel } from './components';

// During feature initialization
export const registerMiCaracteristica = (): FeatureConfig => {
  // Normal registration with the table system
  registerTable(FEATURES.MI_ENTIDAD, { ... });
  
  // Registration with the new registry system
  entityDatafetcherRegistryFactory.registerEntityType(
    'mi-entidad',          // Entity type (singular)
    fetchEntidades,        // Function to fetch entities
    getEntidadLabel        // Function to get the label
  );
  
  return miCaracteristicaFeature;
};
```

### 2. Implement Data Fetching Function

The `fetchEntidades` function must implement the `EntityFetcher` interface:

```typescript
// En tu archivo feature/components/index.ts
import { EntityFetcher } from '../../lib/registry';
import { IMiEntidad } from '../models';
import { useMiEntidadStore } from '../store';

// Implement the data fetching function
export const fetchEntidades: EntityFetcher<IMiEntidad> = async (query?: string) => {
  const { entidades } = useMiEntidadStore.getState();
  
  // If there's a query, filter the entities
  if (query) {
    return entidades.filter(e => 
      e.nombre?.toLowerCase().includes(query.toLowerCase()) ||
      e.codigo?.toLowerCase().includes(query.toLowerCase())
    );
  }
  
  return entidades;
};
```

### 3. Implement Label Function

The `getEntidadLabel` function must implement the `OptionLabelGetter` interface:

```typescript
// En tu archivo feature/components/index.ts
import { OptionLabelGetter } from '../../lib/registry';
import { IMiEntidad } from '../models';

// Implement the function to get the label
export const getEntidadLabel: OptionLabelGetter = (entidad: IMiEntidad) => {
  if (!entidad) return '';
  return `${entidad.nombre || ''} (${entidad.codigo || ''})`;
};
```

## Implementing a Custom Resolver

For more advanced cases, you can implement a custom resolver for your feature:

### 1. Create the Resolver

```typescript
// In your feature/MiEntidadFieldTypeResolver.ts file
import { IFieldTypeResolver } from '../../lib/registry/interfaces';
import { entityDatafetcherRegistryFactory } from '../../lib/registry';

/**
 * Specific resolver for the Mi Entidad feature
 */
export class MiEntidadFieldTypeResolver implements IFieldTypeResolver {
  resolveFieldType(entity: any, fieldName: string, metadata?: any): string {
    // Specific logic for your feature
    const lowerFieldName = fieldName.toLowerCase();
    
    // Detect fields that reference your entity
    if (lowerFieldName === 'identidad' || lowerFieldName === 'mientidadid') {
      return 'entity-selector:mi-entidad';
    }
    
    // For fields with specific formats in your domain
    if (lowerFieldName.endsWith('_codigo_especial')) {
      return 'mi-formato-especial';
    }
    
    return ''; // Delegate to other resolvers if not handled here
  }
  
  resolveFieldOptions(entity: any, fieldName: string, fieldType: string, metadata?: any): any {
    // Specific configuration for your domain field types
    if (fieldType === 'entity-selector:mi-entidad') {
      return {
        entityType: 'mi-entidad',
        // Other specific options...
      };
    }
    
    if (fieldType === 'mi-formato-especial') {
      return {
        // Custom options for your format
        format: 'XXX-000-000',
        validator: /^[A-Z]{3}-\d{3}-\d{3}$/
      };
    }
    
    return null; // Delegate to other resolvers
  }
}

// Create instance and register it
export const miEntidadFieldTypeResolver = new MiEntidadFieldTypeResolver();

// Register with the factory
import { fieldTypeRegistryFactory } from '../../lib/registry';
fieldTypeRegistryFactory.registerResolver(miEntidadFieldTypeResolver);
```

### 2. Importar y Registrar el Resolver

```typescript
// En tu archivo feature/index.ts
import { miEntidadFieldTypeResolver } from './MiEntidadFieldTypeResolver';

// The resolver self-registers, but we ensure it's imported
const _ = miEntidadFieldTypeResolver;
```

## Usage in Components

Once your entity is registered, it can be automatically selected in fields that reference it:

```typescript
// In a component from another feature
import { createFields } from '../../components/generic/TypedEntityEdit';
import { IOtraEntidad } from '../models';

const camposEditables = createFields<IOtraEntidad>({
  // The field will be automatically detected as a selector for your entity
  // thanks to the EntityFieldTypeResolver or your custom resolver
  IDMiEntidad: { label: "Mi Entidad" },
  
  // Other fields...
  nombre: { label: "Nombre" },
  activo: { label: "Activo" }
});
```

## Best Practices

1. **Cohesion**: Keep all feature-related definitions in its directory
2. **Encapsulation**: Only expose what's necessary through the index.ts file
3. **Naming**: Use consistent names for functions and types
4. **Documentation**: Document exported functions and components
5. **Testing**: Write tests for custom resolvers

## Troubleshooting

### Entity doesn't appear in selectors

- Verify the entity is registered correctly
- Check that the fetchEntidades function returns data
- Look for console errors during registration

### Field type not detected correctly

- Verify the resolver is registered correctly
- Check resolver priority (last registered has higher priority)
- Review field name structure and conventions
