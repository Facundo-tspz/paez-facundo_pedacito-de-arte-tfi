# 09 - Posicionamiento SEO y Accesibilidad: Pedacito de Arte

Este documento define la estrategia de optimización para motores de búsqueda (SEO), metadatos, etiquetas Open Graph y pautas de accesibilidad para la plataforma web de **Pedacito de Arte**.

> **Contexto técnico:** al ser una SPA con Vite, el SEO depende de metadatos dinámicos y de **prerenderizar** las pantallas públicas. Sin prerender, Google y las vistas previas de WhatsApp solo verán el `index.html` base.

---

## 1. Estrategia de Metadatos por Pantalla

Se configuran títulos únicos y descripciones atractivas con `react-helmet-async` para mejorar el posicionamiento orgánico y la tasa de clics (CTR).

### 1. Portada / Inicio (`HomePage.jsx`)
- **Título (`<title>`):** `Pedacito de Arte | Papelería Creativa hecha con amor`
- **Descripción (`meta description`):** `Agendas personalizadas, agendas docentes, revistas para colorear y anotadores artesanales. Papelería única hecha con amor. ¡Armá tu presupuesto y pedilo por WhatsApp!`
- **Palabras Clave (`keywords`):** `papelería artesanal, agendas personalizadas, agendas docentes, revistas para colorear, anotadores, Pedacito de Arte`

### 2. Catálogo (`CatalogPage.jsx`)
- **Título:** `Catálogo de Productos | Pedacito de Arte`
- **Descripción:** `Explorá el catálogo de agendas, agendas docentes, revistas para colorear y anotadores artesanales hechos a pedido.`

### 3. Servicios e Imprenta (`ServicesPage.jsx`)
- **Título:** `Servicios de Imprenta y Diseño | Pedacito de Arte`
- **Descripción:** `Diseño gráfico personalizado, impresiones, diplomas, banners y encuadernación para eventos, docentes y comercios.`

### 4. Detalle de Producto (`ProductDetailPage.jsx`)
- **Título dinámico:** `<nombre del producto> | Pedacito de Arte`
- **Descripción dinámica:** primeros 150 caracteres de la descripción del producto.
- **Canonical:** URL absoluta del producto.

### 5. Contacto (`ContactPage.jsx`)
- **Título:** `Contacto y Presupuestos Especiales | Pedacito de Arte`
- **Descripción:** `Pedí un presupuesto especial para tu proyecto de imprenta, evento o papelería personalizada.`

---

## 2. Configuración de Open Graph (Vista Previa en WhatsApp e Instagram)

Para que los enlaces compartidos muestren una tarjeta visual atractiva, las URLs deben ser **absolutas y sin Markdown** dentro del `content`:

```html
<!-- Open Graph / WhatsApp / Facebook -->
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Pedacito de Arte" />
<meta property="og:title" content="Pedacito de Arte - Papelería creativa hecha con amor" />
<meta property="og:description" content="Agendas personalizadas, cuadernos y papelería artesanal. Armá tu pedido online." />
<meta property="og:image" content="https://pedacitodearte.com/media/og-compartir.jpg" />
<meta property="og:image:alt" content="Logo y agendas artesanales de Pedacito de Arte" />
<meta property="og:url" content="https://pedacitodearte.com/" />
<meta property="og:locale" content="es_AR" />

<!-- Twitter Cards -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Pedacito de Arte - Papelería creativa" />
<meta name="twitter:description" content="Agendas personalizadas, cuadernos y papelería artesanal." />
<meta name="twitter:image" content="https://pedacitodearte.com/media/og-compartir.jpg" />
```

- La imagen Open Graph debe ser **1200×630 px**, JPG/PNG y pesar menos de 300 KB.
- El dominio de ejemplo (`pedacitodearte.com`) debe reemplazarse por el dominio real antes del despliegue.

---

## 3. Prerender, Sitemap y Robots

- **Prerender:** usar `vite-react-ssg` para Inicio, Catálogo, Servicios y Contacto si el tiempo alcanza. Si no se implementa, se usan metadatos dinámicos y se justifica en `docs/PROGRESO.md`; las pantallas `/admin/*` no se prerenderizan ni se indexan.
- **Netlify:** el fallback SPA se declara en `netlify.toml` (`/* → /index.html`, estado 200). Las rutas `/api/*` las maneja `netlify/functions/api.js` con `config.path`; no se agrega un redirect manual de API.
- **`public/robots.txt`:**
  ```txt
  User-agent: *
  Allow: /
  Disallow: /admin
  Disallow: /api/
  Sitemap: https://pedacitodearte.com/sitemap.xml
  ```
- **`public/sitemap.xml`:** incluir `/`, `/catalogo`, `/servicios` y `/contacto`; actualizar en cada despliegue.
- **Canonical:** una URL canónica por pantalla; evitar duplicados con y sin barra final.
- **Admin:** `X-Robots-Tag: noindex` y `Disallow: /admin`.

---

## 4. Datos Estructurados (JSON-LD)

Incluir en el `index.html` o por pantalla:

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "Pedacito de Arte",
  "description": "Papelería creativa hecha con amor: agendas, cuadernos, recetarios y libros para colorear.",
  "image": "https://pedacitodearte.com/media/og-compartir.jpg",
  "url": "https://pedacitodearte.com/",
  "priceRange": "$$",
  "areaServed": "AR"
}
```

- En `ProductDetailPage.jsx`, agregar además un bloque `Product` con `name`, `description`, `image` y `offers` (si hay precio) o `availability` cuando sea "a presupuestar".

---

## 5. Accesibilidad

- Contraste mínimo **WCAG AA**: 4.5:1 en texto normal y 3:1 en texto grande. El CTA usa `--color-secundario-acento` para cumplirlo.
- Todas las imágenes de productos e insumos llevan `alt` descriptivo (obligatorio en `producto_imagenes.alt`).
- Foco visible en todos los interactivos (`focus-visible`) y navegación completa por teclado.
- Formularios con `<label>` asociado, `aria-invalid`, `aria-describedby` y mensajes anunciados con `aria-live`.
- Enlace "Saltar al contenido" al inicio del `body`.
- El panel de administración queda fuera de la indexación (`robots` + `noindex`).
- Textos alternativos y nombres accesibles en íconos que funcionan como botón (`aria-label`).
