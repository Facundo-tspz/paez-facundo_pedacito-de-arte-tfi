---
name: verificar-criterios
description: Verifica criterios de aceptación y RF de una spec de Pedacito de Arte usando pruebas, build y revisión de código. Úsala al cerrar una tarea, antes de un commit o cuando se pida "verificar", "validar spec" o "verificacion.md".
---

# Verificar criterios de una spec

## Cuándo usarla

Usar al terminar una tarea, antes de un commit o cuando se pida comprobar si una spec está cumplida.

## Procedimiento

1. Leer `specs/NNN-nombre/spec.md`, `tasks.md` y `verificacion.md`.
2. Identificar cada criterio de aceptación y cada RF con su ID.
3. Ejecutar, si existen:
   - `npm run lint`
   - `npm run test`
   - `npm run build`
4. Buscar secretos o acceso directo a datos en el cliente:
   - `DATABASE_URL`
   - `CLOUDINARY_API_SECRET`
   - `pg` dentro de `src/`
5. Revisar que las rutas admin no sean indexables y que las respuestas de error no filtren detalles internos.
6. Completar la tabla de `verificacion.md` con evidencia concreta: archivo, test, comando o captura.

## Reglas

- No declarar un criterio cumplido sin evidencia.
- Si una prueba no existe, indicar `Sin prueba` y proponer la mínima necesaria.
- Distinguir `Cumplido`, `Parcial`, `Pendiente` y `No aplica`.
- Si falla el build o el lint, el resultado global es `Parcial` o `Pendiente`, nunca `Cumplido`.
- No modificar código durante la verificación salvo que el usuario lo pida; primero reportar.

## Salida esperada

Un resumen con:

- Estado global.
- Criterios cumplidos.
- Criterios pendientes y bloqueos.
- Evidencia ejecutada.
- Próxima acción mínima.
