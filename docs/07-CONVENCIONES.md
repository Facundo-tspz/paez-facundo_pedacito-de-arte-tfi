# 07 - Convenciones de Código y Git: Pedacito de Arte

Este documento define las reglas de estilo, nomenclatura, estándares de programación y formato de commits para el desarrollo del sistema de **Pedacito de Arte**.

---

## 1. Nomenclatura y Estándares de Código

Para garantizar legibilidad y mantenimiento en el proyecto, se aplican las siguientes convenciones según el stack (**React + Vite + JavaScript ES6+**):

| Elemento | Convención / Formato | Ejemplo |
| :--- | :--- | :--- |
| **Componentes React** | `PascalCase` con extensión `.jsx` | `TarjetaProducto.jsx`, `CarritoDrawer.jsx`, `BotonWhatsApp.jsx` |
| **Hooks Personalizados** | `camelCase` con prefijo `use` | `useProducts.js`, `useCart.js` |
| **Archivos de Utilidad / Servicios** | `camelCase` con extensión `.js` | `formatters.js`, `whatsappService.js`, `imageCompressor.js` |
| **Variables y Funciones** | `camelCase` en español explicativo | `const productosDestacados`, `function agregarAlCarrito()` |
| **Constantes Globales** | `UPPER_SNAKE_CASE` | `MAX_IMAGE_SIZE_MB`, `WHATSAPP_PHONE_NUMBER` |
| **Rutas de API / Endpoints** | `kebab-case` en minúsculas | `/api/productos`, `/api/presupuestos` |
| **Clases de CSS / Tailwind** | `kebab-case` para clases personalizadas; tokens del theme en lugar de HEX sueltos | `.btn-presupuesto`, `.card-pedacito`, `bg-fondo`, `text-principal` |
| **Netlify Functions** | Router único en `netlify/functions/api.js`; helpers en `lib/` | `export default async (request, context) => {}` |
| **Tablas y columnas SQL** | `snake_case` en español | `productos`, `precio_base`, `categoria_id` |
| **Variables de entorno** | `UPPER_SNAKE_CASE`; prefijo `VITE_` solo si es pública | `DATABASE_URL`, `AUTH_SECRET`, `SESSION_TTL_HOURS`, `VITE_WHATSAPP_PHONE_NUMBER` |
| **Roles** | Constante `ROLES`, nunca strings sueltos | `ROLES.SUPER_ADMIN`, `ROLES.ADMIN`, `ROLES.EDITOR` |

---

## 2. Idioma del Código y Comentarios

- **Código y Nombres de Variables:** Se escriben en **español** para mantener coherencia con el dominio de negocio (`producto`, `categoria`, `carrito`, `presupuesto`).
- **Comentarios:** Breves, descriptivos y enfocados en explicar el *porqué* de una lógica compleja (ej. algoritmos de personalización o formateo de mensajes de WhatsApp).

---

## 3. Convención de Git y Commits

Para mantener un historial claro en GitHub, los mensajes de commit deben seguir el estándar **Conventional Commits** con el formato:

`<tipo>(<módulo>): <descripción breve en presente y minúsculas>`

### Tipos de Commits Permitidos
- `feat`: Nueva funcionalidad (ej. `feat(cart): agrega boton de confirmacion por whatsapp`).
- `fix`: Corrección de errores (ej. `fix(catalog): corrige filtrado por categoria infantil`).
- `docs`: Cambios en la documentación Markdown (ej. `docs(brand): actualiza paleta de colores HEX`).
- `style`: Cambios de diseño, formato o estilos de Tailwind sin modificar lógica (ej. `style(ui): ajusta padding en tarjetas de productos`).
- `refactor`: Reestructuración de código sin añadir funciones ni corregir bugs.
- `test`: Altas o ajustes de pruebas automatizadas.
- `chore`: Tareas de mantenimiento, configuración o dependencias.

---

## 4. Estrategia de Ramas

