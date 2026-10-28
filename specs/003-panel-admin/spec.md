# Spec 003 — Panel de administración y usuarios

- **Estado:** Borrador para implementación
- **RF principales:** RF09, RF11
- **RF relacionados:** RF10 (presupuestos, opcional)
- **Prioridad:** Alta
- **Dependencias:** tablas de auth, `lib/db.js`, `lib/auth.js`, `lib/roles.js`, Cloudinary.

## 1. Contexto

El panel es interno y no constituye una cuenta de cliente. El acceso es con usuario y contraseña, sin email. Los roles `super_admin`, `admin` y `editor` limitan las acciones. El `super_admin` gestiona usuarios; el `admin` gestiona catálogo y presupuestos; el `editor` gestiona catálogo sin borrar ni ver presupuestos.

## 2. Historias de usuario

- Como administrador, quiero iniciar sesión con usuario y contraseña para gestionar el sitio.
- Como `editor`, quiero cargar y editar productos e imágenes sin poder borrar ni ver presupuestos.
- Como `admin`, quiero gestionar todo el catálogo y los presupuestos.
- Como `super_admin`, quiero crear usuarios, asignar roles, desactivar cuentas y resetear contraseñas.
- Como usuario del panel, quiero cambiar mi propia contraseña y cerrar sesión.

## 3. Requerimientos funcionales (EARS)

### RF09 — Autenticación y gestión del catálogo

- **RF09.1:** Cuando el usuario envía credenciales válidas, el sistema deberá crear una sesión y una cookie `pda_admin_session` `HttpOnly`, `Secure` en producción y `SameSite=Strict`.
- **RF09.2:** Si las credenciales son inválidas, entonces el sistema deberá responder con el mensaje genérico “Usuario o contraseña incorrectos”.
- **RF09.3:** Si hay 5 intentos fallidos en 15 minutos para un usuario o IP, entonces el sistema deberá bloquear temporalmente y registrar el evento.
- **RF09.4:** Cuando el usuario cierra sesión, el sistema deberá revocar la sesión actual y borrar la cookie.
- **RF09.5:** Mientras la sesión sea válida, el sistema deberá permitir acceder a `/admin` y a las rutas API autorizadas.
- **RF09.6:** Si la sesión expira, está revocada o el usuario está inactivo, entonces el sistema deberá responder 401 y redirigir al login.
- **RF09.7:** Cuando el usuario autenticado tenga `debe_cambiar_password = true`, el sistema deberá exigir el cambio antes de operar en el panel.
- **RF09.8:** El `editor` podrá crear y editar categorías, productos, opciones e imágenes, pero no borrar productos ni ver presupuestos.
- **RF09.9:** El `admin` podrá además desactivar/eliminar productos y ver/cambiar el estado de presupuestos.
- **RF09.10:** El `super_admin` podrá además gestionar usuarios y roles.
- **RF09.11:** Cuando se suba una imagen, el sistema deberá validar MIME real, tamaño máximo de 5 MB y extensión permitida; luego deberá guardarla en Cloudinary y persistir `url` y `public_id`.
- **RF09.12:** Si la validación de imagen falla, entonces el sistema deberá rechazarla sin escribir en Cloudinary ni en la base.
- **RF09.13:** Las rutas `/admin` deberán responder con `X-Robots-Tag: noindex` y no aparecer en el sitemap.
- **RF09.14:** Las mutaciones deberán validar `Origin`/`Host` y registrar acciones sensibles en `admin_auditoria`.

### RF11 — Gestión de usuarios administradores

- **RF11.1:** Cuando el `super_admin` crea un usuario, el sistema deberá validar `usuario` (`[a-z0-9._-]{3,30}`), nombre y rol, y generar una contraseña temporal.
- **RF11.2:** La contraseña temporal se mostrará una sola vez, se guardará solo como hash `bcryptjs` costo 12 y el usuario deberá cambiarla en el próximo ingreso.
- **RF11.3:** Si el usuario ya existe, entonces el sistema deberá responder 409 sin revelar datos adicionales.
- **RF11.4:** Cuando el `super_admin` cambia el rol o desactiva un usuario, el sistema deberá revocar todas sus sesiones.
- **RF11.5:** Cuando el `super_admin` resetea una contraseña, el sistema deberá generar una nueva temporal, revocar sesiones y forzar el cambio.
- **RF11.6:** Si la acción deja al sistema sin ningún `super_admin` activo, entonces el sistema deberá rechazarla.
- **RF11.7:** Un usuario no podrá desactivarse, eliminarse ni quitarse el rol a sí mismo si es el último `super_admin` activo.
- **RF11.8:** El sistema no permitirá contraseñas en texto plano, hashes reversibles ni login por email.
- **RF11.9:** El sistema deberá permitir a cada usuario cambiar su propia contraseña con la contraseña actual y revocar las demás sesiones.
- **RF11.10:** El login no tendrá “recuperar por email”; el mensaje indicará contactar al administrador principal.

## 4. Casos borde

- Usuario inexistente, inactivo o bloqueado.
- Contraseña incorrecta repetida.
- Cookie ausente, alterada, expirada o revocada.
- Usuario desactivado con sesión abierta.
- Cambio de rol con sesión abierta.
- Último `super_admin` activo.
- Nombre de usuario duplicado con mayúsculas.
- Contraseña temporal vencida o ya usada.
- Reset de contraseña propia por API de usuarios.
- Archivo con MIME falso, extensión engañosa o tamaño excesivo.
- Imagen en uso por un producto.
- Petición cross-site o `Origin` inválido.
- Doble clic en crear usuario.
- Falla de Cloudinary o de Neon a mitad de una operación.
- Sesión concurrente en dos dispositivos.

## 5. Fuera de alcance

- Registro público de usuarios.
- Login con Google, Auth0, Clerk, Supabase Auth o Firebase.
- Recuperación por email.
- 2FA.
- Cuentas de cliente.
- Roles personalizados más allá de los tres definidos.
- Borrado físico de usuarios con historial.

## 6. Criterios de aceptación

1. Login, logout y `me` funcionan con cookie segura y sesión opaca.
2. El bloqueo por intentos fallidos se activa a los 5 fallos en 15 minutos.
3. Los permisos se aplican en el servidor; ocultar botones no es suficiente.
4. El `editor` no puede borrar productos ni ver presupuestos.
5. El `admin` gestiona catálogo y presupuestos, pero no usuarios.
6. El `super_admin` crea, edita, desactiva y resetea usuarios.
7. Nunca se puede dejar el sistema sin `super_admin` activo.
8. Las contraseñas se guardan con `bcryptjs` costo 12.
9. Las sesiones se revocan al cambiar rol, desactivar o resetear.
10. La subida de imágenes valida MIME y tamaño, y guarda URL + `public_id`.
11. Las rutas admin son `noindex`.
12. Las acciones sensibles quedan auditadas.
13. Las pruebas cubren auth, roles, último super_admin e imágenes.
14. No hay secretos ni hashes en respuestas o logs.

## 7. Trazabilidad RF

| RF | Prioridad | Incluido | Verificación |
| --- | --- | --- | --- |
| RF09 | Alta | Sí | `verificacion.md` |
| RF11 | Alta | Sí | `verificacion.md` |
| RF10 | Media | Ver spec 002 | `specs/002/verificacion.md` |
