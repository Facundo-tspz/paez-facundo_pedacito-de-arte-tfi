# Plan técnico — Spec 002 Carrito y WhatsApp

## Enfoque

`CartContext` mantiene el carrito y lo persiste en `localStorage`. La confirmación hace `POST /api/presupuestos`; la Function valida IDs y opciones contra Neon, calcula el total y responde `{ presupuestoId, numero, whatsappUrl, mensaje }`. Luego el cliente abre WhatsApp. Si falla el POST, genera el mensaje localmente y avisa.

## Archivos previstos

```text
src/
├── context/
│   └── CartContext.jsx
├── components/
│   ├── BotonAgregar.jsx
│   ├── CarritoResumen.jsx
│   ├── ItemCarrito.jsx
│   ├── SelectorOpciones.jsx
│   └── ModalConfirmacion.jsx
├── pages/
│   └── Carrito.jsx
├── services/
│   ├── api.js
│   └── whatsappService.js
└── utils/
    └── carrito.js
```

## API

### `POST /api/presupuestos`

Payload:

```json
{
  "clienteNombre": "Opcional",
  "clienteContacto": "Opcional",
  "observaciones": "Opcional",
  "items": [
    {
      "productoId": 1,
      "cantidad": 2,
      "opciones": [10, 12],
      "nota": "Para Ana"
    }
  ]
}
```

Respuesta 201:

```json
{
  "presupuestoId": 123,
  "codigo": "PDA-000123",
  "numero": "54911...",
  "whatsappUrl": "https://wa.me/54911...?text=...",
  "mensaje": "Hola, quiero pedir..."
}
```

Validaciones del servidor:

- `items` entre 1 y 20.
- `cantidad` entero entre 1 y 99.
- `productoId` existe y `disponible = true`.
- Opciones pertenecen al producto y respetan grupos requeridos/multiple.
- Precio unitario = `precio_base` + recargos; nunca se toma del cliente.
- Nota y observaciones con longitud máxima.
- `clienteContacto` no se usa para spam ni se expone.

## Base de datos

- Insertar cabecera en `solicitudes_presupuesto`.
- Insertar ítems en `presupuesto_items` con `nombre_producto`, `opciones_seleccionadas` JSONB, `cantidad`, `precio_unitario`, `subtotal`.
- Usar transacción; si falla un ítem, no dejar cabecera huérfana.
- Estado inicial `Enviado`.

## Mensaje de WhatsApp

```text
Hola, soy [nombre]. Quiero pedir un presupuesto a Pedacito de Arte.

Presupuesto: PDA-000123
- 2 x Agenda Docente A5
  Opciones: Tapa dura, Interior rayado
  Nota: Para Ana
  Subtotal: $0 (A presupuestar)

Total estimado: A presupuestar
Observaciones: ...

Quedo atento/a. Gracias.
```

El mensaje se codifica con `encodeURIComponent`. El número se normaliza sin `+`, espacios ni guiones.

## Pruebas

- Cálculo de subtotales y total.
- Validación de opciones obligatorias.
- Persistencia y recuperación de `localStorage`.
- Carrito vacío y límites.
- Construcción del mensaje y codificación.
- API: payload inválido, producto inactivo, opción ajena, éxito y rollback.
- Fallback cuando falla el registro.

## Riesgos

| Riesgo | Mitigación |
| --- | --- |
| Precio manipulado | Cálculo exclusivo en servidor |
| Spam de presupuestos | Validación, límites y monitoreo; no confiar en el cliente |
| Pérdida de carrito | `localStorage` con validación y mensaje de recuperación |
| WhatsApp bloqueado | Enlace alternativo y copiar mensaje |
| Datos personales | Minimización y borrado desde admin |
| Doble envío | Deshabilitar botón y usar idempotencia simple por intento |

## Orden de implementación

1. Modelo de carrito y persistencia.
2. Selector de opciones y agregar.
3. Página de carrito y totales.
4. Endpoint de registro y cálculo servidor.
5. Generación de mensaje y apertura de WhatsApp.
6. Fallback y estados de error.
7. Pruebas y verificación.
