[⬅ Back to Index](./index.md)

# 2. Coding Rules and Best Practices

- **Principios SOLID**: Aplicar siempre SOLID para favorecer la mantenibilidad y la extensibilidad, incluso si obliga a un diseño más detallado y un código más verboso.
- **Cobertura de pruebas**: Toda funcionalidad debe acompañarse de tests unitarios y de integración que verifiquen tanto la lógica pura como las interacciones entre módulos.
- **Separación de capas**:
  1. **Capa de datos**
     - Encapsular todas las operaciones de acceso a datos en una capa independiente.
     - Usar un patrón repositorio o similar, con interfaces/contratos que permitan intercambiar fácilmente la fuente (local, remota, híbrida).
  2. **Lógica de negocio**
     - Extraer validaciones, transformaciones y reglas de negocio a funciones puras en su propia capa.
     - Evitar referencias a la UI: la lógica de negocio solo debe depender de su propia capa de datos y de utilidades.
  3. **Presentación / UI**
     - Mantener los componentes o pantallas centrados en la renderización y el enrutamiento de eventos, delegando la lógica a las capas inferiores.
- **Descomposición de componentes / módulos**:
  - Diseñar unidades (componentes, clases, módulos) con una única responsabilidad.
  - Aplicar el patrón contenedor / presentación (o variaciones equivalentes) para separar lógica de presentación de lógica de gestión de datos.
  - Limitar el tamaño de cada unidad (por ejemplo, < 300 líneas) para facilitar su comprensión y prueba.
- **Gestión de estado**:
  - Elegir una única solución de gestión de estado y usarla de modo consistente.
  - Crear "stores" o módulos de estado por dominio o feature.
  - Proporcionar selectores o getters específicos para evitar renderizados o subscripciones innecesarias.
- **Preparación para tests y mocks**:
  - Inyectar dependencias (interfaces, fábricas o contexto) para poder sustituir implementaciones concretas por dobles de test.
  - Evitar acoplamientos directos a infraestructuras (APIs HTTP, base de datos, sistema de archivos…).
- **Estandarización de eventos**:
  - Definir una convención unificada de nombres y estructuras de manejadores de eventos.
  - Reutilizar funciones o fábricas de eventos para comportamientos comunes (confirmaciones, validaciones…).
- **Abstracción de formularios**:
  - Centralizar la validación y el manejo de cambios de formulario en hooks, clases o utilidades reutilizables.
  - Evitar duplicar lógica de formularios en distintos componentes.
- **Sistema de feedback al usuario**:
  - Implementar un mecanismo global de notificaciones o mensajes (toast, banners, diálogos…).
  - Proveer una API única para mostrar feedback, independiente del framework de UI.
- **Integración con APIs externas**:
  - Adoptar un patrón adaptador o wrapper para desacoplar llamadas HTTP de la lógica de negocio.
  - Gestionar consistentemente estados de carga, éxito y error.
- **Soporte offline y sincronización**:
  - Diseñar pensando en un flujo local-first: la aplicación debe ser plenamente funcional sin conexión.
  - Llevar un registro de cambios locales (nuevos, modificados, eliminados).
  - Implementar un mecanismo de cola y reconciliación con el servidor, con detección de conectividad y resolución de conflictos.
  - Ofrecer indicadores de estado de sincronización y permitir tanto sincronización manual como automática. 