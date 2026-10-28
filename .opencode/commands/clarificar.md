---
description: Revisa una spec y registra ambigüedades, decisiones y supuestos.
---

Vas a clarificar la spec: `$ARGUMENTS`

Leé la spec indicada y `docs/CONSTITUCION.md`, `docs/02-REQUERIMIENTOS.md`, `docs/05-DATOS.md` y `docs/06-DISENO-UI.md`.

Buscá:

- Términos ambiguos o contradictorios.
- Requerimientos que no se pueden verificar.
- Datos que la IA no puede inventar (precios, textos, marcas, medidas).
- Decisiones de modelado que afecten la base.
- Riesgos de seguridad, accesibilidad o privacidad.

Escribí o actualizá `specs/NNN-nombre/clarificacion.md` con una tabla:

| Pregunta | Opciones | Decisión | Justificación | Estado |
| --- | --- | --- | --- | --- |

Marcá como `Pendiente` solo lo que requiera respuesta humana. No avances a código si hay una decisión bloqueante sin resolver.
