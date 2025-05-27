1. **Estética general**: 
   - Usar Material UI moderno y responsivo en toda la app: listas, diálogos, FAB, iconos, grid editable, etc.
2. **UI/UX y diseño**:
   - Todas las operaciones clave usan diálogos modales.
   - Feedback visual consistente (snackbar, tooltips).
   - Validaciones y mensajes de error claros.
   - Botones de acción accesibles y explicativos.
   - Experiencia de usuario optimizada para escritorio y móvil.
   - Layout responsivo y moderno, navegación inferior y menú overflow.
   - Interfaz clara, responsiva y feedback visual ante acciones.
3. **Identificadores internos**
   - Los identificadores internos de las entidades (por ejemplo, IDcliente, IDtarifa, etc.) no deben mostrarse directamente al usuario nunca, excepto que se especifique expresamente en las especificaciones de la aplicación.
   - En su lugar, se deben presentar los datos más representativos y descriptivos de la entidad, como el nombre del cliente, para facilitar la comprensión y mejorar la experiencia de usuario.
4. **Listado de entidades**
   - Para cada entidad existirá un formulario donde se visualizan todas ellas, con paginación, filtrado y búsqueda rápida
   - Se visualiza como una lista de datos de la entidad, con la posibilidad de visualizar,editar y dar de baja cada una de ellas.
   - El filtrado se realizará mediante un icono de filtrado en la parte superior izquierda del listado, que al pulsarlo desplegará un frame de filtrado entre el icono y el listado, desde el que se podrán filtran los registros visibles
   - Si la entidad tiene un campo "Inactivo", en la parte superior izquierda del listado aparecerá un desplegable para indicar si se desean ver Activos, Todos, o solo Inactivos. El valor por defecto para ese desplegable es "Activos"
   - Si un dato está marcado como "Inactivo", mostrará en la lista remarcado en otro color de fondo (por defecto más gris que el color de fondo habitual)
5. **Formularios de entidades**: 
   - Para cada entidad (Pedido, Cliente, Producto, Tarifa, cualquiera adicional), existe un único formulario para visualizar, insertar y editar entidades
   - En formato escritorio, se presentan en diálogos modales unificados, con validación y feedback visual (snackbar)
   - En formato móvil, se presentan en pantalla completa con validación y feedback visual (snackbar)
   - El formulario de visualización de cada entidad mostrará en su esquina superior derecha los iconos para modificar y dar de baja la entidad
   - Las entidades que tienen entidades de detalle asociadas (por ejemplo, Pedido tiene DetallePedido) presentarán, además de los datos de la entidad, una lista de los detalles. Esta lista de detalles funcionará como un grid editable, con validación y feedback visual (snackbar). 
6. **FAB (Floating Action Button) universal**
   - Su ubicación será siempre en la parte inferior derecha de la pantalla.
   - El FAB es el único mecanismo para alta en todas las secciones principales.
   - La acción realizada por el FAB será siempre añadir un nuevo elemento de la sección que se esté visualizando en el momento de pulsarlo
   - Se elimina cualquier otro botón de alta individual en la sección
7. **Indicadores de Estado de Sincronización**
   - Indicador persistente en la interfaz mostrando el estado de sincronización (sincronizado, pendiente, offline)
   - Icono en la barra superior con colores semánticos (verde: sincronizado, amarillo: pendiente, rojo: offline)
   - Snackbar para mostrar errores de sincronización o conflictos
   - Botón en menú overflow para forzar sincronización manual
   - Diálogo para resoluciones de conflictos que requieran intervención del usuario
   - Indicador individual de estado de sincronización en cada elemento de lista (pedido, cliente, etc.) que tenga cambios pendientes