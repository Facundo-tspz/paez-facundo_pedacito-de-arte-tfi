# Tareas — Spec 001 Catálogo

| # | Tarea | Archivos | Listo cuando | RF |
| --- | --- | --- | --- | --- |
| 1 | Revisar y aprobar la spec | `spec.md`, `clarificacion.md` | Decisiones bloqueantes resueltas | — |
| 2 | Crear consultas de categorías y productos | `lib/db.js` | Devuelven datos con parámetros y sin SQL dinámico | RF01/RF02 |
| 3 | Crear endpoints `/api/productos` y `/api/productos/:slug` | `netlify/functions/api.js` | Responden JSON y errores 400/404 | RF01/RF04 |
| 4 | Crear cliente de API y hook de productos | `src/services/api.js`, `src/hooks/useProductos.js` | Maneja carga, éxito, error y reintento | RF01 |
| 5 | Crear tarjeta de producto | `src/components/ProductoCard.jsx` | Muestra imagen, nombre, categoría y precio | RF01 |
| 6 | Crear grilla y estado vacío del catálogo | `src/pages/Catalogo.jsx` | Lista productos y muestra vacío | RF01 |
| 7 | Implementar filtro por categoría | `src/components/FiltrosCatalogo.jsx` | Filtra y actualiza la URL/estado | RF02 |
| 8 | Implementar búsqueda opcional | `src/components/Buscador.jsx` | Filtra por nombre/descripción y muestra vacío | RF03 |
| 9 | Crear detalle por slug | `src/pages/ProductoDetalle.jsx` | Muestra galería, opciones y precio | RF04 |
| 10 | Crear galería accesible | `src/components/GaleriaProducto.jsx` | Imágenes con `alt`, teclado y fallback | RF04 |
| 11 | Crear estados de carga y error | `src/components/EstadoCarga.jsx`, `EstadoError.jsx` | Accesibles y con reintento | RF01/RF04 |
| 12 | Ajustar SEO dinámico | `src/pages/ProductoDetalle.jsx` | `title` y `meta description` por producto | RF04 |
| 13 | Escribir pruebas de catálogo y detalle | `tests/` | Cubren listado, filtro, vacío y 404 | RF01/RF02/RF04 |
| 14 | Verificar y documentar | `specs/001-catalogo/verificacion.md` | Tabla completa con evidencia | Todos |

## Dependencias

- 3 depende de 2.
- 4 depende de 3.
- 5 y 6 dependen de 4.
- 9 depende de 3 y 4.
- 13 depende de 6, 7 y 9.

## Bloqueos

- Fotos reales y textos definitivos: no bloquean el código, sí la aceptación visual final.
- Decisión sobre búsqueda: no bloquea el resto.
