# Generic Development Guidelines (Full, No Rule Lost)

> This document consolidates all generic, techstack-independent rules, standards, and best practices from the original documentation. Every rule, nuance, and conditional is preserved. Use this as the most complete reference for AI-driven or human application development, regardless of programming language or framework.

---

## 1. Overview and Usage

- These guidelines are mandatory for all projects using this techstack.
- Review all documents before starting development.
- Follow the guidelines in your implementation.
- Reference these standards in code reviews.
- Propose updates when improvements are identified.
- Keep these documents up-to-date with industry best practices.
- Document any deviations in project-specific documentation.
- Review and update guidelines periodically.

---

## 2. Coding Rules and Best Practices

- **Principios SOLID**: Aplicar siempre SOLID para favorecer la mantenibilidad y la extensibilidad, incluso si obliga a un diseño más detallado y un código más verboso.
- **Cobertura de pruebas**: Toda funcionalidad debe acompañarse de tests unitarios y de integración que verifiquen tanto la lógica pura como las interacciones entre módulos.
- **Separación de capas**:
  1. **Capa de datos**
     - Encapsular todas las operaciones de acceso a datos en una capa independiente.
     - Usar un patrón repositorio o similar, con interfaces/contratos que permitan intercambiar fácilmente la fuente (local, remota, híbrida).
  2. **Lógica de negocio**
     - Extraer validaciones, transformaciones y reglas de negocio a funciones puras en su propia capa.
     - Evitar referencias a la UI: la lógica de negocio solo debe depender de su propia capa de datos y de utilidades.
  3. **Presentación / UI**
     - Mantener los componentes o pantallas centrados en la renderización y el enrutamiento de eventos, delegando la lógica a las capas inferiores.
- **Descomposición de componentes / módulos**:
  - Diseñar unidades (componentes, clases, módulos) con una única responsabilidad.
  - Aplicar el patrón contenedor / presentación (o variaciones equivalentes) para separar lógica de presentación de lógica de gestión de datos.
  - Limitar el tamaño de cada unidad (por ejemplo, < 300 líneas) para facilitar su comprensión y prueba.
- **Gestión de estado**:
  - Elegir una única solución de gestión de estado y usarla de modo consistente.
  - Crear "stores" o módulos de estado por dominio o feature.
  - Proporcionar selectores o getters específicos para evitar renderizados o subscripciones innecesarias.
- **Preparación para tests y mocks**:
  - Inyectar dependencias (interfaces, fábricas o contexto) para poder sustituir implementaciones concretas por dobles de test.
  - Evitar acoplamientos directos a infraestructuras (APIs HTTP, base de datos, sistema de archivos…).
- **Estandarización de eventos**:
  - Definir una convención unificada de nombres y estructuras de manejadores de eventos.
  - Reutilizar funciones o fábricas de eventos para comportamientos comunes (confirmaciones, validaciones…).
- **Abstracción de formularios**:
  - Centralizar la validación y el manejo de cambios de formulario en hooks, clases o utilidades reutilizables.
  - Evitar duplicar lógica de formularios en distintos componentes.
- **Sistema de feedback al usuario**:
  - Implementar un mecanismo global de notificaciones o mensajes (toast, banners, diálogos…).
  - Proveer una API única para mostrar feedback, independiente del framework de UI.
- **Integración con APIs externas**:
  - Adoptar un patrón adaptador o wrapper para desacoplar llamadas HTTP de la lógica de negocio.
  - Gestionar consistentemente estados de carga, éxito y error.
- **Soporte offline y sincronización**:
  - Diseñar pensando en un flujo local-first: la aplicación debe ser plenamente funcional sin conexión.
  - Llevar un registro de cambios locales (nuevos, modificados, eliminados).
  - Implementar un mecanismo de cola y reconciliación con el servidor, con detección de conectividad y resolución de conflictos.
  - Ofrecer indicadores de estado de sincronización y permitir tanto sincronización manual como automática.

---

## 3. Registry, Feature, and Metadata Patterns

