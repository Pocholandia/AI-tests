# Offline Synchronization

This section describes the offline synchronization process, conflict resolution, and error handling specific to the Preventa application.

## Sync Workflow
1. Identify pending changes in local DB (SyncStatus ≠ "synced")
2. Group by entity type
3. Upload: Send pending_create, pending_update, pending_delete to server
4. Download: Request server changes since last sync
5. Detect and resolve conflicts
6. Update local DB and sync status
7. Generate sync report and update UI

## Conflict Resolution
- Conflicts detected if both local and server versions changed, or deletion/creation conflicts
- Resolution strategies:
  - local_wins: local changes override server
  - server_wins: server changes override local
  - manual: user must resolve field-by-field
- Manual resolution: UI presents conflicting fields, user selects resolution

## Error Handling
- Automatic retry with exponential backoff (3 attempts)
- Rollback transaction if all retries fail
- Persist error state for later resumption
- Sync log with timestamps and results
- Manual sync reset for admins

## Example: Sync Manager (TypeScript)

```typescript
class SyncManager {
  async synchronize() {
    // 1. Get pending changes
    // 2. Upload to server
    // 3. Download server changes
    // 4. Detect conflicts
    // 5. Resolve and update local DB
    // 6. Update sync status
  }
}
```

## UI Feedback
- Persistent sync status indicator (synced, pending, offline)
- Snackbar for sync errors/conflicts
- Dialog for manual conflict resolution

See also: [Error Handling & Feedback](error-handling-feedback.md) for error notification patterns. 