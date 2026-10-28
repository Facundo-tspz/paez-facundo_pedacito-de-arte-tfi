# 03 - Elecciones Tecnológicas: Pedacito de Arte

Este documento define el stack, la infraestructura y los límites operativos del sistema web de **Pedacito de Arte**.

> **Decisión de stack (2026-10-28):** se utiliza **React 18 + Vite (SPA)**, **Netlify Functions 2.0**, **PostgreSQL en Neon** y **Cloudinary**. **Next.js no forma parte del proyecto.** La decisión está sujeta a la confirmación de la cátedra en la clase de consulta.

---

## 1. Stack Tecnológico Oficial

### Frontend y maquetación

| Tecnología | Versión | Función | Justificación |
| :--- | :--- | :--- | :--- |
| **React** | 18+ | Interfaz basada en componentes | Catálogo, carrito, panel y estados complejos de forma modular. |
| **Vite** | 5+ | Bundler y servidor de desarrollo | Build rápido, HMR y salida estática simple para Netlify. |
| **React Router** | 6+ | Enrutamiento SPA | Rutas públicas y panel protegido sin recargas. |
| **Tailwind CSS** | 3+ | Estilos utility-first | Tokens de marca en `tailwind.config.js`; sin HEX sueltos. |
| **DaisyUI** | 4+ | Componentes sobre Tailwind | Tarjetas, botones, modales y skeletons accesibles. |

### Utilidades y librerías de apoyo

| Librería | Función y motivo |
| :--- | :--- |
| **Lucide React** | Íconos SVG livianos. |
| **Sonner** | Toasts de confirmación y error. |
| **Framer Motion** | Transiciones sutiles; opcional si afecta el rendimiento. |
| **React Hook Form** | Formularios de admin y consultas. |
| **browser-image-compression** | Compresión previa a la subida desde el panel. |
| **qrcode.react** | QR para ferias; opcional. |
| **react-helmet-async** | Metadatos por pantalla. |
| **vite-react-ssg** | Prerenderizado de rutas públicas; opcional según tiempo. |

### Backend, datos, storage y deploy

| Componente | Tecnología | Justificación |
| :--- | :--- | :--- |
| **API / Backend** | **Netlify Functions 2.0** (`netlify/functions/api.js`) | Única capa de acceso a datos; router interno para `/api/*`; sin exponer credenciales. |
| **Driver de base** | **@neondatabase/serverless** | Pensado para entornos serverless; pooling y conexiones eficientes. |
| **Base de datos** | **PostgreSQL en Neon** | Relacional, SQL estándar, plan gratuito suficiente para el MVP. |
| **Storage de imágenes** | **Cloudinary** | CDN, transformaciones y URL + `public_id`; sin filesystem efímero. |
| **Autenticación** | Propia: `bcryptjs` + sesiones opacas en PostgreSQL + cookie `HttpOnly` | Roles `super_admin`, `admin`, `editor`; sin proveedores externos. |
| **Control de versiones** | Git + GitHub | Historial, repo público y deploy continuo. |
| **Hosting** | **Netlify** | Deploy desde GitHub, SSL, Functions y previews. |
| **WhatsApp** | Enlace `wa.me` con mensaje prellenado | Sin API Business, sin costo y sin credenciales adicionales. |
| **Calidad** | ESLint + Prettier + Vitest + Testing Library | Lint, formato y pruebas desde la Fase 1. |

---

## 2. Justificación del cambio respecto de Next.js + Vercel

La propuesta de la cátedra sugiere Next.js + Vercel. Para este proyecto se adopta Vite + Netlify + Neon + Cloudinary por estas razones:

1. **Alcance real:** el sitio es una SPA con un panel interno; no necesita SSR, ISR ni rutas de servidor de Next.js.
2. **Costo:** el plan gratuito de Vercel tiene límites de uso comercial y facturación; Netlify Free permite uso comercial con 300 créditos/mes, suficiente si se controlan los deploys.
3. **Base de datos:** Neon Free ofrece 1 GB y 100 CU-horas/mes, con scale-to-zero; no requiere tarjeta.
4. **Imágenes:** Cloudinary Free ofrece 25 créditos/mes y CDN; evita el filesystem efímero de Vercel.
5. **Simplicidad operativa:** un solo repo, un solo sitio Netlify, Functions 2.0 y una base PostgreSQL estándar.
6. **Mantenibilidad:** Vite + React 18 es el stack que el estudiante puede explicar y defender.

