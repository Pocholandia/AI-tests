# Sistema de Registro de Campos (Field Registry)

Este documento describe el sistema de registro de campos implementado en Preventa App, que proporciona un mecanismo flexible y adaptable para la edición y visualización de campos de entidad.

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

Registro centralizado que implementa el patrón de registro para tipos de campos.

### 2. Adaptadores de Campo

Implementación del patrón adaptador para los diferentes tipos de campos.

### 3. Resolución de Tipos

Mecanismo para determinar dinámicamente el tipo de campo adecuado.

### 4. Utilidades con Seguridad de Tipos

Funciones auxiliares para crear definiciones de campo con seguridad de tipos.

## Integración con Tipos Marcados (Branded Types)

Una característica clave del sistema es su integración con los tipos marcados definidos en el paquete core.

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

### Definición de Campos con Seguridad de Tipos

El sistema proporciona utilidades para definir campos con seguridad de tipos:

```typescript
// En un componente de edición de entidad
import { createFields } from '../../../components/generic/TypedEntityEdit';
import { ICliente } from '../models';

// Definición de campos type-safe
const clienteFields = createFields<ICliente>({
  nombre: { label: "Nombre", required: true },
  direccion: { label: "Direcciu00f3n" },
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

## Ejemplo Completo: Cliente

```typescript
// Definición de campos con seguridad de tipos
const clienteFields = createFields<ICliente>({
  RecordIdent: { label: "ID", readOnly: true }, // Auto-detectado como 'id-field'
  nombre: { label: "Nombre", required: true },
  direccion: { label: "Direcciu00f3n" },
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
