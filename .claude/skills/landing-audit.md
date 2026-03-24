---
name: landing-audit
description: Auditar landing page resummo.com contra best practices de conversion, performance, SEO y mobile
argument-hint: [url o path local de la landing]
auto-activate: false
---

# Skill: /landing-audit

Auditar la landing page de Resummo contra las convenciones definidas en `.claude/rules/landing-page.md`.

## Pasos

### 1. Cargar convenciones
Leer `.claude/rules/landing-page.md` para tener la referencia de secciones obligatorias, performance targets, y checklist de assets.

### 2. Verificar estructura HTML
- [ ] Navbar con CTA "Comprar ahora" (sticky en mobile)
- [ ] Hero section con headline + subheadline + CTA + badge social proof
- [ ] Seccion Problema/Dolor (4 pain points)
- [ ] Seccion Solucion (features con iconos)
- [ ] Social Proof (testimonios + logos universidades + TOP 2%)
- [ ] Preview/Ejemplos (carousel con capturas por paquete)
- [ ] Pricing (4 cards comparativas)
- [ ] FAQ (accordion)
- [ ] Final CTA
- [ ] Footer
- [ ] Widget WhatsApp

### 3. Verificar responsive / mobile
- Revisar breakpoints CSS/Tailwind
- Verificar que CTAs son tap-friendly (min 44px)
- Verificar que texto es legible sin zoom
- Verificar que imagenes no se desbordan

### 4. Verificar tracking
- [ ] Meta Pixel presente en todas las paginas
- [ ] GA4 measurement ID presente
- [ ] Eventos de conversion configurados (ViewContent, AddToCart, InitiateCheckout)
- [ ] UTM params en links de ads

### 5. Verificar performance
- [ ] Imagenes optimizadas (WebP, lazy loading)
- [ ] No hay JS blocking en render path
- [ ] Fonts cargadas eficientemente
- [ ] Estimacion de Core Web Vitals (LCP <2.5s, CLS <0.1)

### 6. Verificar SEO
- [ ] Title tag presente y descriptivo
- [ ] Meta description presente
- [ ] OG tags para redes sociales (titulo + imagen + descripcion)
- [ ] Alt text en imagenes
- [ ] URLs amigables

### 7. Verificar CTAs y precios
- [ ] Botones de compra visibles y funcionales
- [ ] Links a pasarela de pago correctos
- [ ] Precios mostrados coinciden con la pasarela
- [ ] Cada paquete tiene su propio boton de compra

### 8. Generar reporte

| Categoria | Estado | Hallazgos |
|-----------|--------|-----------|
| Estructura | OK/WARN/FAIL | [detalle] |
| Mobile | OK/WARN/FAIL | [detalle] |
| Tracking | OK/WARN/FAIL | [detalle] |
| Performance | OK/WARN/FAIL | [detalle] |
| SEO | OK/WARN/FAIL | [detalle] |
| CTAs/Precios | OK/WARN/FAIL | [detalle] |

**Score total:** X/6 categorias OK

**Top 3 acciones prioritarias:**
1. [accion — impacto]
2. [accion — impacto]
3. [accion — impacto]

## Gotchas
- Si la landing esta en Lovable, el codigo puede estar en un repo externo — verificar path
- Si la landing es Next.js, revisar directamente en `landing/src/`
- Performance real solo se puede medir con Lighthouse — este audit es estimacion por codigo
- No confundir "seccion existe" con "seccion esta bien" — verificar contenido tambien

## Reglas
- Solo lectura — NUNCA modificar la landing durante el audit
- Reportar TODOS los hallazgos, no solo los criticos
- Comparar siempre contra `.claude/rules/landing-page.md`
- Si faltan assets de Andre/Nair (capturas, testimonios): incluir en el reporte como blocker
