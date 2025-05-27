# Testing Strategy

## Overview

This document outlines the comprehensive testing strategy for the Preventa application. It covers all levels of testing from unit to end-to-end, along with tools, patterns, and best practices to ensure application quality and reliability.

## Testing Levels

### Unit Testing

#### Purpose
Verify that individual units of code (functions, components, stores) work as expected in isolation.

#### Framework and Tools
- **Jest**: Primary test runner and assertion library
- **React Testing Library**: For testing React components
- **Mock Service Worker (MSW)**: For API mocking

#### Coverage Goals
- **Target**: 80% code coverage for business logic
- **Focus areas**:
  - Repository implementations
  - Business rules and validation logic
  - Store actions and state management
  - Utility functions

#### Examples

**Testing a Utility Function**

```typescript
// formatCurrency.ts
export function formatCurrency(value: number): string {
  return new Intl.NumberFormat('es-ES', {
    style: 'currency',
    currency: 'EUR'
  }).format(value);
}

// formatCurrency.test.ts
import { formatCurrency } from './formatCurrency';

describe('formatCurrency', () => {
  test('formats positive values correctly', () => {
    expect(formatCurrency(10.5)).toBe('10,50 €');
  });
  
  test('formats negative values correctly', () => {
    expect(formatCurrency(-10.5)).toBe('-10,50 €');
  });
  
  test('formats zero correctly', () => {
    expect(formatCurrency(0)).toBe('0,00 €');
  });
});
```

**Testing a Store**

```typescript
// useClienteStore.test.ts
import { useClienteStore } from './useClienteStore';
import { clienteRepository } from '../repositories/implementations/clienteRepository';

// Mock the repository
jest.mock('../repositories/implementations/clienteRepository');

describe('Cliente Store', () => {
  beforeEach(() => {
    // Reset store state
    useClienteStore.setState({
      clientes: [],
      loading: false,
      error: null,
      selectedCliente: null
    });
    
    // Reset mocks
    jest.clearAllMocks();
  });
  
  test('fetchClientes sets loading state and calls repository', async () => {
    // Arrange
    const mockClientes = [{ RecordIdent: '1', Descripcion: 'Test Cliente' }];
    clienteRepository.findAll.mockResolvedValue(mockClientes);
    
    // Act
    await useClienteStore.getState().fetchClientes();
    
    // Assert
    expect(clienteRepository.findAll).toHaveBeenCalled();
    expect(useClienteStore.getState().clientes).toEqual(mockClientes);
    expect(useClienteStore.getState().loading).toBe(false);
  });
  
  test('fetchClientes handles errors correctly', async () => {
    // Arrange
    const errorMessage = 'Network Error';
    clienteRepository.findAll.mockRejectedValue(new Error(errorMessage));
    
    // Act
    await useClienteStore.getState().fetchClientes();
    
    // Assert
    expect(clienteRepository.findAll).toHaveBeenCalled();
    expect(useClienteStore.getState().error).toBe(errorMessage);
    expect(useClienteStore.getState().loading).toBe(false);
  });
});
```

### Component Testing

#### Purpose
Verify that React components render correctly and respond appropriately to user interactions.

#### Framework and Tools
- **Jest**: Test runner
- **React Testing Library**: Component testing
- **user-event**: Simulating user interactions

#### Coverage Goals
- **Target**: 70% component test coverage
- **Focus areas**:
  - Generic reusable components
  - Form components with validation logic
  - Complex interactive components

#### Examples

**Testing a Simple Component**

