# 05 - Modelo de Datos: Pedacito de Arte

Este documento especifica la estructura de la base de datos relacional (PostgreSQL) para el sistema web de **Pedacito de Arte**, detallando las entidades, campos, tipos de datos, restricciones y ejemplos de registros SQL.

> **Acceso:** todas las consultas se ejecutan desde `lib/db.js`, invocadas por `netlify/functions/api.js` en `/api/*`. El cliente nunca accede a la base.

---

## 1. Entidades y Esquema de Tablas

### Tabla `categorias`
Guarda las familias principales de productos para facilitar el filtrado en el catálogo.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador único de la categoría. |
| `nombre` | `VARCHAR(50)` | `NOT NULL UNIQUE` | Nombre visual (ej. "Agendas", "Infantil"). |
| `slug` | `VARCHAR(50)` | `NOT NULL UNIQUE` | Identificador amigable para URL (ej. "agendas", "infantil"). |
| `descripcion` | `TEXT` | `NULL` | Breve detalle explicativo de la categoría. |
| `orden` | `INTEGER` | `DEFAULT 0` | Orden de aparición en filtros. |

---

### Tabla `productos`
Almacena la información de los artículos disponibles en el catálogo.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador único del producto. |
| `codigo` | `VARCHAR(20)` | `NOT NULL UNIQUE` | Código interno de inventario/diseño (ej. "AG-DOC-2026"). |
| `slug` | `VARCHAR(80)` | `NOT NULL UNIQUE`, regex `^[a-z0-9-]{3,80}$` | Identificador legible para la URL del detalle. |
| `nombre` | `VARCHAR(120)` | `NOT NULL` | Nombre comercial del producto. |
| `categoria_id` | `INTEGER` | `FOREIGN KEY` | Referencia a `categorias(id)`. |
| `descripcion` | `TEXT` | `NOT NULL` | Detalle completo, especificación de hojas, encuadernación. |
| `precio_base` | `NUMERIC(12,2)` | `DEFAULT 0.00` | Precio en ARS. `0` significa "a presupuestar". |
| `imagen_url` | `VARCHAR(500)` | `NOT NULL` | URL de la fotografía principal (Cloudinary o `/media/...`). |
| `imagen_public_id` | `VARCHAR(255)` | `NULL` | `public_id` de Cloudinary; `NULL` si la imagen es local. |
| `imagen_alt` | `VARCHAR(160)` | `NOT NULL DEFAULT ''` | Texto alternativo de la imagen principal. |
| `destacado` | `BOOLEAN` | `DEFAULT FALSE` | `TRUE` si se muestra en la portada/inicio. |
| `disponible` | `BOOLEAN` | `DEFAULT TRUE` | Estado de visibilidad en catálogo. |
| `creado_en` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Alta del producto. |
| `actualizado_en` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Última edición (se actualiza desde la API). |

---

### Tabla `producto_imagenes`
Galería adicional por producto (la imagen principal vive en `productos.imagen_url`).

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador de la imagen. |
| `producto_id` | `INTEGER` | `FOREIGN KEY` | Referencia a `productos(id)` con `ON DELETE CASCADE`. |
| `url` | `VARCHAR(500)` | `NOT NULL` | URL de la imagen en Cloudinary o `/media/...`. |
| `public_id` | `VARCHAR(255)` | `NULL` | `public_id` de Cloudinary; `NULL` si es local. |
| `alt` | `VARCHAR(160)` | `NOT NULL` | Texto alternativo obligatorio (accesibilidad). |
| `orden` | `INTEGER` | `DEFAULT 0` | Orden dentro de la galería. |

---

### Tabla `opciones_personalizacion`
Define las variantes de configuración disponibles para cada producto (tipo de hoja, tamaño, laminado).

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador de la opción. |
| `producto_id` | `INTEGER` | `FOREIGN KEY` | Referencia a `productos(id)` con `ON DELETE CASCADE`. |
| `tipo_opcion` | `VARCHAR(50)` | `NOT NULL` | Categoría de variante (ej. "Interior", "Tamaño", "Laminado"). |
| `valor` | `VARCHAR(100)` | `NOT NULL` | Opción concreta (ej. "Hojas Punteadas", "A5 Tapa Dura", "Mate"). |
| `recargo` | `NUMERIC(12,2)` | `DEFAULT 0.00` | Recargo en ARS al elegir esta opción. |
| `requerida` | `BOOLEAN` | `DEFAULT FALSE` | Obliga a elegir una opción del grupo. |
| `multiple` | `BOOLEAN` | `DEFAULT FALSE` | Permite seleccionar más de un valor del grupo. |
| `orden` | `INTEGER` | `DEFAULT 0` | Orden de presentación. |

