# Field Registry System

> **Nota**: Esta documentación ha sido reorganizada. Por favor, consulte [Sistema de Registro - Documentación actualizada](./registry-system/README.md).

## Redirección

El sistema de registro de campos ahora está documentado en las siguientes ubicaciones:

- [Visión general del sistema](./registry-system/README.md)
- [Registro de campos](./registry-system/field-registry.md)
- [Mejoras del sistema](./registry-system/improvements.md)
- [Guía de integración](./registry-system/integration-guide.md)
- [Implementación con React](./techstack/field-registry-react.md)
- [Patrones de diseño utilizados](./generic/registry-patterns.md)

Por favor, actualice sus enlaces a la nueva ubicación.

## Visión General

El sistema Field Registry implementa un patrón de registro centralizado para tipos de campos de formulario, combinado con un patrón adaptador para la representación de campos y un sistema de resolución de tipos en tiempo de ejecución. Este enfoque proporciona:

- **Consistencia de UI**: Todos los campos del mismo tipo se comportan y se ven de forma consistente
- **Extensibilidad**: Nuevos tipos de campos pueden añadirse sin modificar código existente
- **Adaptabilidad**: Los campos pueden adaptarse a diferentes bibliotecas de UI
- **Seguridad de tipos**: Integración con TypeScript para verificar tipos en tiempo de compilación
- **Detección dinámica**: Resolución automática de tipos de campo basada en los datos
- **Soporte para tipos marcados (branded types)**: Integración con el sistema de tipos del core

## Arquitectura

El sistema Field Registry está compuesto por varios componentes clave:

### 1. FieldTypeRegistry

Registro centralizado que implementa el patrón de registro para tipos de campos:

```typescript
// Implementado en /components/generic/FieldTypeRegistry.tsx
export const FieldTypeRegistry = new FieldTypeRegistryClass();

// Registro de un tipo de campo
FieldTypeRegistry.registerFieldType({
  id: 'text',
  name: 'Text',
  category: 'basic',
  createAdapter: () => new TextFieldAdapter()
});
```

### 2. Adaptadores de Campo

Implementación del patrón adaptador para los diferentes tipos de campos:

```typescript
// Implementado en /components/generic/FieldAdapters.tsx
export abstract class BaseFieldAdapter<T = any, Options = any> implements FieldAdapter<T, Options> {
  // Métodos comunes para todos los tipos de campo
  setValue(value: T): void { /* ... */ }
  getValue(): T { /* ... */ }
  validate(): boolean { /* ... */ }
  // Métodos abstractos que cada adaptador debe implementar
  abstract renderEditor(props: FieldRenderProps): React.ReactNode;
  renderViewer(props: FieldRenderProps): React.ReactNode { /* ... */ }
}
```

### 3. Resolución de Tipos

Mecanismo para determinar dinámicamente el tipo de campo adecuado:

```typescript
// Implementado en varios archivos de resolución de tipos
export const brandedTypeResolver: FieldTypeResolver = {
  resolveFieldType: (entity, fieldName) => {
    // Lógica para determinar el tipo de campo
    if (isTipoEmail(entity[fieldName])) return 'email';
    // ...
  },
  resolveFieldOptions: (entity, fieldName, fieldType) => {
    // Lógica para determinar opciones de campo
  }
};
```

### 4. Utilidades con Seguridad de Tipos

Funciones auxiliares para crear definiciones de campo con seguridad de tipos:

```typescript
// Implementado en /components/generic/TypedEntityEdit.ts
export function createField<T, K extends EntityFields<T>>(
  field: K,
  options: Omit<EntityEditField, 'field'>
): EntityEditField { /* ... */ }

export function createFields<T>(
  fieldDefinitions: {
    [K in keyof T]?: Omit<EntityEditField, 'field'>
  }
): EntityEditField[] { /* ... */ }
```

## Integración con Tipos Marcados (Branded Types)

Una característica clave del sistema es su integración con los tipos marcados definidos en el paquete core:

```typescript
// Definidos en packages/core/src/types/common/primitives.ts
export type tipoEmail = string & { readonly [emailBrand]: never };
export type tipoPrecio = number & { readonly [precioBrand]: never };
// ...
```

