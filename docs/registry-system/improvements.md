# Mejoras del Sistema de Registro

Este documento describe las mejoras implementadas en el sistema de registro en Preventa App, que extiende y mejora el patrón de organización basada en características para crear un sistema más desacoplado, mantenible y consistente.

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

## Beneficios del Nuevo Sistema

1. **Reducción del acoplamiento**: Los componentes de infraestructura no dependen de características específicas
2. **Mejor mantenibilidad**: Cada característica gestiona sus propios tipos de campo y referencias a entidades
3. **Mejor testabilidad**: El patrón de inyección de dependencias facilita las pruebas unitarias
4. **Extensibilidad**: Se pueden añadir nuevas características sin modificar el código de infraestructura
5. **Seguridad de tipos**: Tipado fuerte en todo el sistema
