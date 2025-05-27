# Estándares de Configuración

Este documento define los estándares de configuración para el proyecto preventa-app, asegurando consistencia en todos los entornos y fases de desarrollo.

## Estructura de Archivos de Configuración

```
/src
  /config
    constants.ts       # Constantes globales de la aplicación
    db.ts              # Configuración de la base de datos IndexedDB/Dexie
    theme.ts           # Tema de Material UI personalizado
    routes.ts          # Definición de rutas de la aplicación
    serviceWorker.ts   # Configuración del Service Worker
    syncConfig.ts      # Configuración de sincronización
    filterConfig.ts    # Configuración de filtros de la aplicación
    appConfig.ts       # Configuración general de la aplicación
```

## Gestión de Variables de Entorno

### Estructura de .env

```
# Entorno
REACT_APP_ENV=development|production|test

# API (para futuras integraciones)
REACT_APP_API_URL=https://api.example.com
REACT_APP_API_TIMEOUT=30000

# Sincronización
REACT_APP_SYNC_INTERVAL=300000
REACT_APP_SYNC_RETRY_MAX=3

# Características
REACT_APP_ENABLE_ANALYTICS=false
REACT_APP_ENABLE_ERROR_REPORTING=false
```

### Acceso Tipado a Variables de Entorno

```typescript
// config/env.ts
export interface AppEnv {
  env: 'development' | 'production' | 'test';
  apiUrl: string;
  apiTimeout: number;
  syncInterval: number;
  syncRetryMax: number;
  enableAnalytics: boolean;
  enableErrorReporting: boolean;
}

export const env: AppEnv = {
  env: process.env.REACT_APP_ENV as 'development' | 'production' | 'test' || 'development',
  apiUrl: process.env.REACT_APP_API_URL || '',
  apiTimeout: parseInt(process.env.REACT_APP_API_TIMEOUT || '30000', 10),
  syncInterval: parseInt(process.env.REACT_APP_SYNC_INTERVAL || '300000', 10),
  syncRetryMax: parseInt(process.env.REACT_APP_SYNC_RETRY_MAX || '3', 10),
  enableAnalytics: process.env.REACT_APP_ENABLE_ANALYTICS === 'true',
  enableErrorReporting: process.env.REACT_APP_ENABLE_ERROR_REPORTING === 'true'
};
```

## Configuración del Tema Material UI

```typescript
// config/theme.ts
import { createTheme, ThemeOptions } from '@mui/material/styles';

// Paleta de colores corporativa
const palette = {
  primary: {
    main: '#1976d2',
    light: '#42a5f5',
    dark: '#1565c0',
    contrastText: '#fff'
  },
  secondary: {
    main: '#388e3c',
    light: '#4caf50',
    dark: '#2e7d32',
    contrastText: '#fff'
  },
  error: {
    main: '#d32f2f',
    light: '#ef5350',
    dark: '#c62828',
    contrastText: '#fff'
  },
  warning: {
    main: '#ed6c02',
    light: '#ff9800',
    dark: '#e65100',
    contrastText: '#fff'
  },
  info: {
    main: '#0288d1',
    light: '#03a9f4',
    dark: '#01579b',
    contrastText: '#fff'
  },
  success: {
    main: '#2e7d32',
    light: '#4caf50',
    dark: '#1b5e20',
    contrastText: '#fff'
  },
  // Estados de sincronización
  syncStatus: {
    synced: '#2e7d32',    // Verde - sincronizado
    pending: '#ed6c02',    // Naranja - pendiente
    error: '#d32f2f',      // Rojo - error
    offline: '#757575'     // Gris - offline
  }
};

// Configuración del tema
const themeOptions: ThemeOptions = {
  palette,
  typography: {
    fontFamily: '"Roboto", "Helvetica", "Arial", sans-serif',
    fontSize: 14,
    h5: {
      fontWeight: 500
    },
    h6: {
      fontWeight: 500
    }
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          textTransform: 'none',
          borderRadius: 8
        }
      }
    },
    MuiPaper: {
      styleOverrides: {
        root: {
          borderRadius: 8
        }
      }
    },
    MuiFab: {
      styleOverrides: {
        root: {
          borderRadius: 16
        }
      }
    },
    MuiTableRow: {
      styleOverrides: {
        root: {
          '&:last-child td': {
            borderBottom: 0
          }
        }
      }
    }
  },
  shape: {
    borderRadius: 8
  }
};

// Tema para modo claro
export const lightTheme = createTheme({
  ...themeOptions,
  palette: {
    ...themeOptions.palette,
    mode: 'light',
    background: {
      default: '#f5f5f5',
      paper: '#ffffff'
    }
  }
});

// Tema para modo oscuro (futuro)
export const darkTheme = createTheme({
  ...themeOptions,
  palette: {
    ...themeOptions.palette,
    mode: 'dark',
    background: {
      default: '#121212',
      paper: '#1e1e1e'
    }
  }
});

// Tema predeterminado
export default lightTheme;
```

