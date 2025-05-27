# Database Indexing Strategy

## Overview

This document outlines the indexing strategy for the Preventa application's local database (IndexedDB). Proper indexing is critical for application performance, especially when dealing with large datasets in an offline-first application.

## IndexedDB Basics

IndexedDB provides a transactional key-value database system with support for indexes. Unlike SQL databases, IndexedDB:

- Is object-oriented rather than table-oriented
- Uses object stores rather than tables
- Doesn't use SQL for queries
- Supports indexes for efficient queries
- Is optimized for complex JavaScript objects

## Object Store Structure

The Preventa application uses the following IndexedDB object stores:

| Object Store | Primary Key | Auto-increment |
|--------------|-------------|----------------|
| clientes | RecordIdent | No |
| productos | RecordIdent | No |
| tarifas | RecordIdent | No |
| tarifasLineas | RecordIdent | No |
| pedidos | RecordIdent | No |
| pedidosLineas | RecordIdent | No |
| syncEvents | RecordIdent | No |

## Index Definitions

### Clientes Object Store

```typescript
// Database schema definition
db.version(1).stores({
  clientes: 'RecordIdent, Codigo, IDtarifa, Inactivo, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| Codigo | Yes | No | Fast lookup by cliente code |
| IDtarifa | No | No | Filter clients by tarifa |
| Inactivo | No | No | Filter active/inactive clients |
| SyncStatus | No | No | Find records with pending sync status |

### Productos Object Store

```typescript
db.version(1).stores({
  productos: 'RecordIdent, Codigo, Inactivo, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| Codigo | Yes | No | Fast lookup by product code |
| Inactivo | No | No | Filter active/inactive products |
| SyncStatus | No | No | Find records with pending sync status |

### Tarifas Object Store

```typescript
db.version(1).stores({
  tarifas: 'RecordIdent, Codigo, Inactivo, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| Codigo | Yes | No | Fast lookup by tarifa code |
| Inactivo | No | No | Filter active/inactive tarifa |
| SyncStatus | No | No | Find records with pending sync status |

### TarifasLineas Object Store

```typescript
db.version(1).stores({
  tarifasLineas: 'RecordIdent, [IDtarifa+IDproducto], IDtarifa, IDproducto, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| [IDtarifa+IDproducto] | Yes | No | Compound index ensuring uniqueness of product in tarifa |
| IDtarifa | No | No | Find all lines for a specific tarifa |
| IDproducto | No | No | Find all tarifas containing a specific product |
| SyncStatus | No | No | Find records with pending sync status |

### Pedidos Object Store

```typescript
db.version(1).stores({
  pedidos: 'RecordIdent, Numero, IDcliente, FechaHora, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| Numero | Yes | No | Fast lookup by order number |
| IDcliente | No | No | Find all orders for a specific client |
| FechaHora | No | No | Filter orders by date range |
| SyncStatus | No | No | Find records with pending sync status |

### PedidosLineas Object Store

```typescript
db.version(1).stores({
  pedidosLineas: 'RecordIdent, IDpedido, IDproducto, SyncStatus'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| IDpedido | No | No | Find all lines for a specific order |
| IDproducto | No | No | Find all orders containing a specific product |
| SyncStatus | No | No | Find records with pending sync status |

### SyncEvents Object Store

```typescript
db.version(1).stores({
  syncEvents: 'RecordIdent, entityType, eventType, timestamp'
});
```

| Index Name | Unique | Multientry | Purpose |
|------------|--------|------------|---------|
| RecordIdent | Yes | No | Primary key for lookup by ID |
| entityType | No | No | Filter events by entity type |
| eventType | No | No | Filter events by event type |
| timestamp | No | No | Order events chronologically |

## Compound Indexes

Compound indexes are used to optimize specific query patterns in the application:

1. **[IDtarifa+IDproducto]** in tarifasLineas:
   - Ensures a product can only appear once in a tarifa
   - Optimizes lookups by both tarifa and product

2. **Future Compound Indexes to Consider**:
   - **[IDcliente+FechaHora]** in pedidos for efficient date-range filtering by client
   - **[Inactivo+Codigo]** in productos and clientes for filtering active items with code search

## Query Optimization Strategies

### Efficient Filtering

When filtering by multiple criteria, use the most selective index first:

```typescript
// Inefficient: Using less selective index first
await db.pedidos
  .where('FechaHora').aboveOrEqual(startDate)
  .and(pedido => pedido.IDcliente === clienteId)
  .toArray();

// Efficient: Using more selective index first
await db.pedidos
  .where('IDcliente').equals(clienteId)
  .and(pedido => pedido.FechaHora >= startDate)
  .toArray();
```

### Pagination

For large result sets, implement pagination using Dexie's limit() and offset():

```typescript
const pageSize = 20;
const pageNumber = 1; // 1-based

const results = await db.pedidos
  .where('IDcliente').equals(clienteId)
  .offset((pageNumber - 1) * pageSize)
  .limit(pageSize)
  .toArray();
```

### Range Queries

For date ranges, use the appropriate range methods:

```typescript
// Date range query
const startDate = '2025-01-01';
const endDate = '2025-12-31';

const results = await db.pedidos
  .where('FechaHora').between(startDate, endDate)
  .toArray();
```

## Performance Monitoring

### Identifying Slow Queries

In development mode, monitor query performance using the following techniques:

1. **Dexie's Debug Mode**:

```typescript
// Enable Dexie debug mode in development
if (process.env.NODE_ENV === 'development') {
  db.on('ready', () => {
    db.debug = true;
  });
}
```

2. **Performance Measurement**:

```typescript
// Wrap queries in performance measurement
async function measureQueryPerformance(queryFn) {
  const start = performance.now();
  const result = await queryFn();
  const end = performance.now();
  console.log(`Query took ${end - start}ms`);
  return result;
}

// Usage
const results = await measureQueryPerformance(() => 
  db.pedidos.where('IDcliente').equals(clienteId).toArray()
);
```

## Index Maintenance

### Schema Migrations

When adding or modifying indexes, follow these steps:

1. **Increment the Database Version**:

```typescript
// Before: version 1
db.version(1).stores({
  pedidos: 'RecordIdent, Numero, IDcliente, FechaHora, SyncStatus'
});

// After: version 2 with new index
db.version(2).stores({
  pedidos: 'RecordIdent, Numero, IDcliente, FechaHora, SyncStatus, *Tags'
});
```

2. **Handle Upgrades Gracefully**:

```typescript
db.version(2).stores({
  pedidos: 'RecordIdent, Numero, IDcliente, FechaHora, SyncStatus, *Tags'
}).upgrade(tx => {
  // Initialize the new index during upgrade
  return tx.pedidos.toCollection().modify(pedido => {
    // Ensure Tags field exists
    pedido.Tags = pedido.Tags || [];
  });
});
```

## Best Practices

1. **Don't Over-Index**:
   - Each index consumes storage space and slows down write operations
   - Only create indexes for frequently used query patterns
   - Monitor and remove unused indexes

2. **Index Types**:
   - Use regular indexes for equality checks
   - Multi-entry indexes (`*`) for arrays
   - Compound indexes for queries with multiple criteria

3. **Index Size Considerations**:
   - Index properties with smaller data sizes when possible
   - Avoid indexing large text fields or BLOBs

4. **Query Performance**:
   - Always use an index for queries whenever possible
   - Use compound indexes for complex queries
   - Test performance with realistic data volumes

## Conclusion

This indexing strategy provides optimal performance for the Preventa application's common query patterns while maintaining a balance between read performance and write overhead. As the application evolves, periodically review and adjust the indexing strategy based on actual usage patterns and performance metrics.
