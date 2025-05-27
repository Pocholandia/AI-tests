# Directrices de Testing

Este documento define las prácticas de testing para el proyecto preventa-app, esenciales para el desarrollo VIBE (AI-driven). Un testing consistente y bien estructurado permite al AI desarrollar con mayor confianza y detectar problemas tempranamente.

## Estructura y Organización

- Los tests se ubicarán junto a los archivos que prueban con la extensión `.test.tsx` o `.test.ts`
- Para tests más complejos o de integración, se utilizará una carpeta `__tests__` dentro del directorio correspondiente
- Los test suits seguirán una estructura jerárquica que refleje la funcionalidad bajo prueba

## Tecnologías de Testing

- **Framework**: Jest (integrado con Create React App/Vite)
- **Renderizado de Componentes**: React Testing Library
- **Mocks**: Jest + MSW (Mock Service Worker) para endpoints
- **E2E (opcional)**: Cypress para pruebas end-to-end críticas

## Tipos de Tests

### 1. Tests Unitarios

- **Rules**: Todas las funciones en la carpeta `rules` deben tener tests unitarios con una cobertura mínima del 90%
- **Repositories**: Probar cada método aisladamente usando mocks para IndexedDB/Dexie
- **Hooks**: Probar la lógica de los hooks personalizados con `@testing-library/react-hooks`
- **Utilidades**: Todas las funciones de utilidad deben tener tests exhaustivos

### 2. Tests de Componentes

- **Componentes de Presentación**: Probar renderizado y comportamiento UI
- **Componentes Contenedores**: Probar integración con stores y manejo de efectos
- **Formularios**: Probar validación, manejo de errores y submiteo

### 3. Tests de Integración

- **Flujos de Usuario**: Probar flujos completos como crear un pedido o editar un cliente
- **Sincronización**: Probar el comportamiento online/offline
- **Persistencia**: Verificar que los datos se almacenan y recuperan correctamente

## Patrones y Prácticas

### Patrones AAA (Arrange-Act-Assert)

```typescript
describe('miFunction', () => {
  test('debe hacer algo específico', () => {
    // Arrange (Preparar)
    const input = {...};
    
    // Act (Actuar)
    const result = miFunction(input);
    
    // Assert (Comprobar)
    expect(result).toBe(expectedOutput);
  });
});
```

### Testing de Componentes

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { MiComponente } from './MiComponente';

describe('MiComponente', () => {
  test('renderiza correctamente', () => {
    render(<MiComponente />);
    expect(screen.getByText('Texto esperado')).toBeInTheDocument();
  });
  
  test('maneja eventos correctamente', () => {
    const handleClick = jest.fn();
    render(<MiComponente onClick={handleClick} />);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### Mocks de Stores y Repositorios

```typescript
// Crear mock del store
jest.mock('../../stores/pedidoStore', () => ({
  usePedidoStore: jest.fn(() => ({
    pedidos: mockPedidos,
    loading: false,
    error: null,
    fetchPedidos: jest.fn(),
    addPedido: jest.fn(),
  })),
}));

// Crear mock del repositorio
jest.mock('../../repositories/implementations/pedidoRepository', () => ({
  pedidoRepository: {
    getAll: jest.fn().mockResolvedValue(mockPedidos),
    getById: jest.fn().mockResolvedValue(mockPedido),
    save: jest.fn().mockResolvedValue('new-id'),
  },
}));
```

### Testing de Funcionalidad Offline

```typescript
describe('Funcionalidad offline', () => {
  beforeEach(() => {
    // Simular modo offline
    Object.defineProperty(navigator, 'onLine', { value: false, writable: true });
  });
  
  afterEach(() => {
    // Restaurar modo online
    Object.defineProperty(navigator, 'onLine', { value: true, writable: true });
  });
  
  test('los datos se guardan localmente cuando está offline', async () => {
    // Test específico para comportamiento offline
  });
});
```

## Requisitos de Cobertura

- **Rules**: 90%+
- **Repositories**: 85%+
- **Stores**: 80%+
- **Componentes críticos**: 75%+
- **Utilidades**: 95%+

## Pruebas Requeridas para Filtros de Pedidos

Dada la importancia de los filtros de pedidos (según requisitos), estos deben tener tests específicos:

- Probar filtrado por cliente individual
- Probar filtrado por rango de fechas (solo YYYY-MM-DD)
- Probar filtrado por rango de total (min y max)
- Probar todas las combinaciones de filtros posibles
- Verificar que no se muestran pedidos hasta aplicar filtros
- Probar la persistencia de los filtros seleccionados

## CI/CD y Testing Automático

- Los tests deben ejecutarse automáticamente antes de cada commit (pre-commit hook)
- La integración continua debe rechazar código que no pase los tests
- Los tests deben ejecutarse en un entorno aislado con datos de prueba consistentes

## Datos de Prueba

- Crear conjuntos de datos de prueba realistas
- Proporcionar factories y fixtures para generar datos de forma consistente
- Aislar los tests para evitar dependencias entre ellos

## Ejemplo de Factory para Datos de Prueba

```typescript
// factories/pedidoFactory.ts
export const createTestPedido = (overrides = {}) => ({
  RecordIdent: `pedido-${Math.floor(Math.random() * 10000)}`,
  RecordVersion: 1,
  IDcliente: 'cliente-1',
  FechaHora: new Date().toISOString(),
  SyncStatus: 'synced',
  LastSyncDate: new Date().toISOString(),
  LocalModifiedDate: new Date().toISOString(),
  ServerModifiedDate: null,
  // Propiedades adicionales
  ...overrides
});

export const createTestPedidoLinea = (overrides = {}) => ({
  RecordIdent: `linea-${Math.floor(Math.random() * 10000)}`,
  RecordVersion: 1,
  IDpedido: 'pedido-1',
  IDproducto: 'producto-1',
  Cantidad: 1,
  Precio: 10.5,
  Observaciones: '',
  SyncStatus: 'synced',
  // Propiedades adicionales
  ...overrides
});
```