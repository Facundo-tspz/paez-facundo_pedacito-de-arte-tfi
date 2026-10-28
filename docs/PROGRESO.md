# Bitácora de Progreso: Pedacito de Arte

Este archivo es la **memoria de trabajo** del proyecto. Lo usa el agente/IA para registrar cada instrucción, lo que hizo y en qué estado quedó todo, de modo que se pueda retomar el trabajo en otro chat o con otra IA sin perder contexto.

---

## Cómo usar este archivo

1. Antes de empezar una instrucción, leer la sección **Estado actual** y las **Decisiones vigentes**.
2. Al terminar cada instrucción, agregar una entrada en la **Bitácora** con fecha, pedido, acciones, archivos tocados y pendientes.
3. Si una decisión cambia, actualizarla en **Decisiones vigentes** y dejar constancia en la bitácora.
4. No borrar entradas anteriores: la bitácora es acumulativa.
5. Mantener este archivo sincronizado con `docs/CAMBIOS-DOCUMENTACION.md` cuando el cambio sea de documentación.

---

## Estado actual

- **Fecha de última actualización:** 2026-10-28
- **Fase:** Fase 0 en curso. Documentación, arnés SDD y estructura base creados. **No hay código de aplicación todavía.**
- **Etapa del roadmap:** Etapa 0 y 0.1 casi completas. Próxima: Etapa 1 (setup de React + Vite).
- **Repositorio Git:** inicializado y publicado en `main` (commit `8595e7b`). URL actual: <https://github.com/Facundo-tspz/paez-facundo_pedacito-de-arte-tfi>.
- **Renombre pendiente:** `iest-tfi_pedacito-de-arte` (el usuario lo confirma en GitHub).
- **Cuentas:** Netlify disponible. Neon y Cloudinary pendientes de crear.
- **Archivos existentes:** `AGENTS.md`, `CLAUDE.md`, `memory.md`, `README.md`, `docs/`, `specs/`, `.agents/`, `.opencode/`, `public/marca/`, `marca/`, `multimedia/`.
- **Build/DEV:** no aplica (no hay `package.json`).

---

## Decisiones vigentes

| Tema | Decisión |
| :--- | :--- |
| Stack frontend | React 18 + Vite (SPA) + React Router |
| Estilos | Tailwind CSS + DaisyUI con tokens de marca |
| Backend | Netlify Functions 2.0 en `netlify/functions/api.js` (`/api/*`) |
| Base de datos | PostgreSQL en Neon vía `@neondatabase/serverless` (`lib/db.js`) |
| Storage de imágenes | Cloudinary, con `url` y `public_id` en PostgreSQL |
| Autenticación | Login propio multiusuario (usuario + contraseña), roles `super_admin`, `admin` y `editor` |
| Sesiones admin | Token opaco + cookie `httpOnly`, 8 horas, revocables |
| Recuperación | Super admin resetea a otros; emergencia con script local `admin:reset` |
| Alcance Fase 1 | Catálogo, personalización, carrito, WhatsApp, Servicios, Contacto y Admin |
| WhatsApp | Número real por `.env`; no se hardcodea |
| Productos | Cuatro líneas: Agendas, Agendas Docentes, Revistas para Colorear, Anotadores |
| Precios | “A presupuestar” hasta confirmar con la emprendedora |
| SEO | `react-helmet-async`; prerender con `vite-react-ssg` si el tiempo alcanza |
| Calidad | ESLint + Prettier + Vitest + Testing Library |
| SDD | `specs/`, `.agents/skills/`, `.opencode/commands/` |
| Next.js | **Descartado** |
| Vercel / Vercel Blob | **Descartados** |

---

## Próximos pasos

1. Confirmar en clase el alcance del viernes, la aceptación del stack, el nombre del repo y los anexos.
2. Renombrar el repo en GitHub y actualizar el remote; usar email noreply para commits.
3. Crear Neon y Cloudinary; cargar variables en `.env` y Netlify.
4. Ejecutar la **Etapa 1** de `docs/08-TAREAS.md`: inicializar React + Vite, instalar dependencias, configurar Tailwind/tokens, ESLint/Prettier/Vitest y assets.
5. Marcar las casillas de `08-TAREAS.md` a medida que se completen.
6. Registrar cada avance en la bitácora de abajo.

---

## Bitácora

### 2026-10-28 — Instrucción: "Analiza mi trabajo"

- **Pedido:** analizar el estado del proyecto.
- **Hallazgo principal:** solo existían documentación y assets de marca; **no había código**. Además se detectaron contradicciones (Next.js vs Vite), falta de capa backend, SEO con metas inválidas, esquema de presupuesto incompleto, CTA con contraste insuficiente y pantallas ausentes del roadmap.
- **Acciones:** análisis de los 13 documentos y de `marca/`; sin cambios en archivos.
- **Resultado:** se presentó un informe con problemas críticos, inconsistencias y un plan propuesto.

