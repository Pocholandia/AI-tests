# Estructura del Proyecto

Este documento define la estructura de carpetas y archivos obligatoria para el proyecto preventa-app. Esta estructura está diseñada para alinearse con los principios SOLID y las reglas de codificación especificadas en coding-rules.md.

## Estructura de Directorios

```
/src
  /features               # Funcionalidades organizadas por dominio
    /[nombre-feature]/    # Directorio de cada característica de dominio
      /components/        # Componentes UI específicos para esta característica
        /index.ts         # Barrel file para exportar componentes
      /hooks/             # Hooks específicos para esta característica (opcional)
      /models.ts          # Modelos e interfaces para esta característica
      /store.ts           # Estado y lógica de negocio (Zustand store)
      /rules.ts           # Reglas de negocio específicas (opcional)
      /index.ts           # API pública y registro con meta-registry
  /components             # Componentes de UI utilitarios y de infraestructura
    /common               # Componentes compartidos (AppLayout, Notification, etc.)
    /generic              # Componentes genéricos para entidades (EntityList, EntityEdit, etc.)
    /layouts              # Componentes de estructura y layout
    /registry             # Componentes para el sistema de meta-registry
    DataDemoDialog.tsx   # Componentes independientes a nivel de aplicación
  /lib                    # Bibliotecas y utilidades de infraestructura
    /registry             # Sistema de registro mejorado
      /interfaces.ts      # Interfaces centrales para el sistema de registro
      /EntityDatafetcherRegistry.ts  # Implementación del registro de entidades
      /BrandedTypeUtils.ts # Utilidades para tipos marcados (branded types)
      /BaseFieldTypeResolver.ts # Resolución de tipos de campo base
      /EntityFieldTypeResolver.ts # Resolución genérica de tipos de entidad
      /RegistryProvider.tsx # Proveedor de contexto React para el registro
      /initialize.ts     # Inicialización del sistema de registro
      /index.ts          # Exportaciones públicas del sistema
  /hooks                  # Hooks personalizados compartidos
  /models                 # Interfaces y tipos TypeScript compartidos (DEPRECATED: usar feature/models)
  /repositories           # Capa de acceso a datos
    /interfaces           # Interfaces de repositorio
    /implementations      # Implementaciones concretas (IndexedDB)
    /sync                 # Lógica de sincronización
  /utils                  # Funciones de utilidad
  /constants              # Constantes y enumeraciones
  /assets                 # Recursos estáticos (iconos, imágenes)
  /tests                  # Archivos de prueba
```

### Organización Basada en Características (Feature-Based)

La aplicación sigue una organización basada en características donde cada dominio funcional (clientes, productos, tarifas, pedidos, sync) se encapsula en su propia carpeta dentro de `/src/features/`. Esta arquitectura ya está completamente implementada para todos los dominios principales de la aplicación.

Características de esta organización:

1. **Cohesión mejorada**: Todo el código relacionado con una característica se mantiene junto
2. **Acoplamiento reducido**: Las características pueden desarrollarse con mayor independencia
3. **Mantenibilidad mejorada**: Más fácil encontrar y modificar código relacionado
4. **Límites claros**: API bien definida entre características

## Convenciones de Nomenclatura

### Carpetas y Archivos
- Carpetas: Usar camelCase para todas las carpetas (ej: `components`, `hooks`)
- Archivos de componentes: PascalCase (ej: `PedidoList.tsx`, `ClienteForm.tsx`)
- Archivos que no son componentes: camelCase (ej: `useForm.ts`, `pedidoRepository.ts`)
- Archivos de prueba: Mismo nombre que el archivo que prueban + `.test` (ej: `PedidoList.test.tsx`)

### Componentes
- Componentes de React: PascalCase (ej: `PedidoList`, `ClienteForm`)
- Componentes de presentación: sufijo `View` (ej: `PedidoListView`)
- Componentes contenedores: sin sufijo o prefijo `Container` (ej: `PedidoList` o `PedidoListContainer`)

### Interfaces y Tipos
- Interfaces: Prefijo `I` en PascalCase (ej: `IPedido`, `IClienteRepository`)
- Tipos: PascalCase sin prefijo (ej: `PedidoStatus`, `FilterOptions`)

