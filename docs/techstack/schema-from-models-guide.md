# Guide: Creating `schema.ts` from `models.ts` for Any Feature

## Overview

The `schema.ts` file defines the mapping between your TypeScript model properties and the corresponding SQL column types for table creation and migrations. Since TypeScript interfaces are erased at runtime, this schema object is essential for automating table creation and ensuring consistency between your models and your database.

---

## Step-by-Step Instructions

### 1. Review Your `models.ts` File

- Identify the main interface for your entity (e.g., `IProducto` in `productos/models.ts`).
- List all properties, their types, and any optional/required status.

**Example:**
```typescript
export interface IProducto {
  RecordIdent: tipoID;
  nombre: tipoNombre;
  precio: tipoPrecio;
  stock: tipoStock;
  observaciones?: tipoObservaciones;
}
```

---

### 2. Map TypeScript Types to SQLite Types

Create a mapping from your domain types (or primitive types) to SQLite types:

| TypeScript/Domain Type | SQLite Type   |
|-----------------------|--------------|
| `string`              | `TEXT`       |
| `number`              | `REAL`/`INTEGER` |
| `boolean`             | `INTEGER` (0/1) |
| `Date`/timestamp      | `INTEGER` (UNIX epoch) |
| `tipoID`/`tipoNombre` | `TEXT`       |
| ...                   | ...          |

> **Tip:** If you use custom types (like `tipoID`), map them to their underlying primitive.

---

### 3. Create the `schema.ts` File

- In your feature directory (e.g., `features/productos/`), create `schema.ts`.
- Export a plain object mapping property names to SQLite column definitions.

**Example:**
```typescript
// features/productos/schema.ts
export const productoSchema = {
  RecordIdent: 'TEXT PRIMARY KEY',
  nombre: 'TEXT NOT NULL',
  precio: 'REAL NOT NULL',
  stock: 'INTEGER NOT NULL',
  observaciones: 'TEXT'
};
```
- Use `PRIMARY KEY`, `NOT NULL`, etc., as appropriate.

---

### 4. Keep Schema and Model in Sync

- Every time you update the interface in `models.ts`, update the schema in `schema.ts`.
- Ensure all required fields in the interface are marked as `NOT NULL` in the schema.
- Optional fields in the interface should not be marked as `NOT NULL` in the schema.

---

### 5. Use the Schema in Your Repository

- Import the schema in your repository.
- Pass it to your table creation utility (e.g., `BaseRepository.createTableFromSchema('productos', productoSchema)`).

---

## Example: From Model to Schema

**models.ts**
```typescript
export interface ICliente {
  id: string;
  nombre: string;
  direccion?: string;
  telefono?: string;
  createdAt: number;
}
```

**schema.ts**
```typescript
export const clienteSchema = {
  id: 'TEXT PRIMARY KEY',
  nombre: 'TEXT NOT NULL',
  direccion: 'TEXT',
  telefono: 'TEXT',
  createdAt: 'INTEGER NOT NULL'
};
```

---

## Best Practices

- Use consistent naming between model properties and schema fields.
- Always review both files after making changes to your data model.
- Document any custom type mappings at the top of your `schema.ts` for clarity.

---

## Template

```typescript
// features/[feature]/schema.ts
export const [entity]Schema = {
  // propertyName: 'SQL_TYPE [CONSTRAINTS]',
};
```

---

## FAQ

**Q: What if my property is optional in the interface?**  
A: Do not add `NOT NULL` in the schema for that property.

**Q: What about default values?**  
A: You can add `DEFAULT value` in the schema string, e.g., `'INTEGER DEFAULT 0'`.
