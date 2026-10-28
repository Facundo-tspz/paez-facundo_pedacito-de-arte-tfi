# 08 - Plan de Tareas y Hoja de Ruta: Pedacito de Arte

Este documento especifica la secuencia estructurada de etapas y tareas técnicas requeridas para construir la plataforma web de **Pedacito de Arte**. La IA o el desarrollador debe ejecutar estas tareas de a una por vez y marcar con `[x]` cada casilla a medida que se completen.

> **Stack vigente:** React 18 + Vite (SPA) + Netlify Functions 2.0 + PostgreSQL (Neon) + Cloudinary + WhatsApp. **No se usa Next.js ni Vercel Blob.**
> **Alcance Fase 1:** catálogo, personalización, carrito de presupuesto, WhatsApp, Servicios, Contacto y panel de administración interno.
> **Fecha objetivo:** MVP viernes 30-10-2026; evaluación lunes 02-11-2026.

---

## Etapa 0: Documentación (completada)
- [x] Unificar el stack a React + Vite en `AGENTS.md`, `03`, `04`, `08` e `IDENTIDAD-MARCA.md`.
- [x] Incorporar Servicios, Contacto y panel de administración al alcance, requerimientos y arquitectura.
- [x] Corregir el modelo de datos (items de presupuesto, recargos, galería e imágenes).
- [x] Corregir tokens de color, contraste del CTA, metas SEO rotas y políticas de seguridad.
- [x] Documentar la autenticación propia multiusuario, roles, sesiones y recuperación del super admin.
- [x] Crear `docs/CAMBIOS-DOCUMENTACION.md` y `docs/PROGRESO.md`.

---

## Etapa 0.1: Arnés SDD y repositorio (casi completa)
- [x] Crear `docs/CONSTITUCION.md` con principios verificables.
- [x] Crear `docs/PROMPTS.md` con tres errores de IA documentados.
- [x] Crear `docs/DECLARACION-IA.md` y `multimedia/FICHAS-IA.md`.
- [x] Crear `docs/11-GITHUB-Y-DESPLIEGUE.md`.
- [x] Crear las specs `001-catalogo`, `002-presupuesto-whatsapp` y `003-panel-admin`.
- [x] Crear los comandos `/spec`, `/clarificar`, `/plan`, `/tareas`, `/verificar` y `/cambio`.
- [x] Crear las skills `verificar-criterios`, `escribir-spec-ears` y `revisar-multimedia-ia`.
- [x] Copiar los assets de marca a `public/marca/`.
- [x] Inicializar Git, configurar email noreply, commit y push (`8595e7b`).
- [ ] Confirmar el renombre del repositorio y el stack con la cátedra.

---

## Etapa 1: Configuración Inicial e Identidad Visual
- [ ] Inicializar el proyecto con React + Vite en el repositorio ya creado.
- [ ] Instalar e integrar Tailwind CSS, DaisyUI, Lucide React, Sonner y Framer Motion.
- [ ] Instalar React Router, React Hook Form, react-helmet-async, vite-react-ssg, qrcode.react y browser-image-compression.
- [ ] Configurar `tailwind.config.js` y `src/styles/index.css` con los tokens oficiales (`fondo`, `principal`, `dorado`, `secundario`, `acento`, `verde`, `contraste`) y las fuentes *Cormorant Garamond* y *Poppins*.
- [ ] Configurar ESLint, Prettier y Vitest + Testing Library, con scripts `dev`, `build`, `preview`, `lint` y `test`.
- [ ] Exportar el logo a `public/logo.svg` y el isotipo a `public/favicon.ico`; usar `public/marca/` y `public/media/productos/`, sin `public/img/`.
- [ ] Crear `.env.example` (sin secretos) y `.gitignore`; documentar variables en el README.
- [ ] Verificar que `npm run build` compile sin errores.

---

