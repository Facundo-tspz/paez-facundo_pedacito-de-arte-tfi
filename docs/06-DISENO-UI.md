# 06 - Diseño de Interfaz de Usuario (UI): Pedacito de Arte

Este documento especifica la guía de estilos visuales, la aplicación de la identidad de marca en los componentes de interfaz de usuario y el mapa de pantallas de la aplicación web de **Pedacito de Arte**.

> **Regla de tokens:** no se usan HEX sueltos en los componentes. Los colores se consumen como clases del theme de Tailwind, definidas a partir de las variables de `src/styles/index.css`.

---

## 1. Tokens de Color y su Equivalente en Tailwind

| Token CSS | Clave Tailwind | Clase de ejemplo | HEX | Uso |
| :--- | :--- | :--- | :--- | :--- |
| `--color-fondo` | `fondo` | `bg-fondo` | `#FAF3DD` | Fondo dominante y tarjetas. |
| `--color-principal` | `principal` | `text-principal` | `#264653` | Títulos, textos principales y bordes. |
| `--color-complementario-principal` | `dorado` | `bg-dorado` | `#E9C46A` | Badges y detalles. |
| `--color-secundario` | `secundario` | `bg-secundario` | `#E76F51` | Acentos y hover del CTA. |
| `--color-secundario-acento` | `acento` | `bg-acento` | `#C14E33` | CTA con texto blanco (contraste AA). |
| `--color-complementario-secundario` | `verde` | `text-verde` | `#2A9D8F` | Estados, focos y acompañamiento. |
| `--color-contraste-alto` | `contraste` | `text-contraste` | `#5F4A39` | Textos secundarios y bordes. |

---

## 2. Aplicación de la Identidad de Marca en Componentes UI

| Elemento UI | Clases de Tailwind CSS / DaisyUI | Aplicación de Color y Fuente |
| :--- | :--- | :--- |
| **Fondo Principal** | `bg-fondo` | Tono Crema Pálido para un estilo cálido, limpio y artesanal. |
| **Encabezados / Títulos** | `font-serif text-principal` | *Cormorant Garamond* en Azul Medianoche. |
| **Cuerpo y Textos UI** | `font-sans text-contraste` | *Poppins* en Marrón Chocolate para máxima legibilidad. |
| **Botón Primario (CTA)** | `btn bg-acento text-white hover:bg-secundario` | Variante accesible del Rojo Coral con texto blanco (≥ 4.5:1). |
| **Botón Secundario / Filtros** | `btn btn-outline border-principal text-principal` | Azul Medianoche para acciones secundarias. |
| **Badges / Etiqueta Destacada** | `badge bg-dorado text-principal font-semibold` | Naranja Cálido para "Nuevo", "Destacado" o "Agenda Docente". |
| **Tarjetas de Productos** | `card bg-white shadow-md border border-fondo rounded-xl` | Tarjetas blancas con sombras suaves y bordes redondeados. |
| **Buscador e Insumos** | `input input-bordered border-principal focus:ring-verde` | Bordes en Azul Medianoche con foco en Verde Azulado. |
| **Estados de error** | `text-acento` + `aria-live` | Mensajes de validación visibles y anunciados. |

> **Accesibilidad (RNF04):** el Rojo Coral `#E76F51` con texto blanco da ≈ 3.1:1 y **no** cumple AA para texto normal. Por eso el CTA usa `acento` (`#C14E33`, ≈ 4.8:1). No volver a usar `bg-secundario` con texto blanco en botones de texto normal.

---

## 3. Mapa y Estructura de Pantallas

### 1. Portada / Inicio (`HomePage.jsx`)
- **Header / Navbar:** Logo oficial a la izquierda, menú de navegación al centro y botón del Carrito de Presupuesto a la derecha con contador (`badge`).
- **Hero Section:** fondo `bg-fondo`, eslogan *"Papelería creativa hecha con amor"*, foto destacada de agendas artesanales y CTA hacia el catálogo.
- **Grilla de Destacados:** 4 a 6 productos estrella (Agendas Docentes, Recetarios, Libros para Colorear de Bluey).
- **Propuesta de Valor:** tres bloques con íconos de *Lucide React*: *100% Personalizado*, *Diseño Artesanal* y *Atención Directa en Ferias y WhatsApp*.

