# Registry System Improvements

> **Nota**: Esta documentación ha sido reorganizada. Por favor, consulte [Sistema de Registro - Documentación actualizada](./registry-system/README.md).

## Redirección

Las mejoras del sistema de registro ahora están documentadas en las siguientes ubicaciones:

- [Visión general del sistema](./registry-system/README.md)
- [Mejoras del sistema](./registry-system/improvements.md)
- [Guía de integración](./registry-system/integration-guide.md)
- [Implementación con React](./techstack/field-registry-react.md)
- [Patrones de diseño utilizados](./generic/registry-patterns.md)

Por favor, actualice sus enlaces a la nueva ubicación.

## Visión General

El sistema de registro mejorado implementa un patrón de diseño basado en interfaces con inyección de dependencias, eliminando el acoplamiento directo entre los componentes de infraestructura y las características específicas del dominio. Este enfoque proporciona:

- **Desacoplamiento total**: Los componentes de infraestructura no dependen de características específicas
- **Registro automático**: Las características se auto-registran durante la inicialización
- **Extensibilidad mejorada**: Nuevas características pueden implementarse sin modificar código existente
- **Testabilidad**: La inyección de dependencias facilita las pruebas unitarias
- **Cohesión**: Cada característica contiene todas sus definiciones de tipos en una ubicación
- **Consistencia**: Alineado con el patrón de organización basada en características ya implementado

## Nueva Arquitectura

El sistema de registro mejorado está compuesto por varios componentes clave:

### 1. Interfaces Centrales

Un conjunto de interfaces que definen los contratos que deben implementar los componentes:

```typescript
// Implementado en /src/lib/registry/interfaces.ts
export interface IEntityDatafetcherRegistry {
  register<T>(entityType: string, fetcher: EntityFetcher<T>, labelGetter?: OptionLabelGetter): void;
  getFetcher<T>(entityType: string): EntityFetcher<T> | undefined;
  getLabelGetter(entityType: string): OptionLabelGetter | undefined;
  fetch<T>(entityType: string, query?: string): Promise<T[]>;
  getRegisteredEntityTypes(): string[];
}

export interface IFieldTypeResolver {
  resolveFieldType(entity: any, fieldName: string, metadata?: any): string;
  resolveFieldOptions(entity: any, fieldName: string, fieldType: string, metadata?: any): any;
}
```

### 2. Implementaciones Desacopladas

Implementaciones concretas que siguen el patrón de inyección de dependencias:

```typescript
// Implementado en /src/lib/registry/EntityDatafetcherRegistry.ts
export class EntityDatafetcherRegistry implements IEntityDatafetcherRegistry {
  private registry: Map<string, EntityFetcher> = new Map();
  private labelGetters: Map<string, OptionLabelGetter> = new Map();

  // Métodos implementados...
}
```

### 3. Factorías para Registros

Factorías que proporcionan un punto central para el registro de componentes:

```typescript
// Implementado en /src/lib/registry/EntityDatafetcherRegistryFactory.ts
class EntityDatafetcherRegistryFactory {
  private static instance: EntityDatafetcherRegistryFactory;
  private registry: IEntityDatafetcherRegistry;
  
  // Métodos implementados...
  
  public registerEntityType<T>(
    entityType: string,
    fetcher: EntityFetcher<T>,
    labelGetter?: OptionLabelGetter
  ): void {
    // Registro de tipos de entidad
  }
}
```

### 4. Utilidades Genéricas

Componentes genéricos que no dependen de características específicas:

```typescript
// Implementado en /src/lib/registry/BrandedTypeUtils.ts
export class BrandedTypeUtils implements IBrandedTypeUtils {
  public hasSymbolBrand(value: any, brandName: string): boolean {
    // Implementación...
  }
  
  // Métodos implementados...
}
```

### 5. Resolvedores Genéricos

Resolvedores que pueden descubrir dinámicamente los tipos de entidad registrados:

```typescript
// Implementado en /src/lib/registry/EntityFieldTypeResolver.ts
export class EntityFieldTypeResolver implements IFieldTypeResolver {
  resolveFieldType(entity: any, fieldName: string, metadata?: any): string {
    // Descubrimiento dinámico de tipos de entidad
    const lowerFieldName = fieldName.toLowerCase();
    if (lowerFieldName.startsWith('id')) {
      const entityType = lowerFieldName.substring(2);
      const registeredTypes = entityDatafetcherRegistryFactory.getRegisteredEntityTypes();
      // Lógica para resolver el tipo...
    }
    return '';
  }
}
```

### 6. Soporte para React Context

Integración con React Context para acceso global al registro:

```typescript
// Implementado en /src/lib/registry/RegistryProvider.tsx
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

## Integración con Características

Cada característica ahora puede registrarse automáticamente con el sistema de registro:

```typescript
// Implementado en /src/features/tarifas/index.ts
export const registerTarifasFeature = (): FeatureConfig => {
  // Registro con el registro de tablas de la aplicación
  registerTable(FEATURES.TARIFAS, { ... });
  
  // Registro con nuestro nuevo sistema de registro
  entityDatafetcherRegistryFactory.registerEntityType('tarifa', fetchTarifas, getTarifaLabel);
  
  return tarifasFeature;
};
```

## Resolvedores Específicos por Característica

Cada característica puede definir sus propios resolvedores de tipo de campo:

```typescript
// Implementado en /src/features/tarifas/TarifaFieldTypeResolver.ts
export class TarifaFieldTypeResolver implements IFieldTypeResolver {
  resolveFieldType(entity: any, fieldName: string, metadata?: any): string {
    // Lógica específica para tarifas...
    const lowerFieldName = fieldName.toLowerCase();
    if (lowerFieldName === 'idtarifa' || lowerFieldName === 'idtarifas') {
      return 'entity-selector:tarifa';
    }
    return '';
  }
  
  resolveFieldOptions(entity: any, fieldName: string, fieldType: string, metadata?: any): any {
    // Opciones específicas para tarifas...
    if (fieldType === 'entity-selector:tarifa') {
      return { ... };
    }
    return null;
  }
}
```

## Inicialización del Sistema

El sistema se inicializa durante el arranque de la aplicación:

```typescript
// Implementado en /src/App.tsx
import { initializeRegistry, RegistryProvider } from "./lib/registry";

// Inicialización del sistema de registro
initializeRegistry();

export default function App() {
  return (
    <RegistryProvider>
      <RouterProvider router={router} />
    </RegistryProvider>
  );
}
```

## Compatibilidad con Código Existente

El sistema mantiene compatibilidad con el código existente mediante adaptadores:

```typescript
// Implementado en /src/components/fieldregistry/BrandedTypeResolver.ts
export const brandedTypeResolver: FieldTypeResolver = {
  resolveFieldType: (entity, fieldName) => {
    // Intenta resolver referencias a entidades usando el resolver de campo de entidad
    const entityType = entityFieldTypeResolver.resolveFieldType(entity, fieldName);
    if (entityType) {
      return entityType;
    }
    
    // Utiliza las utilidades para verificar tipos marcados
    const brandedType = brandedTypeUtils.getFieldTypeFromBrandedType(value);
    if (brandedType) {
      return brandedType;
    }
    return '';
  },
  
  resolveFieldOptions: (entity, fieldName, fieldType) => {
    // Delega al resolver de campos de entidad
    if (typeof fieldType === 'string' && fieldType.startsWith('entity-selector:')) {
      return entityFieldTypeResolver.resolveFieldOptions(entity, fieldName, fieldType);
    }
    // Lógica adicional...
  }
};
```

## Mejores Prácticas

### Registro de Tipos de Entidad

```typescript
// En la característica:
import { entityDatafetcherRegistryFactory } from '../../lib/registry';

// Registrar durante la inicialización de la característica
entityDatafetcherRegistryFactory.registerEntityType(
  'cliente',             // Tipo de entidad
  fetchClientes,         // Función para obtener datos
  getClienteLabel        // Función para obtener etiqueta
);
```

### Creación de un Resolver Específico

```typescript
import { IFieldTypeResolver } from '../../lib/registry/interfaces';

export class ClienteFieldTypeResolver implements IFieldTypeResolver {
  resolveFieldType(entity: any, fieldName: string): string {
    // Implementación específica para la característica
  }
  
  resolveFieldOptions(entity: any, fieldName: string, fieldType: string): any {
    // Implementación específica para la característica
  }
}

// Registrar con la factoría de registro
import { fieldTypeRegistryFactory } from '../../lib/registry';
fieldTypeRegistryFactory.registerResolver(new ClienteFieldTypeResolver());
```

## Beneficios del Nuevo Sistema

1. **Reducción del acoplamiento**: Los componentes de infraestructura no dependen de características específicas
2. **Mejor mantenibilidad**: Cada característica gestiona sus propios tipos de campo y referencias a entidades
3. **Mejor testabilidad**: El patrón de inyección de dependencias facilita las pruebas unitarias
4. **Extensibilidad**: Se pueden añadir nuevas características sin modificar el código de infraestructura
5. **Seguridad de tipos**: Tipado fuerte en todo el sistema

## Migración desde el Sistema Anterior

Para migrar una característica existente al nuevo sistema de registro:

1. Crear un resolver específico para la característica implementando la interfaz `IFieldTypeResolver`
2. Registrar la característica con el sistema de registro durante su inicialización
3. Mover la lógica específica de la característica desde el resolver genérico al específico
4. Actualizar las importaciones para utilizar los componentes del nuevo sistema

```typescript
// Antes:
import { brandedTypeResolver } from '../components/fieldregistry/BrandedTypeResolver';

// Después:
import { fieldTypeRegistryFactory } from '../lib/registry';
import { MyFeatureFieldTypeResolver } from './MyFeatureFieldTypeResolver';

fieldTypeRegistryFactory.registerResolver(new MyFeatureFieldTypeResolver());
```

## Conclusión

El sistema de registro mejorado refuerza el patrón de organización basada en características en Preventa App, creando una arquitectura más desacoplada, mantenible y extensible. Cada característica ahora tiene control completo sobre sus propios tipos y puede registrarse dinámicamente con el sistema, sin requerir modificaciones en los componentes de infraestructura.
