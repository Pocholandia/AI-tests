# Especificación de datos

## Estructura básica de las tablas

### Tipo de campo "tipoIdentificador"
- Este tipo de campo se definirá en el techstack de cada proyecto.

### Tipo de campo "tipoVersion"
- Este tipo de campo se definirá en el techstack de cada proyecto, y siempre será un número

### Tipo de campo "tipoInactivo"
- Este tipo de campo se definirá en el techstack de cada proyecto, y se usará como si fuese un booleano, aunque internamente será lo que defina el techstack

### Tipo de campo "tipoTimestamp"
- Este tipo de campo se definirá en el techstack de cada proyecto

### Tipo de campo "tipotimestampminutos"
- Este tipo de campo se definirá en el techstack de cada proyecto

### Tipo de campo "tipotimestampsync"
- Este tipo de campo se definirá en el techstack de cada proyecto

### Tipo de campo "tipoEmpresa"
- Este tipo de campo se definirá en el techstack de cada proyecto

### Tipo de campo "tipoEjercicio"
- Este tipo de campo se definirá en el techstack de cada proyecto

### Tipo de campo "tipoCodigo"
- Este tipo de campo se definirá en el techstack de cada proyecto. Será un string de longitud máxima de 10 caracteres, a menos que la implementación concreta indique otro valor.

### Tipo de campo "tipoSerie"
- Este tipo de campo se definirá en el techstack de cada proyecto. Será un string de longitud máxima de 10 caracteres, a menos que la implementación concreta indique otro valor.

### Tipo de campo "tipoNumero"
- Este tipo de campo se definirá en el techstack de cada proyecto. Será un string de longitud máxima de 10 caracteres, a menos que la implementación concreta indique otro valor.

### Tipo de campo "tipoDescripcion"
- Este tipo de campo se definirá en el techstack de cada proyecto. Será un string de longitud máxima de 100 caracteres, a menos que la implementación concreta indique otro valor.

### Tipo de campo "tipoNombre"
- Este tipo de campo se definirá en el techstack de cada proyecto. Será un string de longitud máxima de 0 caracteres, a menos que la implementación concreta indique otro valor.

### Tipo de campo "tipoObservaciones"
- Este tipo de campo se definirá en el techstack de cada proyecto, que deberá definirlo como un texto de longitud ilimitada (Memo, CBLOB...).

### Campos "RecordIdent" y "RecordVersion" (obligatorios para todas las tablas)
- **RecordIdent**: (tipoidentificador) tiene que ser único dentro de cada tabla. En nomenclatura SQL, diríamos que "RecordIdent" es la PRIMARY KEY. Este campo es solo de uso interno, y  habitualmente no se mostrará en el UI. El valor se calcula y asigna automáticamente al crear el registro, y será inmutable. No importa si hay huecos en la numeración: importa que sea único, y que sea fácil y rápido de calcular. Este campo se utilizará en cualquier punto del UI en el que se necesite referirse a un registro de manera unica. Por ejemplo, en la URL de un registro. 
- **RecordVersion**: (tipoversion) Empieza en 1 y se incrementa automáticamente cada vez que se modifica un registro. Es un contador de versiones, útil para "Optimistic Locking" y también para la sincronización. Este campo es solo de uso interno, y habitualmente no se mostrará en el UI

### Campo "ID" (prohibido para todas las tablas)
- No se puede utilizar el campo "ID" en ninguna tabla. Si se desea referirse a un registro de manera unica, se debe utilizar el campo "RecordIdent".

### Campo "Inactivo" (opcional)
- Tipo: tipoInactivo
- Valor por defecto: El que en el techstack corresponda a "false"
- Cada tabla puede tener este campo o no.
- Si la tabla tiene este campo,indica si el registro está inactivo o no.
- Si un registro está inactivo, por defecto no se mostrará en el UI, a menos que se indique lo contrario a través del propio UI.

### Campo "Codigo" (opcional)
- Tipo: tipoCodigo
- Cada tabla puede tener este campo o no.
- No se permite que dos registros tengan el mismo Codigo.
- No puede ser nulo ni ""
- Si la tabla tiene este campo, es un string que identifica de manera única el registro. Por defecto es "".
- Este código es el que ve el usuario en el UI.

### Campo "Serie" y "Numero" (opcionales)
Este apartado solo se aplica cuando una tabla tiene ambos campos Serie y Numero. Si no lo tiene, no se debe usar.
- Serie: tipoSerie
- Numero: tipoNumero 
- No se permite que dos registros tengan el mismo Serie y Numero.
- Numero no puede ser nulo ni ""
- Serie no puede ser nulo pero puede ser ""
- Si una tabla tiene los dos campos Serie y Numero, entonces el par identifica de manera única el registro. Por defecto serie es "" y numero es "".
- El par Serie y Numero juega el mismo papel que el campo "Codigo", es lo que ve el usuario en el UI, de la misma forma que el campo "Codigo".

### Campos "empresa" y "ejercicio" (opcionales)
Este apartado solo se aplica cuando una tabla tiene los campos Empresa y Ejercicio. Si no tiene ambos, ignorar el apartado.
- empresa: tipoEmpresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario.
- ejercicio: tipoEjercicio. Requerido. Si es un número puede ser cero (o si es una string puede ser "" o "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino Empresa,Ejercicio,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino Empresa,Ejercicio,Codigo.

### Campo "empresa" (opcional)
Este apartado solo se aplica cuando una tabla tiene el campo empresa. Si no lo tiene, ignorar el apartado.
- empresa: tipo empresa. Requerido. Si es un número no puede ser cero (o si es una string no puede ser "" ni "0") a menos que la implementación concreta indique lo contrario.
- Si una tabla tiene además los campos Serie y Numero, entonces lo que identifica de forma única al registro no es Serie,Numero sino empresa,Serie,Numero.
- Si una tabla tiene además el campo Codigo, entonces lo que identifica de forma única al registro no es Codigo, sino empresa,Codigo.

### Campo "Descripcion" (opcional)
- tipo: tipoDescripcion. Cada tabla puede tener este campo o no.
- Si la tabla tiene este campo, es un string que describe el registro de forma humanamente comprensible. 
- Cuando se desea mostrar el registro al usuario en el UI, se debe priorizar el campo "Descripcion" sobre todos los demás campos, aunque el campo "Codigo" también se muestre.  

### Campo "Observaciones" (opcional)
- tipo: tipoObservaciones. Cada tabla puede tener este campo o no.
- Si la tabla tiene este campo, es un texto de uso libre que puede contener cualquier información adicional sobre el registro.

## Relaciones entre entidades
- **Se realizan usando los campos `RecordIdent` de la entidad relacionada.**
- Un campo que relaciona con otra tabla se llamará "IDtabla" (por ejemplo, "IDejemplo" para relacionar una tabla cualquiera con la tabla "Ejemplos") y será el campo que se usará para la relación.
- tipo: tipoIdentificador
- Ejemplo: En un pedido, el campo `IDcliente` contiene el `RecordIdent` del cliente.
- Ejemplo: En las líneas de un pedido, `IDpedido` e `IDproducto` apuntan a los identificadores del pedido y el producto, respectivamente.
- Ejemplo: En las una tabla, `IDadicional` apunta a un identificador de una tabla, aunque por el nombre no se pueda inferir qué tabla es.
- **Campo `ID` no existe**: Ninguna tabla o entidad puede tener un campo `ID`. Cuando algún elemento de UI necesita referirse de manera unica a un registro de una tabla, no se utilizará el campo ID de esa tabla(que no existe), sino a su campo RecordIdent.

