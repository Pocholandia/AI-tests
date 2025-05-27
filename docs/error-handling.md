# Patrones de Manejo de Errores

Este documento define los patrones estandarizados para el manejo de errores en la aplicación preventa-app, asegurando consistencia y robustez en la gestión de excepciones y situaciones de error.

## Principios Generales

1. **Visibilidad al Usuario**: Todos los errores deben ser comunicados al usuario de forma clara y comprensible
2. **Registro detallado**: Todos los errores deben registrarse internamente con información de contexto
3. **Recuperabilidad**: Siempre que sea posible, ofrecer opciones de recuperación
4. **Preservación de datos**: Evitar la pérdida de datos del usuario en caso de error
5. **Categorización**: Clasificar los errores según su tipo y gravedad

## Tipos de Errores

### 1. Errores de Validación

- **Gestión**: Directamente en los formularios con React Hook Form
- **Presentación**: Mensajes específicos junto a cada campo
- **Ejemplo**:

```typescript
const { control, handleSubmit, formState: { errors } } = useForm<IPedido>({
  resolver: yupResolver(pedidoSchema)
});
```

### 2. Errores de Aplicación

- **Gestión**: Try/catch en servicios y componentes contenedores
- **Presentación**: Snackbar/Toast con mensaje descriptivo
- **Ejemplo**:

```typescript
try {
  await pedidoRepository.save(pedido);
  notificationService.success('Pedido guardado correctamente');
} catch (error) {
  notificationService.error('Error al guardar el pedido');
  errorService.log('pedido.save', error);
}
```

### 3. Errores de Red/Sincronización

- **Gestión**: En la capa de sincronización con detección automática
- **Presentación**: Indicador de estado + opción de reintento
- **Ejemplo**:

```typescript
try {
  await syncService.syncPedidos();
  setSyncStatus('synced');
} catch (error) {
  setSyncStatus('error');
  syncErrorHandler.handle(error, {
    retryFn: () => syncService.syncPedidos(),
    entityType: 'pedido'
  });
}
```

## Servicio Central de Notificaciones

```typescript
// services/notificationService.ts
export const notificationService = {
  success: (message: string) => {
    // Mostrar snackbar verde
  },
  error: (message: string) => {
    // Mostrar snackbar rojo
  },
  warning: (message: string) => {
    // Mostrar snackbar amarillo
  },
  info: (message: string) => {
    // Mostrar snackbar azul
  }
};
```

## Servicio de Registro de Errores

```typescript
// services/errorService.ts
export const errorService = {
  log: (context: string, error: unknown) => {
    console.error(`[${context}]`, error);
    // En producción, enviar a servicio de monitoreo si está online
    // Almacenar localmente si está offline
  }
};
```

## Componente de Error Boundary

```typescript
// components/common/ErrorBoundary.tsx
import React, { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(_: Error): State {
    return { hasError: true };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    errorService.log('error-boundary', { error, errorInfo });
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-container">
          <h2>Algo salió mal.</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Intentar de nuevo
          </button>
        </div>
      );
    }

    return this.props.children;
  }
}
```

## Gestión de Errores Offline

```typescript
// services/offlineErrorHandler.ts
export const offlineErrorHandler = {
  // Verificar si el error es por falta de conexión
  isOfflineError: (error: unknown): boolean => {
    // Implementación
    return !navigator.onLine || error.message?.includes('network');
  },
  
  // Manejar errores específicos de falta de conexión
  handle: (error: unknown, options: { retryFn?: () => Promise<any> }) => {
    if (offlineErrorHandler.isOfflineError(error)) {
      notificationService.warning('No hay conexión. Los cambios se guardarán localmente.');
      
      // Si hay una función de reintento, registrarla para ejecutar cuando vuelva la conexión
      if (options.retryFn) {
        syncService.addToRetryQueue(options.retryFn);
      }
      
      return true; // Error manejado
    }
    
    return false; // No es un error de conexión
  }
};
```

## Manejo de Errores en Formularios

### Configuración de Validación con Yup

```typescript
// validation/pedidoValidation.ts
import * as yup from 'yup';

export const pedidoSchema = yup.object({
  IDcliente: yup.string().required('El cliente es obligatorio'),
  lineas: yup.array().of(
    yup.object({
      IDproducto: yup.string().required('El producto es obligatorio'),
      Cantidad: yup.number().required('La cantidad es obligatoria').positive('La cantidad debe ser positiva'),
      Precio: yup.number().required('El precio es obligatorio').positive('El precio debe ser positivo')
    })
  ).min(1, 'El pedido debe tener al menos una línea')
});
```