## Configuración de Rutas

```typescript
// config/routes.ts
export interface AppRoute {
  path: string;
  title: string;
  icon: string; // Nombre del icono de MUI
  showInNav: boolean;
}

export const ROUTES = {
  PEDIDOS: {
    path: '/pedidos',
    title: 'Pedidos',
    icon: 'ShoppingCart',
    showInNav: true
  },
  CLIENTES: {
    path: '/clientes',
    title: 'Clientes',
    icon: 'People',
    showInNav: true
  },
  PRODUCTOS: {
    path: '/productos',
    title: 'Productos',
    icon: 'Inventory',
    showInNav: true
  },
  TARIFAS: {
    path: '/tarifas',
    title: 'Tarifas',
    icon: 'LocalOffer',
    showInNav: true
  },
  // Rutas adicionales que no aparecen en la navegación principal
  NUEVO_PEDIDO: {
    path: '/pedidos/nuevo',
    title: 'Nuevo Pedido',
    icon: 'Add',
    showInNav: false
  },
  EDITAR_PEDIDO: {
    path: '/pedidos/:id/editar',
    title: 'Editar Pedido',
    icon: 'Edit',
    showInNav: false
  },
  VER_PEDIDO: {
    path: '/pedidos/:id',
    title: 'Detalle de Pedido',
    icon: 'Visibility',
    showInNav: false
  },
  // Rutas similares para clientes, productos y tarifas...
};

// Rutas de navegación principal (para el menú inferior)
export const getMainNavRoutes = () => 
  Object.values(ROUTES).filter(route => route.showInNav);
```

## Configuración de IndexedDB con Dexie

```typescript
// config/db.ts
import Dexie from 'dexie';
import { IPedido, IPedidoLinea, ICliente, IProducto, ITarifa, ITarifaLinea } from '../models/entities';

class AppDatabase extends Dexie {
  pedidos!: Dexie.Table<IPedido, string>;
  pedidosLineas!: Dexie.Table<IPedidoLinea, string>;
  clientes!: Dexie.Table<ICliente, string>;
  productos!: Dexie.Table<IProducto, string>;
  tarifas!: Dexie.Table<ITarifa, string>;
  tarifasLineas!: Dexie.Table<ITarifaLinea, string>;
  versionHistory!: Dexie.Table<any, number>;
  appliedMigrations!: Dexie.Table<any, string>;

  constructor() {
    super('PedidosDatabase');
    
    // Esquema v1
    this.version(1).stores({
      pedidos: 'RecordIdent, IDcliente, FechaHora, SyncStatus',
      pedidosLineas: 'RecordIdent, IDpedido, IDproducto, SyncStatus',
      clientes: 'RecordIdent, Codigo, IDtarifa, SyncStatus',
      productos: 'RecordIdent, Codigo, SyncStatus',
      tarifas: 'RecordIdent, Codigo, SyncStatus',
      tarifasLineas: 'RecordIdent, IDtarifa, IDproducto, SyncStatus',
      versionHistory: '++version, appliedAt',
      appliedMigrations: 'id, appliedAt'
    });
    
    // Inicializar propiedades
    this.pedidos = this.table('pedidos');
    this.pedidosLineas = this.table('pedidosLineas');
    this.clientes = this.table('clientes');
    this.productos = this.table('productos');
    this.tarifas = this.table('tarifas');
    this.tarifasLineas = this.table('tarifasLineas');
    this.versionHistory = this.table('versionHistory');
    this.appliedMigrations = this.table('appliedMigrations');
  }
}

export const db = new AppDatabase();
```