```typescript
// Cliente.tsx
import React from 'react';
import { ICliente } from '../models/clienteModel';

interface ClienteProps {
  cliente: ICliente;
  onSelect: (cliente: ICliente) => void;
}

export const Cliente: React.FC<ClienteProps> = ({ cliente, onSelect }) => {
  return (
    <div className="cliente-item" data-testid="cliente-item">
      <h3>{cliente.Codigo} - {cliente.Descripcion}</h3>
      <p>{cliente.Contacto || 'Sin contacto'}</p>
      <button onClick={() => onSelect(cliente)}>Ver detalles</button>
    </div>
  );
};

// Cliente.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import { Cliente } from './Cliente';

describe('Cliente component', () => {
  const mockCliente = {
    RecordIdent: '123',
    RecordVersion: '1',
    Codigo: 'C001',
    Descripcion: 'Cliente Test',
    Contacto: 'Juan Pérez',
    Telefono: '123456789',
    Email: 'juan@example.com',
    Web: 'www.example.com',
    IDtarifa: null,
    Inactivo: false
  };
  
  const mockOnSelect = jest.fn();
  
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  test('renders cliente information correctly', () => {
    render(<Cliente cliente={mockCliente} onSelect={mockOnSelect} />);
    
    expect(screen.getByText('C001 - Cliente Test')).toBeInTheDocument();
    expect(screen.getByText('Juan Pérez')).toBeInTheDocument();
    expect(screen.getByText('Ver detalles')).toBeInTheDocument();
  });
  
  test('displays "Sin contacto" when contacto is empty', () => {
    const clienteSinContacto = { ...mockCliente, Contacto: '' };
    render(<Cliente cliente={clienteSinContacto} onSelect={mockOnSelect} />);
    
    expect(screen.getByText('Sin contacto')).toBeInTheDocument();
  });
  
  test('calls onSelect when button is clicked', () => {
    render(<Cliente cliente={mockCliente} onSelect={mockOnSelect} />);
    
    fireEvent.click(screen.getByText('Ver detalles'));
    
    expect(mockOnSelect).toHaveBeenCalledWith(mockCliente);
  });
});
```

**Testing a Form Component**

```typescript
// ClienteForm.test.tsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ClienteForm } from './ClienteForm';

describe('ClienteForm component', () => {
  const mockOnSave = jest.fn();
  const mockOnCancel = jest.fn();
  
  beforeEach(() => {
    jest.clearAllMocks();
  });
  
  test('renders all form fields', () => {
    render(
      <ClienteForm 
        onSave={mockOnSave} 
        onCancel={mockOnCancel} 
      />
    );
    
    expect(screen.getByLabelText(/código/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/descripción/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/contacto/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/teléfono/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/web/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/tarifa/i)).toBeInTheDocument();
    expect(screen.getByRole('checkbox', { name: /inactivo/i })).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /guardar/i })).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /cancelar/i })).toBeInTheDocument();
  });
  
  test('validates required fields', async () => {
    render(
      <ClienteForm 
        onSave={mockOnSave} 
        onCancel={mockOnCancel} 
      />
    );
    
    // Click save without filling required fields
    fireEvent.click(screen.getByRole('button', { name: /guardar/i }));
    
    // Check for validation errors
    await waitFor(() => {
      expect(screen.getByText(/código es obligatorio/i)).toBeInTheDocument();
      expect(screen.getByText(/descripción es obligatoria/i)).toBeInTheDocument();
    });
    
    // Verify onSave was not called
    expect(mockOnSave).not.toHaveBeenCalled();
  });
  
  test('submits form with valid data', async () => {
    render(
      <ClienteForm 
        onSave={mockOnSave} 
        onCancel={mockOnCancel} 
      />
    );
    
    // Fill out the form
    await userEvent.type(screen.getByLabelText(/código/i), 'C001');
    await userEvent.type(screen.getByLabelText(/descripción/i), 'Nuevo Cliente');
    await userEvent.type(screen.getByLabelText(/contacto/i), 'Juan Pérez');
    await userEvent.type(screen.getByLabelText(/teléfono/i), '123456789');
    await userEvent.type(screen.getByLabelText(/email/i), 'juan@example.com');
    
    // Submit the form
    fireEvent.click(screen.getByRole('button', { name: /guardar/i }));
    
    // Verify onSave was called with correct data
    await waitFor(() => {
      expect(mockOnSave).toHaveBeenCalledWith({
        Codigo: 'C001',
        Descripcion: 'Nuevo Cliente',
        Contacto: 'Juan Pérez',
        Telefono: '123456789',
        Email: 'juan@example.com',
        Web: '',
        IDtarifa: null,
        Inactivo: false
      });
    });
  });
  
  test('cancels form editing', () => {
    render(
      <ClienteForm 
        onSave={mockOnSave} 
        onCancel={mockOnCancel} 
      />
    );
    
    // Click cancel button
    fireEvent.click(screen.getByRole('button', { name: /cancelar/i }));
    
    // Verify onCancel was called
    expect(mockOnCancel).toHaveBeenCalled();
    expect(mockOnSave).not.toHaveBeenCalled();
  });
});
```

### Integration Testing

#### Purpose
Verify that different units of the application work together correctly.