- `main`: Rama principal estable en producción, conectada al deploy automático de Netlify.
- `feature/<nombre-tarea>`: Ramas temporales para requerimientos específicos (ej. `feature/catalogo-filtros`, `feature/integracion-whatsapp`, `feature/admin-productos`).
- No se usa `develop` en un trabajo individual; si se crea, debe justificarse.
- Cada tarea se implementa en un commit pequeño y descriptivo. No se mezclan feature, refactor y deploy en el mismo commit.

---

## 5. Acceso a Datos y API

- Las consultas SQL viven **únicamente** en `lib/db.js`, ejecutado del lado servidor por `netlify/functions/api.js` en `/api/*`.
- El cliente consume la API mediante `src/services/api.js`; nunca importa `lib/db.js` ni conoce `DATABASE_URL`.
- Todas las consultas usan parámetros (`$1`, `$2`, ...). Prohibida la concatenación de SQL.
- Los endpoints de administración validan sesión y permisos antes de leer o escribir.
- La autorización se resuelve con `requireAuth([...roles])` en cada ruta admin.
- Los roles se comparan siempre con la constante `ROLES` (`super_admin`, `admin`, `editor`), nunca con strings literales dispersos.
- Las imágenes se suben a Cloudinary desde el servidor; el cliente no recibe `CLOUDINARY_API_SECRET`.

---

## 6. Pruebas y Calidad

- **ESLint + Prettier** para estilo y errores comunes.
- **Vitest + React Testing Library** para pruebas de componentes y utilidades (carrito, formateo, `whatsappService`).
- Scripts obligatorios en `package.json`: `dev`, `build`, `preview`, `lint`, `test`.
- Ninguna tarea se considera terminada sin `npm run lint`, `npm run test` y `npm run build` sin errores.
- Cada spec actualiza su `verificacion.md` con evidencia; no se declara un criterio cumplido sin prueba o inspección reproducible.

### Flujo SDD

1. `/spec` redacta el requerimiento en EARS.
2. `/clarificar` resuelve ambigüedades.
3. `/plan` define el enfoque técnico.
4. `/tareas` divide en pasos de menos de 30 minutos.
5. Implementación y pruebas.
6. `/verificar` actualiza la evidencia.
7. `/cambio` aplica ajustes con spec primero.

---

## 7. Variables de Entorno

| Variable | Ámbito | Uso |
| :--- | :--- | :--- |
| `DATABASE_URL` | Servidor (`netlify/functions`, `lib/db.js`) | Conexión a PostgreSQL en Neon. Nunca se expone al cliente. |
| `AUTH_SECRET` | Servidor (`/api/admin`) | Firma de tokens CSRF y utilidades de autenticación. |
| `SESSION_TTL_HOURS` | Servidor (`/api/admin`) | Duración de la sesión del admin (por defecto `8`). |
| `CLOUDINARY_CLOUD_NAME` | Servidor (`lib/cloudinary.js`) | Cuenta de Cloudinary. |
| `CLOUDINARY_API_KEY` | Servidor (`lib/cloudinary.js`) | API key de Cloudinary. |
| `CLOUDINARY_API_SECRET` | Servidor (`lib/cloudinary.js`) | API secret; nunca en el cliente. |
| `VITE_WHATSAPP_PHONE_NUMBER` | Cliente | Número del taller con código de país, sin `+` (ejemplo temporal: `5491100000000`). |

El archivo `.env.example` debe listar todas las variables con valores de ejemplo, nunca reales.

---

## 8. Scripts de Operación

| Script | Uso | Regla |
| :--- | :--- | :--- |
| `npm run admin:reset -- <usuario> [--rol <rol>]` | Crea o restablece un usuario admin con contraseña temporal y fuerza el cambio. | Solo local con `DATABASE_URL`; nunca se expone por API ni por ruta pública. |
| `npm run admin:list` | Lista usuarios admin (usuario, rol, estado). | Solo lectura; requiere `DATABASE_URL`. |
| `npm run db:setup` | Ejecuta `db/esquema.sql` en Neon. | Requiere `DATABASE_URL`; no usar en producción sin backup. |
| `npm run db:seed` | Carga datos iniciales de catálogo. | No incluye precios ni datos inventados. |
| `npm run dev:netlify` | Levanta el sitio con Netlify CLI y Functions. | Requiere `netlify dev` y variables locales. |