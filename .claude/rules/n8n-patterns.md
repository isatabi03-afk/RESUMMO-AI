# Patrones de Workflows n8n — Resummo

## Servidor
- n8n self-hosted en VPS de FIXU AI (compartido con otros proyectos)
- Community Edition — NO soporta Variables nativas ($vars requiere licencia)
- Usar PLACEHOLDER_* en nodos para valores dinamicos

## Workflows principales

| # | Workflow | Trigger | Funcion |
|---|---------|---------|---------|
| 1 | payment-webhook | Webhook de EasyPay/Culqi | Recibe confirmacion de pago, valida, registra |
| 2 | notion-delivery | Llamado por workflow 1 | Comparte pagina Notion con comprador |
| 3 | confirmation-sender | Llamado por workflow 1 | Envia email/WhatsApp de confirmacion |
| 4 | lead-registration | Webhook de ManyChat | Registra leads interesados en Google Sheets |
| 5 | manychat-backend | Webhook de ManyChat External Request | Logica del agente IA (consultas, respuestas) |
| 6 | daily-report | Cron (8am Peru) | Resumen diario de ventas y leads |

## Patrones de flujo

### Patron 1: Webhook → Validar → Procesar → Confirmar
Usado en: payment-webhook
```
Webhook → Validate Signature → IF valid
  → TRUE: Process Payment (Google Sheets + Notion + Email)
  → FALSE: Log Invalid + Alert Santiago
```

### Patron 2: External Request → Consultar → Responder
Usado en: manychat-backend
```
Webhook (ManyChat) → Extract User Data → Query Google Sheets
  → IF cliente existente: respuesta personalizada
  → IF nuevo lead: registrar + respuesta generica
→ Return Response to ManyChat
```

### Patron 3: Sub-workflow
Usado en: notion-delivery, confirmation-sender
```
Execute Workflow (llamado por payment-webhook)
  → Recibe: email, paquete, nombre
  → Ejecuta logica especifica
  → Retorna: estado (success/error)
```

## Expresiones n8n mas usadas

| Expresion | Uso |
|-----------|-----|
| `{{ $json.body.email }}` | Email del webhook de pago |
| `{{ $json.body.transaction_id }}` | ID de transaccion |
| `{{ $json.body.amount }}` | Monto pagado |
| `{{ $('Google Sheets').first().json.email }}` | Email de Google Sheets |
| `{{ $now.toFormat('yyyy-MM-dd HH:mm') }}` | Timestamp actual |
| `{{ $if($json.paquete === 'All Access', true, false) }}` | Condicional por paquete |

## Reglas obligatorias

1. **Backup local** antes de PUT a API — guardar JSON en `automations/n8n/`
2. **No enviar pinData** en payloads >50KB
3. **Validar webhooks** — verificar firma de la pasarela
4. **Idempotencia** — verificar transaction_id antes de procesar
5. **Error handling** — siempre tener branch de error que notifique a Santiago
6. **No hardcodear credenciales** — usar credentials de n8n
7. **Testear con pinned data** antes de activar en produccion
8. **Documentar cambios** en `automations/README.md`

## Deploy via REST API

```
GET  /api/v1/workflows/{id}     → obtener workflow actual
PUT  /api/v1/workflows/{id}     → actualizar workflow
POST /api/v1/workflows          → crear workflow nuevo
POST /api/v1/workflows/{id}/activate   → activar
POST /api/v1/workflows/{id}/deactivate → desactivar
```

Headers: `X-N8N-API-KEY: <jwt>`

### Protocolo de deploy
1. GET workflow actual → guardar backup local
2. Modificar JSON del workflow
3. PUT workflow actualizado
4. Verificar que el workflow se activo correctamente
5. Testear con un caso real (o pinned data)

## Limitaciones conocidas
- n8n CE NO soporta Variables nativas → usar PLACEHOLDER_*
- Webhook paths deben ser unicos por workflow
- ManyChat External Request tiene timeout de ~10s → mantener flujos rapidos
