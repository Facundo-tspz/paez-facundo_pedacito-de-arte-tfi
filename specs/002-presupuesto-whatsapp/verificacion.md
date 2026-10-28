# Verificación — Spec 002 Carrito y WhatsApp

- **Estado global:** Pendiente
- **Última ejecución:** —
- **Responsable:** —

## Comandos

| Comando | Resultado | Observaciones |
| --- | --- | --- |
| `npm run lint` | Pendiente | — |
| `npm run test` | Pendiente | — |
| `npm run build` | Pendiente | — |

## Criterios de aceptación

| Criterio / RF | Evidencia | Resultado | Observaciones |
| --- | --- | --- | --- |
| RF05.1 Agregar ítem | — | Pendiente | — |
| RF05.2 Resumen y total | — | Pendiente | — |
| RF05.3 Recalcular cantidades | — | Pendiente | — |
| RF05.4 Quitar ítem | — | Pendiente | — |
| RF05.5 Opciones obligatorias | — | Pendiente | — |
| RF05.6 Persistencia | — | Pendiente | — |
| RF05.7 Carrito vacío | — | Pendiente | — |
| RF06.1 Mensaje estructurado | — | Pendiente | — |
| RF06.2 Identificador y saludo | — | Pendiente | — |
| RF06.3 Enlace `wa.me` | — | Pendiente | — |
| RF06.4 Fallback | — | Pendiente | — |
| RF06.5 Sin datos innecesarios | — | Pendiente | — |
| RF10.1 POST antes de abrir | — | Pendiente | — |
| RF10.2 Cálculo en servidor | — | Pendiente | — |
| RF10.3 Validación 400 | — | Pendiente | — |
| RF10.4 Fallback si falla | — | Pendiente | — |
| RF10.5 Cabecera e ítems | — | Pendiente | — |

## Casos borde

| Caso | Evidencia | Resultado |
| --- | --- | --- |
| Cantidad 0/negativa/decimal | — | Pendiente |
| Producto inactivo | — | Pendiente |
| Opción de otro producto | — | Pendiente |
| Carrito corrupto | — | Pendiente |
| WhatsApp bloqueado | — | Pendiente |
| Doble clic | — | Pendiente |

## Seguridad

| Control | Evidencia | Resultado |
| --- | --- | --- |
| Precio no confiado al cliente | — | Pendiente |
| Sin SQL en `src/` | — | Pendiente |
| Sin número hardcodeado | — | Pendiente |
| Sin secretos en mensajes | — | Pendiente |

## Pendientes y bloqueos

- Número de WhatsApp definitivo.
- Decisión final sobre RF10.
- Prueba real de apertura en Android e iOS.
