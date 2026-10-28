---
description: Propone un plan técnico sin escribir código.
---

Vas a planificar la spec: `$ARGUMENTS`

Leé la spec, `clarificacion.md`, `docs/03-TECNOLOGIAS.md`, `docs/04-ARQUITECTURA.md`, `docs/05-DATOS.md` y `docs/10-SEGURIDAD.md`.

Escribí o actualizá `specs/NNN-nombre/plan.md` con:

- Enfoque técnico y archivos afectados.
- Componentes, rutas y endpoints.
- Tablas, campos e índices.
- Flujo de datos cliente → `/api` → `lib/db.js` → Neon.
- Validaciones y manejo de errores.
- Estrategia de pruebas.
- Riesgos y mitigaciones.
- Orden de implementación.

No crees componentes, migraciones ni código. Si falta una decisión, volvé a `/clarificar`.
