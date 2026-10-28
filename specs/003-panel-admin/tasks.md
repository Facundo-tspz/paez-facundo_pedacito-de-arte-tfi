# Tareas — Spec 003 Panel admin

| # | Tarea | Archivos | Listo cuando | RF |
| --- | --- | --- | --- | --- |
| 1 | Aprobar spec y decisiones | `spec.md`, `clarificacion.md` | Decisiones bloqueantes resueltas | — |
| 2 | Crear roles y helpers de auth | `lib/roles.js`, `lib/auth.js` | Hash, token y `requireAuth` con pruebas unitarias | RF09 |
| 3 | Crear rutas de login/logout/me | `netlify/functions/api.js` | Cookie, errores y sesión funcionan | RF09 |
| 4 | Implementar bloqueo por intentos | `lib/auth.js`, `lib/db.js` | 5 fallos/15 min y auditoría | RF09 |
| 5 | Crear `AuthContext` y ruta protegida | `src/context/AuthContext.jsx`, `RutaProtegida.jsx` | Redirige sin sesión y muestra usuario | RF09 |
| 6 | Crear login y layout admin | `LoginAdmin.jsx`, `AdminLayout.jsx` | Login usable y `noindex` | RF09 |
| 7 | Crear CRUD de productos | `ProductosAdmin.jsx`, `ProductoForm.jsx` | Editor crea/edita; admin borra | RF09 |
| 8 | Crear subida a Cloudinary | `SubidorImagen.jsx`, `lib/cloudinary.js` | Valida MIME/tamaño y guarda URL+public_id | RF09 |
| 9 | Crear gestión de usuarios | `UsuariosAdmin.jsx`, endpoints | Crear, rol, desactivar y reset | RF11 |
| 10 | Proteger al último super_admin | `lib/auth.js`, `lib/db.js` | Rechaza desactivar/degradar al último | RF11 |
| 11 | Implementar cambio de contraseña propio | `PerfilAdmin.jsx`, endpoint | Valida actual y revoca otras sesiones | RF11 |
| 12 | Agregar auditoría y CSRF/Origin | `lib/auth.js`, `api.js` | Acciones sensibles registradas | RF09 |
| 13 | Escribir pruebas de auth y roles | `tests/` | Cubren matriz y casos borde | RF09/RF11 |
| 14 | Verificar y documentar | `specs/003-panel-admin/verificacion.md` | Tabla completa con evidencia | Todos |

## Dependencias

- 3 depende de 2.
- 5 y 6 dependen de 3.
- 7 depende de 6.
- 8 depende de 7.
- 9 y 10 dependen de 3.
- 13 depende de 3, 4, 9 y 10.

## Bloqueos

- Usuario `super_admin` inicial para probar.
- Confirmación de si RF10 entra en el MVP.
- Cuenta de Cloudinary.
