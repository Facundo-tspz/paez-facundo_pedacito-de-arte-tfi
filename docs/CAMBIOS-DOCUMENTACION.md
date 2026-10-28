# Registro de Cambios de Documentación: Pedacito de Arte

Este documento registra **qué se cambió en la documentación, por qué se cambió y qué decisiones quedaron tomadas**. Su objetivo es que cualquier persona o IA pueda entender la evolución de la especificación sin revisar el historial completo.

> **Fecha de esta revisión:** 2026-10-28
> **Alcance:** solo documentación. No se escribió código, no se inicializó el proyecto y no se modificaron assets de `marca/`.
> **Contenido:** incluye las revisiones del mismo día: definición base del proyecto, autenticación multiusuario del panel y migración final de stack a Netlify + Neon + Cloudinary.
> **Nota de lectura:** las secciones 1 a 6 conservan el estado histórico de la documentación. El stack vigente y las decisiones finales están en la **sección 7**.

---

## 1. Decisiones tomadas en esta revisión

| Tema | Decisión | Motivo |
| :--- | :--- | :--- |
| **Stack** | **React 18 + Vite (SPA)**. Se descarta Next.js. | `AGENTS.md` decía Next.js, pero `03`, `04` y `08` especificaban React + Vite. Se eligió Vite por decisión del usuario. |
| **Backend** | **Vercel Serverless Functions** en `/api` + `@neondatabase/serverless`. | Un SPA no puede acceder a PostgreSQL sin exponer `DATABASE_URL`. Las Functions son la única capa autorizada a usar `lib/db.js`. |
| **Alcance Fase 1** | Catálogo + personalización + carrito + WhatsApp + **Servicios + Contacto + Admin**. | El usuario pidió incluir todo. Servicios (RF07) y Contacto (RF08) estaban en requerimientos pero ausentes del roadmap. |
| **Panel admin** | Panel interno protegido, **no** una cuenta de cliente. | Permite gestionar catálogo e imágenes sin tocar SQL; no contradice la exclusión de cuentas de usuario. |
| **Imágenes** | Compresión en cliente + almacenamiento en **Vercel Blob** (alternativa Cloudinary). | El filesystem de Vercel es efímero; `browser-image-compression` necesitaba un caso de uso real (el admin). |
| **WhatsApp** | Número de ejemplo `5491100000000` vía `VITE_WHATSAPP_PHONE_NUMBER`. | El número real aún no fue provisto. Se reemplaza antes del despliegue. |
| **SEO en SPA** | `react-helmet-async` + prerender con `vite-react-ssg` + sitemap/robots/JSON-LD. | Una SPA sola indexa mal; el documento de SEO no contemplaba cómo resolverlo. |
| **Contraste CTA** | Se agrega `--color-secundario-acento: #C14E33` (derivado del Rojo Coral). | Blanco sobre `#E76F51` da ≈ 3.1:1 y no cumple WCAG AA. La variante da ≈ 4.8:1. |
| **Calidad** | ESLint + Prettier + Vitest + Testing Library. | No existía estrategia de lint ni pruebas y `AGENTS.md` exige verificación. |

---

## 2. Cambios archivo por archivo

### `AGENTS.md`
- **Stack principal:** de `Next.js (React)` a `React 18 + Vite (SPA) + PostgreSQL (Neon) + Vercel Serverless Functions + WhatsApp`.
- **Regla de base de datos:** ahora aclara que `lib/db.js` se ejecuta en `/api` y que el cliente nunca accede a `DATABASE_URL` ni escribe SQL.
- **Lista de lectura obligatoria:** se agregaron `CAMBIOS-DOCUMENTACION.md` y `PROGRESO.md`.
- **Prohibiciones:** se aclaró que el panel admin es interno y no es una cuenta de cliente.
- **Por qué:** eliminar la contradicción de stack y dejar explícita la frontera cliente/servidor.

### `docs/01-CONTEXTO.md`
- Se agregó el objetivo 4: panel de administración interno.
- Se aclaró que el admin no forma parte de las cuentas de cliente excluidas.
- **Por qué:** el alcance ahora incluye gestión de catálogo y presupuestos.

### `docs/02-REQUERIMIENTOS.md`
- Se agregaron **RF09** (panel admin) y **RF10** (registro de presupuestos).
- **RNF04** ahora exige contraste WCAG AA y navegación por teclado.
- Se agregó **RNF06** (SEO, Open Graph y prerender).
- **Por qué:** reflejar el alcance completo y hacer medible la accesibilidad/SEO.

