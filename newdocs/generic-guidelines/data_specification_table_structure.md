[⬅ Back to Index](./index.md)

# 5. Data Specification and Table Structure

### Tipos de campo (Field Types)
- "tipoIdentificador", "tipoVersion", "tipoInactivo", "tipoTimestamp", "tipotimestampminutos", "tipotimestampsync", "tipoEmpresa", "tipoEjercicio", "tipoCodigo", "tipoSerie", "tipoNumero", "tipoDescripcion", "tipoNombre", "tipoObservaciones":
  - Cada uno se definirá en el techstack de cada proyecto, con las restricciones de longitud y tipo indicadas en el documento original.

### Campos obligatorios para todas las tablas
- **RecordIdent**: (tipoidentificador) debe ser único dentro de cada tabla. Es la PRIMARY KEY. Solo de uso interno, no se muestra en el UI. Se calcula y asigna automáticamente al crear el registro y es inmutable. Puede haber huecos en la numeración. Se usa en cualquier punto del UI donde se necesite referirse a un registro de manera única (por ejemplo, en la URL de un registro).
- **RecordVersion**: (tipoversion) Empieza en 1 y se incrementa automáticamente cada vez que se modifica un registro. Es un contador de versiones, útil para "Optimistic Locking" y sincronización. Solo de uso interno, no se muestra en el UI.

### Campo "ID" (prohibido)
- No se puede utilizar el campo "ID" en ninguna tabla. Si se desea referirse a un registro de manera única, se debe utilizar el campo "RecordIdent".

### Campo "Inactivo" (opcional)
- Tipo: tipoInactivo. Valor por defecto: el que en el techstack corresponda a "false". Si está presente, indica si el registro está inactivo. Si un registro está inactivo, por defecto no se mostrará en el UI, a menos que se indique lo contrario a través del propio UI.

### Campo "Codigo" (opcional)
- Tipo: tipoCodigo. No se permite que dos registros tengan el mismo Codigo. No puede ser nulo ni "". Si está presente, es un string que identifica de manera única el registro. Por defecto es "". Es el código que ve el usuario en el UI.

### Campos "Serie" y "Numero" (opcionales)
- Solo se aplica cuando una tabla tiene ambos campos. No se permite que dos registros tengan el mismo Serie y Numero. Numero no puede ser nulo ni "". Serie no puede ser nulo pero puede ser "". Si una tabla tiene ambos, el par identifica de manera única el registro. Por defecto serie es "" y numero es "". El par Serie y Numero juega el mismo papel que el campo "Codigo".

### Campos "empresa" y "ejercicio" (opcionales)
- Solo se aplica cuando una tabla tiene ambos campos. empresa: tipoEmpresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario. ejercicio: tipoEjercicio. Requerido. Si es un número puede ser cero (o si es una string puede ser "" o "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino Empresa,Ejercicio,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino Empresa,Ejercicio,Codigo.

### Campo "empresa" (opcional)
- Solo se aplica cuando una tabla tiene el campo empresa. empresa: tipo empresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino empresa,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino empresa,Codigo.

### Campo "Descripcion" (opcional)
- tipo: tipoDescripcion. Si está presente, es un string que describe el registro de forma humanamente comprensible. Cuando se desea mostrar el registro al usuario en el UI, se debe priorizar el campo "Descripcion" sobre todos los demás campos, aunque el campo "Codigo" también se muestre.

### Campo "Observaciones" (opcional)
- tipo: tipoObservaciones. Si está presente, es un texto de uso libre que puede contener cualquier información adicional sobre el registro.

### Relaciones entre entidades
- Se realizan usando los campos `RecordIdent` de la entidad relacionada.
- Un campo que relaciona con otra tabla se llamará "IDtabla" (por ejemplo, "IDejemplo" para relacionar una tabla cualquiera con la tabla "Ejemplos") y será el campo que se usará para la relación.
- tipo: tipoIdentificador
- Ejemplo: En un pedido, el campo `IDcliente` contiene el `RecordIdent` del cliente.
- Ejemplo: En las líneas de un pedido, `IDpedido` e `IDproducto` apuntan a los identificadores del pedido y el producto, respectivamente.
- Ejemplo: En una tabla, `IDadicional` apunta a un identificador de una tabla, aunque por el nombre no se pueda inferir qué tabla es.
- **Campo `ID` no existe**: Ninguna tabla o entidad puede tener un campo `ID`. Cuando algún elemento de UI necesita referirse de manera única a un registro de una tabla, no se utilizará el campo ID de esa tabla (que no existe), sino su campo RecordIdent. 