# Plan técnico — Spec 003 Panel admin

## Enfoque

Router de Netlify Functions en `netlify/functions/api.js`. `lib/auth.js` implementa hash, sesiones, cookies y `requireAuth`; `lib/roles.js` centraliza roles; `lib/db.js` contiene las consultas. El frontend monta `/admin` con un layout protegido y páginas por rol. Cloudinary se usa solo desde el servidor.

## Archivos previstos

```text
lib/
├── db.js
├── auth.js
├── roles.js
├── http.js
└── cloudinary.js

netlify/functions/
└── api.js

src/
├── pages/admin/
│   ├── LoginAdmin.jsx
│   ├── DashboardAdmin.jsx
│   ├── ProductosAdmin.jsx
│   ├── ProductoForm.jsx
│   ├── PresupuestosAdmin.jsx
│   ├── UsuariosAdmin.jsx
│   └── PerfilAdmin.jsx
├── components/admin/
│   ├── AdminLayout.jsx
│   ├── RutaProtegida.jsx
│   ├── TablaUsuarios.jsx
│   └── SubidorImagen.jsx
└── context/
    └── AuthContext.jsx
```

## Endpoints

| Método | Ruta | Rol | Uso |
| --- | --- | --- | --- |
| POST | `/api/admin/auth/login` | Público | Login |
| POST | `/api/admin/auth/logout` | Autenticado | Logout |
| GET | `/api/admin/auth/me` | Autenticado | Usuario actual |
| POST | `/api/admin/auth/cambiar-password` | Autenticado | Cambio propio |
| GET | `/api/admin/usuarios` | `super_admin` | Listar |
| POST | `/api/admin/usuarios` | `super_admin` | Crear |
| PATCH | `/api/admin/usuarios/:id` | `super_admin` | Rol/estado |
| POST | `/api/admin/usuarios/:id/reset-password` | `super_admin` | Reset |
| GET/POST | `/api/admin/productos` | Editor+ | Listar/crear |
| PATCH/DELETE | `/api/admin/productos/:id` | Editor+ / Admin+ | Editar / borrar |
| POST | `/api/admin/imagenes` | Editor+ | Subir a Cloudinary |
| GET | `/api/admin/presupuestos` | Admin+ | Listar/estado |

## Autenticación

- `bcryptjs` costo 12; contraseña 10-72 caracteres.
- Token opaco de 32 bytes; hash SHA-256 en `sesiones_admin`.
- Cookie `pda_admin_session`: `HttpOnly`, `Secure` en prod/preview, `SameSite=Strict`, `Path=/`, `Max-Age` según `SESSION_TTL_HOURS`.
- `requireAuth(roles)` valida cookie, hash, expiración, revocación y `activo`.
- Login: comparación de tiempo constante, mensaje genérico, registro de intento, bloqueo 5/15.
- Cambio de contraseña: valida actual, actualiza hash, fuerza `debe_cambiar_password = false` y revoca otras sesiones.
- Reset: solo `super_admin`; temporal aleatoria mostrada una vez; revoca sesiones; fuerza cambio.
- Último `super_admin`: consulta dentro de transacción antes de desactivar/degradar.

## Cloudinary

- `POST /api/admin/imagenes` recibe `multipart/form-data` o base64 acotado.
- Validar MIME real con `file-type` o firma binaria, extensión y tamaño ≤ 5 MB.
- Subir con `public_id` UUID en `pedacito-de-arte/productos`.
- Guardar `url` y `public_id`; si falla la DB, borrar el asset recién subido.
- No exponer `CLOUDINARY_API_SECRET`.

## Frontend

- `AuthContext` consulta `/api/admin/auth/me` al montar.
- `RutaProtegida` redirige a `/admin/login` si no hay sesión.
- El layout oculta acciones no permitidas, pero el servidor siempre revalida.
- Formularios con React Hook Form y validación compartida.
- `noindex` en el layout y `robots.txt`.

## Pruebas

- Hash/verify y política de contraseñas.
- Login válido/inválido, bloqueo y logout.
- Sesión expirada/revocada y usuario inactivo.
- Matriz de permisos por rol.
- Último `super_admin` (desactivar, degradar, resetear).
- Reset y revocación de sesiones.
- Validación de imágenes y rollback.
- Auditoría sin datos sensibles.
- CSRF/Origin.

## Riesgos

| Riesgo | Mitigación |
| --- | --- |
| Fuerza bruta | Bloqueo 5/15, registro y mensajes genéricos |
| XSS roba sesión | Cookie `HttpOnly`, CSP y escape de salida |
| Escalada de privilegios | `requireAuth` en servidor y pruebas por rol |
| Pérdida de acceso | Protección del último `super_admin` + script local |
| Imagen maliciosa | Validar MIME real, tamaño y extensión |
| Costos Cloudinary | Comprimir, limitar variantes y monitorear créditos |
| Fuga de datos | No devolver hashes, tokens ni stack traces |

## Orden de implementación

1. `lib/roles.js`, `lib/auth.js` y esquema de auth.
2. Login/logout/me y cookie.
3. `RutaProtegida` y layout.
4. CRUD de productos e imágenes.
5. Usuarios y protección del último `super_admin`.
6. Presupuestos para admin.
7. Auditoría, noindex y CSRF.
8. Pruebas y verificación.
