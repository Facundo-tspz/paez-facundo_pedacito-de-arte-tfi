---
description: Ejecuta pruebas y contrasta los criterios de una spec.
---

Vas a verificar la spec: `$ARGUMENTS`

Pasos:

1. Leé `spec.md`, `tasks.md` y `verificacion.md`.
2. Ejecutá, si existen: `npm run lint`, `npm run test` y `npm run build`.
3. Revisá cada criterio de aceptación y cada RF contra código, pruebas o verificación manual.
4. Buscá secretos, `DATABASE_URL` o claves en `src/`.
5. Actualizá `specs/NNN-nombre/verificacion.md` con:

| Criterio / RF | Evidencia | Resultado | Observaciones |
| --- | --- | --- | --- |

No declares un criterio cumplido sin evidencia. Si algo falla, indicá el archivo y la línea y proponé la corrección mínima.
