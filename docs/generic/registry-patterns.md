# Patrones de Registro y Diseño

Este documento describe los patrones de diseño generales implementados en el Sistema de Registro, independientemente de la tecnología o framework específico utilizado.

## Patrón de Registro (Registry Pattern)

El Patrón de Registro proporciona un lugar centralizado donde los componentes pueden registrarse y ser descubiertos en tiempo de ejecución.

### Características

- **Centralización**: Punto único para registro y descubrimiento
- **Desacoplamiento**: Los componentes no necesitan conocerse directamente
- **Extensibilidad**: Nuevos componentes pueden añadirse sin modificar código existente
- **Dinamismo**: Permite la composición dinámica de componentes en tiempo de ejecución

### Estructura Básica

```
+----------------+      +----------------+
|                |      |                |
|   Component A  +----->+    Registry    +<-----+
|                |      |                |      |
+----------------+      +----------------+      |
                                               |
+----------------+                             |
|                |                             |
|   Component B  +-----------------------------+
|                |
+----------------+
```

### Implementación Genérica

```typescript
class Registry<T> {
  private items: Map<string, T> = new Map();

  register(key: string, item: T): void {
    this.items.set(key, item);
  }

  get(key: string): T | undefined {
    return this.items.get(key);
  }

  getAll(): T[] {
    return Array.from(this.items.values());
  }

  getKeys(): string[] {
    return Array.from(this.items.keys());
  }
}
```

## Patrón Adaptador (Adapter Pattern)

El Patrón Adaptador permite que interfaces incompatibles trabajen juntas, envolviendo una clase existente con una nueva interfaz.

### Características

- **Compatibilidad**: Permite la interoperabilidad entre interfaces incompatibles
- **Reutilización**: Permite reutilizar código existente con nuevas interfaces
- **Separación**: Separa la lógica de negocio de la lógica de presentación
- **Mantenibilidad**: Facilita el cambio de implementaciones subyacentes

### Estructura Básica

```
+----------------+      +----------------+      +----------------+
|                |      |                |      |                |
|    Client      +----->+    Adapter     +----->+    Adaptee     |
|                |      |                |      |                |
+----------------+      +----------------+      +----------------+
```

### Implementación Genérica

```typescript
interface Target {
  request(): void;
}

class Adaptee {
  specificRequest(): void {
    // Implementación específica
  }
}

class Adapter implements Target {
  private adaptee: Adaptee;

  constructor(adaptee: Adaptee) {
    this.adaptee = adaptee;
  }

  request(): void {
    // Adaptar la llamada a la interfaz específica
    this.adaptee.specificRequest();
  }
}
```

## Patrón de Inyección de Dependencias (Dependency Injection)

El Patrón de Inyección de Dependencias invierte el control de la creación de dependencias, proporcionándolas desde el exterior.

### Características

- **Inversión de Control**: Las dependencias se proporcionan en lugar de crearlas internamente
- **Testabilidad**: Facilita la creación de pruebas unitarias con mocks
- **Desacoplamiento**: Reduce el acoplamiento entre componentes
- **Configurabilidad**: Permite cambiar implementaciones sin modificar código

### Estructura Básica

```
+----------------+       +----------------+
|                |       |                |
|    Injector    +------>+    Client      |
|                |       |                |
+----------------+       +-------+--------+
                                 |
                                 v
                         +-------+--------+
                         |                |
                         |   Dependency   |
                         |   Interface    |
                         |                |
                         +----------------+
```

### Implementación Genérica

```typescript
interface Service {
  execute(): void;
}

class ServiceImplementation implements Service {
  execute(): void {
    // Implementación específica
  }
}

class Client {
  private service: Service;

  constructor(service: Service) {
    this.service = service;
  }

  doSomething(): void {
    this.service.execute();
  }
}

// Uso con inyección de dependencias
const service = new ServiceImplementation();
const client = new Client(service);
```

## Patrón de Fábrica (Factory Pattern)

El Patrón de Fábrica centraliza la creación de objetos, encapsulando la lógica de creación.

