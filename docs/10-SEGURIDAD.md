# 10 - Políticas de Seguridad: Pedacito de Arte

Este documento especifica las prácticas de seguridad, protección de datos sensibles, manejo de variables de entorno y reglas de validación para la plataforma web de **Pedacito de Arte**.

> **Principio rector:** el navegador nunca toca la base de datos. Todo el acceso pasa por `netlify/functions/api.js` en `/api/*`, que valida la entrada y, cuando corresponde, la sesión del administrador.

---

## 1. Frontera Cliente / Servidor

- El SPA (`src/`) solo habla con `/api` mediante `src/services/api.js`.
- `lib/db.js` y `DATABASE_URL` viven exclusivamente del lado servidor; nunca se importan desde `src/`.
- Ningún componente del cliente escribe SQL ni conoce cadenas de conexión.
- Las respuestas de la API no exponen stack traces ni detalles internos de la base.

---

## 2. Protección de Variables de Entorno y Credenciales

- **Variables Sensibles (`.env`):** `DATABASE_URL`, `AUTH_SECRET`, `CLOUDINARY_API_KEY` y `CLOUDINARY_API_SECRET` se almacenan solo en el servidor y nunca se exponen al cliente. `SESSION_TTL_HOURS` (8 por defecto) es configuración del servidor.
- **Inclusión en `.gitignore`:** `.env.local`, `.env` y cualquier archivo con credenciales quedan fuera del repositorio.
- **Prefijo para Variables Públicas:** únicamente las variables leídas por el navegador usan el prefijo `VITE_` (ej. `VITE_WHATSAPP_PHONE_NUMBER`). El número de WhatsApp es público por diseño.
- **`.env.example`:** lista todas las variables con valores de ejemplo; nunca contiene secretos reales.

---

## 3. Prevención de Inyecciones SQL y Sanitización

Todas las consultas a PostgreSQL (Neon) se ejecutan en `lib/db.js` con marcadores de parámetros:

```javascript
// ❌ INCORRECTO: vulnerable a inyección SQL
const query = `SELECT * FROM productos WHERE categoria_id = ` + categoriaId;

// ✅ CORRECTO: consulta parametrizada segura
const query = 'SELECT * FROM productos WHERE categoria_id = $1';
const values = [categoriaId];
const result = await db.query(query, values);
```

Reglas adicionales:
- Prohibida la concatenación de cadenas en SQL, incluso con datos "internos".
- Validar y normalizar en la API antes de consultar: IDs numéricos, booleanos, longitudes máximas y enums (`estado`).
- Escapar/limpiar el texto libre (nombre, contacto, observaciones, nota) antes de persistirlo.
- No devolver más columnas de las necesarias (evitar filtrar datos internos).

---

## 4. Autenticación y Autorización del Panel

El panel es interno y **no** constituye una cuenta de cliente. La autenticación es **propia**: no se usan Auth0, Clerk, Supabase Auth ni Firebase.

### 4.1 Roles y permisos

| Acción | `editor` | `admin` | `super_admin` |
| :--- | :---: | :---: | :---: |
| Ver dashboard | ✓ | ✓ | ✓ |
| Crear/editar productos, categorías y opciones | ✓ | ✓ | ✓ |
| Subir/eliminar imágenes | ✓ | ✓ | ✓ |
| Desactivar/eliminar productos | ✗ | ✓ | ✓ |
| Ver y cambiar estado de presupuestos | ✗ | ✓ | ✓ |
| Gestionar usuarios y roles | ✗ | ✗ | ✓ |
| Restablecer contraseñas de otros | ✗ | ✗ | ✓ |
| Cambiar su propia contraseña | ✓ | ✓ | ✓ |

- Los permisos se validan **siempre en el servidor** con `requireAuth([...roles])`. Ocultar botones en la UI es solo cosmético.
- Las constantes de rol se centralizan en `lib/roles.js` (`ROLES`); prohibido comparar strings sueltos.

### 4.2 Usuarios y contraseñas

- Login con `usuario` + `password`, **sin email**. Usuario único, en minúsculas, `[a-z0-9._-]{3,30}`.
- Hash con `bcryptjs` (costo 12). Prohibido texto plano o cifrado reversible.
- Contraseña de mínimo 10 caracteres y máximo 72 (límite de bcrypt). Se permiten frases largas; no se exige composición artificial.
- Error genérico: *"Usuario o contraseña incorrectos"*, para no revelar si el usuario existe.
- `debe_cambiar_password = TRUE` obliga a cambiarla antes de operar en el panel.

### 4.3 Sesiones

- Token opaco de 32 bytes (`crypto.randomBytes`); en la base se guarda solo su hash SHA-256 en `sesiones_admin`.
- Cookie `pda_admin_session`: `HttpOnly`, `Secure`, `SameSite=Strict`, `Path=/`, duración **8 horas** (`SESSION_TTL_HOURS`).
- El token nunca se guarda en `localStorage` ni en el estado global.
- `GET /api/admin/auth/me` valida la sesión y devuelve solo `{ id, usuario, nombre, rol }`.
- Logout revoca la sesión actual. Resetear contraseña, cambiar rol o desactivar un usuario **revoca todas sus sesiones**.
- El cambio de contraseña propia exige la contraseña actual y revoca las demás sesiones.

