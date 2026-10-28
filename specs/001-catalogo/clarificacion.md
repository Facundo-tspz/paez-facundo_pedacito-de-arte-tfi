# Clarificación — Spec 001 Catálogo

## Decisiones

| Pregunta | Opciones | Decisión | Justificación | Estado |
| --- | --- | --- | --- | --- |
| ¿Las tapas y diseños son productos o variantes? | Productos separados / opciones | Opciones dentro del producto | Evita duplicar fichas y facilita el catálogo | Resuelta |
| ¿Qué categorías se muestran? | Libres / fijas | Agendas, Agendas Docentes, Revistas para Colorear, Anotadores | Son las cuatro líneas reales del emprendimiento | Resuelta |
| ¿Qué hacer si no hay precio? | Ocultar / “A presupuestar” | Mostrar “A presupuestar” con `precio_base = 0` o `NULL` | El taller trabaja a pedido y no hay lista de precios cerrada | Resuelta |
| ¿Se muestra stock? | Sí / No | No | El alcance no incluye inventario | Resuelta |
| ¿Se permite buscar? | Sí / No | Incluir si el tiempo alcanza; RF03 es prioridad media | Mejora la experiencia sin bloquear el MVP | Resuelta |
| ¿Paginación? | Sí / No | No en la primera versión; filtrado en cliente | Hay pocas líneas y productos | Resuelta |
| ¿Cómo se ordenan los productos? | Manual / Nombre / Fecha | `orden` ascendente y luego nombre | Permite destacar líneas sin lógica compleja | Resuelta |
| ¿Las imágenes son reales o IA? | IA / Reales | Reales para el catálogo; IA solo para hero y escenas | La IA no debe falsear el producto | Resuelta |
| ¿Qué pasa con productos inactivos? | Mostrar / Ocultar | Ocultar en catálogo y detalle público | Evita mostrar productos discontinuados | Resuelta |
| ¿El detalle usa `slug` o `id`? | ID / Slug | Slug legible y único | Mejor SEO y URLs compartibles | Resuelta |
| ¿Cómo se muestra el precio? | Número / Formato ARS | `Intl.NumberFormat('es-AR', { style: 'currency', currency: 'ARS' })`; si es 0, “A presupuestar” | Consistencia y claridad | Resuelta |
| ¿Cuántas imágenes por producto? | Ilimitadas / Límite | Una principal y hasta 5 adicionales | Suficiente para el taller y controla peso | Resuelta |
| ¿Qué texto alternativo se usa? | Nombre / Descripción | Nombre + categoría + variante visible | Accesibilidad y contexto | Resuelta |
| ¿La búsqueda ignora acentos? | Sí / No | Sí, normalizando con `NFD` | Evita falsos negativos | Resuelta |
| ¿Se indexan los detalles? | Sí / No | Sí, con `title` y `meta description` dinámicos | SEO del catálogo | Resuelta |

## Preguntas que requieren respuesta humana

| Pregunta | Bloquea | Estado |
| --- | --- | --- |
| ¿Cuáles son los nombres y descripciones comerciales definitivos? | No para el scaffold; sí para contenido final | Pendiente |
| ¿Qué fotos reales hay disponibles por producto? | No para el código; sí para la entrega visual | Pendiente |
| ¿Se incluye la búsqueda en el MVP? | No; es opcional | Pendiente de decisión final |

## Supuestos permitidos

- Los productos se cargan inicialmente por seed y luego se gestionan desde el panel.
- Una categoría puede existir sin productos.
- El catálogo público no requiere autenticación.
- La API devuelve JSON y errores con códigos HTTP estándar.
- El frontend no conoce IDs internos de opciones; usa IDs públicos de la API.
