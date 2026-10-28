# 04 - Arquitectura del Proyecto: Pedacito de Arte

Este documento define la estructura de carpetas, la organización de módulos y la responsabilidad de cada capa de **Pedacito de Arte**.

> **Stack:** React 18 + Vite (SPA) + Netlify Functions 2.0 + PostgreSQL (Neon) + Cloudinary.

---

## 1. Árbol de carpetas

```text
paez-facundo_pedacitodearte/
├── .agents/
│   └── skills/                   # Skills del proyecto
│       ├── verificar-criterios/
│       ├── escribir-spec-ears/
│       └── revisar-multimedia-ia/
├── .opencode/
│   └── commands/                 # Comandos /spec, /clarificar, /plan, /tareas, /verificar, /cambio
├── docs/                         # Documentación, bitácora y decisiones
├── specs/                        # Especificaciones SDD
│   ├── 001-catalogo/
│   ├── 002-presupuesto-whatsapp/
│   └── 003-panel-admin/
├── db/
│   ├── esquema.sql               # DDL de tablas, índices y restricciones
│   ├── seed.sql                  # Datos iniciales
│   └── crear-superadmin.js       # Alta/reset local del super admin
├── lib/
│   ├── db.js                     # Conexión Neon + consultas parametrizadas
│   ├── auth.js                   # Hash, sesiones, cookies y requireAuth
│   ├── roles.js                  # Constante ROLES y matriz de permisos
│   ├── http.js                   # Respuestas JSON, errores y validación
│   └── cloudinary.js             # Firma y subida de imágenes
├── netlify/
│   └── functions/
│       └── api.js                # Router /api/* (Functions 2.0)
├── public/
│   ├── marca/                    # Logos e identidad visual
│   ├── media/                    # Imágenes públicas del catálogo
│   ├── robots.txt
│   └── sitemap.xml
├── multimedia/
│   ├── imagenes/                 # Piezas IA y mockups
│   ├── video/                    # Guion y videos
│   ├── graficos/                 # Infografía y gráficos
│   └── FICHAS-IA.md
├── src/
│   ├── assets/
│   ├── components/
│   │   ├── common/
│   │   ├── catalog/
│   │   ├── cart/
│   │   ├── admin/
│   │   └── ui/
│   ├── context/
│   │   ├── CartContext.jsx
│   │   └── AuthContext.jsx
│   ├── hooks/
│   ├── layout/
│   │   ├── MainLayout.jsx
│   │   └── AdminLayout.jsx
│   ├── pages/
│   │   ├── HomePage.jsx
│   │   ├── CatalogPage.jsx
│   │   ├── ProductDetailPage.jsx
│   │   ├── ServicesPage.jsx
│   │   ├── ContactPage.jsx
│   │   ├── NotFoundPage.jsx
│   │   └── admin/
│   ├── routes/
│   │   └── AppRouter.jsx
│   ├── services/
│   │   ├── api.js
│   │   ├── whatsappService.js
│   │   ├── authService.js
│   │   └── storageService.js
│   ├── styles/
│   ├── utils/
│   ├── App.jsx
│   └── main.jsx
├── tests/
├── AGENTS.md
├── CLAUDE.md
├── memory.md
├── netlify.toml
├── index.html
├── tailwind.config.js
├── postcss.config.js
├── vite.config.js
├── package.json
└── README.md
```

---

## 2. Responsabilidad de las capas

| Capa | Ubicación | Responsabilidad | Regla |
| :--- | :--- | :--- | :--- |
| **Presentación** | `src/` | UI, estado, ruteo y formularios. | No conoce credenciales ni escribe SQL. |
| **Servicios cliente** | `src/services/` | `fetch` hacia `/api`, WhatsApp y subida. | No importa `lib/db.js`. |
| **API serverless** | `netlify/functions/api.js` | Validación, autorización y orquestación. | Toda lectura/escritura pasa por `/api`. |
| **Acceso a datos** | `lib/db.js` | Conexión Neon y consultas parametrizadas. | Único lugar con SQL. |
| **Persistencia** | `db/` + Neon | Esquema, seed y datos reales. | Cambios vía `db/esquema.sql`. |
| **Storage** | Cloudinary | Imágenes subidas desde el admin. | Se guardan `url` y `public_id` en PostgreSQL. |
| **SDD** | `specs/`, `.agents/`, `.opencode/` | Especificaciones, skills y comandos. | Spec antes de código. |

