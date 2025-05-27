# Techstack Documentation

This directory contains technology-specific documentation for the Preventa application's tech stack, focusing on implementation details and best practices for the following technologies:
- TypeScript
- React
- Material UI
- Zustand
- Dexie
- Vite
- [Meta-Registry](./meta-registry.md)

## Documentation Files

- `readme.md`: This file - Overview of techstack documentation
- `coding-rules-react.md`: React-specific coding standards and patterns
- `component-templates.md`: Reusable component templates and patterns
- `pluggable-editor-react.md`: Documentation for the React implementation of the pluggable editor
- `vibe-standards-react.md`: VIBE coding principles implementation in React
- `meta-registry.md`: Documentation for the Meta-Registry system

## Related Documentation

- For techstack-agnostic guidelines, see `../generic/readme.md`
- For project-specific documentation, see the main `../readme.md` and `../docs/readme.md`

## Framework Components

### Location
- `src/components/generic/`

### Purpose
Project-independent components designed for reuse across multiple projects. These components serve as the foundation for building application-specific components.

### View Components

#### Core Entity Components
- **EntityList.tsx**: Displays a paginated, searchable list of entities
- **EntityView.tsx**: Shows a single entity with actions for editing or deletion
- **EntityEdit.tsx**: Form component for editing entity data with save/cancel functionality
- **EntityConfirmDeletion.tsx**: Modal dialog for confirming entity deletion

#### Specialized Entity Components
- **EntityWithInactivoList.tsx**: Extends EntityList with filtering for active/inactive status
- **EntityWithDetailsEdit.tsx**: Extends EntityEdit with support for nested detail records

### Editor Components
- **EntitySelectorEditor.tsx**: Generic combobox for entity selection with clear functionality

## Implementation Guidelines

1. Follow the patterns and standards outlined in each document
2. Use the provided component templates as a starting point
3. Maintain consistency with the VIBE coding principles
4. Document any deviations or custom implementations

## Best Practices

- Keep components small and focused on a single responsibility
- Follow the established patterns for state management
- Ensure all components are properly typed with TypeScript
- Write unit tests for all components
- Document component props and usage examples

## Maintenance

- Update documentation when making changes to components
- Keep up with updates to the underlying technologies
- Review and refactor components periodically for performance and maintainability
