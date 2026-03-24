# Automatizaciones — Resummo

## Inventario de workflows n8n

| # | Workflow | Estado | Proposito |
|---|---------|--------|-----------|
| 1 | payment-webhook | [PENDIENTE] | Recibe confirmacion de pago, valida, registra |
| 2 | notion-delivery | [PENDIENTE] | Comparte pagina Notion con comprador |
| 3 | confirmation-sender | [PENDIENTE] | Envia email/WhatsApp de confirmacion |
| 4 | lead-registration | [PENDIENTE] | Registra leads de ManyChat en Google Sheets |
| 5 | manychat-backend | [PENDIENTE] | Logica del agente IA |
| 6 | daily-report | [PENDIENTE] | Resumen diario de ventas |

## ManyChat flows

| Flow | Estado | Proposito |
|------|--------|-----------|
| Main Flow | [PENDIENTE] | Saludo → clasificar → recomendar → CTA |
| FAQ Flow | [PENDIENTE] | Responder preguntas frecuentes |
| Post-purchase | [PENDIENTE] | Verificar acceso, soporte |
| Human Handoff | [PENDIENTE] | Derivar a Andre/Nair/Santiago |

## Estructura de directorios

```
automations/
├── n8n/              JSONs exportados de workflows n8n
├── manychat/         Exports y configs de ManyChat
└── scripts/          Scripts JS de deploy y utilidades
```

## Servidor n8n
- Self-hosted en VPS de FIXU AI
- URL: [CONFIGURAR]
- Acceso via REST API (ver `.claude/rules/n8n-patterns.md`)

## Patrones
Ver `.claude/rules/n8n-patterns.md` y `.claude/rules/manychat-patterns.md`
