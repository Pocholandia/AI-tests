# Database Integration

## SQLite WASM (with OPFS)
- Use SQLite compiled to WebAssembly (WASM) with Origin-Private File System (OPFS) for browser persistence
- Initialization flow: load WASM module, initialize database, set up schema/migrations
- Use `SQLiteService` as a singleton for DB connection and queries
- `DatabaseInitializer` handles schema creation and migrations
- Extend `BaseRepository` for each entity, implementing CRUD and custom queries
- Use prepared statements and batch operations for performance
- Handle errors gracefully and provide user-friendly messages

## Dexie.js (IndexedDB)
- Use Dexie.js for local storage and offline-first data
- Organize tables with required sync fields (`SyncStatus`, etc.)
- Schema migrations must be atomic and tracked
- Use repository pattern for data access

## Schema Generation from Models
- Define TypeScript interfaces in `models.ts` for each entity
- Create a `schema.ts` mapping model properties to SQL column types
- Keep schema and model in sync; required fields in interface must be `NOT NULL` in schema
- Map custom types (e.g., `tipoID`) to their underlying primitives in schema

## Example: Model to Schema
```typescript
// models.ts
export interface ICliente {
  id: string;
  nombre: string;
  direccion?: string;
  telefono?: string;
  createdAt: number;
}

// schema.ts
export const clienteSchema = {
  id: 'TEXT PRIMARY KEY',
  nombre: 'TEXT NOT NULL',
  direccion: 'TEXT',
  telefono: 'TEXT',
  createdAt: 'INTEGER NOT NULL'
};
```

## Best Practices
- Always use the database initializer at app startup
- Use one repository per entity
- Keep business logic in the repository layer
- Use transactions for complex operations
- Index frequently queried columns
- Test with in-memory DB for integration tests 