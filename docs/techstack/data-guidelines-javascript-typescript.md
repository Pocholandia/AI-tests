## Inicio

Puntualizaciones a docs/generic/data-guidelines.md

## Tipos Primitivos con Marca (Branded Types)

El sistema utiliza tipos con marca (branded types) para garantizar la seguridad de tipos en tiempo de compilación. Estos tipos son alias de tipos primitivos pero son tratados como tipos distintos por TypeScript.

- **tipoID**: `string` - Identificador interno único
- **tipoVersion**: `string` - Versión de un registro
- **tipoEmpresa**: `number` - Identificador de empresa
- **tipoEjercicio**: `number` - Identificador de ejercicio contable
- **tipoSerie**: `string` - Identificador de serie (ej: serie de facturación)
- **tipoNumero**: `string` - Número de documento
- **tipoCodigo**: `string` - Código identificativo
- **tipoFecha**: `string` - Fecha en formato UTC en formato ISO 8601, sin tiempo
- **tipoFechaHora**: `string` - Fecha y hora completas en formato UTC en formato ISO 8601
- **tipoFechaHoraMinutos**: `string` - Fecha y hora redondeada a minutos en formato UTC en formato ISO 8601

Estos tipos se utilizan para garantizar que no se puedan mezclar accidentalmente identificadores de diferentes dominios, mejorando la seguridad de tipos en la aplicación.

## Tipos de Campos para JavaScript/TypeScript

### tipotimestampsync
- string de timestamp UTC en formato ISO 8601

## Campos para Sincronización

Además de RecordIdent y RecordVersion definidos en data-guidelines.md, todas las tablas tendrán los siguientes campos adicionales para soportar la sincronización:

- **SyncStatus**: enum ["synced", "pending_create", "pending_update", "pending_delete"] - Estado actual de sincronización
- **LastSyncDate**: tipotimestampsync - Última fecha en que el registro se sincronizó con éxito
- **LocalModifiedDate**: tipotimestampsync - Fecha de la última modificación local
- **ServerModifiedDate**: tipotimestampsync - Fecha de la última modificación en el servidor
- **ConflictResolution**: opcional, enum ["local_wins", "server_wins", "manual"] - Estrategia de resolución para conflictos


## Estructura de las tablas del proyecto
Todas las tablas tendrán los campos RecordIdent y RecordVersion como se definen en data-guidelines.md, por lo que no se especificarán en la siguiente descripción.

Que un campo sea *requerido* significa que no puede ser nulo. Sí podrá ser cero o "" si el tipo de campo lo permite.

1. **Pedidos**:
   - Campos: numero (autoincrementado, único), IDcliente, fechaHora (tipotimestampminutos)
   - Requeridos: IDcliente, fechaHora, numero.
   - Los Pedidos tienen una tabla de detalle llamada PedidosLineas.
2. **PedidosLineas**:
   - Campos: IDpedido, IDproducto, cantidad (>0), precio, observaciones.
   - Requeridos: IDpedido, IDproducto, cantidad, precio.
3. **Clientes**:
   - Campos: codigo, descripcion, persona de contacto, telefono, email, web, IDtarifa, inactivo.
   - Requeridos: codigo, descripcion.
4. **Productos**:
   - Campos: codigo, descripcion, precio, iva, inactivo.
   - Requeridos: todos los campos.
5. **Tarifas**:
   - Campos: codigo, descripcion, inactivo.
   - Requeridos: codigo, descripcion.
6. **TarifasLineas**:
   - Campos: IDtarifa, IDproducto, precio.
   - Requeridos: todos los campos.
   - Restricción: un IDproducto solo puede aparecer una vez por IDtarifa (el par IDtarifa,IDproducto es único).

## Indicaciones adicionales
1. **Pedidos**
   - Eliminar un Pedido elimina todas sus PedidosLineas.
   - La fechaHora de un Pedido se debe generar automáticamente al crear el Pedido y no puede modificarse.
2. **Tarifas**
   - Eliminar una Tarifa elimina todas sus TarifasLineas.
   - No se puede eliminar una Tarifa si está en uso por algún cliente. Si la tarifa está en uso por clientes, el UI ofrecerá la opción de transferir esos clientes a otra tarifa, vaciar el campo IDtarifa del cliente o cancelar.
3. **Productos**:
   - No se puede eliminar un Producto si está en uso por alguna Tarifa. Si el Producto está en uso por tarifas, el UI ofrecerá la opción de quitarlo de todas las tarifas (es decir, eliminar todas las TarifasLineas que lo contengan), o cancelar.
   - No se puede eliminar un Producto si está en uso por algún Pedido. Esto generará un mensaje de error.
4. **Clientes**:
   - No se puede eliminar un Cliente que tenga Pedidos. Si el Cliente está en uso por pedidos, el UI ofrecerá la opción de transferir esos pedidos a otro Cliente, eliminar todos esos pedidos, o cancelar.