#### Framework and Tools
- **Jest**: Test runner
- **React Testing Library**: Component testing
- **Mock Service Worker**: API mocking
- **Testing Library User Event**: User interaction simulation

#### Coverage Goals
- **Target**: Key user flows covered
- **Focus areas**:
  - Entity list + detail + edit flows
  - Data synchronization process
  - Login and authentication flows

#### Examples

**Testing Cliente List and Detail Flow**

```typescript
// ClienteListAndDetail.test.tsx
import React from 'react';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ClientesPage } from './ClientesPage';
import { useClienteStore } from '../stores/useClienteStore';

// Mock the store
jest.mock('../stores/useClienteStore');

describe('Cliente List and Detail Flow', () => {
  const mockClientes = [
    {
      RecordIdent: '1',
      RecordVersion: '1',
      Codigo: 'C001',
      Descripcion: 'Cliente 1',
      Contacto: 'Contacto 1',
      Telefono: '111111111',
      Email: 'cliente1@example.com',
      Web: '',
      IDtarifa: null,
      Inactivo: false
    },
    {
      RecordIdent: '2',
      RecordVersion: '1',
      Codigo: 'C002',
      Descripcion: 'Cliente 2',
      Contacto: 'Contacto 2',
      Telefono: '222222222',
      Email: 'cliente2@example.com',
      Web: '',
      IDtarifa: null,
      Inactivo: false
    }
  ];
  
  const mockFetchClientes = jest.fn().mockResolvedValue(undefined);
  const mockGetClienteById = jest.fn();
  const mockSetSelectedCliente = jest.fn();
  
  beforeEach(() => {
    jest.clearAllMocks();
    
    // Setup mock store implementation
    useClienteStore.mockImplementation(() => ({
      clientes: mockClientes,
      loading: false,
      error: null,
      selectedCliente: null,
      fetchClientes: mockFetchClientes,
      getClienteById: mockGetClienteById,
      setSelectedCliente: mockSetSelectedCliente
    }));
  });
  
  test('loads and displays clientes list', async () => {
    render(<ClientesPage />);
    
    // Verify list is loaded
    expect(mockFetchClientes).toHaveBeenCalled();
    
    // Verify clientes are displayed
    expect(screen.getByText('Cliente 1')).toBeInTheDocument();
    expect(screen.getByText('Cliente 2')).toBeInTheDocument();
  });
  
  test('navigates from list to detail view', async () => {
    // Mock getClienteById to return the first cliente
    mockGetClienteById.mockResolvedValue(mockClientes[0]);
    
    render(<ClientesPage />);
    
    // Click on the first cliente in the list
    await userEvent.click(screen.getByText('Cliente 1'));
    
    // Verify getClienteById was called with correct ID
    expect(mockGetClienteById).toHaveBeenCalledWith('1');
    
    // Verify cliente detail dialog is displayed
    await waitFor(() => {
      expect(screen.getByText(/detalles del cliente/i)).toBeInTheDocument();
      expect(screen.getByText('C001')).toBeInTheDocument();
      expect(screen.getByText('Contacto 1')).toBeInTheDocument();
      expect(screen.getByText('111111111')).toBeInTheDocument();
    });
  });
  
  test('navigates from detail to edit view', async () => {
    // Setup selected cliente in the store
    useClienteStore.mockImplementation(() => ({
      clientes: mockClientes,
      loading: false,
      error: null,
      selectedCliente: mockClientes[0],
      fetchClientes: mockFetchClientes,
      getClienteById: mockGetClienteById,
      setSelectedCliente: mockSetSelectedCliente
    }));
    
    render(<ClientesPage />);
    
    // Verify cliente detail dialog is displayed with edit button
    expect(screen.getByText(/detalles del cliente/i)).toBeInTheDocument();
    
    // Click on the edit button
    await userEvent.click(screen.getByText(/editar/i));
    
    // Verify edit form is displayed
    await waitFor(() => {
      expect(screen.getByText(/editar cliente/i)).toBeInTheDocument();
      
      // Verify form is populated with cliente data
      expect(screen.getByDisplayValue('C001')).toBeInTheDocument();
      expect(screen.getByDisplayValue('Cliente 1')).toBeInTheDocument();
      expect(screen.getByDisplayValue('Contacto 1')).toBeInTheDocument();
    });
  });
});
```

### End-to-End Testing

#### Purpose
Verify that the entire application works correctly from the user's perspective, including all integrations.