---

### Tabla `solicitudes_presupuesto`
Registra los presupuestos armados por los clientes antes de ser derivados a WhatsApp.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Número correlativo del presupuesto. |
| `fecha` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Fecha y hora de creación. |
| `cliente_nombre` | `VARCHAR(120)` | `NULL` | Nombre declarado por el cliente. |
| `cliente_contacto` | `VARCHAR(120)` | `NULL` | Teléfono o red de contacto. |
| `monto_total` | `NUMERIC(12,2)` | `NOT NULL` | Sumatoria de los ítems (puede ser `0` si es a presupuestar). |
| `estado` | `VARCHAR(20)` | `DEFAULT 'Enviado'` | `'Enviado'`, `'En Taller'` o `'Entregado'`. |
| `observaciones` | `TEXT` | `NULL` | Notas del pedido. |

---

### Tabla `presupuesto_items`
Detalle de cada ítem que compone un presupuesto. Sin esta tabla el presupuesto registrado no es reconstruible.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador del ítem. |
| `presupuesto_id` | `INTEGER` | `FOREIGN KEY` | Referencia a `solicitudes_presupuesto(id)` con `ON DELETE CASCADE`. |
| `producto_id` | `INTEGER` | `FOREIGN KEY NULL` | Referencia a `productos(id)` con `ON DELETE SET NULL`. |
| `nombre_producto` | `VARCHAR(120)` | `NOT NULL` | Copia del nombre al momento del pedido (histórico). |
| `opciones_seleccionadas` | `JSONB` | `DEFAULT '[]'` | Opciones elegidas (interior, laminado, etc.) con su recargo. |
| `cantidad` | `INTEGER` | `NOT NULL CHECK (cantidad > 0)` | Unidades pedidas. |
| `precio_unitario` | `NUMERIC(12,2)` | `NOT NULL` | Precio base + recargos al momento del pedido. |
| `subtotal` | `NUMERIC(12,2)` | `NOT NULL` | `cantidad * precio_unitario`. |
| `nota` | `TEXT` | `NULL` | Nombre o frase a estampar en la tapa. |

---

## 2. Autenticación del Panel (tablas de administración)

### Tabla `admin_usuarios`
Usuarios internos del panel. El login es con **usuario y contraseña**, sin email.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador del usuario admin. |
| `usuario` | `VARCHAR(30)` | `NOT NULL`, único sin distinguir mayúsculas | Nombre de usuario para login (`[a-z0-9._-]`, minúsculas). |
| `nombre` | `VARCHAR(120)` | `NOT NULL` | Nombre visible de la persona. |
| `password_hash` | `VARCHAR(255)` | `NOT NULL` | Hash `bcryptjs` (costo 12). Nunca texto plano. |
| `rol` | `VARCHAR(20)` | `NOT NULL`, `CHECK` | `super_admin`, `admin` o `editor`. |
| `activo` | `BOOLEAN` | `DEFAULT TRUE` | Permite bloquear el acceso sin borrar historial. |
| `debe_cambiar_password` | `BOOLEAN` | `DEFAULT TRUE` | Fuerza el cambio en el próximo ingreso (altas y reseteos). |
| `intentos_fallidos` | `INTEGER` | `DEFAULT 0` | Contador de fallos consecutivos. |
| `bloqueado_hasta` | `TIMESTAMP` | `NULL` | Fin del bloqueo temporal por intentos fallidos. |
| `ultimo_acceso` | `TIMESTAMP` | `NULL` | Último login exitoso. |
| `creado_en` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Alta del usuario. |
| `actualizado_en` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Última edición. |

---