### Stores de Zustand
- Archivos: camelCase con sufijo `Store` (ej: `pedidoStore.ts`)
- Hooks: camelCase con prefijo `use` (ej: `usePedidoStore`)

### Repositorios
- Interfaces: Prefijo `I` y sufijo `Repository` (ej: `IPedidoRepository`)
- Implementaciones: Sufijo que indique la implementación (ej: `PedidoIndexedDBRepository`)

## Principios de Organización

1. **Separación Modular**: 
   - Cada dominio (pedidos, clientes, etc.) debe tener sus propios componentes, rules y stores dentro de su directorio en `/features/`
   - Toda funcionalidad compartida entre múltiples features debe extraerse a componentes genéricos o utilidades

2. **Principio de Responsabilidad Única**: 
   - Cada archivo debe tener una única responsabilidad
   - Evitar archivos extensos (>300 líneas)
   - Cada feature debe representar un dominio de negocio claramente definido

3. **Importaciones y Dependencias**:
   - Prohibir importaciones cíclicas
   - Seguir la jerarquía de dependencias: UI → stores → repositories → utils
   - Las rules y repositories no pueden importar componentes o stores
   - **Importante:** Al importar desde features, solo importar desde su API pública (index.ts)

4. **Coherencia en la Sincronización**:
   - La feature `sync` contiene todos los componentes, modelos y lógica relacionados con sincronización
   - La lógica de sincronización de bajo nivel sigue en repositories/sync
   - Los stores deben incluir selectores para estados de sincronización

## Manejo de Aspectos Transversales (Cross-Cutting Concerns)

### Criterios para Componentes de Dominio vs Utilitarios

Para determinar si un componente debe estar en `/features/` o en `/components/`:

1. **Componentes de dominio** (en `/features/[feature]/components/`):
   - Representan entidades o conceptos específicos del negocio
   - Tienen fuertes dependencias con un modelo de dominio específico
   - Están asociados con un único dominio funcional
   - Ejemplo: `ClientesList.tsx`, `PedidoEdit.tsx`

2. **Componentes utilitarios** (en `/components/`):
   - Proveen funcionalidad genérica no específica de un dominio
   - Son independientes de los modelos de negocio concretos
   - Pueden ser reutilizados por múltiples features
   - Ejemplo: `Notification.tsx`, `DataDemoDialog.tsx`, `MetadataExplorerDialog.tsx`

### Patrones para Aspectos Transversales

1. **Utilidades Compartidas**:
   - Colocar en `/utils/` cuando son funciones puras sin estado
   - Colocar en `/hooks/` cuando necesitan estado o efectos de React
   - Agrupar por funcionalidad, no por feature que las usa

2. **Componentes de Infraestructura**:
   - Mantener en `/components/registry/` los componentes relacionados con el sistema de meta-registro
   - Mantener en `/components/generic/` los componentes reutilizables basados en patrones CRUD

3. **Servicios y Bibliotecas**:
   - Colocar en `/lib/` las funcionalidades de infraestructura que no son específicas de un dominio
   - Cada subdirectorio de `/lib/` debe tener su propio `index.ts` como API pública

## Creación de Nuevas Features

Al crear una nueva característica de dominio:

1. **Estructura requerida**:
   ```
   /features/nueva-feature/
     /components/
       /index.ts          # Barrel file (obligatorio)
       NuevoComponente.tsx
     models.ts            # Modelos de datos (obligatorio)
     store.ts             # Estado (obligatorio)
     index.ts             # API pública (obligatorio)
   ```

2. **Integración con meta-registry**:
   - Registrar la característica y sus componentes en el archivo `index.ts`
   - Seguir el patrón establecido en las features existentes

3. **Importaciones**:
   - Exportar todos los componentes a través del barrel file
   - En el `index.ts` principal, re-exportar selectivamente solo lo que debe ser público
   - Al importar de otras features, siempre usar su API pública (nunca imports directos a archivos internos)

## Exportaciones

- Usar barrel files (index.ts) para exportar múltiples componentes desde una carpeta
- Evitar exportaciones default, preferir exportaciones nombradas
- Exportar tipos junto con los componentes/funciones que los utilizan