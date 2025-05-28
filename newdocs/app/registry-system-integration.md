# Registry System Integration

This section explains how features in the Preventa application integrate with the Registry System for dynamic forms, selectors, and metadata.

## Entity Registration
- Each feature registers its entity type with the registry in its `index.ts`:

```typescript
import { entityDatafetcherRegistryFactory } from '../../lib/registry';
import { fetchClientes, getClienteLabel } from './components';

entityDatafetcherRegistryFactory.registerEntityType(
  'cliente',
  fetchClientes,
  getClienteLabel
);
```

## Field Type Resolvers
- Features can provide custom field type resolvers for special fields:

```typescript
export class ClienteFieldTypeResolver implements IFieldTypeResolver {
  resolveFieldType(entity, fieldName) {
    if (fieldName === 'IDtarifa') return 'entity-selector:tarifa';
    return '';
  }
}
```

- Register the resolver with the registry factory.

## Best Practices
- Keep all registry-related code within the feature directory
- Only expose necessary functions/types via `index.ts`
- Document and test custom resolvers

See also: [Feature-Based Organization](feature-organization.md) for feature structure. 