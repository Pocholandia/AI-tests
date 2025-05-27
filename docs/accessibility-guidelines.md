# Accessibility Guidelines

## Overview

This document provides comprehensive accessibility guidelines for the Preventa application. Following these guidelines ensures that the application is usable by people with various disabilities and meets international accessibility standards.

## Standards Compliance

The Preventa application should conform to the following standards:

- **WCAG 2.1 Level AA**: Web Content Accessibility Guidelines
- **WAI-ARIA 1.1**: Accessible Rich Internet Applications specifications
- **EN 301 549**: European accessibility requirements for ICT products and services

## Core Principles

### 1. Perceivable

Information and user interface components must be presentable to users in ways they can perceive.

#### Text Alternatives

- Provide text alternatives for non-text content
- Use `alt` attributes for images
- Provide transcripts for audio content

```jsx
// Good example
<img src="logo.png" alt="Preventa Logo" />

// Bad example
<img src="logo.png" /> // Missing alt text
```

#### Time-Based Media

- Provide alternatives for time-based media (videos, animations)
- Include captions for videos
- Avoid content that flashes more than 3 times per second

#### Adaptable Content

- Create content that can be presented in different ways without losing information
- Use proper semantic HTML elements
- Maintain a logical reading order in the DOM

```jsx
// Good example
<article>
  <h1>Customer Details</h1>
  <section>
    <h2>Contact Information</h2>
    <p>...</p>
  </section>
</article>

// Bad example
<div>
  <div>Customer Details</div>
  <div>
    <div>Contact Information</div>
    <div>...</div>
  </div>
</div>
```

#### Distinguishable Content

- Make it easy for users to see and hear content
- Maintain minimum contrast ratio of 4.5:1 for normal text and 3:1 for large text
- Don't use color as the only means of conveying information
- Ensure text can be resized up to 200% without loss of functionality

```jsx
// Good example
<p>
  This field is required <span aria-hidden="true" className="required-icon">*</span>
</p>

// Bad example
<p>
  This field is <span className="text-red">required</span> // Using only color to indicate importance
</p>
```

### 2. Operable

User interface components and navigation must be operable.

#### Keyboard Accessibility

- Make all functionality available from a keyboard
- Avoid keyboard traps
- Provide visible focus indicators
- Implement logical tab order

```jsx
// Good example - Custom button with keyboard support
const CustomButton = ({ onClick, children }) => {
  return (
    <div
      role="button"
      tabIndex={0}
      onClick={onClick}
      onKeyDown={(e) => {
        if (e.key === 'Enter' || e.key === ' ') {
          onClick(e);
        }
      }}
    >
      {children}
    </div>
  );
};

// Better example - Use native elements when possible
const Button = ({ onClick, children }) => {
  return (
    <button
      onClick={onClick}
    >
      {children}
    </button>
  );
};
```

#### Enough Time

- Provide users enough time to read and use content
- Allow users to extend, adjust, or disable time limits
- Warn users about timeouts and provide options to extend

```jsx
// Example of session timeout warning
const SessionTimeoutWarning = () => {
  const [timeLeft, setTimeLeft] = useState(300); // 5 minutes
  
  // ... timer logic
  
  return (
    <Dialog open={timeLeft < 60} aria-live="polite">
      <h2>Session About to Expire</h2>
      <p>Your session will expire in {timeLeft} seconds.</p>
      <div>
        <button onClick={extendSession}>Extend Session</button>
        <button onClick={logout}>Logout</button>
      </div>
    </Dialog>
  );
};
```

#### Seizures and Physical Reactions

- Do not design content in a way that is known to cause seizures
- Avoid rapid flashing animations
- Provide controls for any animated content

#### Navigable

- Provide ways to help users navigate and find content
- Use descriptive page titles
- Implement proper heading structure
- Provide "Skip to main content" links
- Ensure that focus order follows a logical sequence

```jsx
// Skip link example
const SkipLink = () => {
  return (
    <a
      href="#main-content"
      className="skip-link"
    >
      Skip to main content
    </a>
  );
};

// Page structure
const Page = () => {
  return (
    <>
      <SkipLink />
      <Header />
      <main id="main-content">
        <h1>Page Title</h1>
        {/* Page content */}
      </main>
      <Footer />
    </>
  );
};
```

#### Input Modalities

- Make functionality operable through various inputs beyond keyboard
- Ensure touch targets are at least 44x44 pixels
- Provide alternative input methods

### 3. Understandable

Information and operation of the user interface must be understandable.

#### Readable

- Make text content readable and understandable
- Identify the language of the page and any language changes
- Explain any unusual words or jargon
- Expand abbreviations on first use

```jsx
// Language identification
<html lang="es">
  <body>
    <p>Este es un texto en español.</p>
    <p lang="en">This is a text in English.</p>
  </body>
</html>
```

#### Predictable

- Make pages appear and operate in predictable ways
- Don't change context automatically when elements receive focus
- Don't initiate changes automatically when users input data
- Keep navigation mechanisms consistent across pages