### 2026-10-28 — Instrucción: definición de stack y alcance

- **Pedido:** responder las preguntas pendientes.
- **Respuestas del usuario:**
  1. Usar **React + Vite**.
  2. **Todo incluido** (Servicios, Contacto y Admin).
  3. Usar un **número de WhatsApp de ejemplo** por ahora.
- **Acciones:** se registraron las decisiones y se propuso un plan de 10 fases.

### 2026-10-28 — Instrucción: actualizar documentación y crear MDs de cambios/progreso

- **Pedido:** aplicar solo cambios de documentación (sin codear), crear un MD de cambios con el porqué y un MD de progreso reanudable.
- **Acciones realizadas:**
  - `AGENTS.md`: stack a React + Vite, frontera de datos, lectura obligatoria ampliada, aclaración del admin.
  - `docs/01-CONTEXTO.md`: objetivo de panel admin y aclaración de alcance.
  - `docs/02-REQUERIMIENTOS.md`: RF09, RF10, RNF04 actualizado y RNF06.
  - `docs/03-TECNOLOGIAS.md`: backend serverless, Neon serverless, Vercel Blob, auth, SEO, calidad y variables.
  - `docs/04-ARQUITECTURA.md`: árbol con `api/`, `lib/`, `db/`, admin, storage y flujo de datos.
  - `docs/05-DATOS.md`: `producto_imagenes`, `presupuesto_items`, recargos/requeridas y SQL/seed actualizados.
  - `docs/06-DISENO-UI.md`: tokens en lugar de HEX, CTA accesible y pantallas de Servicios/Contacto/Admin/404.
  - `docs/07-CONVENCIONES.md`: convenciones de API/SQL/env, commits `test`/`chore`, pruebas y variables.
  - `docs/08-TAREAS.md`: Etapa 0 completada y etapas 1 a 9 con Servicios, Contacto, Admin y SEO/QA.
  - `docs/09-SEO.md`: metas corregidas, prerender, sitemap/robots, JSON-LD y accesibilidad.
  - `docs/10-SEGURIDAD.md`: frontera cliente/servidor, auth admin, uploads, cabeceras y checklist.
  - `docs/IDENTIDAD-MARCA.md`: assets reales, ruta de estilos Vite y token `acento`.
  - Creados: `docs/CAMBIOS-DOCUMENTACION.md` y `docs/PROGRESO.md`.
- **Archivos creados:** `docs/CAMBIOS-DOCUMENTACION.md`, `docs/PROGRESO.md`.
- **Archivos modificados:** `AGENTS.md`, `docs/01-CONTEXTO.md`, `docs/02-REQUERIMIENTOS.md`, `docs/03-TECNOLOGIAS.md`, `docs/04-ARQUITECTURA.md`, `docs/05-DATOS.md`, `docs/06-DISENO-UI.md`, `docs/07-CONVENCIONES.md`, `docs/08-TAREAS.md`, `docs/09-SEO.md`, `docs/10-SEGURIDAD.md`, `docs/IDENTIDAD-MARCA.md`.
- **Verificación:** revisión de coherencia entre documentos; 14 archivos Markdown con bloques de código balanceados; sin clases HEX arbitrarias en `06`; sin referencias a Next.js salvo las decisiones explícitas. No se ejecutó build (no hay proyecto aún).
- **Pendiente:** Etapa 1. Confirmar número real de WhatsApp, dominio y storage definitivo.

### 2026-10-28 — Instrucción: definir login y panel propios (multiusuario)

- **Pedido:** crear login y admin propios, sin proveedores ajenos; varios usuarios con roles.
- **Decisiones del usuario:**
  1. Roles: `super_admin`, `admin` y `editor`.
  2. Login con **usuario + contraseña**, sin email.
  3. Reset de contraseñas a cargo del `super_admin`; el botón de olvido indica contactar al administrador principal.
  4. Recuperación del super admin mediante **script local**.
  5. Sesión de **8 horas**; sin 2FA por ahora.
- **Acciones realizadas (solo documentación):**
  - `docs/05-DATOS.md`: tablas `admin_usuarios`, `sesiones_admin`, `admin_intentos_login` y `admin_auditoria`; DDL, índices y reglas.
  - `docs/10-SEGURIDAD.md`: sección de autenticación reescrita (roles, login, sesiones, bloqueo, reset, emergencia, auditoría); se quitó `ADMIN_PASSWORD`.
  - `docs/03-TECNOLOGIAS.md`: auth propia multiusuario y variables `AUTH_SECRET` / `SESSION_TTL_HOURS`.
  - `docs/04-ARQUITECTURA.md`: endpoints de auth/perfil/usuarios, helpers y páginas de admin.
  - `docs/07-CONVENCIONES.md`: `ROLES`, `requireAuth`, scripts `admin:reset`/`admin:list`.
  - `docs/02-REQUERIMIENTOS.md`: RF09 ampliado, RF11, RNF07, HU-02 y criterios.
  - `docs/06-DISENO-UI.md`: login, olvido de contraseña, usuarios, perfil, modal de reset y estados.
  - `docs/08-TAREAS.md`: Etapa 7 reescrita con usuarios, roles y recuperación.
  - `docs/01-CONTEXTO.md` y `AGENTS.md`: alcance multiusuario y prohibiciones.
  - `docs/CAMBIOS-DOCUMENTACION.md`: sección 6 con la revisión.
