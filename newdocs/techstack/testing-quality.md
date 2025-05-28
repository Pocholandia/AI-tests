# Testing & Quality

## Tools
- **Jest** for unit and integration testing
- **React Testing Library** for component and UI tests

## Patterns
- Test rendering, empty states, loading, and filter combinations
- Test interaction of hooks (e.g., `useForm`, `useFilter`) and Contexts
- Use in-memory databases for integration tests involving data access
- Mock database layers when testing components that use the database
- Reset database state between tests

## Quality Practices
- Enforce strict coding standards with ESLint and Prettier
- Run `npm run lint` and `npm run format` regularly
- Use commit hooks to prevent code that doesn't pass linting
- Write unit tests for all components and business logic
- Document component props and usage examples
- Review and refactor components periodically for performance and maintainability 