#### Framework and Tools
- **Cypress**: E2E test framework
- **Cypress Testing Library**: Enhanced selectors

#### Coverage Goals
- **Target**: All critical user flows covered
- **Focus areas**:
  - End-to-end CRUD operations
  - Login and authentication
  - Offline functionality
  - Data synchronization

#### Examples

**Testing Cliente CRUD Operations**

```typescript
// cypress/integration/cliente.spec.ts
describe('Cliente CRUD Operations', () => {
  beforeEach(() => {
    // Setup: Log in and navigate to clientes page
    cy.login('testuser', 'password');
    cy.visit('/clientes');
    cy.wait('@getClientes');
  });
  
  it('creates a new cliente', () => {
    // Click on new cliente button
    cy.findByRole('button', { name: /nuevo cliente/i }).click();
    
    // Fill the form
    cy.findByLabelText(/código/i).type('CYP001');
    cy.findByLabelText(/descripción/i).type('Cliente Cypress');
    cy.findByLabelText(/contacto/i).type('Contacto Cypress');
    cy.findByLabelText(/teléfono/i).type('123456789');
    cy.findByLabelText(/email/i).type('cypress@example.com');
    
    // Submit the form
    cy.findByRole('button', { name: /guardar/i }).click();
    
    // Verify success message
    cy.findByText(/cliente creado con éxito/i).should('be.visible');
    
    // Verify cliente appears in the list
    cy.findByText('Cliente Cypress').should('be.visible');
  });
  
  it('views cliente details', () => {
    // Find and click on an existing cliente
    cy.findByText('Cliente Cypress').click();
    
    // Verify detail dialog is displayed
    cy.findByText(/detalles del cliente/i).should('be.visible');
    
    // Verify cliente data is displayed
    cy.findByText('CYP001').should('be.visible');
    cy.findByText('Contacto Cypress').should('be.visible');
    cy.findByText('123456789').should('be.visible');
    cy.findByText('cypress@example.com').should('be.visible');
  });
  
  it('updates a cliente', () => {
    // Find and click on an existing cliente
    cy.findByText('Cliente Cypress').click();
    
    // Click edit button
    cy.findByRole('button', { name: /editar/i }).click();
    
    // Modify form fields
    cy.findByLabelText(/contacto/i).clear().type('Contacto Actualizado');
    cy.findByLabelText(/teléfono/i).clear().type('987654321');
    
    // Submit the form
    cy.findByRole('button', { name: /guardar/i }).click();
    
    // Verify success message
    cy.findByText(/cliente actualizado con éxito/i).should('be.visible');
    
    // Verify cliente details were updated
    cy.findByText('Cliente Cypress').click();
    cy.findByText('Contacto Actualizado').should('be.visible');
    cy.findByText('987654321').should('be.visible');
  });
  
  it('deletes a cliente', () => {
    // Find and click on an existing cliente
    cy.findByText('Cliente Cypress').click();
    
    // Click delete button
    cy.findByRole('button', { name: /eliminar/i }).click();
    
    // Confirm deletion
    cy.findByRole('button', { name: /confirmar/i }).click();
    
    // Verify success message
    cy.findByText(/cliente eliminado con éxito/i).should('be.visible');
    
    // Verify cliente no longer appears in the list
    cy.findByText('Cliente Cypress').should('not.exist');
  });
});
```

**Testing Offline Functionality**

```typescript
// cypress/integration/offline.spec.ts
describe('Offline Functionality', () => {
  beforeEach(() => {
    // Setup: Log in and navigate to clientes page
    cy.login('testuser', 'password');
    cy.visit('/clientes');
    cy.wait('@getClientes');
  });
  
  it('creates a cliente while offline and syncs when back online', () => {
    // Go offline
    cy.log('Going offline');
    cy.goOffline();
    
    // Create a new cliente while offline
    cy.findByRole('button', { name: /nuevo cliente/i }).click();
    
    // Fill the form
    cy.findByLabelText(/código/i).type('OFF001');
    cy.findByLabelText(/descripción/i).type('Cliente Offline');
    cy.findByLabelText(/contacto/i).type('Contacto Offline');
    
    // Submit the form
    cy.findByRole('button', { name: /guardar/i }).click();
    
    // Verify success message
    cy.findByText(/cliente creado con éxito/i).should('be.visible');
    
    // Verify cliente appears in the list with offline indicator
    cy.findByText('Cliente Offline').should('be.visible');
    cy.findByTestId('offline-indicator').should('be.visible');
    
    // Go back online
    cy.log('Going back online');
    cy.goOnline();
    
    // Trigger sync
    cy.findByRole('button', { name: /sincronizar/i }).click();
    
    // Verify sync success message
    cy.findByText(/sincronización completada/i).should('be.visible');
    
    // Verify offline indicator is gone
    cy.findByText('Cliente Offline').should('be.visible');
    cy.findByTestId('offline-indicator').should('not.exist');
  });
});
```