### Tabla `sesiones_admin`
Sesiones activas del panel. Permite logout, revocación masiva y auditoría de accesos.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador de la sesión. |
| `usuario_id` | `INTEGER` | `FOREIGN KEY` | Referencia a `admin_usuarios(id)` con `ON DELETE CASCADE`. |
| `token_hash` | `VARCHAR(64)` | `NOT NULL UNIQUE` | SHA-256 del token opaco; el token original solo vive en la cookie. |
| `creado_en` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Inicio de la sesión. |
| `expira_en` | `TIMESTAMP` | `NOT NULL` | Vencimiento (8 horas por defecto). |
| `revocada_en` | `TIMESTAMP` | `NULL` | Marca de cierre/revocación. |
| `ip` | `VARCHAR(45)` | `NULL` | IP de origen (IPv4/IPv6). |
| `user_agent` | `VARCHAR(255)` | `NULL` | Navegador/dispositivo. |

---

### Tabla `admin_intentos_login`
Registro de intentos para el bloqueo temporal y la detección de fuerza bruta.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador del intento. |
| `usuario` | `VARCHAR(120)` | `NOT NULL` | Usuario intentado, aunque no exista ni cumpla el formato. |
| `ip` | `VARCHAR(45)` | `NULL` | IP del intento. |
| `exito` | `BOOLEAN` | `NOT NULL DEFAULT FALSE` | Resultado del intento. |
| `fecha` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Momento del intento. |

---

### Tabla `admin_auditoria`
Trazabilidad de acciones sensibles del panel.

| Campo | Tipo de Dato | Restricciones | Descripción |
| :--- | :--- | :--- | :--- |
| `id` | `SERIAL` | `PRIMARY KEY` | Identificador del evento. |
| `actor_id` | `INTEGER` | `FOREIGN KEY NULL` | Usuario que ejecutó la acción (`ON DELETE SET NULL`). |
| `accion` | `VARCHAR(50)` | `NOT NULL` | `login_ok`, `login_fail`, `logout`, `crear_usuario`, `editar_rol`, `reset_password`, `desactivar_usuario`, `cambiar_password`, etc. |
| `objetivo_id` | `INTEGER` | `FOREIGN KEY NULL` | Usuario afectado, si corresponde. |
| `detalle` | `JSONB` | `DEFAULT '{}'` | Datos adicionales sin contraseñas. |
| `ip` | `VARCHAR(45)` | `NULL` | IP de origen. |
| `fecha` | `TIMESTAMP` | `DEFAULT CURRENT_TIMESTAMP` | Fecha del evento. |

---

## 3. Script SQL de Ejemplo (`db/esquema.sql`)

