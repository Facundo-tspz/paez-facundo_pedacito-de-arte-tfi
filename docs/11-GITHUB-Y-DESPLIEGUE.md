# GitHub y despliegue

## Repositorio

- **Cuenta:** `Facundo-tspz`
- **URL actual:** <https://github.com/Facundo-tspz/paez-facundo_pedacito-de-arte-tfi>
- **Nombre solicitado por el TP:** `iest-tfi_nombre-emprendimiento`
- **Nombre recomendado:** `iest-tfi_pedacito-de-arte`
- **Visibilidad:** público
- **Trabajo:** individual
- **Rama principal:** `main`

El renombre se hace desde GitHub: `Settings` → `Repository name` → `Rename`. GitHub redirige la URL anterior, pero conviene actualizar el remote local y este documento.

```bash
git remote set-url origin https://github.com/Facundo-tspz/iest-tfi_pedacito-de-arte.git
```

## Identidad de commits

Para un repositorio público se usa el email noreply de GitHub, no el email personal:

```bash
git config user.name "Facundo Paez"
git config user.email "Facundo-tspz@users.noreply.github.com"
```

El email noreply se obtiene en GitHub: `Settings` → `Emails` → `Keep my email addresses private`. Si GitHub muestra un identificador con números, usar ese valor exacto.

## Flujo de trabajo

1. Actualizar `docs/PROGRESO.md` y `memory.md` si cambia el alcance.
2. Escribir o actualizar la spec en `specs/NNN/`.
3. Implementar una tarea pequeña.
4. Ejecutar lint, pruebas y build.
5. Actualizar `specs/NNN/verificacion.md`.
6. Commit descriptivo y push a `main`.

Commits sugeridos:

```text
docs: crear constitucion y arnes SDD
feat(catalogo): listar productos desde la API
feat(carrito): persistir items y generar mensaje de WhatsApp
feat(admin): agregar login con sesiones y roles
test(catalogo): cubrir filtros y estado vacio
chore(deploy): configurar Netlify Functions y variables
```

## Netlify

1. Crear el sitio desde GitHub: `Add new project` → `Import an existing project`.
2. Seleccionar el repositorio y la rama `main`.
3. Build command: `npm run build`.
4. Publish directory: `dist`.
5. Functions directory: `netlify/functions` (se declara en `netlify.toml`).
6. Cargar las variables de entorno en `Site configuration` → `Environment variables`.
7. Verificar que `/api/health` responda y que el frontend cargue.
8. Activar Deploy Previews si el plan lo permite.

No se cargan secretos en el repositorio ni en capturas de pantalla.

## Variables en Netlify

| Variable | Secreta | Entorno |
| --- | --- | --- |
| `DATABASE_URL` | Sí | Production, Deploy Previews |
| `AUTH_SECRET` | Sí | Production, Deploy Previews |
| `SESSION_TTL_HOURS` | No | Production |
| `CLOUDINARY_CLOUD_NAME` | No | Production, Deploy Previews |
| `CLOUDINARY_API_KEY` | Sí | Production, Deploy Previews |
| `CLOUDINARY_API_SECRET` | Sí | Production, Deploy Previews |
| `VITE_WHATSAPP_PHONE_NUMBER` | No | Production y build local |

## Neon

- Crear un proyecto y una base.
- Copiar la cadena `DATABASE_URL` con `sslmode=require`.
- Ejecutar `npm run db:setup` y `npm run db:seed` desde local.
- No exponer la cadena en el cliente.
- Revisar el consumo de CU-horas y el almacenamiento en el panel de Neon.

## Cloudinary

- Crear una cuenta y una carpeta `pedacito-de-arte`.
- Cargar las imágenes desde el panel admin o desde la consola de Cloudinary.
- Guardar `url` y `public_id` en la base.
- Configurar transformaciones razonables; no generar variantes innecesarias.
- No subir documentos personales ni imágenes sin derechos.

## Checklist de entrega

- [ ] Repositorio público con el nombre acordado.
- [ ] `README.md` con instrucciones y enlaces.
- [ ] `docs/CONSTITUCION.md`, `docs/PROMPTS.md`, `docs/DECLARACION-IA.md`.
- [ ] `AGENTS.md`, `CLAUDE.md`, `memory.md`.
- [ ] Tres specs con `spec.md`, `clarificacion.md`, `plan.md`, `tasks.md`, `verificacion.md`.
- [ ] `.agents/skills/` con al menos tres skills.
- [ ] `.opencode/commands/` con `/spec`, `/clarificar`, `/verificar`, `/cambio`.
- [ ] `src/`, `tests/`, `public/marca/`, `public/media/`.
- [ ] `multimedia/` y `multimedia/FICHAS-IA.md` completos.
- [ ] Sitio Netlify funcionando y `/api/health` en verde.
- [ ] Repositorio sin secretos.
- [ ] Informe PDF y videos en la Bitácora, con enlaces en `ENLACES.md`.
- [ ] Defensa preparada con demo y explicación del código.