## Configuración del Service Worker

```typescript
// config/serviceWorker.ts
export const serviceWorkerConfig = {
  // Recursos a pre-cachear
  precacheResources: [
    '/',
    '/index.html',
    '/static/js/*.js',
    '/static/css/*.css',
    '/static/media/*.png',
    '/static/media/*.jpg',
    '/static/media/*.svg',
    '/manifest.json'
  ],
  
  // Estrategias de caché por tipo de recurso
  cacheStrategies: {
    // Para recursos estáticos - Cache first, luego red
    static: {
      strategy: 'CacheFirst',
      cacheName: 'static-resources',
      maxAgeSeconds: 30 * 24 * 60 * 60, // 30 días
      maxEntries: 100
    },
    
    // Para API - Network first, con fallback a caché
    api: {
      strategy: 'NetworkFirst',
      cacheName: 'api-resources',
      maxAgeSeconds: 1 * 24 * 60 * 60, // 1 día
      maxEntries: 50
    },
    
    // Para imágenes - Stale while revalidate
    images: {
      strategy: 'StaleWhileRevalidate',
      cacheName: 'image-resources',
      maxAgeSeconds: 7 * 24 * 60 * 60, // 7 días
      maxEntries: 100
    }
  },
  
  // Configuración de sincronización en segundo plano
  backgroundSync: {
    syncName: 'pedidosSync',
    maxRetryTime: 24 * 60 * 60 * 1000 // 24 horas en milisegundos
  }
};

// Funciones de utilidad para registrar/desregistrar el service worker
export const registerServiceWorker = async (): Promise<ServiceWorkerRegistration | null> => {
  if ('serviceWorker' in navigator) {
    try {
      const registration = await navigator.serviceWorker.register('/service-worker.js');
      console.log('Service Worker registrado con éxito:', registration.scope);
      return registration;
    } catch (error) {
      console.error('Error al registrar el Service Worker:', error);
      return null;
    }
  }
  return null;
};

export const unregisterServiceWorker = async (): Promise<boolean> => {
  if ('serviceWorker' in navigator) {
    try {
      const registration = await navigator.serviceWorker.ready;
      const result = await registration.unregister();
      return result;
    } catch (error) {
      console.error('Error al desregistrar el Service Worker:', error);
      return false;
    }
  }
  return false;
};
```

## Configuración de Sincronización

```typescript
// config/syncConfig.ts
import { env } from './env';

export enum SyncStatus {
  SYNCED = 'synced',
  PENDING_CREATE = 'pending_create',
  PENDING_UPDATE = 'pending_update',
  PENDING_DELETE = 'pending_delete',
  ERROR = 'error'
}

export enum ConflictResolutionStrategy {
  LOCAL_WINS = 'local_wins',
  SERVER_WINS = 'server_wins',
  MANUAL = 'manual'
}

export interface SyncConfig {
  // Intervalo de sincronización automática en ms
  syncInterval: number;
  
  // Número máximo de reintentos
  maxRetries: number;
  
  // Estrategia de resolución de conflictos por entidad
  conflictResolution: {
    default: ConflictResolutionStrategy;
    entities: {
      [entityName: string]: ConflictResolutionStrategy;
    };
  };
  
  // Endpoints de API (para futura integración)
  endpoints: {
    [entityName: string]: string;
  };
  
  // Entidades a sincronizar
  entities: string[];
}

export const syncConfig: SyncConfig = {
  syncInterval: env.syncInterval,
  maxRetries: env.syncRetryMax,
  
  conflictResolution: {
    default: ConflictResolutionStrategy.LOCAL_WINS,
    entities: {
      pedidos: ConflictResolutionStrategy.MANUAL,
      // Otras entidades utilizan el valor por defecto
    }
  },
  
  endpoints: {
    pedidos: '/api/pedidos',
    pedidosLineas: '/api/pedidos-lineas',
    clientes: '/api/clientes',
    productos: '/api/productos',
    tarifas: '/api/tarifas',
    tarifasLineas: '/api/tarifas-lineas'
  },
  
  entities: [
    'pedidos',
    'pedidosLineas',
    'clientes',
    'productos',
    'tarifas',
    'tarifasLineas'
  ]
};
```