### Características

- **Centralización**: Centraliza la lógica de creación de objetos
- **Encapsulación**: Oculta los detalles de implementación
- **Abstracción**: Trabaja con interfaces en lugar de implementaciones concretas
- **Extensibilidad**: Facilita la adición de nuevos tipos de objetos

### Estructura Básica

```
+----------------+      +----------------+
|                |      |                |
|    Client      +----->+    Factory     |
|                |      |                |
+----------------+      +-------+--------+
                                |
                        +-------+--------+
                        |                |
                +-------v------+  +------v-------+
                |               |  |              |
                |  Product A    |  |  Product B   |
                |               |  |              |
                +---------------+  +--------------+
```

### Implementación Genérica

```typescript
interface Product {
  operation(): string;
}

class ConcreteProductA implements Product {
  operation(): string {
    return 'Result of ConcreteProductA';
  }
}

class ConcreteProductB implements Product {
  operation(): string {
    return 'Result of ConcreteProductB';
  }
}

class Factory {
  createProduct(type: string): Product {
    switch (type) {
      case 'A':
        return new ConcreteProductA();
      case 'B':
        return new ConcreteProductB();
      default:
        throw new Error(`Product type ${type} not supported`);
    }
  }
}
```

## Patrón de Diseño basado en Interfaces (Interface-Based Design)

El Diseño basado en Interfaces utiliza interfaces para definir contratos entre componentes, permitiendo implementaciones intercambiables.

### Características

- **Contratos claros**: Interfaces definen contratos explícitos entre componentes
- **Sustituibilidad**: Implementaciones pueden ser intercambiadas si respetan el contrato
- **Evolución independiente**: Los componentes pueden evolucionar independientemente
- **Testabilidad**: Facilita la creación de mocks para pruebas

### Estructura Básica

```
+----------------+      +----------------+
|                |      |                |
|    Client      +----->+   Interface    +<---+
|                |      |                |    |
+----------------+      +----------------+    |
                                             |
                        +----------------+    |
                        |                |    |
                        | Implementation +----+
                        |                |
                        +----------------+
```

### Implementación Genérica

```typescript
interface DataProvider {
  getData(): Promise<any[]>;
  saveData(data: any): Promise<void>;
}

class ApiDataProvider implements DataProvider {
  async getData(): Promise<any[]> {
    // Implementación con API
    return fetch('/api/data').then(response => response.json());
  }

  async saveData(data: any): Promise<void> {
    // Implementación con API
    await fetch('/api/data', {
      method: 'POST',
      body: JSON.stringify(data)
    });
  }
}

class LocalStorageDataProvider implements DataProvider {
  async getData(): Promise<any[]> {
    // Implementación con LocalStorage
    return JSON.parse(localStorage.getItem('data') || '[]');
  }

  async saveData(data: any): Promise<void> {
    // Implementación con LocalStorage
    localStorage.setItem('data', JSON.stringify(data));
  }
}

// El cliente solo depende de la interfaz
class DataManager {
  private provider: DataProvider;

  constructor(provider: DataProvider) {
    this.provider = provider;
  }

  async processData(): Promise<void> {
    const data = await this.provider.getData();
    // Procesar datos...
    await this.provider.saveData(data);
  }
}
```

## Aplicación en el Sistema de Registro

El Sistema de Registro de Preventa App combina estos patrones para crear una arquitectura flexible y extensible:

1. **Registry Pattern**: Utilizado para el registro centralizado de tipos de campo y entidades
2. **Adapter Pattern**: Implementado para adaptar diferentes representaciones de campos
3. **Dependency Injection**: Aplicado para proporcionar implementaciones de registro sin acoplamientos directos
4. **Factory Pattern**: Utilizado para crear instancias de adaptadores y resolvedores
5. **Interface-Based Design**: Implementado con interfaces claras para todos los componentes

Esta combinación de patrones permite una arquitectura altamente desacoplada, mantenible y extensible, que se alinea perfectamente con el patrón de organización basada en características adoptado en la aplicación.
