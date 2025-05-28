# Meta-Registry & Extensibility

## What is the Meta-Registry?
- A flexible, decentralized system for managing table metadata in React + TypeScript apps
- Centralizes metadata (labels, UI components, columns, custom logic) while allowing distributed registration
- Supports dynamic component discovery and runtime feature extension

## Usage Patterns
- Register table metadata in each feature's `index.ts` using `registerTable`
- Metadata can include labels, components (list, view, edit), columns, validation rules, permissions, relationships, and search config
- Use hooks (e.g., `useTableMeta`, `useTableColumns`) to access metadata in components
- Built-in Metadata Explorer UI for browsing and debugging registered tables

## Extensibility
- Modular: new features can extend existing tables without modifying core files
- Supports runtime feature discovery and dynamic UI adaptation
- TypeScript type generation can be based on registry metadata for type safety
- Deep merge logic for metadata slices (scalars overwrite, arrays replace, objects merge recursively)

## Best Practices
- Register all relevant metadata at feature initialization
- Use clear, consistent naming for tables and fields
- Validate metadata consistency with provided utilities
- Document all custom metadata and extensions 