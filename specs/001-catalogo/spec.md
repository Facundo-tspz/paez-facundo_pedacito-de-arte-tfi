# Spec 001 — Catálogo público

- **Estado:** Borrador para implementación
- **RF principales:** RF01, RF02, RF04
- **RF opcional:** RF03 (búsqueda)
- **Prioridad:** Alta
- **Dependencias:** esquema `productos`, `categorias`, `producto_imagenes`, `producto_opciones`; API `/api/productos`.

## 1. Contexto

El catálogo es la entrada principal del sitio. Debe mostrar los productos reales del taller, permitir explorar por categoría y abrir el detalle de cada producto con sus variantes. No hay precios definitivos todavía: cuando el precio sea desconocido se muestra “A presupuestar”.

## 2. Historias de usuario

- Como visitante, quiero ver los productos disponibles para conocer qué ofrece Pedacito de Arte.
- Como visitante, quiero filtrar por categoría para encontrar el tipo de producto que busco.
- Como visitante, quiero abrir un producto y ver sus fotos, opciones y descripción antes de agregarlo al presupuesto.
- Como visitante en celular, quiero navegar el catálogo con una interfaz clara y rápida.

## 3. Requerimientos funcionales (EARS)

### RF01 — Listado de productos

- **RF01.1:** Cuando el visitante abre el catálogo, el sistema deberá mostrar solo productos con `disponible = true`.
- **RF01.2:** Cada tarjeta deberá mostrar imagen, nombre, categoría, descripción breve y precio o “A presupuestar”.
- **RF01.3:** Si un producto no tiene imagen, entonces el sistema deberá mostrar un placeholder de marca con texto alternativo.
- **RF01.4:** Mientras los productos se cargan, el sistema deberá mostrar un estado de carga accesible.
- **RF01.5:** Si la carga falla, entonces el sistema deberá mostrar un mensaje de error y un botón para reintentar.

### RF02 — Filtro por categoría

- **RF02.1:** Cuando el visitante selecciona una categoría, el sistema deberá mostrar solo los productos de esa categoría.
- **RF02.2:** Cuando el visitante selecciona “Todas”, el sistema deberá mostrar todos los productos activos.
- **RF02.3:** Si no hay productos en la categoría, entonces el sistema deberá mostrar un estado vacío con una acción para volver a “Todas”.
- **RF02.4:** El filtro deberá actualizar la URL o el estado de forma que se pueda compartir la selección.

### RF03 — Búsqueda (opcional, prioridad media)

- **RF03.1:** Cuando el visitante escribe en el buscador, el sistema deberá filtrar por nombre, descripción o tema.
- **RF03.2:** Si la búsqueda no tiene resultados, entonces el sistema deberá mostrar un estado vacío y limpiar la búsqueda.
- **RF03.3:** La búsqueda no deberá distinguir mayúsculas ni acentos básicos.

### RF04 — Detalle del producto

- **RF04.1:** Cuando el visitante selecciona un producto, el sistema deberá mostrar su detalle por `slug`.
- **RF04.2:** El detalle deberá incluir galería, nombre, categoría, descripción, opciones disponibles y precio o “A presupuestar”.
- **RF04.3:** Si el producto no existe o no está activo, entonces el sistema deberá mostrar un estado “Producto no encontrado”.
- **RF04.4:** Si el producto tiene opciones obligatorias, entonces el sistema deberá exigir una selección antes de permitir agregarlo al presupuesto.
- **RF04.5:** El detalle deberá permitir volver al catálogo conservando el filtro previo cuando sea posible.

## 4. Casos borde

- Catálogo vacío.
- Categoría sin productos.
- Producto inactivo o eliminado.
- Imagen rota o ausente.
- Texto largo en nombre o descripción.
- Producto con precio `0` o `NULL`.
- Producto con una o varias opciones obligatorias.
- Búsqueda con acentos, mayúsculas o caracteres especiales.
- Error de red o API caída.
- Navegación directa a una URL de detalle inexistente.

## 5. Fuera de alcance

- Pago en línea, stock, envíos y cuentas de cliente.
- Reseñas, favoritos y comparador.
- Recomendaciones automáticas.
- Carga de imágenes desde el catálogo público.
- Edición de productos desde el catálogo.

## 6. Criterios de aceptación

1. Un visitante ve solo productos activos, con imagen, nombre, categoría, descripción y precio o “A presupuestar”.
2. El filtro por categoría funciona y muestra un estado vacío cuando corresponde.
3. El detalle carga por `slug` y muestra galería, opciones y precio.
4. Un producto inexistente o inactivo no se muestra y devuelve un estado claro.
5. La interfaz funciona a 360 px, con teclado y foco visible.
6. Todas las imágenes tienen `alt` significativo.
7. Los estados de carga, error y vacío están implementados.
8. Las pruebas cubren listado, filtro, detalle y estados.

## 7. Trazabilidad RF

| RF | Prioridad | Incluido | Verificación |
| --- | --- | --- | --- |
| RF01 | Alta | Sí | `verificacion.md` |
| RF02 | Alta | Sí | `verificacion.md` |
| RF03 | Media | Opcional | `verificacion.md` |
| RF04 | Alta | Sí | `verificacion.md` |

## 8. Preguntas abiertas

- Confirmar si se incluye búsqueda en el MVP o queda como mejora.
- Confirmar los nombres finales de las categorías y los textos comerciales.
- Confirmar las fotos reales disponibles para cada producto.
