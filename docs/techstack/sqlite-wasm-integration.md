# SQLite WASM Integration with OPFS

This document outlines the SQLite WASM integration with Origin-Private File System (OPFS) in the Preventa application, including the initialization flow, architecture, and best practices.

## Overview

The application uses SQLite compiled to WebAssembly (WASM) with OPFS for client-side storage. This provides a persistent, file-based database that works entirely in the browser.

## Initialization Flow

The SQLite WASM initialization follows a strict sequence to ensure proper loading and error handling:

1. **WASM Module Loading**
   - The `sqliteWasmLoader.ts` loads the SQLite WASM module
   - Sets up the Web Worker for database operations
   - Configures the global `sqlite3Worker1Promiser`

2. **Database Initialization**
   - `SQLiteInitializer` component loads during app startup
   - Shows loading state during initialization
   - Handles errors gracefully with user feedback
   - Renders children only after successful initialization

3. **Service Initialization**
   - `SQLiteService` initializes the database connection
   - Automatically loads WASM if not already loaded
   - Sets up the database schema and migrations

## Architecture

### Core Components

1. **SQLiteService**
   - Singleton service that manages the SQLite database connection
   - Handles initialization, queries, and cleanup
   - Manages OPFS storage
   - Automatically loads WASM if needed

2. **DatabaseInitializer**
   - Handles database schema creation and migrations
   - Ensures foreign key constraints are enabled
   - Runs initialization logic on app startup

3. **BaseRepository**
   - Abstract base class for all repository implementations
   - Provides common CRUD operations
   - Enforces consistent data access patterns

### Directory Structure

```
apps/preventa-app/src/
├── components/
│   └── SQLiteInitializer.tsx  # Handles WASM loading and initialization UI
└── utils/
    └── sqliteWasmLoader.ts   # Loads and initializes SQLite WASM

packages/core/src/database/
├── DatabaseInitializer.ts    # Database schema and initialization
├── BaseRepository.ts         # Base repository class
├── sqlite-types.ts           # TypeScript type definitions
└── services/
    └── SQLiteService.ts  # Core database service
```

## Usage

### Initialization

Initialize the database when your application starts:

```typescript
import { databaseInitializer } from '@preventa/core/database';

async function initializeApp() {
  try {
    await databaseInitializer.initialize();
    // App initialization continues...
  } catch (error) {
    console.error('Database initialization failed:', error);
  }
}
```

### Creating a Repository

Extend `BaseRepository` for each entity type:

```typescript
import { BaseRepository } from '@preventa/core/database';

export class ProductoRepository extends BaseRepository<Producto> {
  constructor() {
    super('productos');
  }

  // Custom queries
  async findByCode(codigo: string): Promise<Producto | null> {
    return this.findOne('codigo = ?', [codigo]);
  }
}
```

### Using the Repository

```typescript
const productoRepo = new ProductoRepository();

// Create
const newId = await productoRepo.insert({
  codigo: 'PROD-001',
  descripcion: 'Producto de ejemplo',
  precio: 99.99,
  stock: 10
});

// Read
const producto = await productoRepo.findOne('id = ?', [newId]);

// Update
await productoRepo.update(newId, { precio: 109.99 });

// Delete
await productoRepo.delete(newId);
```

## Best Practices

1. **Initialization**
   - Always use the `SQLiteInitializer` component at the root of your app
   - Handle loading and error states gracefully
   - Ensure proper error boundaries are in place

2. **Repository Pattern**
   - Create one repository per entity
   - Keep business logic in the repository layer
   - Use transactions for complex operations

2. **Error Handling**
   - Always handle database errors gracefully
   - Log errors with sufficient context
   - Provide user-friendly error messages

3. **Performance**
   - Use prepared statements for repeated queries
   - Batch operations when possible
   - Index frequently queried columns

## Vite Configuration

For proper SQLite WASM support, ensure your Vite config includes:

```typescript
// vite.config.ts
export default defineConfig({
  resolve: {
    alias: {
      '@sqlite.org/sqlite-wasm': '@sqlite.org/sqlite-wasm/sqlite-wasm/jswasm/sqlite3.mjs',
    },
  },
  server: {
    headers: {
      'Cross-Origin-Opener-Policy': 'same-origin',
      'Cross-Origin-Embedder-Policy': 'require-corp',
    },
  },
  optimizeDeps: {
    include: ['@sqlite.org/sqlite-wasm'],
    esbuildOptions: {
      target: 'es2020',
    },
  },
});
```

## Limitations

1. **Browser Support**
   - Requires a modern browser with WebAssembly and OPFS support
   - Not all SQLite features are available in the WASM version
   - Requires specific CORS headers to be set

2. **Storage Quotas**
   - Browsers may limit storage to a percentage of disk space
   - Handle quota exceeded errors gracefully

## Testing

When testing components that use the database:

1. Mock the database layer
2. Use an in-memory database for integration tests
3. Reset the database state between tests

## Troubleshooting

### Database Not Initializing

1. **WASM Not Loading**
   - Check browser console for WASM-related errors
   - Ensure the Vite config has the correct headers and aliases
   - Verify the SQLite WASM package is installed

2. **CORS Issues**
   - Ensure the server sends the required CORS headers
   - Check for mixed content warnings (HTTP/HTTPS issues)

3. **OPFS Support**
   - Verify the browser supports OPFS
   - Check for storage quota limitations

4. **Common Errors**
   - `SQLite WASM worker not initialized`: The WASM module wasn't loaded before use
   - `SharedArrayBuffer not available`: Missing CORS headers or running in an insecure context
   - `Module not found`: Incorrect import path for SQLite WASM

### Queries Not Working

1. Check SQL syntax
2. Verify table and column names
3. Check for transaction conflicts

## Future Improvements

1. Add schema migration system
2. Implement database backup/restore
3. Add database versioning
4. Implement offline-first synchronization