### Centralized, Type-Safe, and Extensible Registries
- Use centralized registries for features, field types, and table metadata to enable dynamic discovery, extensibility, and decoupling.
- Registries should be type-safe, supporting strong typing and contract enforcement.
- Support self-registration and auto-registration of features and entities during initialization.
- Allow dynamic registration and unregistration of features, field types, and components at runtime.
- Provide APIs for registering, retrieving, listing, and unregistering items (features, field types, etc.).
- Support error handling for duplicate registration, missing features, and other registry operations.
- Enable plugin systems, feature toggles, dynamic routing, analytics, access control, progressive enhancement, feature discovery, and modular architecture through registry patterns.

### Field Registry and Field Type Resolution
- Implement a field registry for form field types, supporting dynamic detection and adaptation to different UI libraries.
- Use adapters and resolvers to decouple field rendering and validation from business logic and UI framework.
- Support branded types and type-safe field definitions.
- Provide utilities for defining fields with type safety and for dynamic field type resolution based on entity data and metadata.
- Maintain a set of pre-defined field types (e.g., text, number, checkbox, date, select, etc.) and allow easy extension.
- Ensure all fields of the same type behave and appear consistently across the application.
- Allow custom field type resolvers for advanced or domain-specific cases.

### Meta-Registry and Table Metadata
- Use a decentralized, extensible registry for table metadata, supporting per-table registration and dynamic component discovery.
- Support hook-based APIs for consuming metadata in components.
- Provide a metadata explorer UI for debugging and exploring registered tables.
- Support deep merging of metadata slices and automated testing for metadata consistency.
- Allow registration and retrieval of ad-hoc data (e.g., sync handlers, feature flags) per table.

### Feature Registry
- Register features with unique IDs, names, descriptions, icons, components, and launch functions.
- Support registering features with or without UI components.
- Provide methods to retrieve, list, and unregister features, and to check for feature existence.
- Support error handling for duplicate and missing features.
- Enable use cases such as feature toggles, plugin systems, analytics, access control, progressive enhancement, dynamic routing, feature discovery, and modular architecture.

---

## 4. Component and Feature Modularity

- Keep components and features small, focused, and self-contained.
- Use clear, consistent naming conventions (e.g., kebab-case for feature IDs).
- Document all exported functions, components, and features with clear descriptions.
- Expose only what is necessary through index files; encapsulate internal details.
- Separate concerns: keep launch logic minimal and delegate to other modules.
- Write unit tests for all components and features.
- Handle errors gracefully and document error handling strategies.
- Maintain consistency with established coding principles and patterns.
- Periodically review and refactor components and features for maintainability and performance.

---

## 5. Data Specification and Table Structure

### Tipos de campo (Field Types)
- "tipoIdentificador", "tipoVersion", "tipoInactivo", "tipoTimestamp", "tipotimestampminutos", "tipotimestampsync", "tipoEmpresa", "tipoEjercicio", "tipoCodigo", "tipoSerie", "tipoNumero", "tipoDescripcion", "tipoNombre", "tipoObservaciones":
  - Cada uno se definirá en el techstack de cada proyecto, con las restricciones de longitud y tipo indicadas en el documento original.

### Campos obligatorios para todas las tablas
- **RecordIdent**: (tipoidentificador) debe ser único dentro de cada tabla. Es la PRIMARY KEY. Solo de uso interno, no se muestra en el UI. Se calcula y asigna automáticamente al crear el registro y es inmutable. Puede haber huecos en la numeración. Se usa en cualquier punto del UI donde se necesite referirse a un registro de manera única (por ejemplo, en la URL de un registro).
- **RecordVersion**: (tipoversion) Empieza en 1 y se incrementa automáticamente cada vez que se modifica un registro. Es un contador de versiones, útil para "Optimistic Locking" y sincronización. Solo de uso interno, no se muestra en el UI.

### Campo "ID" (prohibido)
- No se puede utilizar el campo "ID" en ninguna tabla. Si se desea referirse a un registro de manera única, se debe utilizar el campo "RecordIdent".

### Campo "Inactivo" (opcional)
- Tipo: tipoInactivo. Valor por defecto: el que en el techstack corresponda a "false". Si está presente, indica si el registro está inactivo. Si un registro está inactivo, por defecto no se mostrará en el UI, a menos que se indique lo contrario a través del propio UI.

### Campo "Codigo" (opcional)
- Tipo: tipoCodigo. No se permite que dos registros tengan el mismo Codigo. No puede ser nulo ni "". Si está presente, es un string que identifica de manera única el registro. Por defecto es "". Es el código que ve el usuario en el UI.

