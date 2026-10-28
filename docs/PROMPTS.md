# Registro de prompts y errores de IA

Este documento cumple el requisito del TFI de registrar prompts relevantes y al menos tres errores detectados en el uso de IA, con su corrección y evidencia.

## Formato de registro

| Campo | Contenido |
| --- | --- |
| Fecha | `AAAA-MM-DD` |
| Herramienta | Modelo o asistente usado |
| Objetivo | Qué se intentaba resolver |
| Prompt | Resumen fiel del pedido |
| Resultado | Qué produjo la IA |
| Error | Qué estaba mal o era ambiguo |
| Detección | Cómo se detectó |
| Corrección | Qué se cambió |
| Evidencia | Archivo, commit o prueba |

## Errores detectados y corregidos

### Caso 1 — Hosting y storage asumidos sin verificar límites

- **Fecha:** 2026-10-28
- **Herramienta:** asistente de IA en el chat de planificación.
- **Objetivo:** definir el despliegue del MVP.
- **Prompt:** «Armá la arquitectura para publicar el catálogo con panel admin y base de datos».
- **Resultado:** la primera versión asumió Vercel con Vercel Blob y Next.js.
- **Error:** no se verificaron los límites comerciales del plan gratuito ni la necesidad real de Next.js.
- **Detección:** al comparar alternativas y revisar los términos de uso y cuotas de Vercel, Neon y Cloudinary.
- **Corrección:** se adoptó React 18 + Vite + Netlify Functions + Neon + Cloudinary. La decisión y los límites quedaron en `docs/03-TECNOLOGIAS.md`.
- **Evidencia:** `docs/03-TECNOLOGIAS.md`, `docs/CAMBIOS-DOCUMENTACION.md` §7.

### Caso 2 — Presupuesto sin detalle de ítems

- **Fecha:** 2026-10-28
- **Herramienta:** asistente de IA en la documentación.
- **Objetivo:** modelar el carrito de presupuesto.
- **Prompt:** «Definí las tablas para guardar los presupuestos enviados a WhatsApp».
- **Resultado:** una tabla `presupuestos` con total y datos de contacto, sin detalle de productos.
- **Error:** se perdía el detalle de productos, variantes y cantidades; el total no era reconstruible ni auditable.
- **Detección:** revisión del esquema contra el requerimiento de carrito y el mensaje de WhatsApp.
- **Corrección:** se agregó `presupuesto_items` con producto, variante, cantidad, precio unitario y subtotal históricos.
- **Evidencia:** `docs/05-DATOS.md`, `specs/002-presupuesto-whatsapp/`.

### Caso 3 — Autenticación de un solo administrador

- **Fecha:** 2026-10-28
- **Herramienta:** asistente de IA en la documentación.
- **Objetivo:** definir el acceso al panel.
- **Prompt:** «Agregá login para el panel de administración».
- **Resultado:** una única contraseña de administrador guardada en una variable de entorno.
- **Error:** no permitía multiusuario, roles, auditoría, revocación de sesiones ni recuperación segura; contradecía el requerimiento del panel.
- **Detección:** contraste con `AGENTS.md` y con el requerimiento de roles `super_admin`, `admin` y `editor`.
- **Corrección:** autenticación propia con `admin_usuarios`, `sesiones_admin`, `admin_intentos_login`, `admin_auditoria`, `bcryptjs` y tokens opacos.
- **Evidencia:** `docs/05-DATOS.md`, `docs/10-SEGURIDAD.md`, `specs/003-panel-admin/`.

## Prompts relevantes para multimedia

| Fecha | Pieza | Herramienta | Prompt resumido | Resultado | Revisión humana |
| --- | --- | --- | --- | --- | --- |
| Pendiente | Hero del taller | Pendiente | Escena cálida de taller artesanal, sin marcas ni texto | Pendiente | Pendiente |
| Pendiente | Agenda docente | Pendiente | Producto ficticio sobre escritorio, luz natural | Pendiente | Pendiente |
| Pendiente | Revista infantil | Pendiente | Diseño genérico, sin personajes registrados | Pendiente | Pendiente |
| Pendiente | Mockups | Pendiente | Notebook y celular mostrando el sitio | Pendiente | Pendiente |

## Prompts de desarrollo

| Fecha | Objetivo | Prompt resumido | Archivos afectados | Revisión |
| --- | --- | --- | --- | --- |
| 2026-10-28 | Migrar documentación a Netlify/Neon/Cloudinary | Reemplazar Vercel y Blob, justificar el stack y actualizar variables | `docs/`, `AGENTS.md` | Pendiente de verificación final |
| 2026-10-28 | Crear arnés SDD | Generar constitución, specs, comandos y skills | `docs/CONSTITUCION.md`, `specs/`, `.agents/`, `.opencode/` | Pendiente de revisión |

## Regla de uso

No se pegan en este archivo claves, tokens, datos personales de clientes ni conversaciones privadas. Los prompts se resumen y se vinculan a la pieza o tarea correspondiente.