### Hook Personalizado para Errores de Formulario

```typescript
// hooks/useFormErrorHandler.ts
import { useForm, UseFormProps, FieldValues } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import { AnyObjectSchema } from 'yup';
import { notificationService } from '../services/notificationService';

export function useFormErrorHandler<T extends FieldValues>(
  schema: AnyObjectSchema,
  options?: UseFormProps<T>
) {
  const formMethods = useForm<T>({
    ...options,
    resolver: yupResolver(schema),
    mode: 'onBlur'
  });
  
  const { handleSubmit } = formMethods;
  
  // Wrapper para handleSubmit que maneja errores globales
  const handleSubmitWithErrorHandling = (
    onValid: (data: T) => void | Promise<void>,
    onInvalid?: (errors: any) => void
  ) => {
    return handleSubmit(async (data) => {
      try {
        await onValid(data);
      } catch (error) {
        notificationService.error('Ha ocurrido un error al procesar el formulario');
        errorService.log('form-submission', error);
      }
    }, (errors) => {
      notificationService.error('Por favor, corrija los errores del formulario');
      if (onInvalid) onInvalid(errors);
    });
  };
  
  return {
    ...formMethods,
    handleSubmitWithErrorHandling
  };
}
```

## Patrones de Resolución de Conflictos en Sincronización

```typescript
export enum ConflictResolutionStrategy {
  LOCAL_WINS = 'local_wins',
  SERVER_WINS = 'server_wins',
  MANUAL = 'manual',
}

export interface ConflictResolutionConfig {
  defaultStrategy: ConflictResolutionStrategy;
  entitiesConfig?: {
    [entityName: string]: ConflictResolutionStrategy;
  };
}

// Configuración predeterminada
export const conflictResolutionConfig: ConflictResolutionConfig = {
  defaultStrategy: ConflictResolutionStrategy.LOCAL_WINS,
  entitiesConfig: {
    pedidos: ConflictResolutionStrategy.MANUAL, // Los conflictos en pedidos requieren resolución manual
  }
};
```

## Códigos de Mensajes de Error Estandarizados

```typescript
// constants/errorCodes.ts
export enum ErrorCode {
  // Errores de validación
  VALIDATION_REQUIRED = 'VALIDATION_REQUIRED',
  VALIDATION_FORMAT = 'VALIDATION_FORMAT',
  VALIDATION_MIN = 'VALIDATION_MIN',
  VALIDATION_MAX = 'VALIDATION_MAX',
  
  // Errores de entidad
  ENTITY_NOT_FOUND = 'ENTITY_NOT_FOUND',
  ENTITY_ALREADY_EXISTS = 'ENTITY_ALREADY_EXISTS',
  ENTITY_REFERENCED = 'ENTITY_REFERENCED',
  
  // Errores de sincronización
  SYNC_CONFLICT = 'SYNC_CONFLICT',
  SYNC_FAILED = 'SYNC_FAILED',
  SYNC_NETWORK = 'SYNC_NETWORK',
  
  // Errores generales
  UNEXPECTED = 'UNEXPECTED',
}

export const errorMessages: Record<ErrorCode, string> = {
  [ErrorCode.VALIDATION_REQUIRED]: 'Este campo es obligatorio',
  [ErrorCode.VALIDATION_FORMAT]: 'El formato no es válido',
  [ErrorCode.VALIDATION_MIN]: 'El valor es menor al mínimo permitido',
  [ErrorCode.VALIDATION_MAX]: 'El valor es mayor al máximo permitido',
  
  [ErrorCode.ENTITY_NOT_FOUND]: 'No se encontró el elemento solicitado',
  [ErrorCode.ENTITY_ALREADY_EXISTS]: 'Ya existe un elemento con estos datos',
  [ErrorCode.ENTITY_REFERENCED]: 'No se puede eliminar porque está siendo utilizado',
  
  [ErrorCode.SYNC_CONFLICT]: 'Se ha detectado un conflicto de sincronización',
  [ErrorCode.SYNC_FAILED]: 'No se pudo completar la sincronización',
  [ErrorCode.SYNC_NETWORK]: 'Problema de conexión durante la sincronización',
  
  [ErrorCode.UNEXPECTED]: 'Ha ocurrido un error inesperado',
};
```