### `docs/03-TECNOLOGIAS.md`
- Se dejó constancia explícita de que **Next.js no se usa**.
- Se agregó la capa backend (Vercel Functions, Neon serverless), storage (Vercel Blob), autenticación admin y librerías de SEO/calidad.
- Se actualizó el diagrama de conexión y se agregó la tabla de variables de entorno.
- **Por qué:** faltaba definir cómo el SPA accede a la base y dónde se guardan las imágenes.

### `docs/04-ARQUITECTURA.md`
- Se agregaron `api/`, `lib/db.js`, `db/esquema.sql`, `db/seed.sql`, `vercel.json`, `README.md` y `.env.example`.
- Se agregaron páginas y componentes de admin, `AuthContext`, `useAuth`, `AdminLayout`, `authService`, `storageService`.
- Se sumaron `robots.txt` y `sitemap.xml` en `public/`.
- Se documentaron las capas (presentación, servicios, API, datos, storage) y el flujo de datos.
- **Por qué:** el árbol anterior no contemplaba backend, admin ni storage; además duplicaba `api.js` como si fuera conexión a base.

### `docs/05-DATOS.md`
- Se agregó `producto_imagenes` (galería con `alt` obligatorio).
- Se amplió `opciones_personalizacion` con `recargo`, `requerida`, `multiple` y `orden`.
- Se amplió `solicitudes_presupuesto` con datos de cliente y observaciones.
- Se agregó `presupuesto_items` (detalle histórico con opciones en JSONB).
- Se agregaron índices, `creado_en`, `actualizado_en` y reglas de integridad/negocio.
- **Por qué:** sin `presupuesto_items` el presupuesto guardado no era reconstruible y las opciones no podían afectar el precio.

### `docs/06-DISENO-UI.md`
- Se reemplazaron los HEX hardcodeados por tokens del theme (`bg-fondo`, `text-principal`, `bg-dorado`, etc.).
- Se documentó `acento` para el CTA y se explicó por qué no usar `bg-secundario` con texto blanco.
- Se agregaron las pantallas de Servicios, Contacto, Admin y 404.
- Se sumaron estados vacíos, foco visible y validación accesible.
- **Por qué:** la propia guía prohibía HEX sueltos y el CTA no cumplía contraste.

### `docs/07-CONVENCIONES.md`
- Se agregaron convenciones de handlers serverless, tablas SQL y variables de entorno.
- Se sumaron los commits `test` y `chore`.
- Se agregaron secciones de acceso a datos/API, pruebas/calidad y variables de entorno.
- **Por qué:** faltaban reglas para el backend y para verificar el trabajo.

### `docs/08-TAREAS.md`
- Se agregó la **Etapa 0 (documentación, completada)**.
- Se incorporaron las etapas de **API/datos**, **Servicios/Contacto**, **Admin** y **SEO/Calidad**.
- Se actualizaron las tareas de setup (librerías, lint/test, `.env.example`, logo SVG).
- Se movió el deploy a la Etapa 9 y se agregaron pruebas productivas.
- **Por qué:** el roadmap anterior terminaba en el carrito y no contemplaba backend, admin ni las pantallas faltantes.

### `docs/09-SEO.md`
- Se corrigieron las URLs de Open Graph y Twitter que estaban envueltas en Markdown dentro de `<meta content>`.
- Se agregaron títulos/canonical por pantalla, prerender, `robots.txt`, `sitemap.xml` y JSON-LD.
- Se amplió accesibilidad (contraste, foco, formularios, `alt`, skip link, noindex admin).
- **Por qué:** el HTML de metas era inválido y la SPA necesitaba una estrategia de indexación.

### `docs/10-SEGURIDAD.md`
- Se definió la frontera cliente/servidor y el uso exclusivo de `/api`.
- Se agregaron login multiusuario, roles, sesiones opacas, bloqueo por intentos, reset de contraseñas, recuperación local del super admin, CSRF y auditoría.
- Se agregaron reglas de subida de imágenes, datos personales, cabeceras de seguridad y un checklist previo a publicar.
- **Por qué:** el documento original solo cubría variables de entorno e inyección SQL; con admin y uploads el riesgo crece.