## Configuración General de la Aplicación

```typescript
// config/appConfig.ts
export const appConfig = {
  // Información de la aplicación
  app: {
    name: 'Preventa App',
    version: '1.0.0',
    buildNumber: '20250509',
    copyrightYear: 2025
  },
  
  // Configuración de paginación
  pagination: {
    defaultPageSize: 20,
    pageSizeOptions: [10, 20, 50, 100]
  },
  
  // Configuración de validación
  validation: {
    minSearchLength: 2,
    maxSearchLength: 50,
    codigoMaxLength: 20,
    descripcionMaxLength: 100,
    observacionesMaxLength: 500,
    telefonoRegex: /^\+?[0-9]{9,15}$/,
    emailRegex: /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/,
    webRegex: /^(https?:\/\/)?([\da-z.-]+)\.([a-z.]{2,6})([/\w .-]*)*\/?$/
  },
  
  // Formatos de fecha y número
  formats: {
    dateFormat: 'DD/MM/YYYY',
    timeFormat: 'HH:mm',
    dateTimeFormat: 'DD/MM/YYYY HH:mm',
    currencyFormat: {
      style: 'currency',
      currency: 'EUR',
      minimumFractionDigits: 2,
      maximumFractionDigits: 2
    }
  },
  
  // Tiempos de espera para notificaciones
  notifications: {
    successDuration: 3000,
    errorDuration: 5000,
    warningDuration: 4000,
    infoDuration: 3000
  },
  
  // Configuración de pedidos
  pedidos: {
    maxLineas: 100,
    cantidadMinima: 1,
    cantidadMaxima: 9999
  }
};
```

## Configuración de Filtros

```typescript
// config/filterConfig.ts
export const filterConfig = {
  // Estados predeterminados de filtros
  defaultEmptyState: true,  // Mostrar estado vacío hasta que se apliquen filtros
  
  // Configuración de persistencia de filtros
  persistFilters: true,     // Persistir selecciones de filtros entre sesiones
  filterStorageKey: 'app_filters',
  
  // Configuraciones específicas por entidad
  entities: {
    pedidos: {
      dateFormat: 'YYYY-MM-DD',  // Formato usado para filtrado por fecha (solo parte fecha)
      availableFilters: ['cliente', 'fechaRango', 'totalRango'],
      combineOperator: 'AND',    // Cómo se combinan múltiples filtros (AND/OR)
      requiredFilters: true      // Si se requiere al menos un filtro
    },
    clientes: {
      availableFilters: ['codigo', 'descripcion', 'tarifa'],
      combineOperator: 'OR',
      requiredFilters: false
    },
    productos: {
      availableFilters: ['codigo', 'descripcion', 'precio'],
      combineOperator: 'OR',
      requiredFilters: false
    }
  },
  
  // Configuración de componentes de filtro
  components: {
    dateRangePicker: {
      maxDaysRange: 365,
      defaultRange: 30
    },
    numericRangePicker: {
      stepSize: 1,
      decimals: 2
    },
    clienteSelector: {
      searchFields: ['Codigo', 'Descripcion'],
      displayField: 'Descripcion',
      valueField: 'RecordIdent'
    }
  }
};
```