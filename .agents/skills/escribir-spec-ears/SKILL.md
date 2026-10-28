---
name: escribir-spec-ears
description: Redacta requerimientos en formato EARS y detalla casos borde, fuera de alcance y criterios verificables para las specs de Pedacito de Arte. Úsala al crear o revisar una spec, antes de planificar o codificar.
---

# Escribir una spec en EARS

## Cuándo usarla

Usar cuando se crea una spec nueva, se convierte un requerimiento en criterios verificables o se detecta una spec ambigua.

## Patrones EARS

- **Ubicuo:** `El sistema deberá...`
- **Basado en evento:** `Cuando <evento>, el sistema deberá...`
- **Basado en estado:** `Mientras <estado>, el sistema deberá...`
- **Basado en opción:** `Donde <opción>, el sistema deberá...`
- **Comportamiento no deseado:** `Si <condición>, entonces el sistema deberá...`

## Estructura mínima

1. Contexto y problema.
2. Historias de usuario.
3. Requerimientos EARS con ID y RF asociado.
4. Casos borde y errores.
5. Fuera de alcance.
6. Criterios de aceptación verificables.
7. Dependencias y preguntas abiertas.

## Reglas para este proyecto

- No inventar precios, textos, medidas, marcas ni datos de la emprendedora.
- Modelar variantes como opciones, no como productos separados.
- El cliente nunca accede a la base; toda lectura/escritura pasa por `/api`.
- Incluir accesibilidad, mobile 360 px y estados vacíos/error.
- Separar RF de prioridad alta (obligatorios) de RF medios (opcionales justificados).
- Cada criterio debe poder verificarse con una prueba, una inspección o un paso manual reproducible.

## Salida esperada

Una spec lista para `/clarificar` y `/plan`, sin código.
