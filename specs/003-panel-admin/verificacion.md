# Verificación — Spec 003 Panel admin

- **Estado global:** Pendiente
- **Última ejecución:** —
- **Responsable:** —

## Comandos

| Comando | Resultado | Observaciones |
| --- | --- | --- |
| `npm run lint` | Pendiente | — |
| `npm run test` | Pendiente | — |
| `npm run build` | Pendiente | — |

## Criterios de aceptación

| Criterio / RF | Evidencia | Resultado | Observaciones |
| --- | --- | --- | --- |
| RF09.1 Login y cookie segura | — | Pendiente | — |
| RF09.2 Mensaje genérico | — | Pendiente | — |
| RF09.3 Bloqueo 5/15 | — | Pendiente | — |
| RF09.4 Logout y revocación | — | Pendiente | — |
| RF09.5 Acceso a `/admin` | — | Pendiente | — |
| RF09.6 Sesión inválida | — | Pendiente | — |
| RF09.7 Cambio obligatorio | — | Pendiente | — |
| RF09.8 Permisos editor | — | Pendiente | — |
| RF09.9 Permisos admin | — | Pendiente | — |
| RF09.10 Permisos super_admin | — | Pendiente | — |
| RF09.11 Subida Cloudinary | — | Pendiente | — |
| RF09.12 Imagen rechazada | — | Pendiente | — |
| RF09.13 Noindex | — | Pendiente | — |
| RF09.14 Auditoría/CSRF | — | Pendiente | — |
| RF11.1 Crear usuario | — | Pendiente | — |
| RF11.2 Temporal y hash | — | Pendiente | — |
| RF11.3 Duplicado 409 | — | Pendiente | — |
| RF11.4 Revocación por rol/estado | — | Pendiente | — |
| RF11.5 Reset y revocación | — | Pendiente | — |
| RF11.6 Último super_admin | — | Pendiente | — |
| RF11.7 Autoprotección | — | Pendiente | — |
| RF11.8 Sin texto plano | — | Pendiente | — |
| RF11.9 Cambio propio | — | Pendiente | — |
| RF11.10 Sin email | — | Pendiente | — |

## Seguridad

| Control | Evidencia | Resultado |
| --- | --- | --- |
| Hash bcrypt costo 12 | — | Pendiente |
| Token no en `localStorage` | — | Pendiente |
| Cookie `HttpOnly`/`Secure`/`SameSite` | — | Pendiente |
| `requireAuth` en cada ruta | — | Pendiente |
| Sin secretos en respuestas | — | Pendiente |
| Cloudinary solo servidor | — | Pendiente |
| `Origin` validado | — | Pendiente |

## Roles

| Acción | Editor | Admin | Super_admin | Evidencia |
| --- | --- | --- | --- | --- |
| Ver dashboard | — | — | — | Pendiente |
| Crear/editar producto | — | — | — | Pendiente |
| Borrar producto | — | — | — | Pendiente |
| Ver presupuestos | — | — | — | Pendiente |
| Gestionar usuarios | — | — | — | Pendiente |

## Pendientes y bloqueos

- `super_admin` inicial.
- Cuenta Cloudinary.
- Decisión sobre RF10 en el MVP.
