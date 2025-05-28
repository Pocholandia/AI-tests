# Testing & Acceptance Criteria

This section describes the testing requirements and acceptance criteria specific to the Preventa application.

## Acceptance Criteria
- Create, edit, and delete orders, clients, and products using modal forms, with correct data and validations
- Data persists after closing and reopening the app
- Clients and products can be associated with orders
- Orders can be filtered by client, product, date, price, and free text
- Clients can be searched and their associated orders viewed
- Order lines and entire orders can be deleted with confirmation
- Editing an order reflects changes correctly
- UI is clear, responsive, and provides visual feedback
- Validations and error messages are shown as appropriate

## Testing Strategy
- Unit tests for business logic, validation, and store actions (target: 80% coverage)
- Component tests for forms, lists, and interactive components (target: 70% coverage)
- End-to-end tests for main user flows (CRUD, filtering, sync, demo data)

## Example: Unit Test (Jest)

```typescript
// Example: formatCurrency utility
import { formatCurrency } from './formatCurrency';
describe('formatCurrency', () => {
  test('formats positive values', () => {
    expect(formatCurrency(10.5)).toBe('10,50 €');
  });
});
```

## Example: Component Test (React Testing Library)

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { Cliente } from './Cliente';
describe('Cliente component', () => {
  test('renders cliente info', () => {
    render(<Cliente cliente={mockCliente} onSelect={jest.fn()} />);
    expect(screen.getByText('C001 - Cliente Test')).toBeInTheDocument();
  });
});
``` 