## Test Organization

### File Structure

```
src/
|-- components/
|   |-- ComponentName.tsx
|   |-- __tests__/
|       |-- ComponentName.test.tsx
|-- stores/
|   |-- useStoreName.ts
|   |-- __tests__/
|       |-- useStoreName.test.ts
|-- utils/
|   |-- utilityName.ts
|   |-- __tests__/
|       |-- utilityName.test.ts
|-- integration-tests/
|   |-- flows/
|       |-- entityFlow.test.tsx
|-- cypress/
    |-- integration/
        |-- entity.spec.ts
```

### Naming Conventions

- **Unit test files**: `ComponentName.test.tsx` or `functionName.test.ts`
- **Integration test files**: `featureFlow.test.tsx`
- **E2E test files**: `feature.spec.ts`
- **Test descriptions**: Should clearly describe what is being tested
  - Use the pattern: "should [expected behavior] when [condition]"

## Testing Best Practices

### General Guidelines

1. **Follow the AAA pattern**:
   - **Arrange**: Set up the test context
   - **Act**: Execute the code being tested
   - **Assert**: Verify the expected outcome

2. **Test behavior, not implementation**:
   - Focus on what the code does, not how it does it
   - Avoid testing implementation details when possible

3. **Use descriptive test names**:
   - Tests should read like specifications
   - Someone unfamiliar with the code should understand what's being tested

4. **Keep tests independent**:
   - Each test should run in isolation
   - Tests should not depend on the order of execution

### Component Testing Guidelines

1. **Use user-centric queries**:
   - Prefer queries that simulate how users interact with the UI
   - Use `getByRole`, `getByLabelText`, etc. over `getByTestId`

2. **Test user interactions**:
   - Test both happy paths and edge cases
   - Use `userEvent` over `fireEvent` when simulating user interactions

3. **Mock dependencies**:
   - Mock external dependencies (API calls, stores)
   - Focus on testing the component's behavior

### Store Testing Guidelines

1. **Reset state between tests**:
   - Use `beforeEach` to reset store state
   - Avoid test interdependence

2. **Test action side effects**:
   - Verify state changes after actions
   - Test error handling

3. **Mock external dependencies**:
   - Mock repositories and other services
   - Focus on testing store logic

## Continuous Integration

### Test Execution

All tests are run automatically on the CI pipeline:

1. **On Pull Request**:
   - Unit tests
   - Integration tests
   - E2E tests on critical paths

2. **On Merge to Main**:
   - All tests, including comprehensive E2E suite

### Performance Metrics

The CI pipeline collects performance metrics:

- **Test execution time**
- **Code coverage**
- **Bundle size changes**

## Test Data Management

### Fixtures

- **Unit/Integration tests**: Use in-memory fixtures
- **E2E tests**: Use database seeding scripts

### Mock Implementations

```typescript
// repositoryMocks.ts
export const createMockClienteRepository = () => ({
  findAll: jest.fn(),
  findById: jest.fn(),
  create: jest.fn(),
  update: jest.fn(),
  delete: jest.fn()
});

// useMockStore.ts
export const useMockClienteStore = () => {
  // Create a real store instance
  const store = useClienteStore;
  
  // Reset store before returning
  store.setState({
    clientes: [],
    loading: false,
    error: null,
    selectedCliente: null
  });
  
  // Return store with mocked actions
  return {
    ...store,
    fetchClientes: jest.fn(),
    getClienteById: jest.fn(),
    createCliente: jest.fn(),
    updateCliente: jest.fn(),
    deleteCliente: jest.fn(),
    setSelectedCliente: jest.fn()
  };
};
```

## Conclusion

This testing strategy provides a comprehensive approach to ensuring the quality and reliability of the Preventa application. By implementing tests at all levels, from unit to end-to-end, we can catch issues early and provide a robust user experience. Regular review and updating of tests alongside feature development will maintain the effectiveness of our testing approach.
