# Glosario de Términos / Glossary of Terms

## Propósito / Purpose

Este documento proporciona una referencia estandarizada para los términos utilizados en toda la documentación del proyecto Preventa. Su objetivo es asegurar la consistencia terminológica en español e inglés.

This document provides a standardized reference for terms used throughout the Preventa project documentation. Its purpose is to ensure terminology consistency in both Spanish and English.

## Entidades del Sistema / System Entities

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Cliente** | **Customer** | Entidad que representa a un comprador en el sistema. / Entity representing a buyer in the system. |
| **Producto** | **Product** | Artículo o servicio que puede ser vendido a un cliente. / Item or service that can be sold to a customer. |
| **Pedido** | **Order** | Solicitud de compra realizada por un cliente. / Purchase request made by a customer. |
| **Línea de Pedido** | **Order Line** | Elemento individual dentro de un pedido. / Individual item within an order. |
| **Tarifa** | **Price List** | Conjunto de precios específicos para productos. / Set of specific prices for products. |
| **Línea de Tarifa** | **Price List Line** | Precio específico de un producto en una tarifa. / Specific price of a product in a price list. |

## Campos Comunes / Common Fields

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **RecordIdent** | **RecordIdent** | Identificador único para cada registro. / Unique identifier for each record. |
| **RecordVersion** | **RecordVersion** | Versión del registro para control de concurrencia. / Record version for concurrency control. |
| **Código** | **Code** | Identificador alfanumérico legible para una entidad. / Human-readable alphanumeric identifier for an entity. |
| **Descripción** | **Description** | Texto descriptivo de una entidad. / Descriptive text for an entity. |
| **Inactivo** | **Inactive** | Estado que indica si una entidad está activa o no. / Status indicating whether an entity is active or not. |

## Campos de Sincronización / Synchronization Fields

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Estado de Sincronización** | **SyncStatus** | Estado actual del registro en relación a la sincronización. / Current status of the record in relation to synchronization. |
| **Fecha de Última Sincronización** | **LastSyncDate** | Fecha de la última sincronización exitosa. / Date of the last successful synchronization. |
| **Fecha de Modificación Local** | **LocalModifiedDate** | Fecha de la última modificación en el dispositivo local. / Date of the last modification on the local device. |
| **Fecha de Modificación en Servidor** | **ServerModifiedDate** | Fecha de la última modificación en el servidor. / Date of the last modification on the server. |
| **Resolución de Conflictos** | **ConflictResolution** | Estrategia para resolver conflictos de sincronización. / Strategy to resolve synchronization conflicts. |

## Estados de Sincronización / Synchronization States

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Sincronizado** | **synced** | El registro está sincronizado con el servidor. / The record is synchronized with the server. |
| **Pendiente de Creación** | **pending_create** | El registro está creado localmente y pendiente de subir al servidor. / The record is created locally and pending upload to the server. |
| **Pendiente de Actualización** | **pending_update** | El registro está modificado localmente y pendiente de actualizar en el servidor. / The record is modified locally and pending update on the server. |
| **Pendiente de Eliminación** | **pending_delete** | El registro está marcado para eliminación localmente. / The record is marked for deletion locally. |

## Términos de la Interfaz de Usuario / User Interface Terms

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Diálogo Modal** | **Modal Dialog** | Ventana que aparece sobre el contenido principal y requiere interacción. / Window that appears over the main content and requires interaction. |
| **Barra de Notificaciones** | **Snackbar** | Elemento de UI que muestra mensajes temporales. / UI element that displays temporary messages. |
| **Pantalla Principal** | **Main Screen** | Vista principal de la aplicación. / Main view of the application. |
| **Filtros** | **Filters** | Controles para filtrar listas de datos. / Controls to filter data lists. |
| **Generación de Datos Demo** | **Demo Data Generation** | Función para crear datos de prueba. / Function to create test data. |

## Términos Técnicos / Technical Terms

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Almacenamiento Fuera de Línea** | **Offline Storage** | Capacidad de almacenar datos sin conexión a internet. / Ability to store data without internet connection. |
| **Sincronización** | **Synchronization** | Proceso de actualizar datos entre dispositivo y servidor. / Process of updating data between device and server. |
| **Gestión de Estado** | **State Management** | Sistema para gestionar el estado de la aplicación (Zustand). / System for managing application state (Zustand). |
| **Repositorio** | **Repository** | Capa de acceso a datos que abstrae la fuente. / Data access layer that abstracts the source. |
| **Reglas de Negocio** | **Business Rules** | Lógica que define el comportamiento del sistema. / Logic that defines system behavior. |
| **Validación** | **Validation** | Proceso de verificar que los datos cumplen con requisitos. / Process of verifying that data meets requirements. |

## Términos de Desarrollo / Development Terms

| Español | English | Descripción / Description |
|---------|---------|---------------------------|
| **Componente** | **Component** | Elemento reutilizable de interfaz en React. / Reusable interface element in React. |
| **Tienda** | **Store** | Almacén de estado de Zustand. / Zustand state store. |
| **Migración de Datos** | **Data Migration** | Proceso de actualizar esquemas de base de datos. / Process of updating database schemas. |
| **Modo Fuera de Línea** | **Offline Mode** | Funcionamiento sin conexión a internet. / Operation without internet connection. |
| **Trabajador de Servicio** | **Service Worker** | Script que permite funcionalidades offline. / Script that enables offline functionality. |
| **Aplicación Web Progresiva** | **Progressive Web App (PWA)** | Aplicación web con capacidades de aplicación nativa. / Web application with native app capabilities. |

## Convenciones de Nomenclatura / Naming Conventions

### Recomendaciones Generales / General Recommendations

- Usar **camelCase** para nombres de variables y funciones en código.
- Usar **PascalCase** para nombres de componentes React y clases.
- Usar **snake_case** para nombres de archivos de documentación.
- Usar **kebab-case** para URLs y nombres de archivos CSS.

### Convenciones para Documentación / Documentation Conventions

- Los archivos de documentación deben usar la extensión `.md`.
- Los nombres de archivos deben ser descriptivos y usar guiones (`-`) entre palabras.
- Los encabezados deben usar la capitalización de títulos en español o inglés según el idioma del documento.

## Uso de este Glosario / Using this Glossary

Cuando escribas documentación o código para el proyecto Preventa:

1. Consulta este glosario para asegurar el uso consistente de términos.
2. Si un término no está en el glosario pero se usará frecuentemente, proponlo para su inclusión.
3. Respeta las convenciones de nomenclatura establecidas.
4. Para documentación bilingüe, usa los términos equivalentes correctos en cada idioma.

When writing documentation or code for the Preventa project:

1. Consult this glossary to ensure consistent use of terms.
2. If a term is not in the glossary but will be used frequently, propose it for inclusion.
3. Follow the established naming conventions.
4. For bilingual documentation, use the correct equivalent terms in each language.
