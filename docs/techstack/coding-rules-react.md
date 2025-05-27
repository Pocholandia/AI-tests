# Reglas de codificación específicas para
1. **React con TypeScript**
2. **Material UI**
3. **Zustand**
4. **Dexie.js**
5. **Service Worker**

1. **Framework y tecnologías**
     * Usar **React + TypeScript** como base de la UI.
     * Emplear **Material UI** para componentes visuales.
     * Gestionar el estado con **Zustand**, organizando stores por dominio.
     * Persistir datos locales con **IndexedDB** (Dexie.js).
     * Añadir un **Service Worker** para cache y offline.

2. **Capa de datos en React**
     * Carpeta `src/repositories/` con interfaces TS y clases que implementan la API local (Dexie) o remota.
     * Usar patrón adaptador para encapsular Dexie vs. fetch/axios.

3. **Lógica de negocio en React**
     * Carpeta `src/rules/` con funciones puras de validación y utilidades.
     * Asegurar que nunca importen nada de React o Material UI.

4. **Descomposición de componentes en React**
     * Componentes funcionales de React, cada uno con un único propósito.
     * Separar carpetas `components/` y `containers/`.
     * Límite de 300 líneas por archivo JSX/TSX.

5. **Stores de Zustand**
     * Crear un store por entidad (p. ej. `useClientesStore`, `usePedidosStore`).
     * Definir selectores internos para minimizar renders.
     * Añadir middleware `persist` de Zustand para IndexedDB o localStorage.
     * Combinar/decomponer stores mediante `combine` cuando convenga.

6. **Inyección de dependencias en React**
     * Pasar servicios de repositorio vía React Context o props.
     * Utilizar hooks de fábrica (`createXService`) para instanciar con configuraciones distintas.

7. **Formularios con hooks**
     * Hook genérico `useForm<T>()` para validación y state.
     * Hooks concretos por entidad, p. ej. `useProductoForm()` devuelve campos, validaciones y submit.

8. **Feedback UI en React**
     * React Context `NotificationContext` con proveedor en App.
     * Hook `useNotification()` para disparar toasts de Material UI.

9. **Integración API en React**
     * Hooks `useApiService()` que envuelven fetch/axios.
     * HOCs o hooks `useAsync` para gestionar loading/error/data.

10. **Offline-first y sincronización en React**
      * Registrar Service Worker con Workbox o manualmente.
      * Uso de Dexie para marcar entidades con un estado (`new/updated/deleted`).
      * Hook `useSync()` que detecta `navigator.onLine`, procesa cola y resuelve conflictos.
      * Componentes o barras de estado (“Sincronizando…”, “Offline”) integrados en el layout.
      * Botón de “Sincronizar ahora” y auto-sync en background.