```sql
-- ============================================================
-- Pedacito de Arte - Esquema PostgreSQL (Neon)
-- ============================================================

CREATE TABLE categorias (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE,
    slug VARCHAR(50) NOT NULL UNIQUE,
    descripcion TEXT,
    orden INTEGER DEFAULT 0
);

CREATE TABLE productos (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(20) NOT NULL UNIQUE,
    slug VARCHAR(80) NOT NULL UNIQUE
        CHECK (slug ~ '^[a-z0-9-]{3,80}$'),
    nombre VARCHAR(120) NOT NULL,
    categoria_id INTEGER REFERENCES categorias(id) ON DELETE SET NULL,
    descripcion TEXT NOT NULL,
    precio_base NUMERIC(12,2) DEFAULT 0.00,
    imagen_url VARCHAR(500) NOT NULL,
    imagen_public_id VARCHAR(255),
    imagen_alt VARCHAR(160) NOT NULL DEFAULT '',
    destacado BOOLEAN DEFAULT FALSE,
    disponible BOOLEAN DEFAULT TRUE,
    creado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    actualizado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE producto_imagenes (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER NOT NULL REFERENCES productos(id) ON DELETE CASCADE,
    url VARCHAR(500) NOT NULL,
    public_id VARCHAR(255),
    alt VARCHAR(160) NOT NULL,
    orden INTEGER DEFAULT 0
);

CREATE TABLE opciones_personalizacion (
    id SERIAL PRIMARY KEY,
    producto_id INTEGER NOT NULL REFERENCES productos(id) ON DELETE CASCADE,
    tipo_opcion VARCHAR(50) NOT NULL,
    valor VARCHAR(100) NOT NULL,
    recargo NUMERIC(12,2) DEFAULT 0.00,
    requerida BOOLEAN DEFAULT FALSE,
    multiple BOOLEAN DEFAULT FALSE,
    orden INTEGER DEFAULT 0
);

CREATE TABLE solicitudes_presupuesto (
    id SERIAL PRIMARY KEY,
    fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    cliente_nombre VARCHAR(120),
    cliente_contacto VARCHAR(120),
    monto_total NUMERIC(12,2) NOT NULL DEFAULT 0.00,
    estado VARCHAR(20) DEFAULT 'Enviado',
    observaciones TEXT
);

CREATE TABLE presupuesto_items (
    id SERIAL PRIMARY KEY,
    presupuesto_id INTEGER NOT NULL REFERENCES solicitudes_presupuesto(id) ON DELETE CASCADE,
    producto_id INTEGER REFERENCES productos(id) ON DELETE SET NULL,
    nombre_producto VARCHAR(120) NOT NULL,
    opciones_seleccionadas JSONB DEFAULT '[]',
    cantidad INTEGER NOT NULL CHECK (cantidad > 0),
    precio_unitario NUMERIC(12,2) NOT NULL,
    subtotal NUMERIC(12,2) NOT NULL,
    nota TEXT
);

-- ============================================================
-- Autenticación del panel interno
-- ============================================================

CREATE TABLE admin_usuarios (
    id SERIAL PRIMARY KEY,
    usuario VARCHAR(30) NOT NULL
        CHECK (usuario ~ '^[a-z0-9._-]{3,30}$'),
    nombre VARCHAR(120) NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    rol VARCHAR(20) NOT NULL DEFAULT 'editor'
        CHECK (rol IN ('super_admin', 'admin', 'editor')),
    activo BOOLEAN DEFAULT TRUE,
    debe_cambiar_password BOOLEAN DEFAULT TRUE,
    intentos_fallidos INTEGER DEFAULT 0,
    bloqueado_hasta TIMESTAMP,
    ultimo_acceso TIMESTAMP,
    creado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    actualizado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Login sin distinguir mayúsculas/minúsculas
CREATE UNIQUE INDEX idx_admin_usuario_lower ON admin_usuarios (LOWER(usuario));

CREATE TABLE sesiones_admin (
    id SERIAL PRIMARY KEY,
    usuario_id INTEGER NOT NULL REFERENCES admin_usuarios(id) ON DELETE CASCADE,
    token_hash VARCHAR(64) NOT NULL UNIQUE,
    creado_en TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expira_en TIMESTAMP NOT NULL,
    revocada_en TIMESTAMP,
    ip VARCHAR(45),
    user_agent VARCHAR(255)
);

CREATE TABLE admin_intentos_login (
    id SERIAL PRIMARY KEY,
    usuario VARCHAR(120) NOT NULL,
    ip VARCHAR(45),
    exito BOOLEAN NOT NULL DEFAULT FALSE,
    fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE admin_auditoria (
    id SERIAL PRIMARY KEY,
    actor_id INTEGER REFERENCES admin_usuarios(id) ON DELETE SET NULL,
    accion VARCHAR(50) NOT NULL,
    objetivo_id INTEGER REFERENCES admin_usuarios(id) ON DELETE SET NULL,
    detalle JSONB DEFAULT '{}',
    ip VARCHAR(45),
    fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Índices de apoyo
CREATE INDEX idx_productos_categoria ON productos(categoria_id);
CREATE INDEX idx_productos_disponible ON productos(disponible);
CREATE INDEX idx_opciones_producto ON opciones_personalizacion(producto_id);
CREATE INDEX idx_items_presupuesto ON presupuesto_items(presupuesto_id);
CREATE INDEX idx_admin_sesiones_usuario ON sesiones_admin(usuario_id);
CREATE INDEX idx_admin_sesiones_expira ON sesiones_admin(expira_en);
CREATE INDEX idx_admin_intentos_usuario ON admin_intentos_login(usuario, fecha);
CREATE INDEX idx_admin_intentos_ip ON admin_intentos_login(ip, fecha);
CREATE INDEX idx_admin_auditoria_fecha ON admin_auditoria(fecha);

-- ============================================================
-- Datos de ejemplo (db/seed.sql)
-- ============================================================
-- El super admin inicial NO se inserta aquí: se crea con
-- `npm run admin:reset -- <usuario> --rol super_admin` y se fuerza
-- el cambio de contraseña en el primer ingreso.

INSERT INTO categorias (nombre, slug, descripcion, orden) VALUES
('Agendas', 'agendas', 'Agendas personalizadas para distintos usos', 1),
('Agendas Docentes', 'agendas-docentes', 'Agendas con organización para docentes', 2),
('Revistas para Colorear', 'revistas-para-colorear', 'Revistas y actividades para colorear', 3),
('Anotadores', 'anotadores', 'Anotadores con hojas rayadas', 4);

INSERT INTO productos (codigo, slug, nombre, categoria_id, descripcion, precio_base, imagen_url, imagen_alt, destacado) VALUES
('AG-01', 'agenda-personalizada', 'Agenda personalizada', 1, 'Agenda artesanal con tapa e interior personalizables.', 0.00, '/media/productos/agenda.jpg', 'Agenda artesanal personalizada', TRUE),
('AG-DOC-01', 'agenda-docente', 'Agenda Docente', 2, 'Agenda A5 con organizador de alumnos, calificaciones y planificador mensual.', 0.00, '/media/productos/agenda-docente.jpg', 'Agenda Docente con planificador mensual', TRUE),
('REV-COL-01', 'revista-para-colorear', 'Revista para Colorear', 3, 'Revista de actividades con ilustraciones genéricas para pintar.', 0.00, '/media/productos/revista-colorear.jpg', 'Revista para colorear con ilustraciones infantiles', FALSE),
('ANO-01', 'anotador', 'Anotador', 4, 'Anotador artesanal con hojas rayadas y tapa personalizable.', 0.00, '/media/productos/anotador.jpg', 'Anotador artesanal con hojas rayadas', FALSE);

INSERT INTO opciones_personalizacion (producto_id, tipo_opcion, valor, recargo, requerida, multiple, orden) VALUES
(2, 'Interior', 'Hojas Lisas', 0.00, TRUE, FALSE, 1),
(2, 'Interior', 'Rayadas', 0.00, TRUE, FALSE, 2),
(2, 'Interior', 'Punteadas', 0.00, TRUE, FALSE, 3),
(2, 'Interior', 'Diagramado Docente', 500.00, TRUE, FALSE, 4),
(2, 'Laminado', 'Brillo', 0.00, FALSE, FALSE, 1),
(2, 'Laminado', 'Mate', 300.00, FALSE, FALSE, 2);

INSERT INTO producto_imagenes (producto_id, url, alt, orden) VALUES
(2, '/media/productos/agenda-docente-interior.jpg', 'Interior de la Agenda Docente con planificador mensual', 1),
(2, '/media/productos/agenda-docente-tapa.jpg', 'Tapa de la Agenda Docente', 2);
```

