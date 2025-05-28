# Data Modeling & Synchronization

## Branded Types (TypeScript)
- Use branded types for domain-specific fields to ensure type safety
  - `tipoID`, `tipoVersion`, `tipoEmpresa`, `tipoEjercicio`, `tipoSerie`, `tipoNumero`, `tipoCodigo`, `tipoFecha`, `tipoFechaHora`, `tipoFechaHoraMinutos`
- Prevents accidental mixing of identifiers across domains

## Field Types & Table Structure
- All tables must have `RecordIdent` (unique, primary key) and `RecordVersion` (incremented on update)
- Do not use a generic `ID` field; always use `RecordIdent`
- Optional fields: `Inactivo`, `Codigo`, `Serie`, `Numero`, `Descripcion`, `Observaciones`, `empresa`, `ejercicio`
- Relationships use `IDtabla` fields referencing `RecordIdent` of related tables

## Synchronization Fields
- All tables must include:
  - `SyncStatus`: enum ["synced", "pending_create", "pending_update", "pending_delete"]
  - `LastSyncDate`: ISO 8601 timestamp
  - `LocalModifiedDate`: ISO 8601 timestamp
  - `ServerModifiedDate`: ISO 8601 timestamp
  - `ConflictResolution` (optional): enum ["local_wins", "server_wins", "manual"]

## Offline-First & Sync Rules
- Local-first: all data is stored and modified locally, then synced
- Unique identifiers must be universally unique (UUIDs or similar)
- Track all changes while offline for later sync
- Conflict detection based on version and timestamps
- Automatic and manual conflict resolution supported
- Schema migrations must be atomic and tracked

## Example: Branded Type Definition
```typescript
type tipoID = string & { __brand: 'tipoID' };
```

## Example: Table Fields
```typescript
{
  RecordIdent: tipoID;
  RecordVersion: tipoVersion;
  SyncStatus: 'synced' | 'pending_create' | ...;
  // ...other fields
}
``` 