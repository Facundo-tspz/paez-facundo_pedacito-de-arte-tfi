# Plan técnico — Spec 001 Catálogo

## Enfoque

SPA React con rutas `/` y `/producto/:slug`. El catálogo consume `GET /api/productos` y el detalle `GET /api/productos/:slug`. Las Functions validan parámetros y consultan Neon mediante `lib/db.js`. No hay SQL en `src/`.

## Archivos previstos

```text
src/
├── pages/
│   ├── Inicio.jsx
│   ├── Catalogo.jsx
│   └── ProductoDetalle.jsx
├── components/
│   ├── ProductoCard.jsx
│   ├── FiltrosCatalogo.jsx
│   ├── GaleriaProducto.jsx
│   ├── EstadoCarga.jsx
│   ├── EstadoVacio.jsx
│   └── EstadoError.jsx
├── hooks/
│   └── useProductos.js
├── services/
│   └── api.js
└── utils/
    └── formato.js
```

## API

| Método | Ruta | Respuesta |
| --- | --- | --- |
| GET | `/api/productos` | Lista de productos activos con categoría, imagen principal y opciones |
| GET | `/api/productos/:slug` | Detalle con galería y opciones |

Parámetros opcionales de `/api/productos`: `categoria` y `q`. Si no se usan, devuelve todos los activos.

## Base de datos

Consultas en `lib/db.js`:

- `listarProductos({ categoria, q })`: join con `categorias`, imagen principal y opciones; filtra `disponible = true`.
- `obtenerProductoPorSlug(slug)`: producto + categoría + imágenes + opciones.
- `listarCategorias()`: categorías ordenadas.

Índices necesarios:

- `productos(slug)` único.
- `productos(disponible, orden)`.
- `productos(categoria_id)`.
- `producto_imagenes(producto_id, orden)`.
- `producto_opciones(producto_id, orden)`.

## Flujo

1. `Catalogo.jsx` monta y llama `useProductos`.
2. `useProductos` hace `fetch('/api/productos')`.
3. La Function consulta Neon y responde JSON.
4. El componente renderiza tarjetas, filtros y estados.
5. Al abrir una tarjeta, React Router navega a `/producto/:slug`.
6. El detalle carga la API y muestra galería y opciones.

## Validaciones

- `slug`: regex `^[a-z0-9-]{3,80}$`; si no, 400.
- `categoria`: slug válido; si no existe, lista vacía.
- `q`: máximo 80 caracteres; se recorta y normaliza.
- Respuestas sin datos sensibles.
- Errores 404 para producto inexistente/inactivo.

## Pruebas

- Unitarias de `formato.js` y normalización de búsqueda.
- Componentes con Testing Library: tarjeta, filtro, estados vacío/error.
- API con tests de integración si el entorno lo permite; si no, pruebas manuales documentadas.
- Accesibilidad: navegación por teclado y labels.

## Riesgos

| Riesgo | Mitigación |
| --- | --- |
| Muchas imágenes y peso | Cloudinary con transformaciones y `loading="lazy"` |
| Productos sin foto | Placeholder de marca y `alt` claro |
| Búsqueda inexacta | Normalización NFD y búsqueda en nombre/descripción |
| Datos reales incompletos | Seed con textos provisionales marcados y revisión de la emprendedora |
| SEO en SPA | `title`/`meta` dinámicos; evaluar prerender si el tiempo alcanza |

## Orden de implementación

1. API de categorías y productos.
2. Catálogo con tarjetas.
3. Filtro por categoría.
4. Detalle por slug.
5. Estados de carga, error y vacío.
6. Accesibilidad y SEO.
7. Pruebas y verificación.
