# Data Validation Rules

## Overview

This document defines comprehensive validation rules for all entities in the Preventa application. These rules must be implemented in both the UI forms and the data layer to ensure data integrity and consistency.

## General Validation Rules

### String Fields

- **Required strings**: Must not be empty or contain only whitespace
- **Maximum lengths**: As specified per field below
- **Trimming**: All string fields should be trimmed of whitespace before validation and storage
- **Special characters**: Unless otherwise specified, only alphanumeric characters, spaces, and basic punctuation are allowed

### Numeric Fields

- **Required numbers**: Must have a valid numeric value
- **Ranges**: Must be within the specified minimum and maximum values
- **Decimals**: Precision as specified per field
- **Rounding**: Monetary values should be rounded to 2 decimal places

### Date Fields

- **Format**: ISO 8601 (YYYY-MM-DDTHH:mm:ss.sssZ) in UTC
- **Range validation**: As specified per field
- **Current dates**: When a current date is required, use UTC

## Entity-Specific Validation Rules

### Cliente

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **Codigo** | string | Yes | 1-20 chars, alphanumeric, must be unique |
| **Descripcion** | string | Yes | 1-100 chars |
| **Contacto** | string | No | 0-100 chars |
| **Telefono** | string | No | 0-20 chars, valid phone format if provided |
| **Email** | string | No | Valid email format if provided |
| **Web** | string | No | Valid URL format if provided |
| **IDtarifa** | string | No | Must reference a valid Tarifa.RecordIdent if provided |
| **Inactivo** | boolean | Yes | true/false |

