[⬅ Back to Index](./index.md)

# 10. Testing Guidelines

- Los tests se ubicarán junto a los archivos que prueban con la extensión `.test.tsx` o `.test.ts`
- Para tests más complejos o de integración, se utilizará una carpeta `__tests__` dentro del directorio correspondiente
- Los test suits seguirán una estructura jerárquica que refleje la funcionalidad bajo prueba
- Framework: Jest (integrado con Create React App/Vite)
- Renderizado de Componentes: React Testing Library
- Mocks: Jest + MSW (Mock Service Worker) para endpoints
- E2E (opcional): Cypress para pruebas end-to-end críticas
- Tipos de tests: unitarios, de componentes, de integración
- Patrones AAA (Arrange-Act-Assert)
- Mocks de stores y repositorios
- Testing de funcionalidad offline
- Requisitos de cobertura: Rules 90%+, Repositories 85%+, Stores 80%+, Componentes críticos 75%+, Utilidades 95%+
- Pruebas requeridas para filtros de pedidos: filtrado por cliente, rango de fechas, rango de total, combinaciones, persistencia de filtros
- CI/CD y testing automático: pre-commit hook, integración continua, entorno aislado
- Datos de prueba: conjuntos realistas, factories y fixtures, aislamiento de tests

## Expanded Testing Guidelines

- Test files must follow naming conventions: `ComponentName.test.tsx`, `functionName.test.ts`, etc.
- Test descriptions should clearly state the expected behavior and condition ("should [behavior] when [condition]").
- Follow the AAA pattern: Arrange, Act, Assert.
- Test behavior, not implementation details; use user-centric queries (e.g., `getByRole`, `getByLabelText`).
- Keep tests independent; reset state between tests.
- Mock external dependencies (APIs, stores, repositories) as needed.
- Use fixtures and in-memory data for unit/integration tests; use seeding scripts for E2E tests.
- All tests must run automatically in CI on PRs and merges; collect coverage and performance metrics.
- Maintain high coverage: Rules 90%+, Repositories 85%+, Stores 80%+, Critical Components 75%+, Utilities 95%+.
- Regularly review and update tests alongside feature development. 