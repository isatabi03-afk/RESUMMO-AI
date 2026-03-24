---
name: technical-decisions
description: Log de decisiones tecnicas de Resummo con razonamiento. Actualizar al tomar decisiones de stack, arquitectura o integraciones
type: project
---

# Decisiones Tecnicas — Resummo

## 2026-03 — Stack inicial definido

### Landing page
**Opciones:** Lovable vs Next.js vs Framer
**Decision:** [PENDIENTE — evaluar con agente research]
**Consideraciones:**
- Lovable: rapido de prototipar, facil para non-devs, limitado en personalizacion
- Next.js: control total, SEO nativo, requiere mas tiempo de dev
- Framer: buen diseno, limitado en logica custom

### Pasarela de pagos
**Opciones:** EasyPay vs Culqi vs Mercado Pago
**Decision:** [PENDIENTE — depende de Andre/Nair crear cuenta]
**Consideraciones:**
- EasyPay: mas metodos locales (Yape, Plin), ~5% comision
- Culqi: mas establecido, ~3.99% + IGV, mejor documentacion API
- Mercado Pago: menos popular en Peru para este segmento

### Automatizacion
**Decision:** n8n self-hosted en VPS de FIXU AI
**Razonamiento:** Ya tenemos infra de n8n corriendo para otros clientes, costo marginal $0

### Agente IA
**Decision:** ManyChat (frontend) + n8n (backend)
**Razonamiento:** Instagram DMs es el canal principal. ManyChat tiene integracion nativa con Instagram. n8n maneja la logica compleja

### Base de datos
**Decision:** Google Sheets (Fase 1), evaluar Supabase para Fase 3+
**Razonamiento:** Andre/Nair ya usan Google Sheets. No introducir complejidad innecesaria en Fase 1. Migrar cuando escale

### Entrega de producto
**Decision:** Notion API para sharing automatico + Google Drive para Anki
**Razonamiento:** El producto vive en Notion. Automatizar lo que Andre/Nair hacen manualmente hoy
**Riesgo:** Notion API puede no soportar sharing directo — investigar con agente research
