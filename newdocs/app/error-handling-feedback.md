# Error Handling & Feedback

This section describes how errors are managed and how user feedback is provided in the Preventa application.

## General Principles
- All errors must be communicated clearly to the user
- Errors are logged internally with context
- Offer recovery options when possible
- Prevent data loss on error
- Classify errors by type and severity

## Error Types & Handling

### Validation Errors
- Managed directly in forms (e.g., React Hook Form)
- Show specific messages next to each field

### Application Errors
- Use try/catch in services and container components
- Show descriptive snackbar/toast messages
- Log errors with context for debugging

### Network/Sync Errors
- Handle in the sync layer with automatic detection
- Show sync status indicator and retry option
- Use snackbar for sync errors/conflicts

## Notification Service Example

```typescript
export const notificationService = {
  success: (message: string) => {/* show green snackbar */},
  error: (message: string) => {/* show red snackbar */},
  warning: (message: string) => {/* show yellow snackbar */},
  info: (message: string) => {/* show blue snackbar */}
};
```

## Error Boundary Example

```typescript
export class ErrorBoundary extends Component<Props, State> {
  // ...
  render() {
    if (this.state.hasError) {
      return (
        <div className="error-container">
          <h2>Algo salió mal.</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Intentar de nuevo
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}
```

## Offline Error Handling
- Detect offline errors and warn the user
- Save changes locally and retry when online
- Register retry functions for failed syncs

See also: [Offline Synchronization](offline-synchronization.md) for sync-specific error handling. 