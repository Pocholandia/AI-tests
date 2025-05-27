# Developer Environment Setup Guide

## Overview

This document provides comprehensive instructions for setting up the development environment for the Preventa application. It covers all necessary steps from initial setup to running the application in various environments.

## Prerequisites

- **Node.js**: Version 18.x or higher
- **npm**: Version 8.x or higher
- **Git**: For version control
- **Visual Studio Code** (recommended): With the following extensions:
  - ESLint
  - Prettier
  - TypeScript React code snippets
  - Material-UI Snippets

## Initial Setup

### 1. Clone the Repository

```bash
# Clone the repository
git clone https://github.com/JavierusTk/preventa-app.git

# Navigate to the project directory
cd preventa-app
```

### 2. Install Dependencies

```bash
# Install project dependencies
npm install

# Verify installation was successful
npm list --depth=0
```

### 3. Environment Configuration

1. Create an environment file for local development:

```bash
# Create a .env.local file from the template
cp .env.example .env.local
```

2. Open `.env.local` in your editor and set the appropriate values:

```
# API Configuration
VITE_API_URL=http://localhost:3000/api
VITE_API_TIMEOUT=30000

# Feature Flags
VITE_ENABLE_SYNC=true
VITE_ENABLE_DEMO_DATA=true

# Authentication
VITE_AUTH_PROVIDER=local
```

## Running the Development Environment

### 1. Start the Development Server

```bash
# Start the development server
npm run dev
```

The development server will start on `http://localhost:5173` by default.

### 2. Building for Production

```bash
# Create a production build
npm run build

# Preview the production build locally
npm run preview
```

### 3. Running Tests

```bash
# Run all tests
npm test

# Run tests with coverage
npm test:coverage

# Run specific test file
npm test -- -t "Component Name"
```

## Database Setup

The Preventa application uses IndexedDB for local storage, which is automatically set up when the application runs for the first time.

### Initializing Demo Data

1. Start the application in development mode
2. Click on the menu icon in the top-right corner
3. Select "Generate Demo Data"
4. Configure the quantity of each entity to generate
5. Click "Generate" to populate the database with demo data

## Debugging

### Using Browser DevTools

1. Open Chrome DevTools (F12 or Ctrl+Shift+I)
2. Navigate to the "Application" tab
3. Under "Storage", expand "IndexedDB"
4. Select the Preventa database to inspect tables and records

### Debug Logging

The application uses a custom logging system that can be enabled in development:

1. Open the browser console
2. Execute the following command: `localStorage.setItem('debug', 'preventa:*')`
3. Refresh the page to enable detailed logging

Available debug namespaces:
- `preventa:api` - API calls and responses
- `preventa:sync` - Synchronization process
- `preventa:db` - Database operations
- `preventa:ui` - UI rendering and events

## Common Issues and Solutions

### "TypeScript Cannot Find Module" Error

If TypeScript reports that it cannot find a module, try the following:

```bash
# Clear TypeScript cache
npm run clean:ts

# Rebuild TypeScript definitions
npm run build:types
```

### IndexedDB Upgrade Issues

If the application fails to upgrade the IndexedDB schema:

1. Open Chrome DevTools
2. Navigate to Application > Storage > IndexedDB
3. Delete the Preventa database
4. Refresh the page to recreate the database with the current schema

### Material UI Theme Customization

The application uses Material UI with a custom theme. To modify the theme:

1. Edit `src/theme/theme.ts`
2. Use the Material UI theming system to customize colors, typography, etc.
3. Changes will be applied immediately in development mode

## Working with the Code

### Code Organization

The codebase follows a feature-based organization pattern:

```
src/
├── components/       # Reusable UI components
│   ├── generic/      # Generic, reusable components
│   ├── clientes/     # Cliente-specific components
│   ├── productos/    # Producto-specific components
│   └── pedidos/      # Pedido-specific components
├── models/           # TypeScript interfaces and types
├── repositories/     # Data access layer
│   ├── interfaces/   # Repository interfaces
│   └── implementations/ # Concrete implementations
├── rules/            # Business rules and validation
├── services/         # Service layer
├── stores/           # Zustand stores
├── theme/            # UI theme configuration
├── utils/            # Utility functions
└── views/            # Main application views/pages
```

### Coding Standards

The project follows strict coding standards that are enforced through ESLint and Prettier:

- Run `npm run lint` to check for coding issues
- Run `npm run format` to automatically format code
- Commit hooks will prevent committing code that doesn't pass linting

Refer to `docs/techstack/coding-rules-react.md` for detailed coding standards.

## Deployment

### Development Deployment

```bash
# Deploy to development environment
npm run deploy:dev
```

### Staging Deployment

```bash
# Deploy to staging environment
npm run deploy:staging
```

### Production Deployment

```bash
# Deploy to production environment
npm run deploy:prod
```

## CI/CD Pipeline

The project uses GitHub Actions for continuous integration and deployment:

1. Every push to the `main` branch triggers a CI build
2. Successful builds on `main` are automatically deployed to the development environment
3. Tags with the format `v*` trigger production deployments
4. Pull requests trigger test runs and preview deployments

Refer to `.github/workflows/` for the CI/CD configuration files.

## Additional Resources

- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Material UI Documentation](https://mui.com/material-ui/getting-started/)
- [Zustand Documentation](https://github.com/pmndrs/zustand)
- [Dexie.js Documentation](https://dexie.org/docs/)
- [Vite Documentation](https://vitejs.dev/guide/)

## Troubleshooting

If you encounter any issues not covered in this guide, please:

1. Check the project issue tracker for similar problems
2. Review the application logs for error messages
3. Contact the development team via the project chat channel

## Contributing

Please refer to `CONTRIBUTING.md` for contribution guidelines, including:

- Code review process
- Pull request guidelines
- Commit message formatting
- Feature branch naming conventions
