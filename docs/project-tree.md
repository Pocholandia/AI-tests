# Preventa App - Project File Tree

projects-folder/
├── docs/
│   ├── accessibility-guidelines.md
│   ├── api-specification.md
│   ├── architecture.md
│   ├── configuration-standards.md
│   ├── context-guide.md
│   ├── data-demo-generation.md
│   ├── data-structure.md
│   ├── data-validation-rules.md
│   ├── database-indexing.md
│   ├── developer-setup.md
│   ├── entity-relationship-model.md
│   ├── error-handling.md
│   ├── feature-based-organization.md
│   ├── generic/
│   │   ├── analisis.md
│   │   ├── coding-rules.md
│   │   ├── data-guidelines.md
│   │   ├── data-migration.md
│   │   ├── data-synchronization.md
│   │   ├── pluggable-editor.md
│   │   ├── readme.md
│   │   └── testing-guidelines.md
│   ├── glossary.md
│   ├── historia.md
│   ├── navigation-flows.md
│   ├── pedidos-filtering.md
│   ├── project-structure.md
│   ├── project-tree.md
│   ├── readme.md
│   ├── registry-system/
│   │   ├── README.md
│   │   ├── field-registry.md
│   │   ├── improvements.md
│   │   └── integration-guide.md
│   ├── registry-system-improvements.md
│   ├── state-management.md
│   ├── synchronization-process.md
│   ├── techstack/
│   │   ├── coding-rules-react.md
│   │   ├── component-templates.md
│   │   ├── data-guidelines-javascript-typescript.md
│   │   ├── coding-rules-react.md
│   │   ├── component-templates.md
│   │   ├── data-guidelines-javascript-typescript.md
│   │   ├── details-plugin.md
│   │   ├── entity-plugin-system.md
│   │   ├── feature-registry.md
│   │   ├── field-registry-react.md
│   │   ├── meta-registry/
│   │   │   ├── analysis.md
│   │   │   ├── deepMerge.md
│   │   │   ├── enhanced-registry.md
│   │   │   └── usage-guide.md
│   │   ├── meta-registry.md
│   │   ├── pluggable-editor-react.md
│   │   ├── readme.md
│   │   ├── stores.md
│   │   └── vibe-standards-react.md
│   ├── testing-strategy.md
│   ├── UI.md
│   └── ui-components-specifications.md
├── node_modules/
├── public/
│   └── vite.svg
├── src/
│   ├── assets/
│   │   └── react.svg
│   ├── components/
│   │   ├── common/
│   │   │   ├── AppLayout.tsx
│   │   │   └── Notification.tsx
│   │   ├── generic/
│   │   │   ├── (EntityEdit.tsx, EntityList.tsx, etc.)
│   │   ├── layouts/
│   │   │   └── (layout components)
│   │   ├── registry/
│   │   │   └── MetadataExplorerDialog.tsx
│   │   └── DataDemoDialog.tsx
│   ├── constants/
│   ├── features/
│   │   ├── clientes/
│   │   │   ├── components/
│   │   │   │   ├── ClienteEdit.tsx
│   │   │   │   ├── ClienteSelectorEditor.tsx
│   │   │   │   ├── ClientesConfirmDeletion.tsx
│   │   │   │   ├── ClientesList.tsx
│   │   │   │   └── index.ts
│   │   │   ├── hooks/
│   │   │   ├── index.ts
│   │   │   ├── models.ts
│   │   │   └── store.ts
│   │   ├── pedidos/
│   │   │   ├── components/
│   │   │   │   ├── PedidoEdit.tsx
│   │   │   │   ├── PedidoSelectionEditor.tsx
│   │   │   │   ├── PedidosConfirmDeletion.tsx
│   │   │   │   ├── PedidosFiltros.tsx
│   │   │   │   ├── PedidosList.tsx
│   │   │   │   └── index.ts
│   │   │   ├── index.ts
│   │   │   ├── models.ts
│   │   │   ├── rules.ts
│   │   │   └── store.ts
│   │   ├── productos/
│   │   │   ├── components/
│   │   │   │   ├── ProductoEdit.tsx
│   │   │   │   ├── ProductoSelectionEditor.tsx
│   │   │   │   ├── ProductosConfirmDeletion.tsx
│   │   │   │   ├── ProductosList.tsx
│   │   │   │   └── index.ts
│   │   │   ├── index.ts
│   │   │   ├── models.ts
│   │   │   └── store.ts
│   │   ├── sync/
│   │   │   ├── components/
│   │   │   │   ├── SyncEventsConfirmDeletion.tsx
│   │   │   │   ├── SyncEventsList.tsx
│   │   │   │   └── index.ts
│   │   │   ├── index.ts
│   │   │   ├── models.ts
│   │   │   └── store.ts
│   │   └── tarifas/
│   │       ├── components/
│   │       │   ├── TarifaEdit.tsx
│   │       │   ├── TarifaSelectionEditor.tsx
│   │       │   ├── TarifasConfirmDeletion.tsx
│   │       │   ├── TarifasList.tsx
│   │       │   └── index.ts
│   │       ├── index.ts
│   │       ├── models.ts
│   │       └── store.ts
│   ├── hooks/
│   ├── lib/
│   │   ├── registry/
│   │   │   └── (registry functionality)
│   ├── models/
│   │   └── (deprecated model interfaces)
│   ├── repositories/
│   │   ├── implementations/
│   │   ├── interfaces/
│   │   │   ├── IClienteRepository.ts
│   │   │   ├── IPedidoRepository.ts
│   │   │   ├── IProductoRepository.ts
│   │   │   ├── ISyncEventRepository.ts
│   │   │   └── ITarifaRepository.ts
│   │   └── sync/
│   ├── rules/
│   │   ├── calculations/
│   │   ├── transformations/
│   │   └── validation/
│   │       └── (validation files)
│   ├── services/
│   ├── stores/
│   │   └── (deprecated stores - moved to features)
│   ├── tests/
│   │   └── lib/
│   │       └── registry/
│   ├── utils/
│   │   └── generateDemoData.ts
│   ├── App.tsx
│   ├── index.css
│   └── index.tsx
├── .env
├── .eslintrc.cjs
├── .prettierrc
├── index.html
├── manifest.json
├── package-lock.json
├── package.json
├── readme.md
├── TEMP/
│   └── (temporary files that must be ignored unless specified)
├── TO-DO/
│   └── (analysis and improvement files)
├── tsconfig.json
├── vite.config.ts
└── wind_instructions.md

## Notes
- The project now follows a feature-based organization pattern where domain features are separated into their own directories under `/src/features/`
- Each feature contains its own:  
  - `components/`: UI components specific to the feature
  - `models.ts`: Type definitions and interfaces
  - `store.ts`: State management (Zustand store)
  - `index.ts`: Public API and meta-registry registration
- Components in `/src/components/` are now only utility and infrastructure components that don't belong to specific domain features:
  - `common/`: Shared components like layouts and notifications
  - `generic/`: Reusable entity-agnostic components
  - `registry/`: Components for the metadata registry system
- TypeScript is used throughout for type safety
- The application uses Material-UI for consistent UI components
- Documentation is organized in a hierarchical structure:
  - `/docs`: Main documentation directory with core project files
  - `/docs/generic`: Techstack-independent guidelines and standards
  - `/docs/techstack`: Technology-specific documentation (React, TypeScript, etc.)
