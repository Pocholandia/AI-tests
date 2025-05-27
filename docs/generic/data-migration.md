# Guía de Migraciones y Versionado de Datos

Este documento establece las directrices para gestionar migraciones de esquema y versionado de datos en la aplicación preventa-app, especialmente importante para aplicaciones con funcionalidad offline.

## Principios de Versionado

1. **Compatibilidad hacia atrás**: Las nuevas versiones deben poder leer datos de versiones anteriores
2. **Migraciones automáticas**: Las actualizaciones de esquema deben ejecutarse automáticamente
3. **Atomicidad**: Las migraciones deben ser atómicas, completándose en su totalidad o no ejecutándose
4. **Registro**: Mantener registro de todas las migraciones ejecutadas
5. **Control de versiones**: Cada versión de la aplicación tiene un número de versión de esquema asociado

## Estructura de Versiones

```typescript
// config/versionConfig.ts
export const DATABASE_VERSION = 1; // Incrementar con cada cambio de esquema

export interface VersionHistory {
  version: number;
  appliedAt: string;
  description: string;
}
```

## Configuración de Base de Datos con Dexie

```typescript
// config/db.ts
import Dexie from 'dexie';
import { DATABASE_VERSION } from './versionConfig';
import { applyMigrations } from './migrations';

class PedidosDatabase extends Dexie {
  pedidos: Dexie.Table<IPedido, string>;
  pedidosLineas: Dexie.Table<IPedidoLinea, string>;
  clientes: Dexie.Table<ICliente, string>;
  productos: Dexie.Table<IProducto, string>;
  tarifas: Dexie.Table<ITarifa, string>;
  tarifasLineas: Dexie.Table<ITarifaLinea, string>;
  versionHistory: Dexie.Table<VersionHistory, number>;

  constructor() {
    super('PedidosDatabase');
    
    // Definición inicial de esquema v1
    this.version(1).stores({
      pedidos: 'RecordIdent, IDcliente, FechaHora, SyncStatus',
      pedidosLineas: 'RecordIdent, IDpedido, IDproducto, SyncStatus',
      clientes: 'RecordIdent, Codigo, IDtarifa, SyncStatus',
      productos: 'RecordIdent, Codigo, SyncStatus',
      tarifas: 'RecordIdent, Codigo, SyncStatus',
      tarifasLineas: 'RecordIdent, IDtarifa, IDproducto, SyncStatus',
      versionHistory: '++version, appliedAt'
    });
    
    // Esquema v2 (ejemplo de migración)
    this.version(2).stores({
      pedidos: 'RecordIdent, IDcliente, FechaHora, SyncStatus, NuevoCampo',
      // ... resto de tablas sin cambios
    }).upgrade(tx => {
      // Lógica de migración para v2
      return tx.pedidos.toCollection().modify(pedido => {
        pedido.NuevoCampo = '';
      });
    });
    
    // Asegurarnos de que definimos todos los esquemas hasta la versión actual
    if (DATABASE_VERSION > this.verno) {
      console.error(`Error de configuración: DATABASE_VERSION (${DATABASE_VERSION}) es mayor que la versión de esquema definida (${this.verno})`);
    }
    
    // Inicializar propiedades
    this.pedidos = this.table('pedidos');
    this.pedidosLineas = this.table('pedidosLineas');
    this.clientes = this.table('clientes');
    this.productos = this.table('productos');
    this.tarifas = this.table('tarifas');
    this.tarifasLineas = this.table('tarifasLineas');
    this.versionHistory = this.table('versionHistory');
  }
}

export const db = new PedidosDatabase();

// Registrar la migración después de que la base de datos esté abierta
db.on('ready', async () => {
  try {
    // Registrar la versión actual en el historial si no existe
    const existingVersion = await db.versionHistory
      .where('version')
      .equals(db.verno)
      .first();
      
    if (!existingVersion) {
      await db.versionHistory.add({
        version: db.verno,
        appliedAt: new Date().toISOString(),
        description: `Actualización automática a versión ${db.verno}`
      });
    }
    
    // Aplicar migraciones adicionales si son necesarias
    await applyMigrations(db);
  } catch (error) {
    console.error('Error al registrar versión de base de datos:', error);
  }
});
```

## Sistema de Migraciones