```typescript
export const validateCliente = (cliente: ICliente): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!cliente.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!cliente.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  
  // Codigo validation
  if (!cliente.Codigo) {
    errors.push({ field: 'Codigo', message: 'El código es obligatorio' });
  } else if (cliente.Codigo.length > 20) {
    errors.push({ field: 'Codigo', message: 'El código no puede tener más de 20 caracteres' });
  } else if (!/^[a-zA-Z0-9\-_]+$/.test(cliente.Codigo)) {
    errors.push({ field: 'Codigo', message: 'El código solo puede contener letras, números, guiones y guiones bajos' });
  }
  
  // Descripcion validation
  if (!cliente.Descripcion) {
    errors.push({ field: 'Descripcion', message: 'La descripción es obligatoria' });
  } else if (cliente.Descripcion.length > 100) {
    errors.push({ field: 'Descripcion', message: 'La descripción no puede tener más de 100 caracteres' });
  }
  
  // Optional fields validation
  if (cliente.Contacto && cliente.Contacto.length > 100) {
    errors.push({ field: 'Contacto', message: 'El contacto no puede tener más de 100 caracteres' });
  }
  
  if (cliente.Telefono && cliente.Telefono.length > 20) {
    errors.push({ field: 'Telefono', message: 'El teléfono no puede tener más de 20 caracteres' });
  }
  
  if (cliente.Email && !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(cliente.Email)) {
    errors.push({ field: 'Email', message: 'El email no tiene un formato válido' });
  }
  
  if (cliente.Web && !/^(http:\/\/|https:\/\/)[a-zA-Z0-9]+([\-\.]{1}[a-zA-Z0-9]+)*\.[a-zA-Z]{2,}(:[0-9]{1,5})?(\/.*)?$/.test(cliente.Web)) {
    errors.push({ field: 'Web', message: 'La web no tiene un formato válido' });
  }
  
  // Validate IDtarifa references a valid Tarifa if provided
  // Note: This requires additional logic to check against available tarifas
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

### Producto

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **Codigo** | string | Yes | 1-20 chars, alphanumeric, must be unique |
| **Descripcion** | string | Yes | 1-100 chars |
| **Precio** | number | Yes | > 0, max 2 decimal places |
| **IVA** | number | Yes | >= 0, max 2 decimal places |
| **Inactivo** | boolean | Yes | true/false |

```typescript
export const validateProducto = (producto: IProducto): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!producto.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!producto.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  
  // Codigo validation
  if (!producto.Codigo) {
    errors.push({ field: 'Codigo', message: 'El código es obligatorio' });
  } else if (producto.Codigo.length > 20) {
    errors.push({ field: 'Codigo', message: 'El código no puede tener más de 20 caracteres' });
  } else if (!/^[a-zA-Z0-9\-_]+$/.test(producto.Codigo)) {
    errors.push({ field: 'Codigo', message: 'El código solo puede contener letras, números, guiones y guiones bajos' });
  }
  
  // Descripcion validation
  if (!producto.Descripcion) {
    errors.push({ field: 'Descripcion', message: 'La descripción es obligatoria' });
  } else if (producto.Descripcion.length > 100) {
    errors.push({ field: 'Descripcion', message: 'La descripción no puede tener más de 100 caracteres' });
  }
  
  // Precio validation
  if (producto.Precio === undefined || producto.Precio === null) {
    errors.push({ field: 'Precio', message: 'El precio es obligatorio' });
  } else if (producto.Precio <= 0) {
    errors.push({ field: 'Precio', message: 'El precio debe ser mayor que cero' });
  } else if (!Number.isFinite(producto.Precio) || String(producto.Precio).split('.')[1]?.length > 2) {
    errors.push({ field: 'Precio', message: 'El precio debe tener máximo 2 decimales' });
  }
  
  // IVA validation
  if (producto.IVA === undefined || producto.IVA === null) {
    errors.push({ field: 'IVA', message: 'El IVA es obligatorio' });
  } else if (producto.IVA < 0) {
    errors.push({ field: 'IVA', message: 'El IVA no puede ser negativo' });
  } else if (!Number.isFinite(producto.IVA) || String(producto.IVA).split('.')[1]?.length > 2) {
    errors.push({ field: 'IVA', message: 'El IVA debe tener máximo 2 decimales' });
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

### Tarifa

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **Codigo** | string | Yes | 1-20 chars, alphanumeric, must be unique |
| **Descripcion** | string | Yes | 1-100 chars |
| **Inactivo** | boolean | Yes | true/false |

```typescript
export const validateTarifa = (tarifa: ITarifa): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!tarifa.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!tarifa.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  
  // Codigo validation
  if (!tarifa.Codigo) {
    errors.push({ field: 'Codigo', message: 'El código es obligatorio' });
  } else if (tarifa.Codigo.length > 20) {
    errors.push({ field: 'Codigo', message: 'El código no puede tener más de 20 caracteres' });
  } else if (!/^[a-zA-Z0-9\-_]+$/.test(tarifa.Codigo)) {
    errors.push({ field: 'Codigo', message: 'El código solo puede contener letras, números, guiones y guiones bajos' });
  }
  
  // Descripcion validation
  if (!tarifa.Descripcion) {
    errors.push({ field: 'Descripcion', message: 'La descripción es obligatoria' });
  } else if (tarifa.Descripcion.length > 100) {
    errors.push({ field: 'Descripcion', message: 'La descripción no puede tener más de 100 caracteres' });
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

### TarifaLinea

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **IDtarifa** | string | Yes | Must reference a valid Tarifa.RecordIdent |
| **IDproducto** | string | Yes | Must reference a valid Producto.RecordIdent |
| **Precio** | number | Yes | >= 0, max 2 decimal places |

```typescript
export const validateTarifaLinea = (tarifaLinea: ITarifaLinea): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!tarifaLinea.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!tarifaLinea.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  if (!tarifaLinea.IDtarifa) errors.push({ field: 'IDtarifa', message: 'La tarifa es obligatoria' });
  if (!tarifaLinea.IDproducto) errors.push({ field: 'IDproducto', message: 'El producto es obligatorio' });
  
  // Precio validation
  if (tarifaLinea.Precio === undefined || tarifaLinea.Precio === null) {
    errors.push({ field: 'Precio', message: 'El precio es obligatorio' });
  } else if (tarifaLinea.Precio < 0) {
    errors.push({ field: 'Precio', message: 'El precio no puede ser negativo' });
  } else if (!Number.isFinite(tarifaLinea.Precio) || String(tarifaLinea.Precio).split('.')[1]?.length > 2) {
    errors.push({ field: 'Precio', message: 'El precio debe tener máximo 2 decimales' });
  }
  
  // Note: Additional validation to ensure the IDtarifa/IDproducto combination is unique
  // would be implemented at the repository level
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

### Pedido

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **Numero** | number | Yes | Auto-generated, unique, positive integer |
| **IDcliente** | string | Yes | Must reference a valid Cliente.RecordIdent |
| **FechaHora** | string | Yes | ISO 8601 datetime format |

```typescript
export const validatePedido = (pedido: IPedido): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!pedido.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!pedido.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  
  // Numero validation
  if (!pedido.Numero) {
    errors.push({ field: 'Numero', message: 'El número es obligatorio' });
  } else if (!Number.isInteger(pedido.Numero) || pedido.Numero <= 0) {
    errors.push({ field: 'Numero', message: 'El número debe ser un entero positivo' });
  }
  
  // IDcliente validation
  if (!pedido.IDcliente) {
    errors.push({ field: 'IDcliente', message: 'El cliente es obligatorio' });
  }
  
  // FechaHora validation
  if (!pedido.FechaHora) {
    errors.push({ field: 'FechaHora', message: 'La fecha y hora es obligatoria' });
  } else {
    try {
      const date = new Date(pedido.FechaHora);
      if (isNaN(date.getTime())) {
        errors.push({ field: 'FechaHora', message: 'La fecha y hora no tiene un formato válido' });
      }
    } catch (e) {
      errors.push({ field: 'FechaHora', message: 'La fecha y hora no tiene un formato válido' });
    }
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

### PedidoLinea

| Field | Type | Required | Validation Rules |
|-------|------|----------|------------------|
| **RecordIdent** | string | Yes | UUID format, must be unique |
| **RecordVersion** | string | Yes | Non-empty string |
| **IDpedido** | string | Yes | Must reference a valid Pedido.RecordIdent |
| **IDproducto** | string | Yes | Must reference a valid Producto.RecordIdent |
| **Cantidad** | number | Yes | > 0 |
| **Precio** | number | Yes | >= 0, max 2 decimal places |
| **Observaciones** | string | No | 0-200 chars |

```typescript
export const validatePedidoLinea = (pedidoLinea: IPedidoLinea): ValidationResult => {
  const errors: ValidationError[] = [];
  
  // Required fields
  if (!pedidoLinea.RecordIdent) errors.push({ field: 'RecordIdent', message: 'El identificador es obligatorio' });
  if (!pedidoLinea.RecordVersion) errors.push({ field: 'RecordVersion', message: 'La versión es obligatoria' });
  if (!pedidoLinea.IDpedido) errors.push({ field: 'IDpedido', message: 'El pedido es obligatorio' });
  if (!pedidoLinea.IDproducto) errors.push({ field: 'IDproducto', message: 'El producto es obligatorio' });
  
  // Cantidad validation
  if (pedidoLinea.Cantidad === undefined || pedidoLinea.Cantidad === null) {
    errors.push({ field: 'Cantidad', message: 'La cantidad es obligatoria' });
  } else if (pedidoLinea.Cantidad <= 0) {
    errors.push({ field: 'Cantidad', message: 'La cantidad debe ser mayor que cero' });
  }
  
  // Precio validation
  if (pedidoLinea.Precio === undefined || pedidoLinea.Precio === null) {
    errors.push({ field: 'Precio', message: 'El precio es obligatorio' });
  } else if (pedidoLinea.Precio < 0) {
    errors.push({ field: 'Precio', message: 'El precio no puede ser negativo' });
  } else if (!Number.isFinite(pedidoLinea.Precio) || String(pedidoLinea.Precio).split('.')[1]?.length > 2) {
    errors.push({ field: 'Precio', message: 'El precio debe tener máximo 2 decimales' });
  }
  
  // Observaciones validation
  if (pedidoLinea.Observaciones && pedidoLinea.Observaciones.length > 200) {
    errors.push({ field: 'Observaciones', message: 'Las observaciones no pueden tener más de 200 caracteres' });
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
};
```

## Data Integrity Constraints

### Uniqueness Constraints

- **Cliente.Codigo**: Must be unique across all clients
- **Producto.Codigo**: Must be unique across all products
- **Tarifa.Codigo**: Must be unique across all price lists
- **TarifaLinea**: The combination of IDtarifa and IDproducto must be unique
- **Pedido.Numero**: Must be unique across all orders

### Referential Integrity Constraints

- **Cliente.IDtarifa**: Must reference a valid Tarifa.RecordIdent or be null
- **TarifaLinea.IDtarifa**: Must reference a valid Tarifa.RecordIdent
- **TarifaLinea.IDproducto**: Must reference a valid Producto.RecordIdent
- **Pedido.IDcliente**: Must reference a valid Cliente.RecordIdent
- **PedidoLinea.IDpedido**: Must reference a valid Pedido.RecordIdent
- **PedidoLinea.IDproducto**: Must reference a valid Producto.RecordIdent

### Cascading Constraints

- **Pedido deletion**: Must cascade to delete all associated PedidoLineas
- **Tarifa deletion**: Must cascade to delete all associated TarifasLineas

## Validation Implementation

### Client-Side Validation

- Apply validation rules in form components before submission
- Provide immediate visual feedback for invalid fields
- Prevent form submission until all validation errors are resolved

### Server-Side Validation

- Apply the same validation rules on the server before persisting data
- Return detailed validation errors to the client
- Implement database-level constraints when possible

## Validation Testing

### Test Cases

For each entity, test the following scenarios:

1. **Valid data**: All required fields provided with valid values
2. **Missing required fields**: Each required field omitted one by one
3. **Invalid formats**: Valid but incorrect formats for each field
4. **Boundary conditions**: Values at or near the limits for each field
5. **Referential integrity**: Invalid references to related entities

## Database Indexing Strategy

To improve query performance, the following fields should be indexed:

- All **RecordIdent** fields (primary keys)
- All **Codigo** fields
- **Pedido.IDcliente**
- **Pedido.FechaHora**
- **PedidoLinea.IDpedido**
- **TarifaLinea.IDtarifa**
- **TarifaLinea.IDproducto**
- **Cliente.IDtarifa**

Composite indexes:
- (TarifaLinea.IDtarifa, TarifaLinea.IDproducto)
- (Pedido.IDcliente, Pedido.FechaHora)
