Las siguientes reglas provinientes del estándar VIBE se han ajustado o añadido para un entorno **React + TypeScript + Material UI**:

1. **Directrices visuales**
     * Usar componentes de **Material UI** (MUI) y su sistema de **Theme.spacing(1) = 8 px**.
     * Aplicar los componentes de layout (`<Grid>`, `<Box>`) de MUI para mantener la rejilla.

2. **Estructura de carpetas**
     * `src/components/` → componentes puros de presentación.
     * `src/containers/` → componentes conectados a lógica o estado.
     * `src/hooks/` → hooks reutilizables (`useFilter`, `useForm`, `useNotification`).

3. **Gestión de estado y memoización**
     * Usar **React.memo**, `useMemo` y `useCallback` en componentes de alto coste.
     * Para estado global/filtrado, considerar **React Context** o soluciones como **Zustand**.

4. **Virtualización**
     * Emplear bibliotecas como **react-window** o **react-virtualized** para listas largas.

5. **Optimistic UI**
     * Integrar con **React Query** o **SWR** para gestionar peticiones y actualizaciones optimistas.

6. **Filtros: componentes estándar**
     * Crear componentes TSX genéricos:
       * `EntityFilter.tsx`
       * `DateRangeFilter.tsx` (prop `dateFormat="YYYY-MM-DD"`)
       * `NumericRangeFilter.tsx` (prop `step`, `unit`)
       * `EntitySelectFilter.tsx` (prop `displayProperty`, `valueProperty`)
     * Publicar typings en `src/types/filters.ts`.

7. **Manejo de formularios**
     * Definir un hook genérico `useForm<T>(options)` en `src/hooks/useForm.ts`.
     * Crear hooks específicos (`useClienteForm`, `usePedidoForm`) que orquesten validaciones de **Yup** o **Zod**.

8. **Feedback y notificaciones**
     * Implementar `NotificationContext` con MUI `<Snackbar>` y hook `useNotification()` para disparar toasts.

9. **Accesibilidad en React**
     * Verificar con **@mui/utils/useIsFocusVisible** y atributos `aria-*`.
     * Asegurar que componentes personalizados extiendan correctamente los props de accesibilidad.

10. **Pruebas en React**
      * Usar **Jest** + **React Testing Library**.
      * Probar:
        * Renderizado de filtros, estados vacíos, cargas, combinación de filtros.
        * Interacción de hooks (`useFilter`, `useForm`) y Contextos.

11. **Plantillas de componentes**
      * Basarse en `component-templates.tsx` (en `docs/`) para generar nuevos componentes siguiendo VIBE.

12. **Anti-patrones**
      * Evitar “pantallas-zombie” sin reagrupar dependencias de estado.
      * Prevenir rerenders masivos usando correctamente `key`, `memo` y selectores de estado.