### `docs/IDENTIDAD-MARCA.md`
- Se corrigió la ubicación del logo: ahora `public/logo.svg` + `public/favicon.ico`, con los nombres reales de `marca/` y la tarea de exportar a SVG/ICO.
- Se cambió `app/globals.css` (Next) por `src/styles/index.css` (Vite) y se indicó reflejar los tokens en Tailwind.
- Se agregó `--color-secundario-acento` como variante accesible derivada.
- Se cerró el bloque de código CSS que había quedado sin delimitador final.
- **Por qué:** el documento contradecía el stack elegido, no había logo vectorial y el bloque de tokens rompía el renderizado Markdown.

---

## 3. Contradicciones resueltas

| Antes | Ahora |
| :--- | :--- |
| `AGENTS.md`: Next.js | React + Vite en todos los documentos. |
| `IDENTIDAD-MARCA.md`: `app/globals.css` | `src/styles/index.css` + theme de Tailwind. |
| `lib/db.js` sin ubicación en el árbol | `lib/db.js` + `api/` documentados. |
| `src/services/api.js` como "conexión a DB" | Cliente HTTP del SPA; la DB solo se toca en `lib/db.js`. |
| `06`: HEX hardcodeados | Tokens de Tailwind. |
| `09`: URLs Markdown en `content` | URLs absolutas planas. |
| `solicitudes_presupuesto` sin detalle | `presupuesto_items` agregado. |
| Servicios/Contacto/Admin fuera del roadmap | Etapas 6 y 7 agregadas. |

---

## 4. Supuestos y preguntas abiertas

1. **Storage de imágenes:** ~~se asumió Vercel Blob~~. Superado por la sección 7: se adopta **Cloudinary** con `url` + `public_id`.
2. **Número de WhatsApp real:** pendiente. Se usa `5491100000000` de ejemplo.
3. **Dominio real:** pendiente. Se usa `pedacitodearte.com` en SEO.
4. **Prenderizado:** se asumió `vite-react-ssg`. Si genera fricción con el admin, se puede limitar a las rutas públicas.
5. **Auth admin:** resuelto en la sección 6: multiusuario con roles, tablas propias y script de emergencia local.

---

## 5. Próximo paso

Ejecutar la **Fase 0** (repo, arnés SDD y primer push) y luego la **Etapa 1** de `08-TAREAS.md` (inicialización del proyecto). Antes de codear, confirmar con la cátedra el alcance del viernes y el stack.

---

## 6. Revisión: autenticación multiusuario (2026-10-28)

### 6.1 Decisiones

| Tema | Decisión | Motivo |
| :--- | :--- | :--- |
| **Roles** | `super_admin`, `admin`, `editor` | Permite dar acceso controlado al equipo del taller. |
| **Login** | Usuario + contraseña, **sin email** | Pedido explícito del usuario. |
| **Permisos** | Matriz aprobada (editor catálogo, admin catálogo+presupuestos, super_admin todo) | Separar responsabilidades sin cuentas de cliente. |
| **Sesión** | Token opaco + cookie `httpOnly` de **8 horas** | Revocable y segura; duración acorde a la jornada. |
| **Intentos** | Bloqueo tras 5 fallos en 15 minutos | Mitigar fuerza bruta. |
| **Reset** | Solo `super_admin`; temporal mostrada una vez, cambio obligatorio y revocación de sesiones | El usuario olvidadizo no queda trabado y no se filtran contraseñas. |
| **Olvido** | El botón indica contactar al administrador principal; sin email | Evita depender de un proveedor de correo. |
| **Emergencia** | Script local `npm run admin:reset` con `DATABASE_URL` | Recupera al super admin sin exponer un endpoint público. |
| **2FA** | No por ahora | Se puede sumar más adelante sin rediseñar el modelo. |
| **Proveedores externos** | Prohibidos (Auth0, Clerk, Supabase Auth, Firebase) | Autenticación 100% propia sobre PostgreSQL. |

### 6.2 Cambios aplicados

- `docs/05-DATOS.md`: tablas `admin_usuarios`, `sesiones_admin`, `admin_intentos_login` y `admin_auditoria`; DDL, índices y reglas de integridad.
- `docs/10-SEGURIDAD.md`: sección 4 reescrita (roles, usuarios, sesiones, intentos, reset, recuperación del super admin, CSRF y auditoría); se eliminó `ADMIN_PASSWORD`.
- `docs/03-TECNOLOGIAS.md`: auth propia multiusuario y variables `AUTH_SECRET` / `SESSION_TTL_HOURS`.
- `docs/04-ARQUITECTURA.md`: endpoints de auth, perfil y usuarios; helpers `auth.js`/`roles.js`; páginas y reglas de autorización.
- `docs/07-CONVENCIONES.md`: constante `ROLES`, `requireAuth`, scripts `admin:reset`/`admin:list` y variables.
- `docs/02-REQUERIMIENTOS.md`: RF09 ampliado, RF11, RNF07, HU-02 y criterios de aceptación.
- `docs/06-DISENO-UI.md`: login, "olvidé mi contraseña", gestión de usuarios, perfil, modal de reset y estados de autenticación.
- `docs/08-TAREAS.md`: Etapa 7 reescrita con usuarios, roles, sesiones y recuperación.
- `docs/01-CONTEXTO.md` y `AGENTS.md`: alcance multiusuario y prohibiciones de auth externa/contraseñas en texto plano.
- `docs/PROGRESO.md`: estado, decisiones y bitácora actualizados.