#### Input Assistance

- Help users avoid and correct mistakes
- Identify input errors clearly
- Provide clear labels and instructions
- Suggest corrections for errors when possible
- Provide validation before submitting forms with legal or financial data

```jsx
// Form field with error handling
const FormField = ({ id, label, error, ...props }) => {
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input
        id={id}
        aria-invalid={Boolean(error)}
        aria-describedby={error ? `${id}-error` : undefined}
        {...props}
      />
      {error && (
        <div id={`${id}-error`} className="error-message" role="alert">
          {error}
        </div>
      )}
    </div>
  );
};
```

### 4. Robust

Content must be robust enough to be interpreted by a wide variety of user agents, including assistive technologies.

#### Compatible

- Ensure compatibility with current and future user tools
- Use valid HTML
- Provide name, role, and value for all UI components
- Ensure status messages can be programmatically determined

```jsx
// Status message example
const StatusMessage = ({ message, type }) => {
  return (
    <div
      role="status"
      aria-live={type === 'error' ? 'assertive' : 'polite'}
      className={`status-${type}`}
    >
      {message}
    </div>
  );
};
```

## Implementation Guidelines for Preventa Components

### Forms and Data Entry

All forms in the Preventa application must follow these guidelines:

#### Labels and Instructions

- Every input must have a visible, associated label
- Use `<label>` elements with proper `for` attributes
- Provide clear instructions for complex inputs
- Group related fields with `<fieldset>` and `<legend>`

```jsx
// Cliente Form Example
const ClienteForm = () => {
  return (
    <form>
      <div className="form-group">
        <label htmlFor="codigo">Código</label>
        <input
          id="codigo"
          name="codigo"
          type="text"
          required
          aria-required="true"
        />
      </div>
      
      <fieldset>
        <legend>Información de Contacto</legend>
        
        <div className="form-group">
          <label htmlFor="contacto">Persona de Contacto</label>
          <input
            id="contacto"
            name="contacto"
            type="text"
          />
        </div>
        
        <div className="form-group">
          <label htmlFor="telefono">Teléfono</label>
          <input
            id="telefono"
            name="telefono"
            type="tel"
            aria-describedby="telefono-hint"
          />
          <div id="telefono-hint" className="hint-text">
            Formato: 123-456-789
          </div>
        </div>
      </fieldset>
      
      <button type="submit">Guardar</button>
    </form>
  );
};
```

#### Error Handling

- Clearly identify errors
- Provide text descriptions of errors
- Associate error messages with the appropriate fields
- Use appropriate ARIA attributes (`aria-invalid`, `aria-describedby`)

```jsx
// Error handling in form submission
const ValidatedForm = () => {
  const [values, setValues] = useState({});
  const [errors, setErrors] = useState({});
  
  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validateForm(values);
    
    if (Object.keys(validationErrors).length > 0) {
      setErrors(validationErrors);
      // Announce to screen readers that there are errors
      document.getElementById('form-errors-summary').focus();
    } else {
      // Submit form
    }
  };
  
  return (
    <form onSubmit={handleSubmit} noValidate>
      {Object.keys(errors).length > 0 && (
        <div
          id="form-errors-summary"
          className="error-summary"
          role="alert"
          tabIndex={-1}
        >
          <h2>Por favor corrija los siguientes errores:</h2>
          <ul>
            {Object.values(errors).map((error, index) => (
              <li key={index}>{error}</li>
            ))}
          </ul>
        </div>
      )}
      
      {/* Form fields */}
      
      <button type="submit">Guardar</button>
    </form>
  );
};
```

### Dialogs and Modals

Modal dialogs in the Preventa application require special attention for accessibility:

#### Focus Management

- Trap focus within the modal when open
- Return focus to the triggering element when closed
- Set initial focus to the first focusable element or close button

#### Keyboard Interaction

- Allow closing with ESC key
- Ensure all controls are keyboard accessible
- Maintain a logical tab order

#### ARIA Attributes

- Use `role="dialog"` or `role="alertdialog"`
- Include `aria-labelledby` referencing the dialog title
- Use `aria-describedby` if needed for longer descriptions
- Set `aria-modal="true"`

```jsx
// Accessible Modal Component
const Modal = ({ isOpen, onClose, title, children }) => {
  const modalRef = useRef(null);
  const triggerRef = useRef(document.activeElement);
  
  useEffect(() => {
    const handleEscape = (e) => {
      if (e.key === 'Escape') onClose();
    };
    
    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      return () => {
        document.removeEventListener('keydown', handleEscape);
        // Return focus to the element that opened the modal
        triggerRef.current?.focus();
      };
    }
  }, [isOpen, onClose]);
  
  // Focus trap implementation...
  
  if (!isOpen) return null;
  
  return (
    <div className="modal-overlay">
      <div
        ref={modalRef}
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
        className="modal"
      >
        <header>
          <h2 id="modal-title">{title}</h2>
          <button
            aria-label="Cerrar"
            onClick={onClose}
            className="close-button"
          >
            ×
          </button>
        </header>
        <div className="modal-content">
          {children}
        </div>
      </div>
    </div>
  );
};
```

