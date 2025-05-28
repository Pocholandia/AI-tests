# Generic Development Guidelines

This document consolidates universal, techstack-independent rules, standards, and best practices for application development. It is intended as a foundation for AI-driven or human development, regardless of programming language or framework.

---

## 1. Core Principles

- **Maintainability & Extensibility**: Always favor designs that are easy to maintain and extend, even if they require more detailed planning or verbose code.
- **Separation of Concerns**: Clearly separate data access, business logic, and presentation/UI layers.
- **Single Responsibility**: Each component, module, or class should have a single, well-defined responsibility.
- **Pluggability & Extensibility**: Architect systems so that new components can be registered, discovered, and used dynamically.
- **Type Safety & Contracts**: Define clear interfaces or contracts for components and data structures.
- **Centralized Configuration**: Use a single source of truth for configuration and registration.
- **Consistent Naming & Structure**: Apply unified naming conventions and structural patterns across the codebase.

---

## 2. Coding Standards & Best Practices

- **SOLID Principles**: Apply SOLID principles to promote maintainable and extensible code.
- **Testing Coverage**: All functionality must be accompanied by unit and integration tests covering both pure logic and module interactions.
- **Layered Architecture**:
  - **Data Layer**: Encapsulate all data access operations in a dedicated layer, using repository or similar patterns with swappable interfaces.
  - **Business Logic Layer**: Extract validations, transformations, and business rules into pure functions or modules, independent of UI.
  - **Presentation/UI Layer**: Focus UI components on rendering and event routing, delegating logic to lower layers.
- **Component/Module Decomposition**: Design units with a single responsibility and limit their size for clarity and testability.
- **State Management**: Use a single, consistent state management solution. Organize state by domain/feature and provide selectors/getters to avoid unnecessary updates.
- **Dependency Injection**: Inject dependencies (interfaces, factories, or context) to enable easy mocking and testing. Avoid direct coupling to infrastructure.
- **Event Standardization**: Define unified conventions for event names and handler structures. Reuse event factories for common behaviors.
- **Form Abstraction**: Centralize form validation and change handling in reusable utilities. Avoid duplicating form logic.
- **User Feedback System**: Implement a global notification or messaging mechanism, with a unified API for feedback, independent of UI framework.
- **External API Integration**: Use adapter/wrapper patterns to decouple external API calls from business logic. Consistently manage loading, success, and error states.
- **Offline Support & Synchronization**:
  - Design for local-first operation: the app must be fully functional offline.
  - Track all local changes for later synchronization.
  - Implement a queue and reconciliation mechanism for server sync, with conflict detection and resolution.
  - Provide sync status indicators and support both manual and automatic sync.

---

## 3. Data Management & Table Structure

- **Universal Identifiers**: Every table/entity must have a unique identifier field (e.g., `RecordIdent`), which is immutable and used for all references.
- **Versioning**: Every record must have a version field (e.g., `RecordVersion`) incremented on each modification, supporting optimistic locking and sync.
- **No Generic 'ID' Field**: Do not use a generic `ID` field; always use explicit, unique identifiers.
- **Optional Fields**:
  - `Inactivo`: Boolean-like field to mark records as inactive (default: false). Inactive records are hidden by default.
  - `Codigo`: Unique, non-null string identifier for user-facing identification.
  - `Serie` and `Numero`: If both exist, their pair uniquely identifies the record for the user.
  - `Descripcion`: Human-readable description, prioritized in UI.
  - `Observaciones`: Free-text field for additional info.
  - `empresa` and `ejercicio`: Used for multi-company or multi-period data; if present, they participate in unique identification.
- **Entity Relationships**: Use explicit foreign keys referencing unique identifiers (e.g., `IDcliente` refers to `RecordIdent` of the related entity).

---

## 4. Data Synchronization & Migration

- **Synchronization Fields** (required for sync-enabled tables):
  - `SyncStatus`: Enum indicating sync state (e.g., synced, pending_create, pending_update, pending_delete).
  - `LastSyncDate`, `LocalModifiedDate`, `ServerModifiedDate`: Timestamps for tracking changes and sync events.
  - `ConflictResolution`: (Optional) Enum for conflict strategy (e.g., local_wins, server_wins, manual).
- **Synchronization Rules**:
  - Local-first: All data is stored and operated on locally first.
  - Universal uniqueness: Identifiers must be unique even when generated offline (e.g., UUIDs).
  - Change tracking: All offline changes must be tracked for later sync.
  - Conflict detection: Use versions and timestamps to detect conflicts.
  - Automatic and manual conflict resolution.
  - Automatic, atomic migrations with full logging and version control.
- **Migration Principles**:
  - Backward compatibility: New versions must read old data.
  - Automatic, atomic migrations.
  - Maintain a log of all migrations and schema versions.

---

## 5. Registry & Design Patterns

- **Registry Pattern**: Centralize registration and discovery of components, types, or features. Enables decoupling and dynamic composition.
- **Adapter Pattern**: Use adapters to allow incompatible interfaces to work together, decoupling business logic from implementation details.
- **Dependency Injection**: Provide dependencies externally to enable testability and flexibility.
- **Factory Pattern**: Centralize object creation, abstracting implementation details and enabling extensibility.
- **Interface-Based Design**: Define clear contracts for components, allowing interchangeable implementations and independent evolution.
- **Pluggable Editor Registry**:
  - Allow arbitrary editor components to be registered and used by key.
  - Support namespacing, override, unregister, and discovery of editors.
  - Always provide a fallback editor.

---

## 6. Testing Standards

- **Test Placement**: Place tests alongside the code they test, using clear naming conventions.
- **Test Types**:
  - Unit tests for all pure logic and utility functions.
  - Component tests for UI and container logic.
  - Integration tests for user flows, sync, and persistence.
- **Test Patterns**: Use Arrange-Act-Assert (AAA) structure for clarity.
- **Mocks & Isolation**: Mock dependencies and isolate tests to avoid cross-test interference.
- **Coverage Requirements**: Set and enforce minimum coverage thresholds for different code areas (e.g., rules, repositories, stores, critical components, utilities).
- **Test Data**: Use realistic test data and factories/fixtures for consistency.
- **Automation**: Run tests automatically before commits and in CI/CD pipelines. Reject code that fails tests.

---

## 7. Documentation & Maintenance

- **Documentation**: Keep all documentation up-to-date with code changes. Reference generic standards in code reviews and propose improvements as needed.
- **Deviation Logging**: Document any deviations from these standards in project-specific documentation.
- **Periodic Review**: Regularly review and update guidelines to reflect best practices.

---

## 8. Best Practices Summary

- Register and document all pluggable components at startup.
- Use clear, namespaced keys for organization.
- Always provide fallbacks for robustness.
- Favor explicit contracts and interfaces for all components and data.
- Design for testability, maintainability, and extensibility from the start.
- Prioritize user feedback, error handling, and offline support in all designs.

---

This document is fully standalone and intended as a universal reference for generic application development. 