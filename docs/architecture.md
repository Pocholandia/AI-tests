# Preventa Application Architecture

## Overview

This document provides a comprehensive view of the Preventa application's architecture, including component relationships, data flow, and key workflows.

## Architecture Diagram

```mermaid
graph TD
    subgraph "User Interface"
        UI[UI Components] --> Forms[Entity Forms]
        UI --> Lists[Entity Lists]
        UI --> Filters[Filter Components]
        UI --> Navigation[Navigation]
    end

    subgraph "State Management"
        Zustand[Zustand Stores] --> ClienteStore[Cliente Store]
        Zustand --> ProductoStore[Producto Store]
        Zustand --> TarifaStore[Tarifa Store]
        Zustand --> PedidoStore[Pedido Store]
        Zustand --> SyncStore[Sync Store]
    end

    subgraph "Data Layer"
        Repositories[Repositories] --> ClienteRepo[Cliente Repository]
        Repositories --> ProductoRepo[Producto Repository]
        Repositories --> TarifaRepo[Tarifa Repository]
        Repositories --> PedidoRepo[Pedido Repository]
        Repositories --> SyncRepo[Sync Repository]
        DB[Dexie/IndexedDB] --> Tables[Database Tables]
    end

    subgraph "Business Logic"
        Validators[Validators] --> Rules[Business Rules]
        Rules --> EntityRules[Entity Rules]
        Rules --> SyncRules[Sync Rules]
    end

    UI --> Zustand
    Zustand --> Repositories
    Repositories --> DB
    Repositories --> Validators
```

## Component Structure

```mermaid
graph TD
    App[App Component] --> Routes[Route Components]
    Routes --> EntityPages[Entity Pages]
    EntityPages --> ClientePage[Cliente Page]
    EntityPages --> ProductoPage[Producto Page]
    EntityPages --> TarifaPage[Tarifa Page]
    EntityPages --> PedidoPage[Pedido Page]
    
    subgraph "Generic Components"
        GenericComponents[Generic Components] --> EntityList[Entity List]
        GenericComponents --> EntityView[Entity View]
        GenericComponents --> EntityEdit[Entity Edit]
        GenericComponents --> EntityConfirmDeletion[Entity Confirm Deletion]
    end
    
    subgraph "Registry System"
        RegistrySystem[Registry System] --> FieldRegistry[Field Type Registry]
        RegistrySystem --> EntityDatafetcherRegistry[Entity Registry]
        RegistrySystem --> TypeResolvers[Type Resolvers]
        TypeResolvers --> BaseResolver[Base Resolver]
        TypeResolvers --> EntityResolver[Entity Resolver]
        TypeResolvers --> FeatureResolvers[Feature-Specific Resolvers]
    end
    %% Documentación detallada en /docs/registry-system/
    
    GenericComponents --> RegistrySystem
    
    subgraph "Entity-Specific Components"
        SpecificComponents[Entity-Specific Components] --> ClienteComponents[Cliente Components]
        SpecificComponents --> ProductoComponents[Producto Components]
        SpecificComponents --> TarifaComponents[Tarifa Components]
        SpecificComponents --> PedidoComponents[Pedido Components]
    end
    
    EntityPages --> GenericComponents
    EntityPages --> SpecificComponents
```

## Data Flow

```mermaid
sequenceDiagram
    participant User
    participant UI as UI Component
    participant Store as Zustand Store
    participant Repository
    participant DB as IndexedDB
    
    User->>UI: Interaction (e.g., Create Order)
    UI->>Store: Dispatch Action
    Store->>Repository: Call Repository Method
    Repository->>Repository: Validate Data
    Repository->>DB: Persist Data
    DB-->>Repository: Confirm Success
    Repository-->>Store: Update State
    Store-->>UI: Render Updated UI
    UI-->>User: Display Feedback
```

## Key Workflows

### Order Creation Workflow

