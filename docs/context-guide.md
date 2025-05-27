# Preventa App - Context Guide

## Project Overview
Preventa App is a sales management application built with React, TypeScript, and Material-UI. It provides functionality for managing clients, products, pricing, and orders with a focus on sales operations.

## Key Components

### Core Entities
- **Clientes (Customers)**: Customer management with contact information and tariff assignments
- **Productos (Products)**: Product catalog with pricing and stock management
- **Tarifas (Pricing)**: Configurable pricing structures with line items
- **Pedidos (Orders)**: Sales order management with filtering capabilities
- **Sync Events**: Tracks data synchronization status

### Framework Components
- **EntityEdit**: Reusable form component with pluggable editors
- **EntityList**: Configurable list component with sorting and filtering
- **EntityView**: Read-only view for entity details
- **EntityEditRegistry**: System for registering custom field editors

## Technical Stack
- **Frontend**: React 18 with TypeScript
- **UI Framework**: Material-UI (MUI)
- **State Management**: Zustand
- **Routing**: React Router
- **Build Tool**: Vite
- **Code Quality**: TypeScript, ESLint, Prettier


## Important Files
- `src/App.tsx`: Main application component with routing
- `src/components/common/AppLayout.tsx`: Main layout component
- `src/stores/`: Zustand stores for state management
- `src/rules/pedidoRules.ts`: Core order filtering logic
- `project/pedidos-filtering.md`: Detailed specification for order filtering

## Development Guidelines
1. Follow TypeScript strict typing
2. Use Material-UI components when possible
3. Keep business logic in the `rules` directory
4. Use Zustand for state management
5. Document complex components and business logic

## Common Tasks
- Adding a new entity: Create model, store, components, and update routing
- Adding a field to an entity: Update model, store, and components
- Implementing a new filter: Update the relevant store and UI components

## Notes for AI Assistant
- Always check `pedidoRules.ts` for order filtering logic
- Refer to `EntityEdit` and `EntityList` documentation for UI components
- Check existing stores for state management patterns
- Review `pedidos-filtering.md` for order filtering requirements