El sistema Field Registry detecta automáticamente estos tipos marcados y asigna el tipo de campo adecuado:

```typescript
// Implementado en /components/generic/BrandedTypeResolver.ts
export const isTipoEmail = (value: any): boolean =>
  typeof value === 'string' && hasSymbolBrand(value, 'emailBrand');

// En el resolver
if (isTipoEmail(value)) return 'email';
```

## Tipos de Campos Predefinidos

El sistema incluye varios tipos de campos predefinidos:

| ID | Descripción | Tipo de Datos | Branded Type |
|----|-------------|---------------|-------------|
| `text` | Campo de texto básico | `string` | `tipoNombre`, etc. |
| `number` | Campo numérico | `number` | - |
| `checkbox` | Casilla de verificación | `boolean` | `tipoInactivo` |
| `date` | Selector de fecha | `Date` | `tipoFecha` |
| `datetime` | Fecha y hora | `Date` | `tipoFechaHora` |
| `email` | Email con validación | `string` | `tipoEmail` |
| `tel` | Teléfono con formato | `string` | `tipoTelefono` |
| `select` | Selector de opciones | `string` | - |
| `multi-select` | Selector múltiple | `string[]` | - |
| `precio` | Campo monetario | `number` | `tipoPrecio` |
| `importe` | Campo de importe | `number` | `tipoImporte` |
| `porcentaje` | Campo de porcentaje | `number` | `tipoPorcentajeIVA` |
| `id-field` | Campo ID (solo lectura) | `string` | `tipoID` |
| `nif-cif` | NIF/CIF con validación | `string` | - |

## Uso del Field Registry

### Registro de Tipos de Campo

```typescript
// En la inicialización de la aplicación
import { registerDefaultFieldTypes } from './components/generic/DefaultFieldTypes';
import { registerBrandedFieldTypes } from './components/generic/BrandedFieldTypes';

registerDefaultFieldTypes();
registerBrandedFieldTypes();
```

### Definición de Campos con Seguridad de Tipos

```typescript
// En un componente de edición de entidad
import { createFields } from '../../../components/generic/TypedEntityEdit';
import { ICliente } from '../models';

// Definición de campos type-safe
const clienteFields = createFields<ICliente>({
  nombre: { label: "Nombre", required: true },
  direccion: { label: "Dirección" },
  telefono: { label: "Teléfono" }, // Tipo auto-detectado como 'tel'
  email: { label: "Email" },       // Tipo auto-detectado como 'email'
  IDtarifa: { label: "ID Tarifa", editor: "tarifa.selector" },
  Inactivo: { label: "Inactivo" }, // Tipo auto-detectado como 'checkbox'
  observaciones: { label: "Observaciones" }
});
```

### Uso con EntityEdit

```typescript
<EntityEdit
  entity={cliente}
  title="Cliente"
  fields={clienteFields}
  viewMode={ENTITY_VIEW_MODE.EVM_EDIT}
  onSave={handleSave}
/>
```

## Extensión del Sistema

### Creación de un Nuevo Tipo de Campo

1. **Crear un adaptador para el tipo**:

```typescript
export class MyCustomFieldAdapter extends BaseFieldAdapter<MyValueType, MyOptionsType> {
  renderEditor(props: FieldRenderProps): React.ReactNode {
    // Implementación de renderizado para edición
  }
  
  renderViewer(props: FieldRenderProps): React.ReactNode {
    // Implementación de renderizado para visualización
  }
}
```

2. **Registrar el tipo de campo**:

```typescript
FieldTypeRegistry.registerFieldType({
  id: 'my-custom-field',
  name: 'Mi Campo Personalizado',
  category: 'custom',
  createAdapter: () => new MyCustomFieldAdapter(),
  defaultOptions: { /* opciones por defecto */ }
});
```

### Creación de un Resolver para Tipos Personalizados

```typescript
FieldTypeRegistry.registerResolver({
  resolveFieldType: (entity, fieldName) => {
    // Lógica para determinar cuando usar tu tipo personalizado
    if (fieldName.startsWith('custom')) return 'my-custom-field';
    return '';
  },
  resolveFieldOptions: () => ({
    // Opciones por defecto para este resolver
  })
});
```

