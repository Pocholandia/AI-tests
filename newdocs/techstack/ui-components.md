# UI & Component Standards

## Visual & UX Guidelines
- Use **Material UI (MUI)** components and theming (`Theme.spacing(1) = 8px`)
- Layout with MUI `<Grid>`, `<Box>` for grid consistency
- Follow VIBE coding principles adapted for React + TypeScript + MUI

## Component Structure
- Presentation components in `src/components/`
- Container components in `src/containers/`
- Use provided [component templates](../techstack/component-templates.md) for consistency
- Limit each component to a single responsibility
- Max 300 lines per JSX/TSX file

## Standard Components & Patterns
- Generic filters: `EntityFilter.tsx`, `DateRangeFilter.tsx`, `NumericRangeFilter.tsx`, `EntitySelectFilter.tsx`
- Publish typings for filters in `src/types/filters.ts`
- Use generic and entity-specific hooks for forms (e.g., `useForm<T>()`, `useClienteForm`)
- Implement `NotificationContext` with MUI `<Snackbar>` and `useNotification()`

## Accessibility
- Use `@mui/utils/useIsFocusVisible` and `aria-*` attributes
- Custom components must extend accessibility props
- Maintain minimum contrast and keyboard navigation

## Anti-Patterns
- Avoid "zombie screens" (stale state dependencies)
- Prevent mass rerenders with correct use of `key`, `memo`, and selectors

## Testing
- Use **Jest** + **React Testing Library**
- Test rendering, empty states, loading, filter combinations, and hook/context interactions 