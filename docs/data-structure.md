## Inicio

Siempre siguiendo las Indicaciones en data-guidelines.md

## Campos para Sincronización

Además de RecordIdent y RecordVersion definidos en data-guidelines.md, todas las tablas tendrán los siguientes campos adicionales para soportar la sincronización:

- **SyncStatus**: enum ["synced", "pending_create", "pending_update", "pending_delete"] - Estado actual de sincronización
- **LastSyncDate**: timestamp - Última fecha en que el registro se sincronizó con éxito
- **LocalModifiedDate**: timestamp - Fecha de la última modificación local
- **ServerModifiedDate**: timestamp - Fecha de la última modificación en el servidor
- **ConflictResolution**: opcional, enum ["local_wins", "server_wins", "manual"] - Estrategia de resolución para conflictos
Estos Timestamps son UTC en formato ISO 8601

## Estructura de las tablas del proyecto
Todas las tablas tendrán los campos RecordIdent y RecordVersion definidos en data-guidelines.md, por lo que no se especificarán en la siguiente descripción.

1. **Pedidos**:
   - Campos: Numero (autoincrementado), IDcliente, FechaHora (Timestamp UTC en formato ISO 8601, ajustado hasta el minuto)
   - Requeridos: IDcliente, FechaHora, Numero.
   - Los Pedidos tienen una tabla de detalle llamada PedidosLineas.
2. **PedidosLineas**:
   - Campos: IDpedido, IDproducto, cantidad (>0), precio, observaciones.
   - Requeridos: IDpedido, IDproducto, cantidad, precio.
3. **Clientes**:
   - Campos: Código, descripción, persona de contacto, teléfono, email, web, IDtarifa, Inactivo.
   - Requeridos: Código, descripción.
4. **Productos**:
   - Campos: Código, descripción, precio, IVA, Inactivo.
   - Requeridos: todos los campos.
5. **Tarifas**:
   - Campos: Código, descripción, Inactivo.
   - Requeridos: Código, descripción.
6. **TarifasLineas**:
   - Campos: IDtarifa, IDproducto, Precio.
   - Requeridos: IDtarifa, IDproducto, Precio.
   - Restricción: un IDproducto solo puede aparecer una vez por IDtarifa (el par IDtarifa,IDproducto es único).

## Indicaciones adicionales
1. **Pedidos**
   - Eliminar un Pedido elimina todas sus PedidosLineas.
   - La FechaHora de un Pedido se debe generar automáticamente al crear el Pedido y no puede modificarse.
2. **Tarifas**
   - Eliminar una Tarifa elimina todas sus TarifasLineas.
   - No se puede eliminar una Tarifa si está en uso por algún cliente. Si la tarifa está en uso por clientes, el UI ofrecerá la opción de transferir esos clientes a otra tarifa, vaciar el campo IDtarifa del cliente o cancelar.
3. **Productos**:
   - No se puede eliminar un Producto si está en uso por alguna Tarifa. Si el Producto está en uso por tarifas, el UI ofrecerá la opción de quitarlo de todas las tarifas (es decir, eliminar todas las TarifasLineas que lo contengan), o cancelar.
   - No se puede eliminar un Producto si está en uso por algún Pedido. Esto generará un mensaje de error.
4. **Clientes**:
   - No se puede eliminar un Cliente que tenga Pedidos. Si el Cliente está en uso por pedidos, el UI ofrecerá la opción de transferir esos pedidos a otro Cliente, eliminar todos esos pedidos, o cancelar.