```mermaid
sequenceDiagram
    participant User
    participant OrderForm
    participant PedidoStore
    participant ClienteStore
    participant ProductoStore
    participant PedidoRepository
    participant DB
    
    User->>OrderForm: Initialize New Order
    OrderForm->>ClienteStore: Get Client List
    ClienteStore-->>OrderForm: Client Data
    User->>OrderForm: Select Client
    OrderForm->>ProductoStore: Get Products (filtered by client's tarifa)
    ProductoStore-->>OrderForm: Product Data with Prices
    User->>OrderForm: Add Product Lines
    User->>OrderForm: Submit Order
    OrderForm->>PedidoStore: Create Order
    PedidoStore->>PedidoRepository: Save Order + Lines
    PedidoRepository->>DB: Persist to IndexedDB
    DB-->>PedidoRepository: Confirm Success
    PedidoRepository-->>PedidoStore: Update State
    PedidoStore-->>OrderForm: Order Created
    OrderForm-->>User: Success Feedback
```

### Synchronization Workflow

```mermaid
sequenceDiagram
    participant User
    participant UI
    participant SyncStore
    participant EntityStores
    participant SyncRepository
    participant LocalDB
    participant Server
    
    User->>UI: Initiate Sync
    UI->>SyncStore: Start Sync Process
    SyncStore->>EntityStores: Get Pending Changes
    EntityStores-->>SyncStore: Pending Items
    SyncStore->>SyncRepository: Prepare Sync Package
    SyncRepository->>Server: Send Changes
    Server->>Server: Process Changes
    Server-->>SyncRepository: Return Server Changes
    SyncRepository->>SyncRepository: Detect Conflicts
    alt No Conflicts
        SyncRepository->>LocalDB: Update Local Database
        SyncRepository->>SyncStore: Update Sync Status
    else Conflicts Detected
        SyncRepository->>SyncStore: Report Conflicts
        SyncStore->>UI: Show Conflict Resolution UI
        User->>UI: Resolve Conflicts
        UI->>SyncStore: Apply Resolutions
        SyncStore->>SyncRepository: Save Resolutions
        SyncRepository->>LocalDB: Update with Resolutions
    end
    SyncStore-->>UI: Sync Complete
    UI-->>User: Display Sync Results
```

## Technology Stack Integration

```mermaid
graph TD
    React[React + TypeScript] --> MaterialUI[Material UI]
    React --> Zustand[Zustand State]
    React --> ReactRouter[React Router]
    
    Zustand --> Middleware[Zustand Middleware]
    Middleware --> Persist[Persist Middleware]
    Middleware --> Immer[Immer Middleware]
    
    Dexie[Dexie.js] --> IndexedDB[IndexedDB]
    
    ServiceWorker[Service Worker] --> CacheAPI[Cache API]
    ServiceWorker --> OfflineSupport[Offline Support]
    
    Vite[Vite Build Tool] --> BuildProcess[Build Process]
    Vite --> DevServer[Development Server]
```

## Performance Considerations

### Client-Side Rendering
- Components use React.memo for memoization when appropriate
- Lists implement virtualization for handling large datasets
- Heavy operations are performed in web workers when possible

### Data Management
- IndexedDB queries use efficient indexing strategies
- State updates are batched when possible
- Large datasets are paginated with infinite scrolling

### Offline Support
- Critical assets are precached during installation
- Background sync is used for data synchronization
- Optimistic UI updates provide immediate feedback

## Scalability Considerations

### Data Volume
- Application is designed to handle:
  - Up to 10,000 products
  - Up to 5,000 customers
  - Up to a total of 50,000 orders
  - Up to 200,000 order lines

### Performance Targets
- Initial load time: Under 2 seconds on average connections
- Time to interactive: Under 3 seconds
- List rendering: Under 500ms for 1,000 items
- Form submission: Under 300ms processing time

## Security Considerations

### Data Storage
- Sensitive data is not stored in localStorage
- IndexedDB data is encrypted when containing sensitive information

### Authentication
- Token-based authentication with secure storage
- Automatic token refresh mechanism
- Session timeout handling

### API Communication
- All API requests use HTTPS
- CSRF protection implemented
- Rate limiting applied to prevent abuse
