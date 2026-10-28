# Pedacito de Arte

Sistema web del taller **Pedacito de Arte**, desarrollado como Trabajo Final Integrador (TFI / TP N.º 4). El sitio presenta el catálogo de productos, permite armar un carrito de presupuesto y deriva el pedido a WhatsApp. Incluye un panel de administración interno con autenticación propia y roles.

## Estado

- **Fase actual:** 0 completada — estructura, documentación SDD y specs publicadas.
- **MVP:** catálogo, carrito de presupuesto, WhatsApp y panel de administración.
- **Sitio publicado:** pendiente de conectar Netlify.
- **Repositorio:** <https://github.com/Facundo-tspz/paez-facundo_pedacito-de-arte-tfi> (commit inicial `8595e7b`).
- **Nombre previsto del repositorio:** `iest-tfi_pedacito-de-arte` (el renombre se confirma en GitHub).

## Stack

| Capa | Tecnología |
| --- | --- |
| Interfaz | React 18 + Vite + React Router + Tailwind CSS + DaisyUI |
| API | Netlify Functions 2.0 (`netlify/functions/api.js`) |
| Base de datos | PostgreSQL en Neon |
| Imágenes | Cloudinary |
| Autenticación | Propia, usuario + contraseña, sesiones opacas y roles |
| Deploy | Netlify + GitHub |
| Pruebas | Vitest + Testing Library; Playwright si el tiempo lo permite |

El cambio respecto del stack sugerido en clase (Next.js + Vercel) está justificado en `docs/03-TECNOLOGIAS.md`.

## Documentación

La lectura obligatoria antes de modificar el proyecto está en `AGENTS.md`. Los documentos principales son:

- `docs/IDENTIDAD-MARCA.md`
- `docs/CONSTITUCION.md`
- `docs/01-CONTEXTO.md`
- `docs/02-REQUERIMIENTOS.md`
- `docs/03-TECNOLOGIAS.md`
- `docs/04-ARQUITECTURA.md`
- `docs/05-DATOS.md`
- `docs/06-DISENO-UI.md`
- `docs/07-CONVENCIONES.md`
- `docs/08-TAREAS.md`
- `docs/09-SEO.md`
- `docs/10-SEGURIDAD.md`
- `docs/11-GITHUB-Y-DESPLIEGUE.md`
- `docs/PROMPTS.md`
- `docs/DECLARACION-IA.md`
- `docs/CAMBIOS-DOCUMENTACION.md`
- `docs/PROGRESO.md`
- `memory.md`

Las especificaciones SDD están en `specs/`.

## Estructura

```text
.
├── .agents/skills/            # Skills del proyecto
├── .opencode/commands/        # Comandos reutilizables
├── docs/                      # Documentación y bitácora
├── specs/                     # Especificaciones SDD
├── public/
│   ├── marca/                 # Logos e identidad visual
│   └── media/                 # Imágenes públicas del catálogo
├── multimedia/                # Piezas generadas con IA y fichas
├── src/                       # Aplicación React
├── tests/                     # Pruebas automatizadas
├── netlify/                   # Functions (se crea en la Fase 1)
├── lib/                       # Acceso a datos y helpers (Fase 1)
├── db/                        # Esquema y seed (Fase 1)
├── netlify.toml               # Configuración de Netlify (Fase 1)
└── AGENTS.md                  # Reglas para agentes de IA
```

## Puesta en marcha

```bash
npm install
cp .env.example .env
npm run dev
```

Para probar las Functions en local se usará Netlify CLI:

```bash
npm run dev:netlify
```

Los scripts definitivos se agregan en la Fase 1 junto con el scaffold de Vite.

## Variables de entorno

| Variable | Ámbito | Uso |
| --- | --- | --- |
| `DATABASE_URL` | Servidor | Conexión a Neon |
| `AUTH_SECRET` | Servidor | Firma de tokens CSRF y material auxiliar |
| `SESSION_TTL_HOURS` | Servidor | Duración de la sesión, por defecto 8 |
| `CLOUDINARY_CLOUD_NAME` | Servidor | Cuenta de Cloudinary |
| `CLOUDINARY_API_KEY` | Servidor | API key de Cloudinary |
| `CLOUDINARY_API_SECRET` | Servidor | API secret de Cloudinary |
| `VITE_WHATSAPP_PHONE_NUMBER` | Cliente | Número público de WhatsApp |

Nunca se commitea `.env`. Las variables reales se cargan en Netlify. El cliente no recibe `DATABASE_URL` ni las claves de Cloudinary.

## Comandos SDD

- `/spec` — redactar o actualizar una especificación.
- `/clarificar` — revisar ambigüedades antes de codificar.
- `/plan` — proponer un plan técnico sin escribir código.
- `/tareas` — dividir el plan en tareas de menos de 30 minutos.
- `/verificar` — ejecutar pruebas y contrastar criterios.
- `/cambio` — aplicar un cambio con spec, código, pruebas y commit.

## Seguridad

- Autenticación propia sobre PostgreSQL; sin Auth0, Clerk, Supabase Auth ni Firebase.
- Contraseñas con `bcryptjs` (costo 12). Prohibido texto plano.
- Sesiones con token opaco almacenado como hash; cookie `HttpOnly`, `Secure` en producción y `SameSite=Strict`.
- Consultas SQL parametrizadas en `lib/db.js`; el navegador nunca ejecuta SQL.
- Sin pasarelas de pago ni cuentas de cliente en esta fase.

## Multimedia e IA

Las piezas generadas con IA se registran en `multimedia/FICHAS-IA.md` y en `docs/DECLARACION-IA.md`. Las imágenes de producto del catálogo deben ser fotografías reales tomadas por el emprendedor. La IA no reemplaza la evidencia del producto real.

## Licencia

Proyecto académico y comercial de Pedacito de Arte. Las imágenes de marca pertenecen a la emprendedora. Las piezas de IA se documentan con su herramienta, prompt y condiciones de uso.
