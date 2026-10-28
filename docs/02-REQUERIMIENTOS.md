# 02 - Requerimientos del Sistema: Pedacito de Arte

Este documento detalla la lista de requerimientos funcionales (RF) y no funcionales (RNF) que debe cumplir la plataforma web de **Pedacito de Arte**.

---

## 1. Requerimientos Funcionales (RF)

| ID | Requerimiento Funcional | Descripción | Prioridad |
| :--- | :--- | :--- | :--- |
| **RF01** | Catálogo interactivo de productos | Mostrar la lista completa de productos (agendas, agendas docentes, revistas para colorear y anotadores) con imagen, título, descripción y precio orientativo. | **Alta** |
| **RF02** | Filtrado por categoría | Permitir al usuario filtrar los productos por categorías (*Agendas*, *Agendas Docentes*, *Revistas para Colorear*, *Anotadores*). | **Alta** |
| **RF03** | Búsqueda por palabra clave | Incluir un buscador en tiempo real para encontrar productos por nombre o temática (ej. "Docente", "A5", "infantil"). | **Media** |
| **RF04** | Detalle de producto y personalización | Permitir ver la ficha detallada de un producto con sus opciones de personalización (tipo de interior, diseño de tapa, tipo de anillado). | **Alta** |
| **RF05** | Carrito de presupuesto | Permitir agregar, modificar cantidades y quitar productos de un carrito de compras temporal. | **Alta** |
| **RF06** | Envío de pedido por WhatsApp | Generar un mensaje estructurado con el resumen del carrito (productos, detalles seleccionados y monto total) y redirigir al WhatsApp del taller. | **Alta** |
| **RF07** | Sección de Servicios e Impresiones | Mostrar una página informativa sobre los servicios de diseño gráfico, impresión personalizada y encuadernaciones para eventos o comercios. | **Media** |
| **RF08** | Formulario de consulta personalizada | Formulario de contacto para solicitar presupuestos especiales de trabajos a medida que no estén en el catálogo estándar. | **Media** |
| **RF09** | Panel de administración interno | Permitir al taller iniciar sesión con usuario y contraseña, y gestionar categorías, productos, opciones de personalización e imágenes según el rol (`super_admin`, `admin`, `editor`). | **Alta** |
| **RF10** | Registro de presupuestos | Guardar en PostgreSQL cada presupuesto derivado a WhatsApp con su detalle de ítems y estado, para seguimiento interno del taller. | **Media** |
| **RF11** | Gestión de usuarios administradores | Permitir al `super_admin` crear usuarios, asignar roles, activar/desactivar cuentas y restablecer contraseñas (con cambio obligatorio). El login es por usuario, sin email. | **Alta** |

---

## 2. Requerimientos No Funcionales (RNF)

- **RNF01 - Diseño Responsivo:** La interfaz debe adaptarse perfectamente a dispositivos móviles, tablets y computadoras de escritorio (Mobile First, dado que el público de Instagram ingresa desde celulares).
- **RNF02 - Rendimiento y Velocidad:** La página debe cargar en menos de 2 segundos en conexiones móviles promedio.
- **RNF03 - Identidad Visual y Usabilidad:** Aplicar estrictamente la paleta de colores (`#FAF3DD`, `#264653`, `#E76F51`, `#E9C46A`, `#2A9D8F`) y las tipografías (*Cormorant Garamond* y *Poppins*) definidas en `IDENTIDAD-MARCA.md`.
- **RNF04 - Accesibilidad:** Garantizar contraste mínimo WCAG AA (4.5:1 en texto normal y 3:1 en texto grande), etiquetas `alt` descriptivas en todas las fotos de productos, foco visible, navegación por teclado e íconos legibles.
- **RNF05 - Disponibilidad y Despliegue:** El sitio debe estar alojado en Netlify con HTTPS, deploy continuo desde GitHub y Functions para `/api/*`.
- **RNF06 - SEO y Compartibilidad:** Cada pantalla debe exponer metadatos únicos y Open Graph válido. El prerender de Inicio, Catálogo y Servicios es deseable; si no se implementa, se justifica en `docs/09-SEO.md`.
- **RNF07 - Autenticación Interna:** Contraseñas hasheadas (`bcryptjs`), sesión en cookie `httpOnly` de 8 horas, bloqueo temporal por intentos fallidos y permisos validados en el servidor según rol.
- **RNF08 - Operación Gratuita y Monitoreo:** El MVP debe funcionar dentro de los planes gratuitos de Netlify, Neon y Cloudinary, con revisión periódica de cuotas y sin tarjetas de crédito obligatorias.

---

## 3. Ejemplo de Historia de Usuario y Criterios de Aceptación

### Historia de Usuario (HU-01)
> **Como** cliente interesado en la papelería artesanal,  
> **Quiero** seleccionar mis productos y personalizaciones en la web para enviarlos por WhatsApp,  
> **Para** no tener que redactar la lista de pedido manualmente ni consultar precios uno por uno.

### Criterios de Aceptación
- [x] El botón "Agregar al Carrito" incrementa el contador visual en el encabezado.
- [x] El usuario puede abrir la vista del carrito y modificar la cantidad de cada ítem o eliminarlo.
- [x] El sistema calcula automáticamente el monto subtotal de la compra.
- [x] Al hacer clic en "Confirmar pedido por WhatsApp", se abre la aplicación de WhatsApp con un mensaje preformateado que incluye la lista detallada y el total del presupuesto.

### Historia de Usuario (HU-02)
> **Como** dueño del taller,  
> **Quiero** administrar usuarios, roles y contraseñas desde un panel propio,  
> **Para** dar acceso controlado a mi equipo sin depender de servicios externos.

### Criterios de Aceptación
- [ ] El login usa usuario y contraseña; no se solicita email.
- [ ] Tras 5 intentos fallidos en 15 minutos, la cuenta se bloquea temporalmente.
- [ ] El `super_admin` puede crear usuarios y asignar rol `admin` o `editor`.
- [ ] El `super_admin` puede resetear una contraseña; el usuario debe cambiarla al ingresar y sus sesiones previas se revocan.
- [ ] El botón "¿Olvidaste tu contraseña?" indica comunicarse con el administrador principal.
- [ ] Un `editor` no puede borrar productos ni ver presupuestos; un `admin` no puede gestionar usuarios.
- [ ] No se puede desactivar, degradar ni eliminar al último `super_admin` activo.