---

## 4. Reglas de Integridad y Negocio

- **Precios:** `precio_base = 0` representa "a presupuestar"; el total se calcula como `cantidad * (precio_base + recargos)`.
- **Histórico:** `presupuesto_items.nombre_producto` y `precio_unitario` se copian al momento del pedido para que cambios futuros no alteren presupuestos viejos.
- **Opciones:** los grupos con `requerida = TRUE` deben validarse en la API antes de registrar el presupuesto.
- **Imágenes:** en PostgreSQL se guardan `url` y `public_id`; los archivos viven en Cloudinary o, para el seed, en `public/media/`. Las fotos del catálogo deben ser reales.
- **Borrado:** productos con presupuestos asociados no se eliminan físicamente; se marcan con `disponible = FALSE`.
- **Usuarios admin:** `usuario` es el identificador de login (sin email) y se normaliza a minúsculas. La contraseña se guarda **siempre** como hash `bcryptjs`; nunca en texto plano.
- **Sesiones:** el token original vive solo en la cookie `httpOnly`; en la base se guarda su hash SHA-256. Resetear contraseña, cambiar rol o desactivar un usuario **revoca todas sus sesiones**.
- **Roles:** los permisos se validan en la API, no en el cliente. `super_admin` gestiona usuarios; `admin` gestiona catálogo y presupuestos; `editor` gestiona catálogo sin borrar ni ver presupuestos.
- **Último super admin:** no se puede desactivar, degradar ni eliminar al último `super_admin` activo.
- **Auditoría:** reseteos, cambios de rol, altas/bajas y accesos quedan registrados en `admin_auditoria` (sin contraseñas ni tokens).
