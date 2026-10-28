# Memoria del proyecto

Última actualización: 2026-10-28

## Estado

- Fase 0 completada en el repositorio: estructura, arnés SDD y documentación publicados en `main` (commit `8595e7b`).
- Repositorio público e individual. URL actual: <https://github.com/Facundo-tspz/paez-facundo_pedacito-de-arte-tfi>.
- Renombre pendiente a `iest-tfi_pedacito-de-arte`; el usuario lo confirma en GitHub.
- Cuenta Netlify disponible. Neon y Cloudinary serán creados por el usuario.
- No hay código de aplicación, base de datos ni deploy todavía.

## Decisiones cerradas

- Stack: React 18 + Vite + React Router + Tailwind/DaisyUI + Netlify Functions + Neon PostgreSQL + Cloudinary.
- Se descartaron Next.js/Vercel y Supabase por costo, límites del plan gratuito y alcance real.
- MVP: catálogo, carrito de presupuesto, derivación a WhatsApp y panel admin con roles.
- 4 líneas de producto: Agendas, Agendas Docentes, Revistas para Colorear, Anotadores.
- Las variantes (tapa, interior, secciones) se modelan como opciones, no como productos separados.
- Precios reales pendientes: usar “A presupuestar” con `precio_base = 0`.
- Fotografías reales para el catálogo; 8 imágenes con IA para hero, escenas, personas ficticias y mockups.
- Evitar marcas infantiles registradas en prompts y textos públicos sin autorización.
- Auth propia: roles `super_admin`, `admin`, `editor`, `bcryptjs`, sesiones opacas y cookie `HttpOnly`.

## Pendientes inmediatos

1. Confirmar en clase el alcance de la entrega del viernes 30-10-2026: ¿solo repo/sitio o también informe y multimedia?
2. Confirmar aceptación del stack Vite + Netlify + Neon + Cloudinary.
3. Confirmar nombre definitivo del repositorio y anexos del TP.
4. Crear Neon y Cloudinary; cargar variables en `.env` y Netlify.
5. Tomar fotos reales y definir descripciones, precios y diseños.
6. Scaffold de Vite, esquema SQL, seed y primer deploy.

## Próximo paso

Scaffold de la aplicación con Vite, migración del esquema a Neon y primer deploy en Netlify. Antes, confirmar el stack y el alcance con la cátedra.

## Reglas para retomar

- Leer `AGENTS.md`, `docs/CONSTITUCION.md` y `docs/PROGRESO.md`.
- Revisar la spec activa antes de tocar código.
- No commitear `.env` ni secretos.
- Actualizar este archivo al cerrar cada fase.