### 4.4 Intentos fallidos y fuerza bruta

- Cada intento se registra en `admin_intentos_login` (usuario, IP, resultado, fecha).
- Bloqueo temporal tras 5 fallos en 15 minutos (por usuario e IP), reflejado en `bloqueado_hasta`.
- El mensaje de error no revela si el usuario existe ni si está bloqueado.
- El bloqueo no puede dejar fuera al último `super_admin`: el script local de emergencia siempre puede restaurar el acceso.

### 4.5 Restablecimiento de contraseñas

- Solo `super_admin` puede resetear la contraseña de otros usuarios.
- Se genera una contraseña temporal aleatoria, se muestra **una sola vez**, se guarda su hash, se marca `debe_cambiar_password = TRUE` y se revocan todas sus sesiones.
- El botón **"¿Olvidaste tu contraseña?"** no envía emails: muestra el mensaje *"Comunicate con el administrador principal para restablecerla."*
- Cada usuario puede cambiar su propia contraseña desde su perfil.
- No se puede resetear, eliminar ni desactivar al último `super_admin` activo.

### 4.6 Recuperación del super admin

- Si el super admin olvida su contraseña, se usa el script local `npm run admin:reset -- <usuario> [--rol <rol>]` con `DATABASE_URL` en la máquina del dueño.
- El script no se expone por API ni por ruta pública. Crea el usuario si no existe o actualiza su hash, fuerza el cambio y revoca sesiones.
- Alternativa de continuidad: mantener un segundo `super_admin` de respaldo (decisión operativa).

### 4.7 CSRF, auditoría y noindex

- En mutaciones, validar `Origin`/`Host` y `SameSite=Strict`; no se aceptan formularios cross-site. Si se agrega token CSRF, se firma con `AUTH_SECRET`.
- Registrar en `admin_auditoria`: login, logout, reset, cambios de rol, altas/bajas y cambios de contraseña.
- El panel responde con `X-Robots-Tag: noindex` y `Disallow: /admin` en `robots.txt`.

---

## 5. Subida de Imágenes

- Validar extensión y MIME real (`image/jpeg`, `image/png`, `image/webp`) del lado servidor; no confiar en el nombre del archivo.
- Límite de tamaño (ej. 5 MB) y rechazo de archivos no-imagen o con contenido ejecutable.
- Comprimir en el cliente con `browser-image-compression` antes de subir, pero **revalidar** en el servidor.
- Guardar los archivos en **Cloudinary**; en PostgreSQL se persisten `url` y `public_id`. Si falla la escritura en la base, eliminar el asset recién subido.
- Generar nombres únicos (UUID) y no reutilizar nombres provistos por el usuario.
- El endpoint de carga requiere sesión válida con rol `editor`, `admin` o `super_admin`.

---

## 6. Datos Personales y Legal

- Solo se recolectan los datos necesarios para el presupuesto: nombre, contacto y observaciones.
- El formulario de contacto debe informar el uso de los datos y enlazar a una política de privacidad mínima (Ley 25.326 de Argentina).
- Los presupuestos se conservan para seguimiento interno; el admin puede eliminarlos cuando ya no sean necesarios.
- No se integran pagos ni se almacenan datos de tarjetas en la Fase 1.

---

## 7. Cabeceras y Endurecimiento

Configurar en `netlify.toml`:
- `Strict-Transport-Security` (HSTS).
- `X-Content-Type-Options: nosniff`.
- `X-Frame-Options: DENY`.
- `Referrer-Policy: strict-origin-when-cross-origin`.
- `Content-Security-Policy` acorde a los orígenes usados (Neon, Cloudinary, Google Fonts).
- `X-Robots-Tag: noindex` para `/admin/*`.

---

## 8. Checklist Mínimo Antes de Publicar

- [ ] `.env` fuera de Git y `.env.example` sin secretos.
- [ ] `DATABASE_URL`, `AUTH_SECRET` y `CLOUDINARY_API_SECRET` inaccesibles desde el bundle del cliente.
- [ ] Todas las consultas parametrizadas.
- [ ] Login multiusuario con `bcryptjs`, bloqueo por intentos y cookie `httpOnly`.
- [ ] Roles validados en el servidor con `requireAuth([...roles])`.
- [ ] Reset de contraseña solo por `super_admin` y sesiones revocadas.
- [ ] Script de emergencia `admin:reset` solo local, nunca como endpoint público.
- [ ] Endpoints admin protegidos y con validación de entrada.
- [ ] Subida de imágenes validada por tipo y tamaño.
- [ ] Cabeceras de seguridad activas en producción.
- [ ] Política de privacidad publicada.
