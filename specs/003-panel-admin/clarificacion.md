# Clarificación — Spec 003 Panel admin

## Decisiones

| Pregunta | Opciones | Decisión | Justificación | Estado |
| --- | --- | --- | --- | --- |
| ¿Auth externa? | Sí / No | No; auth propia | Requisito de `AGENTS.md` y del TP | Resuelta |
| ¿Login con email? | Sí / No | No; usuario + contraseña | Evita dependencia de email y datos personales | Resuelta |
| ¿Duración de sesión? | 1 h / 8 h / 7 días | 8 h configurables con `SESSION_TTL_HOURS` | Equilibrio seguridad/uso | Resuelta |
| ¿Token en `localStorage`? | Sí / No | No; cookie `HttpOnly` y hash en base | Mitiga XSS | Resuelta |
| ¿Bloqueo por intentos? | Sí / No | 5 fallos / 15 min por usuario e IP | Fuerza bruta | Resuelta |
| ¿Reset por email? | Sí / No | No; solo `super_admin` genera temporal | Sin servicio de email | Resuelta |
| ¿Borrar usuarios? | Físico / Lógico | Desactivar (`activo = false`) | Conserva auditoría e historial | Resuelta |
| ¿Último super_admin? | Permitir / Bloquear | Bloquear desactivar, eliminar o degradar | Evita perder acceso | Resuelta |
| ¿Quién ve presupuestos? | Todos / Admin+ | `admin` y `super_admin` | `editor` solo catálogo | Resuelta |
| ¿Quién borra productos? | Editor / Admin+ | `admin` y `super_admin` | Protege integridad | Resuelta |
| ¿Imágenes en Cloudinary o repo? | Repo / Cloudinary | Cloudinary; URL + `public_id` en base | No inflar el repo y permitir CDN | Resuelta |
| ¿Tamaño máximo de imagen? | 2 MB / 5 MB / 10 MB | 5 MB y compresión previa | Equilibrio calidad/costo | Resuelta |
| ¿Formatos permitidos? | Todos / JPEG-PNG-WebP | JPEG, PNG y WebP | Compatibilidad y seguridad | Resuelta |
| ¿Seed de admin? | Password fija / script local | `npm run admin:reset -- <usuario> --rol super_admin` | Sin credenciales por defecto | Resuelta |
| ¿Auditoría? | Sí / No | Sí, acciones sensibles en `admin_auditoria` | Trazabilidad | Resuelta |
| ¿CSRF? | Token / Origin | Validar `Origin`/`Host` + `SameSite=Strict`; token firmado si se agrega | Suficiente para el alcance | Resuelta |
| ¿Rutas admin indexables? | Sí / No | No; `noindex` y `Disallow` | Seguridad por oscuridad + práctica | Resuelta |

## Preguntas que requieren respuesta humana

| Pregunta | Bloquea | Estado |
| --- | --- | --- |
| ¿Quién será el usuario `super_admin` inicial? | Bloquea la prueba de login, no el código | Pendiente |
| ¿Se necesitan dos `super_admin` de respaldo? | No; recomendable | Pendiente |
| ¿El panel gestiona presupuestos en el MVP? | No; RF10 es prioridad media | Pendiente |

## Supuestos permitidos

- El panel se usa en desktop y tablet; debe ser usable en mobile.
- El `super_admin` inicial se crea con el script local, no con un seed público.
- Cloudinary se configura con una carpeta `pedacito-de-arte`.
- Las imágenes pueden reutilizarse; no se borran automáticamente al eliminar un producto.
- El sistema no envía emails.