## Integración con Feature-Based Organization

El sistema Field Registry extiende el patrón de organización basada en características:

1. **Tipos de Campo Específicos de Características**: Cada característica puede definir sus propios tipos de campo

```
/features
  /clientes
    /components
      ClienteFieldTypes.tsx   # Tipos de campo específicos de clientes
```

2. **Inicialización de Tipos en Características**: Cada característica puede registrar sus tipos en su inicialización

```typescript
// En features/clientes/index.ts
import { registerClienteFieldTypes } from './components/ClienteFieldTypes';

// Registro de tipos de campo para clientes
registerClienteFieldTypes();
```

## Buenas Prácticas

- **Reutilización de adaptadores**: Extiende los adaptadores existentes cuando sea posible
- **Consistencia visual**: Mantén una experiencia de usuario consistente entre tipos similares
- **Validación apropiada**: Implementa validación adecuada para cada tipo de campo
- **Opciones con valores predeterminados**: Proporciona opciones predeterminadas razonables
- **Seguridad de tipos**: Utiliza las utilidades con seguridad de tipos para definiciones de campo
- **Detección inteligente**: Implementa resolvers que detecten patrones comunes

## Beneficios del Enfoque

1. **Menos código repetitivo**: Define la lógica de renderizado y validación una sola vez
2. **Coherencia visual**: Todos los campos del mismo tipo se ven y comportan igual
3. **Adaptabilidad**: Facilita cambiar la implementación subyacente sin afectar a los componentes
4. **Escalabilidad**: Añadir nuevos tipos de campo no requiere modificar código existente
5. **Seguridad de tipos**: Integración con TypeScript para prevenir errores
6. **Mejor mantenibilidad**: Centraliza la lógica de renderizado y validación

## Ejemplo Completo: Cliente

```typescript
// Definición de campos con seguridad de tipos
const clienteFields = createFields<ICliente>({
  RecordIdent: { label: "ID", readOnly: true }, // Auto-detectado como 'id-field'
  nombre: { label: "Nombre", required: true },
  direccion: { label: "Dirección" },
  telefono: { label: "Teléfono" },             // Auto-detectado como 'tel'
  email: { label: "Email" },                   // Auto-detectado como 'email'
  IDtarifa: { label: "Tarifa", editor: "tarifa.selector" },
  Inactivo: { label: "Inactivo" },             // Auto-detectado como 'checkbox'
  observaciones: { label: "Observaciones" }    // Auto-detectado como 'textarea'
});

// Uso en componente
function ClienteEdit({ cliente, onSave }) {
  return (
    <EntityEdit
      entity={cliente}
      title="Cliente"
      fields={clienteFields}
      viewMode={ENTITY_VIEW_MODE.EVM_EDIT}
      validationSchema={clienteSchema}
      onSave={onSave}
    />
  );
}
```

Este sistema completa la implementación del patrón de organización basada en características, extendiendo el concepto a los campos de formulario y aprovechando el sistema de tipos marcados para proporcionar una experiencia de desarrollo más robusta y una interfaz de usuario más consistente.

## Sistema de Registro Mejorado

La arquitectura del Field Registry ha evolucionado para alinearse mejor con el patrón de organización basada en características. Se ha implementado un sistema totalmente desacoplado donde:

1. **Interfaces claras**: Se definen interfaces para cada componente del sistema
2. **Desacoplamiento total**: Los componentes de infraestructura no dependen de características específicas
3. **Auto-registro**: Cada característica se registra automáticamente con el sistema
4. **Descubrimiento dinámico**: Los tipos de entidad se detectan en tiempo de ejecución
5. **Inyección de dependencias**: Se eliminan los patrones singleton rígidos

Para más detalles sobre las mejoras implementadas, incluyendo ejemplos de código y patrones de uso, consulte:

[Mejoras del Sistema de Registro](./registry-system-improvements.md)

Estas mejoras completan la transición al patrón de organización basada en características, ahora aplicándose a todos los aspectos de la aplicación, incluyendo el sistema de registros y la resolución de tipos de campo.
