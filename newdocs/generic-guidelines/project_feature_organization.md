[⬅ Back to Index](./index.md)

# 1D. Project and Feature Organization

- Use a **feature-based organization**: each domain/feature has its own directory with components, store, models, and public API (`index.ts`).
- Only import from other features via their public API (never internal files).
- Shared utilities and generic components must be extracted to `/utils/` or `/components/generic/`.
- Use barrel files (`index.ts`) for exports; prefer named exports over default exports.
- Each file should have a single responsibility and be <300 lines when possible.
- Avoid cyclic imports; follow the dependency hierarchy: UI → stores → repositories → utils.
- When creating new features, follow the established directory and export structure. 