### Data Tables

Tables used for displaying data must be properly structured for accessibility:

#### Table Structure

- Use proper table elements (`<table>`, `<thead>`, `<tbody>`, `<th>`, `<td>`)
- Include proper scope attributes on header cells
- Use `<caption>` for table title when appropriate

#### Complex Tables

- Associate data cells with headers using `headers` attribute in complex tables
- For very complex tables, consider alternative presentations

```jsx
// Accessible Data Table
const PedidosTable = ({ pedidos }) => {
  return (
    <table>
      <caption>Lista de Pedidos</caption>
      <thead>
        <tr>
          <th scope="col">Número</th>
          <th scope="col">Cliente</th>
          <th scope="col">Fecha</th>
          <th scope="col">Total</th>
          <th scope="col">Acciones</th>
        </tr>
      </thead>
      <tbody>
        {pedidos.map(pedido => (
          <tr key={pedido.RecordIdent}>
            <td>{pedido.Numero}</td>
            <td>{pedido.Cliente?.Descripcion}</td>
            <td>{formatDate(pedido.FechaHora)}</td>
            <td>{formatCurrency(pedido.Total)}</td>
            <td>
              <button
                aria-label={`Ver detalles del pedido ${pedido.Numero}`}
                onClick={() => viewPedido(pedido)}
              >
                Ver
              </button>
              <button
                aria-label={`Editar pedido ${pedido.Numero}`}
                onClick={() => editPedido(pedido)}
              >
                Editar
              </button>
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  );
};
```

### Navigation and Menus

Navigation components must be accessible to all users:

#### Menu Structure

- Use appropriate semantic elements (`<nav>`, `<ul>`, `<li>`, etc.)
- Include ARIA roles when necessary
- Ensure proper keyboard navigation

#### Dropdown Menus

- Use `aria-expanded` to indicate state
- Include `aria-haspopup` when appropriate
- Manage focus for keyboard users

```jsx
// Accessible Navigation Menu
const MainNavigation = () => {
  return (
    <nav aria-label="Navegación principal">
      <ul className="main-menu">
        <li><a href="/clientes">Clientes</a></li>
        <li><a href="/productos">Productos</a></li>
        <li>
          <button
            aria-expanded={isOpen}
            aria-haspopup="true"
            onClick={toggleDropdown}
          >
            Pedidos
          </button>
          {isOpen && (
            <ul className="submenu">
              <li><a href="/pedidos/nuevo">Nuevo Pedido</a></li>
              <li><a href="/pedidos">Ver Pedidos</a></li>
              <li><a href="/pedidos/informes">Informes</a></li>
            </ul>
          )}
        </li>
        <li><a href="/tarifas">Tarifas</a></li>
      </ul>
    </nav>
  );
};
```

## Testing for Accessibility

### Automated Testing

Incorporate these tools into the development workflow:

- **ESLint with jsx-a11y**: Catch common accessibility issues during development
- **Axe**: Integrate automated testing in CI/CD pipeline
- **Lighthouse**: Perform periodic audits

### Manual Testing

Regularly conduct these manual tests:

1. **Keyboard Navigation**: Test the entire application using only keyboard
2. **Screen Reader Testing**: Test with NVDA (Windows) and VoiceOver (macOS)
3. **Zoom Testing**: Test at 200% zoom to ensure readability
4. **Contrast Checking**: Verify all text meets contrast requirements

### Testing Checklist

- [ ] All interactive elements are keyboard accessible
- [ ] Focus indicators are clearly visible
- [ ] Heading structure is logical and sequential
- [ ] Images have appropriate alt text
- [ ] Color is not the only means of conveying information
- [ ] Text has sufficient contrast against background
- [ ] Forms have properly associated labels
- [ ] Error messages are clear and programmatically associated with fields
- [ ] ARIA attributes are used correctly
- [ ] Dynamic content changes are announced to screen readers

## Mobile Accessibility

### Touch Targets

- Ensure touch targets are at least 44x44 pixels
- Provide adequate spacing between touch targets

### Gestures

- Provide alternative methods for complex gestures
- Ensure all functionality can be accessed without gestures that require dexterity

### Screen Orientation

- Support both portrait and landscape orientations
- Don't restrict the interface to a single orientation

## Resources

### Guidelines and Standards

- [WCAG 2.1](https://www.w3.org/TR/WCAG21/)
- [WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices-1.1/)
- [Inclusive Components](https://inclusive-components.design/)

### Testing Tools

- [axe-core](https://github.com/dequelabs/axe-core)
- [WAVE Web Accessibility Evaluation Tool](https://wave.webaim.org/)
- [Lighthouse](https://developers.google.com/web/tools/lighthouse)
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)

## Conclusion

Implementing these accessibility guidelines in the Preventa application ensures that all users, regardless of ability, can effectively use the system. Accessibility should be considered from the beginning of the development process and maintained throughout the application lifecycle.
