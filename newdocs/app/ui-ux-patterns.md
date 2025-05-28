# UI & UX Patterns

This section details the user interface and user experience patterns that are specific to the Preventa application.

## General Aesthetics
- Use modern, responsive Material UI throughout: lists, dialogs, FAB, icons, editable grids, etc.
- Consistent visual feedback (snackbar, tooltips).
- Clear validation and error messages.
- Action buttons must be accessible and descriptive.
- Optimized for both desktop (modal dialogs) and mobile (full-screen forms).

## Entity Forms & Lists
- Each entity has a single form for view, insert, and edit.
- Desktop: forms in unified modal dialogs with validation and feedback.
- Mobile: forms in full-screen dialogs.
- Entity lists support pagination, filtering, and quick search.
- Filtering icon in the top left opens a filter frame above the list.
- If entity has an 'Inactivo' field, a dropdown appears to filter by status (default: Activos).
- Inactive items are visually distinguished (e.g., grayed background).

## Detail & Edit Dialogs
- Detail dialogs show entity data and associated details (e.g., order lines for Pedidos) as editable grids.
- Edit dialogs mark required fields with * and show errors below invalid fields.
- Save validates all fields; cancel returns to view mode.

## Floating Action Button (FAB)
- Always in the bottom right.
- Only mechanism for adding new items in main sections.
- Removes any other add buttons from the section.

## Synchronization Status Indicators
- Persistent indicator in the UI for sync status (synced, pending, offline).
- Icon in the top bar with semantic colors (green: synced, yellow: pending, red: offline).
- Snackbar for sync errors/conflicts.
- Overflow menu button for manual sync.
- Dialog for conflict resolution if needed.
- Individual sync status indicator for each list item with pending changes.

## Internal Identifiers
- Internal entity IDs (e.g., IDcliente, IDtarifa) must not be shown to users unless explicitly specified.
- Show descriptive data (e.g., customer name) instead.

## Example: Entity List Page Wireframe

```
┌──────────────────────────────────────────────────────┐
│ AppBar                                         🔍 ≡ │
├──────────────────────────────────────────────────────┤
│ [Page Title]                   [+ New] [Filter ▼]   │
├──────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────┐  │
│ │ Filter Panel (expandable)                       │  │
│ └─────────────────────────────────────────────────┘  │
├──────────────────────────────────────────────────────┤
│ ┌─────┬───────────────┬────────────┬───────────────┐ │
│ │     │ Column 1      │ Column 2   │ Column 3      │ │
│ ├─────┼───────────────┼────────────┼───────────────┤ │
│ │ ☐   │ Item 1        │ Value 1    │ •••           │ │
│ ├─────┼───────────────┼────────────┼───────────────┤ │
│ │ ☐   │ Item 2        │ Value 2    │ •••           │ │
│ ├─────┼───────────────┼────────────┼───────────────┤ │
│ │ ☐   │ Item 3        │ Value 3    │ •••           │ │
│ ├─────┼───────────────┼────────────┼───────────────┤ │
│ │ ☐   │ Item 4        │ Value 4    │ •••           │ │
│ └─────┴───────────────┴────────────┴───────────────┘ │
│                                                      │
│ ┌─────────────────────────────────────────────────┐  │
│ │ Pagination: [◀ 1 2 3 ... ▶] [10 ▼] per page     │  │
│ └─────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────┘
```

See also: [Techstack UI Components](../techstack/ui-components.md) for reusable component standards. 