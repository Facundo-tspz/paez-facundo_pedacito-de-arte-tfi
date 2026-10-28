# 01 - Contexto del Proyecto: Pedacito de Arte

Este documento especifica el marco conceptual, la problemática comercial, los objetivos, el público destinatario y el alcance de la plataforma web de **Pedacito de Arte**.

---

## 1. Problema de Negocio

Actualmente, **Pedacito de Arte** exhibe y comercializa sus productos de papelería artesanal principalmente a través de puestos físicos en ferias locales de los fines de semana y mediante consultas informales por Instagram y WhatsApp.

Esta modalidad presenta las siguientes limitaciones:
- **Catálogo Desorganizado:** Los clientes no pueden visualizar de forma clara y ordenada la variedad completa de modelos, formatos de hojas, tipos de encuadernación ni temáticas disponibles (como agendas docentes, profesionales, recetarios o libros infantiles para colorear).
- **Proceso Manual Repetitivo:** El proceso de atención al cliente requiere responder de manera individual consultas sobre modelos, precios y opciones de personalización, lo que genera demoras en la respuesta y pérdida de ventas oportunas.
- **Dificultad para Armar Presupuestos:** Al no contar con una herramienta interactiva donde el usuario seleccione productos y opciones personalizadas, el armado de pedidos resulta confuso para el comprador.

---

## 2. Objetivo del Proyecto

Desarrollar un sitio web comercial y catálogo interactivo para **Pedacito de Arte** que permita:
1. Exhibir la totalidad del catálogo de productos y servicios de impresión y encuadernación organizada por categorías y etiquetas.
2. Permitir a los usuarios personalizar sus pedidos (tipo de tapa, diseño, contenido interno) y agregarlos a un carrito de presupuesto.
3. Derivar la orden de pedido estructurada directamente hacia el WhatsApp del taller para concretar el pago y la entrega de forma directa y cercana.
4. Proveer un panel de administración interno (no público) con login propio por usuario y contraseña y roles (`super_admin`, `admin`, `editor`), para gestionar categorías, productos, opciones de personalización, imágenes, presupuestos y usuarios, sin depender de cambios manuales en la base de datos.

---

## 3. Público Destinatario

La plataforma está diseñada para tres perfiles de usuarios principales:
1. **Profesionales y Docentes:** Educadores, médicos, odontólogos y profesionales independientes que requieren agendas personalizadas con planificadores adaptados a sus rutinas de trabajo.
2. **Familias y Público Infantil:** Padres, madres y tutores que buscan libros temáticos para colorear y actividades recreativas (por ejemplo, temáticas como Bluey), además de artículos escolares personalizados.
3. **Público General y Amantes de la Papelería:** Personas organizadas que valoran los productos artesanales e impresos hechos con dedicación, buscando planificadores semanales, cuadernos de notas, recetarios y regalos personalizados.

---

## 4. Productos y Servicios Ofrecidos

### Catálogo de Productos Impresos y Artesanales
- **Agendas Personalizadas (Producto Estrella):** Agendas anuales, docentes, perpetuas y profesionales con opción de tapa dura, laminado y elección de interiores.
- **Anotadores y Cuadernos:** Cuadernos A5 y A6 anillados con hojas lisas, rayadas o punteadas.
- **Libros para Colorear y Recreativos:** Revistas temáticas e infantiles con dibujos y actividades para niños.
- **Recetarios de Cocina:** Cuadernos organizadores para guardar recetas culinarias con secciones temáticas.
- **Papelería para Eventos y Emprendedores:** Banners, certificados, diplomas, stickers en plancha/cortados y tarjetería personal.

### Servicios
- **Diseño Gráfico Personalizado:** Adaptación e impresión de maquetas y solicitudes específicas a pedido del cliente.
- **Encuadernación y Terminaciones:** Laminados (brillo/mate), anillados dobles y corte personalizado.

---

## 5. No Incluye (Por Ahora - Fuera del Alcance de la Fase 1)

Para mantener la simplicidad operativa del taller y asegurar una entrega rápida del sistema, la primera versión del proyecto **excluye explícitamente**:
- **Pasarelas de pago en línea integrada:** No se procesarán cobros automáticos con tarjetas o Mercado Pago en la web; los pagos se coordinan post-pedido por WhatsApp o en la feria.
- **Gestión de cuentas de usuarios y logins:** Los clientes no requerirán crear usuario ni contraseña para armar y enviar sus pedidos.
- **Calcular costos de envío automático:** La entrega (vía retiro en feria, taller o envío local) se pacta individualmente tras la recepción del pedido en WhatsApp.

> El panel de administración mencionado en el objetivo 4 es de uso interno del taller y **no** forma parte de las cuentas de cliente excluidas.