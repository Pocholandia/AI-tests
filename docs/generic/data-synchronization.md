# Especificación de datos para tablas que se sincronizan

## Estructura básica de las tablas que se sincronizan
Todos estos campos son obligatorios en cada tabla que se sincroniza:
- **SyncStatus**: enum ["synced", "pending_create", "pending_update", "pending_delete"] - Estado actual de sincronización
- **LastSyncDate**: timestamp - Última fecha en que el registro se sincronizó con éxito
- **LocalModifiedDate**: timestamp - Fecha de la última modificación local
- **ServerModifiedDate**: timestamp - Fecha de la última modificación en el servidor
- **ConflictResolution**: opcional, enum ["local_wins", "server_wins", "manual"] - Estrategia de resolución para conflictos

## Reglas de sincronización
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

## Observaciones
Por defecto, estos campos no son visibles en el UI. Si se desea mostrarlos, se debe indicar explícitamente en la documentación del UI.
