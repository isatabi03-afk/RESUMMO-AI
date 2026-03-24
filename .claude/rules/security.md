# Seguridad

## Credenciales
- NUNCA incluir API keys, tokens, JWT ni hostnames reales en outputs
- NUNCA commitear .env, credenciales ni tokens a repositorios
- Si se encuentran credenciales en archivos del workspace: no reproducirlas, notificar al usuario

## Servicios con credenciales sensibles
- **EasyPay/Culqi** — API keys de pasarela de pago (produccion y sandbox)
- **Notion API** — Integration token para compartir paginas
- **Google Sheets API** — Service account key para registro de clientes
- **Meta Pixel** — Pixel ID y Conversions API token
- **GA4** — Measurement ID y API secret para server-side events
- **ManyChat** — API key para integracion con n8n
- **n8n** — API key del servidor self-hosted

## Produccion
- La landing page, pasarela de pagos y entrega automatica estan en produccion
- No modificar workflows de n8n ni flows de ManyChat sin confirmacion explicita
- Backup antes de cualquier cambio a workflows (definido en n8n-patterns.md)

## Checklist pre-deploy
- [ ] No hay API keys en el codigo fuente de la landing
- [ ] .env no esta en staging de git
- [ ] Variables de entorno configuradas en el servidor, no hardcodeadas
- [ ] Webhooks de pago validan firma/token de la pasarela
- [ ] Logs no imprimen datos de pago ni emails de clientes