---

## 3. Flujo de datos

```text
[ Componente React ]
        │  src/services/api.js
        ▼
[ /api/* ]
        │  Netlify Function api.js
        │  validación + requireAuth si es admin
        ▼
[ lib/db.js ] ──► [ PostgreSQL (Neon) ]
        │
        └──► [ lib/cloudinary.js ] ──► [ Cloudinary ]
```

- **Catálogo público:** `GET /api/productos` y `GET /api/categorias`.
- **Presupuesto:** `POST /api/presupuestos` valida, calcula, guarda y devuelve el enlace de WhatsApp.
- **Auth admin:** `POST /api/admin/auth/login`, `POST /api/admin/auth/logout`, `GET /api/admin/auth/me`, `POST /api/admin/auth/cambiar-password`.
- **Usuarios:** `GET|POST /api/admin/usuarios`, `PATCH /api/admin/usuarios/:id`, `POST /api/admin/usuarios/:id/reset-password`.
- **Catálogo admin:** CRUD protegido según rol.
- **Imágenes:** `POST /api/admin/imagenes` → Cloudinary.
- **Salud:** `GET /api/health`.

---

## 4. Funciones Netlify

`netlify/functions/api.js` usa Functions 2.0:

```javascript
export default async (request, context) => {
  // router interno por método + ruta
}

export const config = {
  path: "/api/*"
}
```

Reglas:

- No agregar un redirect `/api/*` a `/.netlify/functions/api` en `netlify.toml`; el `config.path` ya expone la ruta.
- El SPA usa fallback `/* → /index.html` con estado 200, sin capturar `/api/*`.
- Las funciones leen variables de entorno del servidor; nunca se importan en `src/`.
- Los errores se devuelven como JSON `{ error: "mensaje" }` sin stack traces.
- El router valida método, ruta, `Origin` en mutaciones y sesión cuando corresponde.

---

## 5. Roles y autorización

| Rol | Alcance |
| :--- | :--- |
| `editor` | Catálogo e imágenes; sin borrar productos ni ver presupuestos. |
| `admin` | Catálogo completo y presupuestos; sin gestionar usuarios. |
| `super_admin` | Todo lo anterior + usuarios, roles y reseteo de contraseñas. |

- `lib/auth.js` expone `getSession`, `requireAuth([...roles])`, `setSessionCookie`, `clearSessionCookie`, `hashPassword` y `verifyPassword`.
- `lib/roles.js` centraliza `ROLES` y la matriz de permisos.
- El cliente recibe el rol desde `/api/admin/auth/me` y adapta la UI, pero el servidor es la fuente de verdad.

---

## 6. Reglas de oro

1. El SPA nunca importa `lib/db.js` ni lee `DATABASE_URL`.
2. Ningún componente escribe SQL ni concatena cadenas en consultas.
3. Las rutas `/admin/*` y `/api/admin/*` requieren sesión válida y rol autorizado.
4. Las imágenes se comprimen en el cliente, se suben al servidor y se almacenan en Cloudinary; en la base se guardan `url` y `public_id`.
5. Los textos visibles y nombres de dominio del negocio se mantienen en español.
6. El script `admin:reset` se ejecuta solo en local con `DATABASE_URL`; nunca se expone por API ni por ruta pública.
7. Resetear contraseña, cambiar rol o desactivar un usuario revoca todas sus sesiones.
8. Ninguna tarea se cierra sin actualizar su `verificacion.md`.
