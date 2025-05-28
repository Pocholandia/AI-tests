# Validation & Business Rules

This section details the validation rules and business logic specific to the Preventa application. These must be enforced in both the UI and data layer.

## General Validation
- Required fields must not be empty or whitespace
- String fields: max lengths as specified per entity
- Numeric fields: valid numbers, ranges, and decimal precision
- Date fields: ISO 8601 UTC format
- All string fields trimmed before validation/storage

## Entity-Specific Validation

### Cliente
- Codigo: 1-20 chars, alphanumeric, unique
- Descripcion: 1-100 chars
- Contacto: up to 100 chars
- Telefono: up to 20 chars, valid format
- Email: valid email format
- Web: valid URL format
- IDtarifa: must reference valid Tarifa if present
- Inactivo: boolean

### Producto
- Codigo: 1-20 chars, alphanumeric, unique
- Descripcion: 1-100 chars
- Precio: >0, max 2 decimals
- IVA: >=0, max 2 decimals
- Inactivo: boolean

### Tarifa
- Codigo: 1-20 chars, alphanumeric, unique
- Descripcion: 1-100 chars
- Inactivo: boolean

### TarifasLineas
- IDtarifa, IDproducto: required, must reference valid entities
- Precio: required, >0, max 2 decimals
- Unique constraint: (IDtarifa, IDproducto)

### Pedido
- Numero: autoincrement, unique
- IDcliente: required, must reference valid Cliente
- FechaHora: auto-generated, immutable

### PedidosLineas
- IDpedido, IDproducto: required, must reference valid entities
- Cantidad: required, >0
- Precio: required, >0, max 2 decimals

## Business Logic Examples

```typescript
// Example: Cliente validation
if (!cliente.Codigo || cliente.Codigo.length > 20) throw Error('Codigo inválido');
if (!cliente.Descripcion || cliente.Descripcion.length > 100) throw Error('Descripcion inválida');
// ...

// Example: Producto validation
if (producto.Precio <= 0) throw Error('Precio debe ser mayor que cero');
if (producto.IVA < 0) throw Error('IVA no puede ser negativo');
// ...
```

See also: [Core Entities & Data Model](core-entities-data-model.md) for field definitions. 