- **Verificación:** coherencia entre documentos y bloques Markdown balanceados. No se ejecutó build (no hay proyecto aún).
- **Pendiente:** Etapa 1. Toda la implementación de auth queda para la Etapa 7.

### 2026-10-28 — Instrucción: crear arnés SDD y estructura del repositorio

- **Pedido:** avanzar con el TFI, ordenar el repo y preparar la entrega del viernes.
- **Decisiones del usuario:**
  1. Autoriza el renombre del repositorio a `iest-tfi_pedacito-de-arte`.
  2. Autoriza usar el email noreply de GitHub.
  3. Creará las cuentas de Neon y Cloudinary.
  4. Llevará las preguntas pendientes a la clase de consulta.
- **Acciones realizadas:**
  - Migración documental de Vercel/Blob a Netlify/Neon/Cloudinary.
  - Creación de `docs/CONSTITUCION.md`, `docs/PROMPTS.md`, `docs/DECLARACION-IA.md` y `docs/11-GITHUB-Y-DESPLIEGUE.md`.
  - Creación de `README.md`, `CLAUDE.md`, `memory.md`, `.gitignore`, `.gitattributes`, `.env.example` y `opencode.json`.
  - Creación de las specs `001-catalogo`, `002-presupuesto-whatsapp` y `003-panel-admin` con `spec`, `clarificacion`, `plan`, `tasks` y `verificacion`.
  - Creación de seis comandos en `.opencode/commands/` y tres skills en `.agents/skills/`.
  - Copia de los assets de marca a `public/marca/`.
  - Ajuste del esquema: `slug` de producto, `imagen_public_id`, `imagen_alt`, `public_id` de galería y URLs a 500 caracteres.
  - Seed actualizado a las cuatro líneas reales y sin marcas infantiles registradas.
- **Verificación:** repositorio inicializado, commit `8595e7b` creado y push a `origin/main` exitoso. Las referencias restantes a Vercel/Blob son históricas o prohibiciones explícitas.
- **Pendiente:** renombre del repo, Neon, Cloudinary, fotos reales, respuestas de la cátedra y scaffold de Vite.

---

## Preguntas abiertas

- ¿La entrega del viernes incluye solo repo/sitio o también informe, video y multimedia?
- ¿La cátedra acepta Vite + Netlify + Neon + Cloudinary?
- ¿Cuál es el nombre definitivo del repositorio y dónde se entregan los Anexos I-IV?
- ¿Cuál es el número real de WhatsApp del taller?
- ¿Cuál es el dominio definitivo? (actual en SEO: `pedacitodearte.com`)
- ¿Se agregará un segundo `super_admin` de respaldo además del script local de emergencia?
- ¿RF03 (búsqueda) y RF10 (registro de presupuestos) entran en el MVP?

---

## Registro de archivos clave

| Archivo | Rol |
| :--- | :--- |
| `AGENTS.md` | Reglas obligatorias para agentes/IA. |
| `CLAUDE.md` | Puntero a `AGENTS.md` y reglas mínimas. |
| `memory.md` | Memoria operativa reanudable. |
| `docs/CONSTITUCION.md` | Principios obligatorios y verificables. |
| `docs/IDENTIDAD-MARCA.md` | Marca, paleta, tipografías y tokens. |
| `docs/01` a `docs/11` | Especificación funcional y técnica. |
| `docs/PROMPTS.md` | Registro de prompts y errores de IA. |
| `docs/DECLARACION-IA.md` | Declaración de uso de IA. |
| `docs/08-TAREAS.md` | Roadmap ejecutable por etapas. |
| `docs/CAMBIOS-DOCUMENTACION.md` | Qué cambió en la documentación y por qué. |
| `docs/PROGRESO.md` | Este archivo: estado y bitácora reanudable. |
| `specs/` | Tres specs SDD con verificación. |
| `.agents/skills/` | Skills del proyecto. |
| `.opencode/commands/` | Comandos reutilizables. |
| `marca/` y `public/marca/` | Assets fuente y públicos de la identidad visual. |
| `multimedia/` | Piezas IA, video, gráficos y fichas. |