```typescript
// config/migrations.ts
import { db } from './db';

interface Migration {
  id: string;
  description: string;
  requiresOnline?: boolean;
  apply: (db: Dexie) => Promise<void>;
}

// Registro de migraciones adicionales (no relacionadas con el esquema)
const migrations: Migration[] = [
  {
    id: 'update-producto-iva-2025-05',
    description: 'Actualizar IVA de productos según nueva normativa',
    apply: async (db) => {
      await db.productos.where('IVA').equals(10).modify({
        IVA: 11
      });
    }
  },
  {
    id: 'sync-status-migration',
    description: 'Inicializar estado de sincronización para registros existentes',
    apply: async (db) => {
      // Funciones para actualizar SyncStatus en cada tabla
      const updateTable = async (table: Dexie.Table) => {
        await table.where('SyncStatus').equals(undefined).modify({
          SyncStatus: 'synced',
          LastSyncDate: new Date().toISOString()
        });
      };
      
      // Aplicar a todas las tablas
      await Promise.all([
        updateTable(db.pedidos),
        updateTable(db.pedidosLineas),
        updateTable(db.clientes),
        updateTable(db.productos),
        updateTable(db.tarifas),
        updateTable(db.tarifasLineas)
      ]);
    }
  }
];

// Tabla para rastrear migraciones aplicadas
interface AppliedMigration {
  id: string;
  appliedAt: string;
}

// Función para aplicar migraciones pendientes
export async function applyMigrations(db: Dexie) {
  try {
    // Crear tabla de migraciones si no existe
    if (!db.tables.some(t => t.name === 'appliedMigrations')) {
      db.version(db.verno + 1).stores({
        appliedMigrations: 'id, appliedAt'
      });
      await db.open();
    }
    
    const appliedMigrationsTable = db.table<AppliedMigration, string>('appliedMigrations');
    
    // Obtener migraciones ya aplicadas
    const appliedMigrationIds = await appliedMigrationsTable.toCollection().primaryKeys();
    
    // Filtrar migraciones pendientes
    const pendingMigrations = migrations.filter(m => !appliedMigrationIds.includes(m.id));
    
    // Separar migraciones según requerimiento de conexión
    const offlineMigrations = pendingMigrations.filter(m => !m.requiresOnline);
    const onlineMigrations = pendingMigrations.filter(m => m.requiresOnline);
    
    // Aplicar migraciones offline inmediatamente
    for (const migration of offlineMigrations) {
      try {
        await db.transaction('rw', db.tables, async () => {
          await migration.apply(db);
          await appliedMigrationsTable.add({
            id: migration.id,
            appliedAt: new Date().toISOString()
          });
        });
        console.log(`Migración aplicada: ${migration.id} - ${migration.description}`);
      } catch (error) {
        console.error(`Error al aplicar migración ${migration.id}:`, error);
      }
    }
    
    // Las migraciones online se manejan de forma diferente
    if (navigator.onLine && onlineMigrations.length > 0) {
      // Aplicar migraciones online
      for (const migration of onlineMigrations) {
        try {
          await migration.apply(db);
          await appliedMigrationsTable.add({
            id: migration.id,
            appliedAt: new Date().toISOString()
          });
          console.log(`Migración online aplicada: ${migration.id}`);
        } catch (error) {
          console.error(`Error al aplicar migración online ${migration.id}:`, error);
        }
      }
    } else if (onlineMigrations.length > 0) {
      console.log(`${onlineMigrations.length} migraciones online pendientes de aplicar cuando haya conexión`);
    }
    
    return true;
  } catch (error) {
    console.error('Error al aplicar migraciones:', error);
    return false;
  }
}
```

## Estrategias para Manejar Cambios de Esquema en Producción

### 1. Actualización Progresiva de Esquema

Cuando se introduce un cambio de esquema importante:

1. **Fase 1 - Compatibilidad dual**:
   - La aplicación lee tanto el formato antiguo como el nuevo
   - Al guardar, utiliza el nuevo formato
   - Se incluye migración para convertir datos antiguos a nuevo formato

2. **Fase 2 - Consolidación** (versión siguiente):
   - Se elimina el soporte para el formato antiguo
   - Se completa la migración de datos restantes

### 2. Sincronización y Cambios de Esquema

Para manejar sincronización durante cambios de esquema:

1. El servidor debe soportar tanto el formato viejo como el nuevo durante un período de transición
2. Los clientes incluyen información de versión en las solicitudes de sincronización
3. El servidor adapta su respuesta según la versión del cliente

## Manejo de Versiones de Aplicación