## Etapa 2: Base de Datos y Capa API
- [ ] Configurar la instancia de PostgreSQL en Neon y definir `DATABASE_URL` en `.env`.
- [ ] Ejecutar `db/esquema.sql` (categorías, productos, producto_imagenes, opciones_personalizacion, solicitudes_presupuesto, presupuesto_items y tablas de auth).
- [ ] Cargar `db/seed.sql` con las cuatro líneas reales (Agendas, Agendas Docentes, Revistas para Colorear, Anotadores) y opciones con recargo.
- [ ] Crear `lib/db.js` con `@neondatabase/serverless` y consultas parametrizadas.
- [ ] Crear `netlify/functions/api.js` con `config.path = "/api/*"` y `GET /api/health`.
- [ ] Implementar `GET /api/categorias` y `GET /api/productos` (listado y detalle por slug).
- [ ] Implementar `POST /api/presupuestos` (registra cabecera + items y devuelve el mensaje de WhatsApp).
- [ ] Crear `src/services/api.js` como cliente fetch del SPA.
- [ ] Probar los endpoints con `npm run dev:netlify` y datos reales.

---

## Etapa 3: Estructura de Navegación y Componentes Base (UI)
- [ ] Configurar el enrutador con React Router DOM en `src/routes/AppRouter.jsx` (rutas públicas y protegidas).
- [ ] Construir `MainLayout.jsx` con `Navbar.jsx` (contador del carrito) y `Footer.jsx`.
- [ ] Diseñar `TarjetaProducto.jsx` con badge de categoría, tokens de marca y `alt` obligatorio.
- [ ] Crear `SkeletonLoader.jsx`, estados vacíos y componentes base (`Boton`, `Badge`, `Modal`).
- [ ] Implementar el menú hamburguesa responsive y el botón flotante de WhatsApp.

---

## Etapa 4: Catálogo Interactivo, Filtros y Búsqueda
- [ ] Implementar `HomePage.jsx` con Hero, eslogan *"Papelería creativa hecha con amor"*, destacados y propuesta de valor.
- [ ] Desarrollar `CatalogPage.jsx` con buscador en tiempo real y filtros por categoría.
- [ ] Agregar paginado o carga incremental y ordenamiento (opcional).
- [ ] Crear `ProductDetailPage.jsx` con galería, selectores de personalización, recargos y campo de nota.
- [ ] Validar que las opciones requeridas bloqueen "Agregar al Presupuesto" hasta completarse.

---

## Etapa 5: Carrito de Presupuesto e Integración con WhatsApp
- [ ] Crear `CartContext.jsx` con agregar, modificar cantidad, eliminar, calcular subtotales y persistencia en `localStorage` (incluyendo variantes).
- [ ] Desarrollar `CarritoDrawer.jsx` e `ItemCarrito.jsx` con avisos de **Sonner**.
- [ ] Implementar `whatsappService.js` para formatear el mensaje y generar la URL `wa.me`.
- [ ] Integrar `POST /api/presupuestos` antes de abrir WhatsApp.
- [ ] Configurar `VITE_WHATSAPP_PHONE_NUMBER` en `.env`; usar un placeholder hasta recibir el real.
- [ ] Probar el flujo completo: selección → carrito → registro → WhatsApp.

---

## Etapa 6: Servicios y Contacto
- [ ] Desarrollar `ServicesPage.jsx` con bloques de diseño, impresión, diplomas, banners y encuadernación.
- [ ] Desarrollar `ContactPage.jsx` con React Hook Form, validación accesible y `aria-live`.
- [ ] Implementar el endpoint de consultas especiales (reutilizando `POST /api/presupuestos` o uno dedicado).
- [ ] Agregar `NotFoundPage.jsx` y enlazarla al router.
- [ ] Verificar navegación y enlaces desde el menú y el footer.

---