### 2. Catálogo Interactivo (`CatalogPage.jsx`)
- **Barra Superior de Control:** buscador en tiempo real y selectores de categoría en *pills* (Agendas, Cuadernos, Infantil, Recetarios, Eventos).
- **Grilla Responsiva:** escritorio 4 columnas (`grid-cols-4`), tablet 2 (`grid-cols-2`), móvil 1 (`grid-cols-1`).
- **Tarjeta de Producto (`TarjetaProducto.jsx`):** imagen con badge de categoría, nombre en *Cormorant Garamond*, descripción breve, precio base o "A presupuestar" y botón "Ver Opciones / Agregar" en `bg-acento`.
- **Estados:** skeleton durante la carga, estado vacío con mensaje cuando no hay resultados.

### 3. Ficha de Detalle y Personalización (`ProductDetailPage.jsx`)
- Galería de imágenes del producto (usa `producto_imagenes`) con `alt` descriptivo.
- Selectores interactivos:
  - *Tipo de Interior:* Hojas Lisas, Rayadas, Punteadas o Diagramado Docente.
  - *Acabado de Tapa:* Laminado Brillo o Laminado Mate.
  - *Notas de Personalización:* campo de texto para el nombre o frase a estampar.
- Muestra el recargo de cada opción y actualiza el precio en vivo.
- Botón "Agregar al Presupuesto" que dispara un toast con **Sonner**.

### 4. Drawer / Carrito de Presupuesto (`CarritoDrawer.jsx`)
- Panel lateral con lista de artículos, variantes de personalización, eliminar ítems y subtotal.
- Botón de enlace a WhatsApp destacado con el ícono de WhatsApp, que ejecuta `whatsappService.js`.

### 5. Servicios e Imprenta (`ServicesPage.jsx`)
- Bloques informativos sobre diseño gráfico, impresión, diplomas, banners, stickers y encuadernación.
- CTA hacia el formulario de contacto y hacia WhatsApp.

### 6. Contacto / Presupuesto Especial (`ContactPage.jsx`)
- Formulario con React Hook Form: nombre, contacto, tipo de trabajo, descripción y adjunto opcional.
- Validación accesible (`aria-invalid`, mensajes con `aria-live`) y toast de confirmación.
- Datos de contacto del taller y horarios de feria.

### 7. Panel de Administración (`/admin/*`)
- `AdminLoginPage.jsx`: usuario + contraseña (sin email). Botón "¿Olvidaste tu contraseña?" que abre un modal con el mensaje *"Comunicate con el administrador principal para restablecerla."* No hay recuperación por email.
- `AdminDashboardPage.jsx`: resumen de productos y presupuestos según el rol.
- `AdminProductosPage.jsx`: tabla con alta/edición/baja lógica, opciones de personalización e imágenes. El rol `editor` no ve acciones de borrado.
- `AdminPresupuestosPage.jsx`: visible para `admin` y `super_admin`; listado con estado (`Enviado`, `En Taller`, `Entregado`).
- `AdminUsuariosPage.jsx` (solo `super_admin`): tabla con usuario, nombre, rol, estado y último acceso; permite crear, cambiar rol, activar/desactivar y resetear contraseña.
- `AdminPerfilPage.jsx`: cambio de contraseña propia (actual + nueva + repetir).
- `ResetPasswordModal`: muestra la contraseña temporal **una sola vez**, con botón de copiar y aviso de cambio obligatorio.
- `AdminLayout.jsx`: menú filtrado por rol, nombre/rol visible y logout.
- Si `debe_cambiar_password = TRUE`, se redirige a `AdminPerfilPage` y se bloquea el resto del panel hasta completar el cambio.
- Estética sobria: `bg-fondo`, tarjetas blancas y acentos en `principal`/`acento`; sin animaciones decorativas.

### 8. Error 404 (`NotFoundPage.jsx`)
- Mensaje amable, ilustración/isologo y botón de regreso al catálogo.

---

## 4. Estados Visuales y Responsive Design

- **Skeletons de Carga:** siluetas animadas con DaisyUI (`skeleton h-32 w-full`) durante las consultas.
- **Notificaciones Toast (Sonner):** esquina inferior derecha al agregar o quitar productos.
- **Optimizaciones Móviles:** menú hamburguesa y botón flotante de WhatsApp permanente en el margen inferior derecho.
- **Foco visible:** todos los interactivos deben mostrar `focus-visible` con anillo en `verde`.
- **Estados vacíos:** catálogo sin resultados, carrito vacío y listados admin sin datos con mensaje y acción sugerida.
- **Estados de autenticación:** credenciales inválidas, cuenta bloqueada, sesión expirada y cambio de contraseña obligatorio, con mensajes claros y sin revelar si el usuario existe.