```typescript
// config/appVersion.ts
export interface AppVersion {
  major: number;  // Cambios incompatibles en API o esquema
  minor: number;  // Nuevas funcionalidades compatibles con versiones anteriores
  patch: number;  // Correcciones de errores sin cambios en API/esquema
  build: string;  // Identificador de construcción (opcional)
}

export const APP_VERSION: AppVersion = {
  major: 1,
  minor: 0,
  patch: 0,
  build: '20250509'
};

export const APP_VERSION_STRING = `${APP_VERSION.major}.${APP_VERSION.minor}.${APP_VERSION.patch}`;

// Verificar compatibilidad entre versiones
export function isVersionCompatible(clientVersion: AppVersion, minCompatible: AppVersion): boolean {
  if (clientVersion.major !== minCompatible.major) {
    return false;
  }
  
  if (clientVersion.major === minCompatible.major && clientVersion.minor < minCompatible.minor) {
    return false;
  }
  
  return true;
}
```

## Actualización de Datos al Iniciar la Aplicación

```typescript
// services/dataUpgradeService.ts
import { db } from '../config/db';
import { applyMigrations } from '../config/migrations';
import { APP_VERSION, APP_VERSION_STRING } from '../config/appVersion';

export const dataUpgradeService = {
  async checkAndUpgradeData(): Promise<boolean> {
    try {
      // Verificar versión anterior almacenada
      const previousVersion = localStorage.getItem('app_version');
      const isFirstRun = !previousVersion;
      
      // Registrar primera ejecución
      if (isFirstRun) {
        await this.handleFirstRun();
        return true;
      }
      
      // Si la versión es diferente, ejecutar migraciones
      if (previousVersion !== APP_VERSION_STRING) {
        await this.handleVersionChange(previousVersion);
      }
      
      // Actualizar versión almacenada
      localStorage.setItem('app_version', APP_VERSION_STRING);
      
      return true;
    } catch (error) {
      console.error('Error durante la actualización de datos:', error);
      return false;
    }
  },
  
  async handleFirstRun(): Promise<void> {
    // Inicialización para primera ejecución
    console.log('Primera ejecución de la aplicación');
    
    // Registrar versión
    localStorage.setItem('app_version', APP_VERSION_STRING);
    
    // Generar datos iniciales si se requiere
  },
  
  async handleVersionChange(previousVersion: string): Promise<void> {
    console.log(`Actualización de versión: ${previousVersion} -> ${APP_VERSION_STRING}`);
    
    // Aplicar migraciones pendientes
    await applyMigrations(db);
    
    // Lógica específica según versión anterior
    if (previousVersion === '1.0.0') {
      // Migración específica de 1.0.0 a versión actual
    }
  }
};
```

## Estrategias para Cambios de Esquema que Afectan la Sincronización

### Ejemplo: Transformadores de Datos para Compatibilidad

```typescript
// sync/dataTransformers.ts
export interface DataTransformer {
  clientToServer: (data: any) => any;
  serverToClient: (data: any) => any;
}

// Transformador para entidad Pedido
export const pedidoTransformer: DataTransformer = {
  clientToServer: (pedido) => {
    // Transformar formato cliente -> servidor
    const serverPedido = {
      ...pedido,
      // Transformaciones específicas
      fecha: pedido.FechaHora.split('T')[0], // Solo fecha para servidor antiguo
      // ...
    };
    
    return serverPedido;
  },
  
  serverToClient: (serverPedido) => {
    // Transformar formato servidor -> cliente
    const clientPedido = {
      ...serverPedido,
      // Transformaciones específicas
      FechaHora: serverPedido.fecha ? `${serverPedido.fecha}T00:00:00Z` : new Date().toISOString(),
      // ...
    };
    
    return clientPedido;
  }
};

// Registro de transformadores
export const transformers: Record<string, DataTransformer> = {
  pedidos: pedidoTransformer,
  // Otros transformadores
};
```

## Verificaciones de Integridad de Datos

```typescript
// services/dataIntegrityService.ts
import { db } from '../config/db';

export const dataIntegrityService = {
  async verifyAndRepair(): Promise<{verified: boolean, issues: string[]}> {
    const issues: string[] = [];
    
    try {
      // 1. Verificar referencias huérfanas en PedidosLineas
      const orphanedLines = await db.pedidosLineas
        .where('IDpedido')
        .filter(async IDpedido => {
          const pedido = await db.pedidos.get(IDpedido);
          return !pedido;
        })
        .toArray();
      
      if (orphanedLines.length > 0) {
        issues.push(`Se encontraron ${orphanedLines.length} líneas de pedido huérfanas`);
        
        // Eliminar líneas huérfanas
        await db.pedidosLineas.bulkDelete(orphanedLines.map(line => line.RecordIdent));
      }
      
      // 2. Verificar otros problemas de integridad referencial
      // ...
      
      return {
        verified: true,
        issues
      };
    } catch (error) {
      console.error('Error en verificación de integridad:', error);
      issues.push('Error durante verificación de integridad: ' + (error as Error).message);
      
      return {
        verified: false,
        issues
      };
    }
  }
};
```