**Contrapartidas:** Netlify Functions tiene menos ecosistema que Vercel; el SPA requiere fallback y cuidado con SEO; Cloudinary y Neon tienen cuotas que deben monitorearse. Estas contrapartidas se aceptan y se documentan.

La confirmación de la cátedra queda pendiente. Si la cátedra exige Next.js, el cambio se registra en `docs/CAMBIOS-DOCUMENTACION.md` antes de implementar.

---

## 3. Límites de los planes gratuitos

| Servicio | Límite relevante | Impacto | Mitigación |
| :--- | :--- | :--- | :--- |
| **Netlify Free** | 300 créditos/mes; 15 por deploy de producción; 20/GB bandwidth; 2/10k requests; 10/GB-hora de cómputo | Se agota con muchos deploys o tráfico alto | Agrupar cambios, evitar deploys innecesarios y monitorear el panel |
| **Neon Free** | 1 GB de storage; 100 CU-horas/mes; 5 GB transferencia; scale-to-zero a los 5 min | Consultas pesadas o muchas conexiones consumen cuota | Pooling, consultas indexadas y evitar bucles de conexión |
| **Cloudinary Free** | 25 créditos/mes; 1 crédito = 1 GB storage, 1 GB bandwidth o 1.000 transformaciones | Imágenes sin optimizar o muchas variantes | Comprimir, limitar tamaños y reutilizar transformaciones |
| **GitHub Free** | Repo público; Actions limitadas | No crítico para el MVP | Deploy desde Netlify, no desde Actions |

> Verificar los límites vigentes antes de publicar. No se depende de tarjetas de crédito para el MVP.

---

## 4. Tecnologías no permitidas

- **Next.js** como framework principal.
- Angular, Vue.js o Svelte.
- MongoDB, Firebase u otras bases NoSQL/BaaS.
- Auth0, Clerk, Supabase Auth o Firebase Auth.
- Bootstrap o Material UI.
- Pasarelas de pago o cuentas de cliente en la Fase 1.
- Vercel Blob como storage principal.

---

## 5. Diagrama de conexión

```text
[ NAVEGADOR CLIENTE / MÓVIL ]
 ├── React 18 + Vite + React Router
 ├── Tailwind CSS + DaisyUI (tokens de marca)
 ├── react-helmet-async (metadatos)
 └── WhatsApp (wa.me)
            │
       HTTPS / fetch
            │
            ▼
[ NETLIFY: SPA + FUNCTIONS 2.0 ]
 ├── /api/*  →  netlify/functions/api.js
 ├── lib/db.js  →  PostgreSQL (Neon)
 ├── lib/auth.js  →  sesiones y roles
 └── lib/cloudinary.js  →  Cloudinary
            │
            ├──► [ POSTGRESQL (NEON) ]
            ├──► [ CLOUDINARY ] (imágenes)
            └──► [ GITHUB ] (repo y deploy)
```

---

## 6. Variables de entorno

| Variable | Ámbito | Uso |
| :--- | :--- | :--- |
| `DATABASE_URL` | Servidor | Conexión a PostgreSQL en Neon con `sslmode=require`. |
| `AUTH_SECRET` | Servidor | Firma de tokens CSRF y utilidades de autenticación. |
| `SESSION_TTL_HOURS` | Servidor | Duración de la sesión del admin; por defecto `8`. |
| `CLOUDINARY_CLOUD_NAME` | Servidor | Nombre de la cuenta. |
| `CLOUDINARY_API_KEY` | Servidor | API key. |
| `CLOUDINARY_API_SECRET` | Servidor | API secret; nunca en el cliente. |
| `VITE_WHATSAPP_PHONE_NUMBER` | Cliente | Número del taller sin `+` ni espacios. |

Todas las variables se documentan en `.env.example` con valores de ejemplo, nunca reales.

---

## 7. Operación y monitoreo

- Revisar semanalmente los créditos de Netlify, Neon y Cloudinary.
- Evitar deploys de producción para cambios triviales; usar previews solo si no consumen cuota.
- Medir el peso de imágenes antes de subirlas.
- No ejecutar migraciones destructivas sin backup.
- Registrar incidentes en `docs/PROGRESO.md`.
