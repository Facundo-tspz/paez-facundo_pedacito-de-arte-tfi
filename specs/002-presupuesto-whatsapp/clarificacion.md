# Clarificación — Spec 002 Carrito y WhatsApp

## Decisiones

| Pregunta | Opciones | Decisión | Justificación | Estado |
| --- | --- | --- | --- | --- |
| ¿El precio se calcula en el cliente o en el servidor? | Cliente / Servidor | Servidor autoritativo; cliente solo estima | Evita manipulación de precios y opciones | Resuelta |
| ¿Qué se guarda en `localStorage`? | Solo IDs / Snapshot | IDs + datos mínimos para mostrar; se revalida al confirmar | Recuperación rápida sin confiar en precios viejos | Resuelta |
| ¿Cuándo se registra el presupuesto? | Al agregar / Al confirmar | Al confirmar, antes de abrir WhatsApp | Evita registros basura | Resuelta |
| ¿Qué pasa si falla el registro? | Bloquear / Continuar | Continuar a WhatsApp con aviso de no registrado | No se pierde la intención de compra | Resuelta |
| ¿Se piden nombre y contacto? | Obligatorio / Opcional / No | Opcional, solo si el visitante los completa; no bloquear | El pedido real se completa en WhatsApp | Resuelta |
| ¿Cómo se identifica el presupuesto? | Sin ID / `PDA-000123` | `PDA-` + ID numérico | Referencia legible para el taller | Resuelta |
| ¿Dónde vive el número de WhatsApp? | Código / `.env` | `VITE_WHATSAPP_PHONE_NUMBER` | Evita hardcodear datos y permite cambiarlo | Resuelta |
| ¿Se permite nota por ítem? | Sí / No | Sí, máximo 200 caracteres | Personalización real del taller | Resuelta |
| ¿Se permiten varias opciones del mismo grupo? | Sí / No | Solo si `multiple = true` | Respeta el modelo de datos | Resuelta |
| ¿Límite de cantidades? | Libre / 1-99 | 1 a 99 por ítem; máximo 20 ítems | Controla abuso y errores | Resuelta |
| ¿Qué formato tiene el mensaje? | Texto libre / Estructurado | Texto estructurado con viñetas y total | Legible en WhatsApp | Resuelta |
| ¿Se usa API oficial de WhatsApp? | Sí / No | No; enlace `wa.me` | Sin costo, sin credenciales y sin cuenta Business | Resuelta |
| ¿Se guardan datos personales? | Sí / No | Solo si el usuario los ingresa; minimización y borrado desde admin | Privacidad y seguimiento | Resuelta |
| ¿RF10 es obligatorio? | Sí / No | Implementar si el tiempo alcanza; RF05/RF06 son bloqueantes | Prioridad media según requerimientos | Resuelta |

## Preguntas que requieren respuesta humana

| Pregunta | Bloquea | Estado |
| --- | --- | --- |
| ¿El número de WhatsApp definitivo está disponible? | Bloquea la prueba real, no el código | Pendiente |
| ¿Se pide nombre/contacto en el carrito o solo en WhatsApp? | No; se adopta opcional | Pendiente de confirmación |
| ¿RF10 entra en el MVP o se posterga? | No; se implementa si hay tiempo | Pendiente |

## Supuestos permitidos

- El visitante puede confirmar sin nombre ni contacto; WhatsApp completa la conversación.
- El taller responde el presupuesto por WhatsApp.
- El estado inicial del presupuesto es `Enviado`.
- El total puede ser `0` cuando todos los ítems son “A presupuestar”.
- El navegador permite abrir `wa.me`; si no, se ofrece enlace y copia.