### 6.3 Reglas clave

- Los permisos se validan **siempre en el servidor**; la UI solo se adapta.
- No se guardan contraseñas ni tokens en texto plano: `bcryptjs` y hash SHA-256.
- El último `super_admin` activo no se puede desactivar, degradar ni eliminar.
- El script de emergencia es local y nunca se expone por API ni por ruta pública.
- Resetear contraseña, cambiar rol o desactivar un usuario revoca todas sus sesiones.

---

## 7. Migración de stack: Netlify + Neon + Cloudinary (2026-10-28)

### 7.1 Decisión

| Tema | Antes | Ahora | Motivo |
| :--- | :--- | :--- | :--- |
| **Hosting** | Vercel | **Netlify** | Uso comercial permitido en el plan gratuito y 300 créditos/mes; el proyecto no necesita Next.js. |
| **API** | Vercel Functions (`/api/`) | **Netlify Functions 2.0** (`netlify/functions/api.js`, `config.path = "/api/*"`) | Router único, compatible con SPA y sin redirect manual de API. |
| **Storage** | Vercel Blob | **Cloudinary** | CDN, transformaciones y plan gratuito; se guardan `url` y `public_id`. |
| **Base** | Neon | **Neon** | Se mantiene PostgreSQL; 1 GB y 100 CU-horas/mes. |
| **Frontend** | React 18 + Vite | **React 18 + Vite** | Se mantiene; no se adopta Next.js. |

### 7.2 Límites considerados

- **Netlify Free:** 300 créditos/mes; 15 por deploy de producción; bandwidth y cómputo acotados.
- **Neon Free:** 1 GB, 100 CU-horas/mes, 5 GB de transferencia y scale-to-zero.
- **Cloudinary Free:** 25 créditos/mes; transformaciones y bandwidth comparten cuota.

### 7.3 Cambios aplicados

- `AGENTS.md`: stack, lectura obligatoria, storage, SDD y prohibiciones.
- `docs/02-REQUERIMIENTOS.md`: categorías reales, RNF05 y RNF08.
- `docs/03-TECNOLOGIAS.md`: stack, justificación, límites gratuitos, diagrama y variables.
- `docs/04-ARQUITECTURA.md`: árbol con `netlify/functions/api.js`, `lib/` y Cloudinary; endpoints y reglas.
- `docs/05-DATOS.md`: acceso por Netlify, `slug` de producto, `imagen_public_id`, `imagen_alt`, `public_id` de galería, URLs de 500 caracteres y seed de cuatro líneas.
- `docs/07-CONVENCIONES.md`: Functions, ramas, variables, scripts y flujo SDD.
- `docs/08-TAREAS.md`: Etapa 0.1, seed real, Cloudinary y Netlify.
- `docs/09-SEO.md`: rutas de imágenes, Netlify y prerender opcional.
- `docs/10-SEGURIDAD.md`: Netlify, Cloudinary, variables y cabeceras.
- `docs/11-GITHUB-Y-DESPLIEGUE.md`: repo, renombre, noreply, Netlify, Neon y Cloudinary.
- `docs/CONSTITUCION.md`, `docs/PROMPTS.md`, `docs/DECLARACION-IA.md`: arnés de IA.
- `specs/001-catalogo`, `specs/002-presupuesto-whatsapp`, `specs/003-panel-admin`: requisitos, decisiones, plan, tareas y verificación.
- `.agents/skills/` y `.opencode/commands/`: skills y comandos SDD.

### 7.4 Pendientes

- Confirmar con la cátedra si el stack es aceptado.
- Confirmar el alcance de la entrega del viernes y el formato de los anexos.
- Renombrar el repositorio y crear las cuentas de Neon y Cloudinary.
- Reemplazar fotos y datos provisionales por los reales.
