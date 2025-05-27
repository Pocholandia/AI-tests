# Organización Basada en Características (Feature-Based Organization)

Este documento describe el patrón de organización basado en características implementado en Preventa App y proporciona lineamientos para mantener y extender esta estructura.

## Visión General

La aplicación Preventa ahora sigue una arquitectura basada en características donde cada dominio funcional de la aplicación (clientes, productos, tarifas, pedidos) se encapsula en su propia carpeta dentro del directorio `/features`. 

Este enfoque ofrece varias ventajas:

- **Mayor cohesión**: Todo el código relacionado con una característica se mantiene junto
- **Menor acoplamiento**: Las características pueden desarrollarse de forma más independiente
- **Mejor mantenibilidad**: Facilita encontrar y actualizar código relacionado
- **Mejor escalabilidad**: Facilita agregar nuevas características sin afectar las existentes
- **Límites claros**: Define claramente las responsabilidades y APIs públicas de cada característica

## Estructura de una Característica

Cada característica sigue una estructura común:

```
/features
  /[nombre-característica]
    /components           # Componentes UI específicos de la característica
      /index.ts           # Exporta los componentes (barrel file)
    models.ts             # Interfaces y tipos para la característica
    store.ts              # Estado y lógica de negocio (Zustand store)
    index.ts              # API pública y registro con meta-registry
```

### Componentes del Index.ts

El archivo `index.ts` de cada característica es crucial ya que define la API pública y registra la característica con el sistema de meta-registry. Debe contener:

1. **Exportaciones de tipos**: Re-exportar los tipos necesarios con `export type`
2. **Exportaciones de store**: Re-exportar el hook de store para uso externo
3. **Exportaciones de componentes**: Re-exportar los componentes necesarios para ruteo y uso externo
4. **Registro con meta-registry**: Registrar la característica y sus componentes

## Integración con Meta-Registry

Cada característica debe registrarse con el sistema de meta-registry para habilitar funcionalidades como exploración de metadatos, vistas dinámicas y capacidades de extensión. Ejemplo:

```typescript
import { registerTable } from '../../lib/registry';
import { Component1, Component2 } from './components';

registerTable('nombreEntidad', {
  labelSingular: 'Etiqueta singular',
  labelPlural: 'Etiqueta plural',
  describe: '{$entidad.campo1} ({$entidad.campo2})',
  defaultColumns: ['campo1', 'campo2', 'campo3'],
  components: {
    list: Component1,
    view: Component2,
    edit: Component3,
  },
});
```

## Importaciones y Exportaciones

Cada característica debe seguir prácticas consistentes de importación y exportación:

1. **Importaciones de otras características**: Solo importar desde la API pública (archivo index.ts) de otras características
2. **Exposición limitada**: No exponer componentes internos o detalles de implementación
3. **Tipos**: Usar `export type` para exportaciones de tipos TypeScript
4. **Exportaciones nombradas**: Evitar exportaciones default, preferir exportaciones nombradas

## Migración al Patrón Feature-Based

Al migrar componentes existentes a la estructura feature-based:

1. **Crear la estructura**: Establecer los directorios necesarios dentro de `/features`
2. **Migrar modelos**: Mover e importar correctamente tipos y dependencias
3. **Migrar stores**: Mover la lógica de estado manteniendo la API
4. **Migrar componentes**: Organizar los componentes en el directorio de la característica
5. **Actualizar importaciones**: Asegurar que todas las referencias en la aplicación apunten a la nueva ubicación
6. **Registrar con meta-registry**: Implementar la integración con el sistema de registro

## Buenas Prácticas

- **Encapsulación**: Mantener la lógica interna encapsulada dentro de la característica
- **API pública mínima**: Exportar solo lo necesario para integración externa
- **Cohesión**: Asegurar que todo el código en una característica está relacionado
- **Transversalidad**: Extraer funcionalidad compartida a componentes genéricos o utilidades
- **Consistencia**: Seguir patrones similares en todas las características

## Extensiones del Patrón

El patrón de organización basada en características ha sido extendido a otros aspectos de la aplicación:

- **[Sistema de Registro](./registry-system/)**: Un sistema completo de registro que extiende el patrón para proporcionar:
  - **[Registro de Campos](./registry-system/field-registry.md)**: Sistema flexible y con seguridad de tipos para la edición de entidades
  - **[Mejoras del Sistema](./registry-system/improvements.md)**: Implementación desacoplada donde cada característica puede auto-registrarse
  - **[Guía de Integración](./registry-system/integration-guide.md)**: Cómo integrar nuevas características con el sistema

- **[Patrones de Registro](./generic/registry-patterns.md)**: Documentación de los patrones de diseño genéricos implementados en el sistema de registro

## Ejemplo: Característica Clientes

La característica de clientes demuestra el patrón:

```
/features
  /clientes
    /components
      ClientesList.tsx         # Lista de clientes
      ClienteEdit.tsx          # Edición de clientes
      ClientesConfirmDeletion.tsx # Confirmación de eliminación
      ClienteSelectorEditor.tsx # Selector de clientes para otras entidades
      index.ts                 # Exportaciones de componentes
    models.ts                  # Interface ICliente y tipos relacionados
    store.ts                   # useClientesStore con Zustand
    index.ts                   # API pública y registro con meta-registry
```

Estos componentes y archivos proporcionan toda la funcionalidad necesaria para la gestión de clientes mientras mantienen claros los límites del dominio.
