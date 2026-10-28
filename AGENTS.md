# Pedacito de Arte: Instrucciones para Agentes de IA

Stack Principal: React 18 + Vite (SPA) + Netlify Functions 2.0 + PostgreSQL (Neon) + Cloudinary + GitHub + WhatsApp

Este documento establece las reglas, el contexto y las directrices obligatorias para cualquier Agente de Inteligencia Artificial o herramienta de desarrollo asistido que colabore en el armado, maquetación o codificación del sistema web de **Pedacito de Arte**.

---

## 1. Misión y Lectura Obligatoria

Antes de generar, modificar o sugerir cualquier línea de código, estilos visuales o componentes, el agente **debe leer y respetar estrictamente** la especificación completa del proyecto almacenada en la carpeta `docs/`:

1. `docs/IDENTIDAD-MARCA.md` — Manual de marca, colores HEX, tipografías y tono de voz.
2. `docs/CONSTITUCION.md` — Principios obligatorios del proyecto.
3. `docs/01-CONTEXTO.md` — Propósito del negocio, catálogo de productos y alcance del sistema.
4. `docs/02-REQUERIMIENTOS.md` — Requerimientos funcionales (RF) y no funcionales (RNF).
5. `docs/03-TECNOLOGIAS.md` — Stack tecnológico oficial y librerías permitidas.
6. `docs/04-ARQUITECTURA.md` — Estructura de archivos y organización del código.
7. `docs/05-DATOS.md` — Esquema de base de datos SQL y modelos.
8. `docs/06-DISENO-UI.md` — Reglas de interfaz de usuario y componentes.
9. `docs/07-CONVENCIONES.md` — Estándares de código, nombres de variables y commits.
10. `docs/08-TAREAS.md` — Hoja de ruta por etapas para ejecución secuencial.
11. `docs/09-SEO.md` — Metadatos, posicionamiento web y accesibilidad.
12. `docs/10-SEGURIDAD.md` — Políticas de protección de datos y variables de entorno.
13. `docs/11-GITHUB-Y-DESPLIEGUE.md` — Repositorio, ramas, variables y checklist de entrega.
14. `docs/PROMPTS.md` — Registro de prompts y errores de IA.
15. `docs/DECLARACION-IA.md` — Declaración de uso de IA.
16. `docs/CAMBIOS-DOCUMENTACION.md` — Registro de cambios y decisiones sobre la documentación.
17. `docs/PROGRESO.md` — Bitácora de avance y estado para retomar el trabajo en otro chat o con otra IA.
18. `memory.md` — Memoria operativa del proyecto.
19. La spec activa en `specs/` — Requisitos, decisiones, plan, tareas y verificación.

---

## 2. Reglas Generales de Trabajo (Lo que DEBE hacer la IA)

- **Coherencia Visual:** Utilizar exclusivamente las variables CSS o clases de Tailwind derivadas de `IDENTIDAD-MARCA.md` (`--color-principal`, `--color-complementario-principal`, `--color-secundario`, `--color-secundario-acento`, `--color-fondo`, `--color-contraste-alto`, etc.).
- **Consultas a Base de Datos:** Centralizar las consultas SQL únicamente en `lib/db.js`, ejecutado del lado servidor dentro de las Netlify Functions (`netlify/functions/api.js`, rutas `/api/*`). El cliente nunca accede a `DATABASE_URL` ni escribe SQL. Utilizar siempre consultas parametrizadas.
- **Autenticación del Panel:** La autenticación del admin es propia y multiusuario (usuario + contraseña, roles `super_admin`, `admin`, `editor`), validada en `/api` con `requireAuth([...roles])`. Prohibido usar proveedores externos de identidad.
- **Imágenes:** Las fotos del catálogo son reales. Las subidas del admin van a Cloudinary; en PostgreSQL se guardan `url` y `public_id`. Las claves de Cloudinary nunca llegan al cliente.
- **Formato de Respuestas:** Escribir código modular, limpio y comentado en español, siguiendo la sintaxis estándar de React 18 + Vite.
- **Desarrollo Guiado por Tareas:** Ejecutar las tareas de a una por vez, basándose en `08-TAREAS.md` y en la spec activa de `specs/`. Ninguna tarea se cierra sin actualizar su `verificacion.md`.
- **Uso de IA:** Registrar prompts y errores en `docs/PROMPTS.md`; declarar las piezas generadas en `docs/DECLARACION-IA.md` y `multimedia/FICHAS-IA.md`. No enviar datos personales ni secretos a herramientas de IA.

---

## 3. Prohibiciones Estrictas (Lo que NUNCA debe hacer la IA)

- **NO inventar ni modificar colores o tipografías:** No usar colores en código duro (hardcoded HEX arbitrarios).
- **NO exponer credenciales:** Jamás incluir `DATABASE_URL`, `CLOUDINARY_API_SECRET`, `AUTH_SECRET` o datos sensibles en el código del lado del cliente, en capturas o en el chat.
- **NO usar autenticación externa:** Nada de Auth0, Clerk, Supabase Auth ni Firebase; el login es propio sobre PostgreSQL.
- **NO guardar contraseñas en texto plano:** Solo hashes `bcryptjs`. El script de emergencia `admin:reset` es local y nunca se expone por API.
- **NO usar Vercel Blob:** El storage oficial es Cloudinary. No agregar dependencias de Vercel ni `BLOB_READ_WRITE_TOKEN`.
- **NO inventar datos del negocio:** Precios, medidas, diseños, testimonios y marcas se confirman con la emprendedora. Si faltan, usar “A presupuestar” o un placeholder explícito.
- **NO asumir requerimientos ambiguos:** Si una consigna o especificación técnica presenta ambigüedad, la IA debe formular preguntas en lugar de alucinar o suponer la implementación.
- **NO incluir pasarelas de pago ni cuentas de usuario:** El alcance actual (fase 1) especifica la gestión de carrito de presupuesto y derivación de pedidos directamente a WhatsApp. El panel de administración es de uso interno del taller y no constituye una cuenta de cliente.

---

## 4. Criterios de Confirmación

Cualquier cambio realizado en el código o en la especificación debe poder previsualizarse correctamente en VS Code (`Ctrl + Shift + V` para archivos Markdown) o verificarse en la consola sin errores de compilación. Antes de cerrar una tarea se ejecutan `npm run lint`, `npm run test` y `npm run build`, y se actualiza la verificación de la spec. No se commitean secretos ni se hace push sin autorización explícita.