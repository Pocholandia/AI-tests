# Preventa Application - Project Documentation

## Overview

This is the main documentation for the Preventa application, a sales order management system with offline capabilities. The application allows users to create and manage orders, customers, products, and pricing structures.

## Documentation Structure

### Core Documentation
- `readme.md`: This file - Main project documentation
- `context-guide.md`: Guidelines for understanding the application context
- `project-tree.md`: Project directory structure and purpose
- `UI.md`: User interface specifications and design guidelines
- `ui-components-specifications.md`: Detailed specifications for UI components with wireframes
- `accessibility-guidelines.md`: Guidelines for ensuring application accessibility
- `configuration-standards.md`: Configuration management standards
- `data-demo-generation.md`: Guidelines for generating demo data
- `data-structure.md`: Database schema and data models
- `data-validation-rules.md`: Detailed validation rules for all entity fields
- `database-indexing.md`: IndexedDB indexing strategy for optimal performance
- `entity-relationship-model.md`: Entity relationship model
- `error-handling.md`: Error management and reporting standards
- `historia.md`: Project history and decision log
- `navigation-flows.md`: Application navigation structure
- `pedidos-filtering.md`: Order filtering specifications
- `project-structure.md`: Project organization and architecture
- `architecture.md`: Application architecture, component relationships, and workflows
- `state-management.md`: Detailed state management patterns using Zustand
- `/registry-system/`: Sistema de registro para formularios y edición de entidades
  - `README.md`: Visión general del sistema de registro
  - `field-registry.md`: Sistema de registro de campos
  - `improvements.md`: Mejoras del sistema para alinearlo con la organización basada en características
  - `integration-guide.md`: Guía para integrar nuevas características con el sistema
- `api-specification.md`: API endpoints for server synchronization
- `synchronization-process.md`: Detailed offline synchronization process
- `developer-setup.md`: Development environment setup guide
- `testing-strategy.md`: Comprehensive testing approach and patterns
- `glossary.md`: Standardized terminology in Spanish and English

### Generic Documentation
Located in `generic/` - Techstack-independent guidelines and standards

### Techstack Documentation
Located in `techstack/` - Technology-specific implementation details

### TO-DO Documentation
Located in `TO-DO/` - Project TODOs

## Techstack

- **Platform**: Web (PWA) for both mobile and desktop
- **Framework**: React with TypeScript
- **UI**: Material UI
- **State Management**: Zustand
- **Storage**: IndexedDB with Dexie.js
- **Offline Support**: Service Worker
- **Build Tool**: Vite

## Getting Started

1. Review the core documentation files listed above
2. Consult the generic documentation for techstack-agnostic guidelines
3. Refer to techstack documentation for implementation details

## Documentation Conventions

- All significant decisions should be logged in `historia.md`
- Follow the standards and guidelines in the respective documentation files
- Keep documentation up-to-date with code changes

## Requisitos funcionales

1. **Formularios de entidades**: Para cada entidad (Cliente, Producto, Tarifa, TarifaLinea, Pedido, PedidoLinea, cualquiera adicional), existe un único formulario para visualizar, insertar y editar entidades. En formato escritorio, se presentan en diálogos modales unificados, con validación y feedback visual (snackbar). En formato móvil, se presentan en pantalla completa.
2. **Pantalla principal**: 
   - Lista de pedidos existentes, a partir de un filtro que permite combinar condiciones de Cliente, rango de fechas (solo parte YYYY-MM-DD), rango de total (min y max), todos combinables. No se muestran pedidos hasta aplicar un filtro. Más documentación en pedidos-filtering.md.
3. **Pedidos**:
   - Se pueden eliminar líneas individuales y pedidos completos (con confirmación visual).
   - Edición de pedidos desde la interfaz.
   - UX de lista y detalle igual que la sección de clientes (moderna, clickable, dialog para detalle/editar/eliminar).
4. **Clientes**:
   - Cada cliente: código, descripción, persona de contacto, teléfono, email, web, tarifa (IDtarifa).
   - Lista y detalle con edición/borrado en diálogo modal.
   - Se puede buscar un cliente, ver datos y lista de pedidos asociados (ordenados por fecha).
5. **Productos**:
   - Lista de productos con nombres.
   - Página de detalle editable/borrable (con confirmación y feedback visual).
   - Cada producto: código, descripción, precio, IVA.
   - En detalle de producto, tabla/grid con todas las TarifasLineas asociadas.
6. **TarifasLineas**:
   - Tabla 'TarifasLineas' con IDtarifa, IDproducto y precio.
   - No puede existir una tarifalinea con el mismo producto para la misma tarifa.
7. **Tarifas**:
   - Tabla 'Tarifas' con código y descripción.
   - Al crear una tarifa, la app navega automáticamente a su detalle para insertar líneas (TarifasLineas) inmediatamente.
   - Tabla/grid de TarifasLineas editable en el detalle de tarifa: cada línea editable en sitio (IDproducto, precio), con botón de borrar por línea (icono papelera roja).
   - Al eliminar una tarifa, se eliminan todas sus TarifasLineas. Si la tarifa está en uso por clientes, se bloquea el borrado y se ofrecen opciones para transferir clientes, dejar tarifa vacía o cancelar.
8. **Datos Demo**: opción en menú overflow para abrir un formulario único que muestra una cuadrícula con cada tabla de datos (en este orden: Productos, Tarifas, Clientes, Pedidos). Cada fila tiene una caja editable para indicar la cantidad de registros a generar, con valores por defecto: 50 (Productos), 9 (Tarifas), 150 (Clientes), 300 (Pedidos). Al confirmar, los datos demo se crean en ese mismo orden para mantener la integridad referencial.
9. **Wipe Datos**: opción en menú overflow para eliminar la totalidad de los clientes

## Pruebas de aceptación
- Crear, editar y eliminar pedidos, clientes y productos usando formularios en modal, con datos correctos y validaciones.
- Al cerrar y volver a abrir la app, los datos persisten.
- Crear clientes y productos y asociarlos correctamente a pedidos.
- Buscar y filtrar pedidos por cliente, producto, fecha, precio y texto libre.
- Buscar clientes y ver su información y lista de pedidos asociados.
- Eliminar líneas de pedido y pedidos completos, con confirmación visual.
- Editar un pedido y verificar que los cambios se reflejan correctamente.
- La interfaz es clara, responsiva y muestra feedback visual ante acciones.
- Validaciones y mensajes de error se muestran cuando corresponde.