## Etapa 7: Panel de Administración Interno (multiusuario y roles)
- [ ] Instalar `bcryptjs` y configurar `AUTH_SECRET`, `SESSION_TTL_HOURS=8`, `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY` y `CLOUDINARY_API_SECRET`; sin contraseña única en el entorno.
- [ ] Crear las tablas de autenticación (`admin_usuarios`, `sesiones_admin`, `admin_intentos_login`, `admin_auditoria`) desde `db/esquema.sql`.
- [ ] Implementar el script local `db/crear-superadmin.js` y el comando `npm run admin:reset -- <usuario> --rol super_admin` (bcrypt, cambio obligatorio y revocación de sesiones).
- [ ] Implementar `lib/roles.js` (constante `ROLES` + matriz de permisos) y `lib/auth.js` (`getSession`, `requireAuth`, cookies y hash de token).
- [ ] Implementar `POST /api/admin/auth/login` (usuario + contraseña, error genérico, bloqueo tras 5 fallos en 15 minutos y auditoría).
- [ ] Implementar `POST /api/admin/auth/logout`, `GET /api/admin/auth/me` y `POST /api/admin/auth/cambiar-password`.
- [ ] Crear `AuthContext.jsx`, `useAuth.js`, `authService.js` y `AdminLayout.jsx` con guards por rol.
- [ ] Desarrollar `AdminLoginPage.jsx` con el modal de "¿Olvidaste tu contraseña?".
- [ ] Desarrollar `AdminDashboardPage.jsx` adaptado al rol.
- [ ] Desarrollar `AdminProductosPage.jsx` con CRUD, opciones e imágenes; el rol `editor` no puede borrar productos.
- [ ] Implementar `POST /api/admin/imagenes` + `lib/cloudinary.js` (comprimir en cliente, validar MIME/tamaño, persistir en Cloudinary y guardar `url` + `public_id`).
- [ ] Desarrollar `AdminPresupuestosPage.jsx` (solo `admin` y `super_admin`) con listado, detalle y cambio de estado.
- [ ] Desarrollar `AdminUsuariosPage.jsx` (solo `super_admin`) con alta, cambio de rol, activación/desactivación y reset.
- [ ] Implementar `POST /api/admin/usuarios/[id]/reset-password` con contraseña temporal mostrada una sola vez, `debe_cambiar_password = TRUE` y revocación de sesiones.
- [ ] Desarrollar `AdminPerfilPage.jsx` y el flujo obligatorio cuando `debe_cambiar_password = TRUE`.
- [ ] Proteger la regla del último `super_admin` activo (no desactivar, degradar ni eliminar).
- [ ] Aplicar `noindex` a `/admin/*` y agregar pruebas de login, bloqueo, permisos y reset.

---

## Etapa 8: SEO, Accesibilidad y Calidad
- [ ] Configurar `react-helmet-async` con títulos, descripciones y canonical por pantalla.
- [ ] Configurar `vite-react-ssg` para prerenderizar Inicio, Catálogo, Servicios y Contacto.
- [ ] Crear `public/robots.txt`, `public/sitemap.xml` y el bloque JSON-LD (`LocalBusiness` y `Product`).
- [ ] Configurar Open Graph y Twitter Cards con URLs absolutas y una imagen 1200×630.
- [ ] Auditar contraste (CTA con `acento`), `alt`, foco visible, teclado y skip link.
- [ ] Ejecutar `npm run lint`, `npm run test` y `npm run build` sin errores.
- [ ] Revisar el sistema con la vista previa de VS Code y confirmar el funcionamiento global.

---

## Etapa 9: Despliegue
- [ ] Crear `netlify.toml` con build, publish, Functions y fallback SPA; sin redirect manual de `/api/*`.
- [ ] Conectar el repositorio de GitHub a Netlify y configurar variables de entorno (producción y preview).
- [ ] Activar cabeceras de seguridad en `netlify.toml` (HSTS, nosniff, CSP, X-Frame-Options).
- [ ] Realizar el deploy continuo desde la rama `main` de GitHub.
- [ ] Probar en producción el flujo catálogo → carrito → WhatsApp, `/api/health` y el login del admin.
- [ ] Reemplazar el número de WhatsApp de ejemplo y el dominio provisional por los reales.
- [ ] Monitorear créditos de Netlify, Neon y Cloudinary.
