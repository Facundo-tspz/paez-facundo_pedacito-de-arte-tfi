# Spec 002 — Carrito de presupuesto y WhatsApp

- **Estado:** Borrador para implementación
- **RF principales:** RF05, RF06
- **RF opcional:** RF10 (registro de presupuestos)
- **Prioridad:** Alta
- **Dependencias:** catálogo, opciones de producto, `solicitudes_presupuesto`, `presupuesto_items`.

## 1. Contexto

El sitio no cobra en línea. El visitante arma un carrito temporal, elige variantes y cantidades, y al confirmar se registra el presupuesto y se abre WhatsApp con un mensaje estructurado. El servidor valida opciones y calcula el total; el cliente solo estima.

## 2. Historias de usuario

- Como visitante, quiero agregar productos con sus opciones y cantidades para pedir un presupuesto.
- Como visitante, quiero modificar o quitar ítems antes de enviar.
- Como visitante, quiero ver un resumen claro y un total estimado.
- Como visitante, quiero que al confirmar se abra WhatsApp con el pedido ya escrito.
- Como responsable del taller, quiero que el presupuesto quede registrado para seguimiento.

## 3. Requerimientos funcionales (EARS)

### RF05 — Carrito de presupuesto

- **RF05.1:** Cuando el visitante agrega un producto, el sistema deberá guardar producto, opciones, cantidad y nota en el carrito.
- **RF05.2:** Mientras el carrito tenga ítems, el sistema deberá mostrar el resumen y el total estimado.
- **RF05.3:** Cuando el visitante cambia la cantidad, el sistema deberá recalcular el subtotal y el total.
- **RF05.4:** Cuando el visitante quita un ítem, el sistema deberá eliminarlo y actualizar el total.
- **RF05.5:** Si una opción obligatoria no está seleccionada, entonces el sistema deberá bloquear el agregado y explicar el motivo.
- **RF05.6:** El carrito deberá persistir en `localStorage` y recuperarse al recargar, descartando datos inválidos.
- **RF05.7:** Si el carrito está vacío, entonces el sistema deberá deshabilitar la confirmación y mostrar una acción para volver al catálogo.

### RF06 — WhatsApp

- **RF06.1:** Cuando el visitante confirma un carrito válido, el sistema deberá generar un mensaje con ítems, opciones, cantidades, subtotales y total.
- **RF06.2:** El mensaje deberá incluir un saludo, el nombre del emprendimiento y un identificador de presupuesto cuando exista.
- **RF06.3:** El sistema deberá abrir `https://wa.me/<numero>?text=<mensaje>` con el número de `VITE_WHATSAPP_PHONE_NUMBER`.
- **RF06.4:** Si el navegador bloquea la apertura, entonces el sistema deberá mostrar un enlace alternativo y permitir copiar el mensaje.
- **RF06.5:** El mensaje no deberá contener datos sensibles innecesarios.

### RF10 — Registro de presupuestos (prioridad media)

- **RF10.1:** Cuando el visitante confirma, el sistema deberá enviar el carrito a `POST /api/presupuestos` antes de abrir WhatsApp.
- **RF10.2:** El servidor deberá validar producto disponible, opciones obligatorias, cantidades y calcular precios desde la base; no confiará en precios enviados por el cliente.
- **RF10.3:** Si la validación falla, entonces el sistema deberá devolver 400 con un mensaje claro y no registrar el presupuesto.
- **RF10.4:** Si el registro falla, entonces el sistema deberá permitir continuar a WhatsApp con el mensaje generado localmente y avisar que el pedido no quedó registrado.
- **RF10.5:** El presupuesto registrado deberá guardar cabecera e ítems históricos con nombre, opciones, precio unitario y subtotal.

## 4. Casos borde

- Carrito vacío.
- Cantidad `0`, negativa, decimal o gigante.
- Producto eliminado, inactivo o con precio cambiado.
- Opción inexistente o de otro producto.
- Opción obligatoria sin elegir.
- Carrito con más de 20 ítems o más de 99 unidades.
- `localStorage` corrupto o deshabilitado.
- Nota con saltos de línea, emojis o caracteres especiales.
- WhatsApp no disponible o bloqueado por el navegador.
- Doble clic en confirmar.
- Error de red al registrar.
- Número de WhatsApp ausente o inválido.

## 5. Fuera de alcance

- Pago en línea, señas o facturación.
- Cálculo de envío.
- Cupones o descuentos.
- Cuentas de cliente.
- Formulario de consulta personalizada (RF08).
- Notificaciones automáticas al taller fuera de WhatsApp.

## 6. Criterios de aceptación

1. Se pueden agregar, modificar y quitar ítems, y el total se recalcula.
2. No se puede agregar un producto sin opciones obligatorias.
3. El carrito persiste y se recupera de forma segura.
4. La confirmación deshabilitada con carrito vacío.
5. El mensaje de WhatsApp incluye todos los datos del pedido.
6. El servidor valida y calcula el total; no acepta precios del cliente.
7. El presupuesto se registra en cabecera e ítems cuando RF10 está activo.
8. Si el registro falla, se puede continuar a WhatsApp con aviso.
9. La interfaz funciona a 360 px y con teclado.
10. Las pruebas cubren cálculos, validaciones y mensaje.

## 7. Trazabilidad RF

| RF | Prioridad | Incluido | Verificación |
| --- | --- | --- | --- |
| RF05 | Alta | Sí | `verificacion.md` |
| RF06 | Alta | Sí | `verificacion.md` |
| RF10 | Media | Sí, si el tiempo alcanza | `verificacion.md` |
