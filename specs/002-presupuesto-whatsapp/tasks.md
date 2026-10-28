# Tareas — Spec 002 Carrito y WhatsApp

| # | Tarea | Archivos | Listo cuando | RF |
| --- | --- | --- | --- | --- |
| 1 | Aprobar spec y decisiones | `spec.md`, `clarificacion.md` | Decisiones bloqueantes resueltas | — |
| 2 | Crear modelo y persistencia del carrito | `src/context/CartContext.jsx`, `src/utils/carrito.js` | Agrega, actualiza, quita y recupera con validación | RF05 |
| 3 | Crear selector de opciones | `src/components/SelectorOpciones.jsx` | Valida obligatorias y múltiples | RF05 |
| 4 | Crear botón agregar | `src/components/BotonAgregar.jsx` | Bloquea sin opciones y muestra feedback | RF05 |
| 5 | Crear página y resumen del carrito | `src/pages/Carrito.jsx`, `CarritoResumen.jsx` | Totales correctos y vacío deshabilitado | RF05 |
| 6 | Crear endpoint de presupuestos | `netlify/functions/api.js`, `lib/db.js` | Valida, calcula y guarda en transacción | RF06/RF10 |
| 7 | Generar mensaje de WhatsApp | `src/services/whatsappService.js` | Incluye ítems, opciones y total | RF06 |
| 8 | Integrar confirmación | `src/pages/Carrito.jsx` | POST, apertura y fallback funcionan | RF06/RF10 |
| 9 | Agregar estados de error y doble envío | `Carrito.jsx` | Botón deshabilitado y mensajes claros | RF06 |
| 10 | Escribir pruebas de cálculo y validación | `tests/` | Cubren subtotales, límites y opciones | RF05/RF06 |
| 11 | Probar API y transacción | `tests/` o verificación manual | Sin cabeceras huérfanas | RF10 |
| 12 | Verificar y documentar | `specs/002-presupuesto-whatsapp/verificacion.md` | Tabla completa con evidencia | Todos |

## Dependencias

- 2 depende de la spec 001 (producto y opciones).
- 6 depende de 2 y del esquema de datos.
- 8 depende de 6 y 7.
- 10 depende de 2, 3 y 5.

## Bloqueos

- Número de WhatsApp definitivo para prueba real.
- Confirmación de si RF10 entra en el MVP.