### Campos "Serie" y "Numero" (opcionales)
- Solo se aplica cuando una tabla tiene ambos campos. No se permite que dos registros tengan el mismo Serie y Numero. Numero no puede ser nulo ni "". Serie no puede ser nulo pero puede ser "". Si una tabla tiene ambos, el par identifica de manera única el registro. Por defecto serie es "" y numero es "". El par Serie y Numero juega el mismo papel que el campo "Codigo".

### Campos "empresa" y "ejercicio" (opcionales)
- Solo se aplica cuando una tabla tiene ambos campos. empresa: tipoEmpresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario. ejercicio: tipoEjercicio. Requerido. Si es un número puede ser cero (o si es una string puede ser "" o "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino Empresa,Ejercicio,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino Empresa,Ejercicio,Codigo.

### Campo "empresa" (opcional)
- Solo se aplica cuando una tabla tiene el campo empresa. empresa: tipo empresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino empresa,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino empresa,Codigo.

### Campo "Descripcion" (opcional)
- tipo: tipoDescripcion. Si está presente, es un string que describe el registro de forma humanamente comprensible. Cuando se desea mostrar el registro al usuario en el UI, se debe priorizar el campo "Descripcion" sobre todos los demás campos, aunque el campo "Codigo" también se muestre.

### Campo "Observaciones" (opcional)
- tipo: tipoObservaciones. Si está presente, es un texto de uso libre que puede contener cualquier información adicional sobre el registro.

### Relaciones entre entidades
- Se realizan usando los campos `RecordIdent` de la entidad relacionada.
- Un campo que relaciona con otra tabla se llamará "IDtabla" (por ejemplo, "IDejemplo" para relacionar una tabla cualquiera con la tabla "Ejemplos") y será el campo que se usará para la relación.
- tipo: tipoIdentificador
- Ejemplo: En un pedido, el campo `IDcliente` contiene el `RecordIdent` del cliente.
- Ejemplo: En las líneas de un pedido, `IDpedido` e `IDproducto` apuntan a los identificadores del pedido y el producto, respectivamente.
- Ejemplo: En una tabla, `IDadicional` apunta a un identificador de una tabla, aunque por el nombre no se pueda inferir qué tabla es.
- **Campo `ID` no existe**: Ninguna tabla o entidad puede tener un campo `ID`. Cuando algún elemento de UI necesita referirse de manera única a un registro de una tabla, no se utilizará el campo ID de esa tabla (que no existe), sino su campo RecordIdent.

---

## 6. Data Synchronization

### Estructura básica de las tablas que se sincronizan
- Todos estos campos son obligatorios en cada tabla que se sincroniza:
  - **SyncStatus**: enum ["synced", "pending_create", "pending_update", "pending_delete"] - Estado actual de sincronización
  - **LastSyncDate**: timestamp - Última fecha en que el registro se sincronizó con éxito
  - **LocalModifiedDate**: timestamp - Fecha de la última modificación local
  - **ServerModifiedDate**: timestamp - Fecha de la última modificación en el servidor
  - **ConflictResolution**: opcional, enum ["local_wins", "server_wins", "manual"] - Estrategia de resolución para conflictos

### Reglas de sincronización
- **Enfoque local-first**: Todos los datos se almacenan primero localmente, permitiendo operaciones completas sin conexión
- **Identificadores únicos**: Todos los identificadores deben ser universalmente únicos incluso cuando se generen offline (usar UUIDs o similar)
- **Tracking de cambios**: El sistema debe rastrear todos los cambios realizados mientras está offline para sincronizarlos posteriormente
- **Detección de conflictos**: Implementar detección de conflictos basado en versiones y timestamps
- **Resolución automática**: Cuando sea posible, resolver conflictos automáticamente usando reglas preestablecidas
- **Resolución manual**: Proporcionar interfaz para resolución manual cuando sea necesario
- **Migraciones automáticas**: Las actualizaciones de esquema deben ejecutarse automáticamente
- **Atomicidad**: Las migraciones deben ser atómicas, completándose en su totalidad o no ejecutándose
- **Registro**: Mantener registro de todas las migraciones ejecutadas
- **Control de versiones**: Cada versión de la aplicación tiene un número de versión de esquema asociado

### Observaciones
- Por defecto, estos campos no son visibles en el UI. Si se desea mostrarlos, se debe indicar explícitamente en la documentación del UI.

---

## 7. Data Migration and Versioning

### Principios de Versionado
1. Compatibilidad hacia atrás: Las nuevas versiones deben poder leer datos de versiones anteriores
2. Migraciones automáticas: Las actualizaciones de esquema deben ejecutarse automáticamente
3. Atomicidad: Las migraciones deben ser atómicas, completándose en su totalidad o no ejecutándose
4. Registro: Mantener registro de todas las migraciones ejecutadas
5. Control de versiones: Cada versión de la aplicación tiene un número de versión de esquema asociado

### Estrategias para Cambios de Esquema
- Actualización progresiva de esquema: Fase 1 (compatibilidad dual), Fase 2 (consolidación)
- Sincronización y cambios de esquema: El servidor debe soportar tanto el formato viejo como el nuevo durante un período de transición. Los clientes incluyen información de versión en las solicitudes de sincronización. El servidor adapta su respuesta según la versión del cliente.
- Manejo de versiones de aplicación: Definir y verificar compatibilidad de versiones (major, minor, patch, build).
- Actualización de datos al iniciar la aplicación: Verificar versión anterior, ejecutar migraciones si es necesario, actualizar versión almacenada.
- Estrategias para cambios de esquema que afectan la sincronización: Usar transformadores de datos para compatibilidad (clientToServer, serverToClient).
- Verificaciones de integridad de datos: Verificar y reparar referencias huérfanas y otros problemas de integridad referencial.

---

## 8. Registry, Pluggability, and Design Patterns

### Pluggable Editor Registry: Principles & Generic API
- Pluggability: Allow arbitrary editor components to be registered and used for any field by string key.
- Type Safety: All editors must conform to a generic props interface or contract.
- Namespacing: Editor keys may use namespaces (e.g. `cliente.selector`) for organization.
- Override & Unregister: Editors can be overridden or removed at runtime.
- Editor Discovery: All registered editors can be listed for debugging or UI-building.
- Default Fallback: Always provide a fallback editor if a custom one is not found.
- Register editors at app startup. Use clear, namespaced keys for organization. Always provide a fallback editor for robustness. Document new editors and their props.

### Registry Pattern
- Centralización: Punto único para registro y descubrimiento
- Desacoplamiento: Los componentes no necesitan conocerse directamente
- Extensibilidad: Nuevos componentes pueden añadirse sin modificar código existente
- Dinamismo: Permite la composición dinámica de componentes en tiempo de ejecución

### Adapter Pattern
- Compatibilidad: Permite la interoperabilidad entre interfaces incompatibles
- Reutilización: Permite reutilizar código existente con nuevas interfaces
- Separación: Separa la lógica de negocio de la lógica de presentación
- Mantenibilidad: Facilita el cambio de implementaciones subyacentes

### Dependency Injection
- Inversión de Control: Las dependencias se proporcionan en lugar de crearlas internamente
- Testabilidad: Facilita la creación de pruebas unitarias con mocks
- Desacoplamiento: Reduce el acoplamiento entre componentes
- Configurabilidad: Permite cambiar implementaciones sin modificar código

### Factory Pattern
- Centralización: Centraliza la lógica de creación de objetos
- Encapsulación: Oculta los detalles de implementación
- Abstracción: Trabaja con interfaces en lugar de implementaciones concretas
- Extensibilidad: Facilita la adición de nuevos tipos de objetos

### Interface-Based Design
- Contratos claros: Interfaces definen contratos explícitos entre componentes
- Sustituibilidad: Implementaciones pueden ser intercambiadas si respetan el contrato
- Evolución independiente: Los componentes pueden evolucionar independientemente
- Testabilidad: Facilita la creación de mocks para pruebas

---

## 9. Component and Feature Modularity (continued)

- Use provided component templates and patterns as a starting point for new components.
- Maintain consistency with VIBE coding principles and established state management patterns.
- Ensure all components are properly typed and documented.
- Write unit tests for all components and document their props and usage examples.
- Update documentation when making changes to components or features.
- Keep up with updates to underlying technologies and periodically review/refactor for performance and maintainability.

---

## 10. Testing Guidelines

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

---

## 11. Meta: Analysis and Classification

- Algunos documentos pueden contener reglas mixtas o ejemplos específicos, pero las reglas aquí incluidas son las que el análisis de los documentos ha clasificado como genéricas y techstack-independientes.

---

## 1A. Naming and Documentation Conventions

- Use **camelCase** for variable and function names in code.
- Use **PascalCase** for component and class names.
- Use **kebab-case** for URLs and CSS file names.
- Use **snake_case** for documentation file names.
- Documentation files must use the `.md` extension and descriptive, hyphen-separated names.
- Headings in documentation should use title capitalization in the document's language.
- Always consult the project glossary for consistent terminology. Propose new terms as needed.
- For bilingual documentation, use the correct equivalent terms in each language.

---

## 1B. Accessibility Guidelines

- Applications must conform to **WCAG 2.1 Level AA**, **WAI-ARIA 1.1**, and **EN 301 549** standards.
- All interactive elements must be keyboard accessible, with logical tab order and visible focus indicators.
- Provide text alternatives for all non-text content (e.g., `alt` attributes for images, transcripts for audio).
- All images require alt text; form fields must have associated labels; use `aria-*` attributes appropriately.
- Maintain a minimum contrast ratio of 4.5:1 for normal text and 3:1 for large text.
- Do not use color as the only means of conveying information; provide alternative indicators.
- Ensure text can be resized up to 200% without loss of functionality.
- Avoid rapid flashing or animations that could trigger seizures; honor user preferences for reduced motion.
- Provide skip links and logical heading structure for navigation.

---

## 1C. Error Handling Principles

- All errors must be communicated to the user in a clear and understandable way.
- Errors must be logged internally with sufficient context for debugging.
- Where possible, provide recovery options to the user and avoid data loss.
- Categorize errors by type and severity (validation, application, network/sync, etc.).
- Use a centralized notification service for user feedback (success, error, warning, info).
- Use a centralized error logging service; log errors locally if offline and send to monitoring when online.
- Use Error Boundaries (or equivalent) in the UI to catch and display unexpected errors.
- Validation errors should be shown next to the relevant fields.
- For network/offline errors, inform the user and queue actions for retry when possible.

---

## 1D. Project and Feature Organization

- Use a **feature-based organization**: each domain/feature has its own directory with components, store, models, and public API (`index.ts`).
- Only import from other features via their public API (never internal files).
- Shared utilities and generic components must be extracted to `/utils/` or `/components/generic/`.
- Use barrel files (`index.ts`) for exports; prefer named exports over default exports.
- Each file should have a single responsibility and be <300 lines when possible.
- Avoid cyclic imports; follow the dependency hierarchy: UI → stores → repositories → utils.
- When creating new features, follow the established directory and export structure.

---

## 1E. Registry, Adapter, DI, Factory, and Interface Patterns

- **Registry Pattern**: Use centralized registries for dynamic discovery, extensibility, and decoupling. Provide APIs for registering, retrieving, listing, and unregistering items. Support error handling for duplicates and missing items.
- **Adapter Pattern**: Use adapters to enable interoperability between incompatible interfaces, separate business logic from presentation, and facilitate code reuse.
- **Dependency Injection**: Provide dependencies externally (not created internally) to enable testability and decoupling. Use interfaces/contracts for all injected dependencies.
- **Factory Pattern**: Centralize object creation logic, encapsulate implementation details, and work with interfaces rather than concrete types.
- **Interface-Based Design**: Define clear contracts between components using interfaces. Implementations can be swapped as long as they respect the contract. This enables independent evolution and easier testing.

---

## 1F. UI Component and API Documentation

- All components must be documented using a standard template (description, props, usage examples).
- Separate components into page, container, and presentation layers:
  - **Page Components**: Top-level, represent a full page.
  - **Container Components**: Manage state and data flow.
  - **Presentation Components**: Render UI based on props, stateless.
- Use responsive design principles (mobile-first, breakpoints, touch targets).
- All components must be accessible and support keyboard navigation and screen readers.

---

## 1G. Testing Guidelines (Expanded)

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

---

## 1H. General Best Practices

- Avoid cyclic imports and keep files focused and small.
- Maintain single responsibility for each file and component.
- Periodically review and refactor code for maintainability and performance.
- Keep documentation up-to-date with code and feature changes.
- Use the glossary and established conventions for all terminology and naming.

---

> This document is fully standalone and intended as a universal reference for generic application development. No rules from the original generic documentation have been omitted. 