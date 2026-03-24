# Analytics y Tracking — GA4 + Meta Pixel

## Principio
Medir todo el funnel: ad → landing → checkout → pago → entrega.
Decisiones de negocio basadas en data, no intuicion.

## Herramientas

| Herramienta | Proposito | Implementacion |
|-------------|-----------|---------------|
| Google Analytics 4 | Comportamiento en landing, conversion | Script en landing + Measurement Protocol |
| Meta Pixel | Atribucion de ads, retargeting | Script en landing + Conversions API |
| Google Sheets | Dashboard manual de ventas | Actualizado via n8n post-pago |

## Eventos de tracking

### Client-side (script en landing)

| Evento | Plataforma | Trigger | Parametros |
|--------|-----------|---------|------------|
| page_view | GA4 + Pixel | Cada carga de pagina | page_title, page_location |
| ViewContent | Pixel | Ver seccion de paquete especifico | content_name (paquete), content_type |
| view_item | GA4 | Ver seccion de paquete especifico | item_name, item_category, price |
| AddToCart | Pixel | Click en boton "Comprar" | content_name, value, currency (PEN) |
| add_to_cart | GA4 | Click en boton "Comprar" | item_name, price, currency |
| InitiateCheckout | Pixel | Redireccion a pasarela de pago | value, currency, num_items |
| begin_checkout | GA4 | Redireccion a pasarela de pago | value, currency |

### Server-side (via n8n post-pago)

| Evento | Plataforma | Trigger | Parametros |
|--------|-----------|---------|------------|
| Purchase | Pixel Conversions API | Pago confirmado | value, currency, content_name, event_id |
| purchase | GA4 Measurement Protocol | Pago confirmado | transaction_id, value, currency, items |

**Server-side es OBLIGATORIO para purchase** — no depender solo del browser (ad blockers, redireccion).

## UTM Parameters — estandar

| Parametro | Formato | Ejemplo |
|-----------|---------|---------|
| utm_source | plataforma | instagram, facebook, whatsapp, organic |
| utm_medium | tipo | paid, social, referral, direct |
| utm_campaign | nombre campania | ciencias-basicas-mar26, all-access-promo |
| utm_content | variante | ad-v1, story-link, bio-link |

Todos los links de ads DEBEN incluir UTMs. Links en bio/linktree tambien.

## Meta Pixel — configuracion

```html
<!-- En <head> de TODAS las paginas de la landing -->
<script>
  !function(f,b,e,v,n,t,s)
  {if(f.fbq)return;n=f.fbq=function(){n.callMethod?
  n.callMethod.apply(n,arguments):n.queue.push(arguments)};
  if(!f._fbq)f._fbq=n;n.push=n;n.loaded=!0;n.version='2.0';
  n.queue=[];t=b.createElement(e);t.async=!0;
  t.src=v;s=b.getElementsByTagName(e)[0];
  s.parentNode.insertBefore(t,s)}(window, document,'script',
  'https://connect.facebook.net/en_US/fbevents.js');
  fbq('init', 'PIXEL_ID_AQUI');
  fbq('track', 'PageView');
</script>
```

## GA4 — configuracion

```html
<!-- En <head> de TODAS las paginas de la landing -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

## Dashboard semanal (Google Sheets)

| Metrica | Fuente | Frecuencia |
|---------|--------|-----------|
| Ventas totales (unidades) | Google Sheets clientes | Diaria via n8n |
| Revenue total (S/) | Google Sheets clientes | Diaria via n8n |
| Ventas por paquete | Google Sheets clientes | Semanal |
| Trafico landing | GA4 | Semanal |
| Conversion rate (visita → compra) | GA4 + Sheets | Semanal |
| CPA (costo por adquisicion) | Meta Ads + Sheets | Semanal |
| ROAS (return on ad spend) | Meta Ads + Sheets | Semanal |
| Top fuentes de trafico | GA4 UTMs | Semanal |

## Reglas
- No trackear PII (nombre, email, telefono) en eventos de analytics
- Server-side purchase events deben incluir event_id para deduplicacion con Pixel
- Verificar que el Pixel se dispara correctamente con Meta Pixel Helper (extension Chrome)
- Verificar GA4 con DebugView antes de produccion
