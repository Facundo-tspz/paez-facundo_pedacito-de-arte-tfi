# Constitución del proyecto

Estos principios son obligatorios para cualquier persona o agente de IA que modifique Pedacito de Arte. Un cambio que los contradiga debe rechazarse o justificarse y aprobarse antes de implementarse.

## 1. Stack explícito y justificado

La aplicación usa React 18 + Vite (SPA), Netlify Functions, PostgreSQL en Neon y Cloudinary. Cambiar de framework, hosting, base de datos o storage requiere una decisión documentada en `docs/CAMBIOS-DOCUMENTACION.md`, una justificación técnica y una actualización de las specs afectadas.

**Verificación:** el stack real coincide con `docs/03-TECNOLOGIAS.md` y el `package.json`.

## 2. Frontera de datos inviolable

El navegador nunca accede a `DATABASE_URL`, a las claves de Cloudinary ni ejecuta SQL. Toda consulta se centraliza en `lib/db.js` y se expone mediante Functions en `/api`. Las consultas usan parámetros, nunca concatenación de valores.

**Verificación:** búsqueda de `DATABASE_URL` y `pg` en `src/` sin resultados; rutas de datos pasan por `/api`.

## 3. Identidad visual sin invenciones

Los colores, tipografías y logos provienen de `docs/IDENTIDAD-MARCA.md` y de `public/marca/`. No se usan HEX arbitrarios ni fuentes ajenas a la marca. Los componentes usan variables CSS o clases derivadas de los tokens.

**Verificación:** revisión visual y búsqueda de HEX fuera de los tokens.

## 4. Calidad verificable antes de cerrar

Ninguna tarea se considera terminada sin lint, pruebas y build en verde, o sin evidencia documentada de por qué no aplica. Cada requerimiento implementado tiene al menos una prueba o una verificación manual registrada en `specs/NNN/verificacion.md`.

**Verificación:** `npm run lint`, `npm run test` y `npm run build`; tabla de verificación actualizada.

## 5. Seguridad y privacidad por defecto

No se commitean secretos. Las credenciales viven en `.env` local y en Netlify. Las contraseñas usan `bcryptjs`; las sesiones son opacas, con cookie `HttpOnly`, `Secure` en producción y `SameSite=Strict`. Los datos personales no se envían a herramientas de IA sin consentimiento y minimización.

**Verificación:** `.env` ignorado; revisión de logs y prompts; pruebas de autenticación y autorización.

## 6. Accesibilidad y rendimiento como requisito

El sitio funciona desde 360 px, con navegación por teclado, foco visible, textos alternativos, contraste AA y formularios etiquetados. El objetivo es Lighthouse ≥ 80 en las cuatro categorías y carga inicial razonable en redes móviles.

**Verificación:** auditoría Lighthouse, recorrido con teclado y revisión de `alt`/labels.

## 7. IA con transparencia y supervisión humana

Toda pieza generada con IA se registra en `multimedia/FICHAS-IA.md` y se declara en `docs/DECLARACION-IA.md`. Se revisan derechos de autor, sesgos, anatomía, texto y veracidad. No se generan personas reales identificables ni se imitan marcas registradas. El código y los prompts generados por IA son revisados por una persona antes de integrarse.

**Verificación:** fichas completas, prompts documentados y revisión humana registrada.

## 8. Spec primero, commits pequeños y bitácora viva

Antes de codificar una funcionalidad se escribe o actualiza su spec en `specs/`. Cada tarea se implementa en un commit pequeño y descriptivo. `docs/PROGRESO.md` y `memory.md` se actualizan al cerrar cada fase para que otra persona o IA pueda retomar el trabajo.

**Verificación:** spec enlazada en el commit; historial de Git legible; bitácora actualizada.
