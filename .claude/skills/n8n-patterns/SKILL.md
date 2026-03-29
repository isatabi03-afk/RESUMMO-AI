---
name: n8n-patterns
description: Patrones de workflows n8n para Resummo. Usar cuando se creen, debugueen o disenien workflows de pago, entrega Notion, agente IA, o reportes automaticos
argument-hint: [descripcion del workflow o problema]
auto-activate: false
---

# Skill: /n8n-patterns

Expertise en workflows n8n de produccion para el ecosistema Resummo: pagos, entrega automatica, agente IA, y reportes.

## Stack de integracion Resummo

| Capa | Servicio | Conexion |
|------|----------|----------|
| Canal de venta | Instagram DM | ManyChat → webhook n8n |
| Canal de soporte | WhatsApp | ManyChat → webhook n8n |
| Pasarela de pago | EasyPay / Culqi | Webhook → n8n |
| Registro | Google Sheets | API REST desde n8n |
| Entrega producto | Notion API | HTTP Request desde n8n |
| Entrega Anki | Google Drive API | HTTP Request desde n8n |
| Confirmacion | Email (Resend/SendGrid) | HTTP Request desde n8n |
| Analytics server-side | GA4 + Meta CAPI | HTTP Request desde n8n |

## Workflows de Resummo

### 1. payment-webhook (CRITICO)
```
Webhook (EasyPay/Culqi)
  → Validate Signature (Code node)
  → IF valid
    → TRUE: Extract Data (nombre, email, paquete, monto, transaction_id)
      → Check Idempotency (Google Sheets: buscar transaction_id)
      → IF nuevo
        → Execute Sub-workflow: notion-delivery
        → Execute Sub-workflow: confirmation-sender
        → Register in Google Sheets
        → Server-side Purchase event (GA4 + Meta CAPI)
      → IF duplicado
        → Log duplicate, no action
    → FALSE: Log Invalid Webhook + Alert Santiago
```

### 2. notion-delivery (sub-workflow)
```
Input: email, paquete, nombre
  → Map paquete → Notion Page ID (Code node con lookup table)
  → HTTP Request: Notion API — share page with email
  → IF success
    → Return { notion_shared: true }
  → IF error
    → Return { notion_shared: false, error: message }
    → Alert Santiago (email/WhatsApp)
```

### 3. confirmation-sender (sub-workflow)
```
Input: email, nombre, paquete, notion_link, drive_link
  → Build email HTML (Code node)
  → HTTP Request: Send email via Resend/SendGrid
  → IF paquete includes Anki
    → Include Drive link in email
  → Return { email_sent: true/false }
```

### 4. lead-registration
```
Webhook (ManyChat — new subscriber)
  → Extract: nombre, ig_username, email (si disponible), paquete_interes
  → Check if exists in Google Sheets
  → IF nuevo: append row
  → IF existente: update last_interaction
```

### 5. manychat-backend
```
Webhook (ManyChat External Request)
  → Extract: user_id, message, context
  → IF intent == "check_purchase"
    → Query Google Sheets by email/ig_username
    → Return purchase status
  → IF intent == "recommend_package"
    → Code node: logica de recomendacion
    → Return recommended package + CTA link
  → IF intent == "get_examples"
    → Return pre-built example URLs by package
  → ELSE
    → Return generic response + human handoff option
```

### 6. daily-report (Cron)
```
Schedule Trigger: 8:00 AM Peru (UTC-5)
  → Google Sheets: Get all rows from today
  → Code node: Calculate metrics
    - ventas_hoy, revenue_hoy, por_paquete, ticket_promedio
  → Compare vs yesterday
  → Format message
  → Send to Santiago (WhatsApp/email)
```

## Patrones reutilizables

### Patron A: Webhook → Validate → Process
Para todo webhook externo (pasarela, ManyChat):
1. Recibir payload
2. Validar firma/token (Code node)
3. IF valido → procesar, ELSE → log + alert

### Patron B: Sub-workflow para logica reutilizable
Cuando la misma logica se usa en >1 workflow:
- notion-delivery: usado por payment-webhook y manual-delivery
- confirmation-sender: usado por payment-webhook y resend-confirmation

### Patron C: Idempotency check
Para todo workflow que procesa pagos:
1. Extraer transaction_id
2. Buscar en Google Sheets
3. IF existe → skip + log, IF no existe → procesar

### Patron D: Error handling con alerta
```
Try (nodo principal)
  → Success: continuar flujo
  → Error:
    → Log error details
    → Send alert to Santiago (WhatsApp/email)
    → Return graceful error (no romper flujo padre)
```

### Patron E: Google Sheets como DB
```
Leer:    Google Sheets node → Operation: Read Rows → Filter by column
Escribir: Google Sheets node → Operation: Append Row
Buscar:  Google Sheets node → Operation: Read Rows → Lookup column = value
Update:  Google Sheets node → Operation: Update Row → Match by row number
```

## Expresiones mas usadas en Resummo

| Expresion | Uso |
|-----------|-----|
| `{{ $json.body.email }}` | Email del webhook de pago |
| `{{ $json.body.transaction_id }}` | ID transaccion de pasarela |
| `{{ $json.body.amount }}` | Monto pagado |
| `{{ $json.body.package }}` | Paquete comprado |
| `{{ $('Google Sheets').first().json.email }}` | Email desde Sheets |
| `{{ $now.setZone('America/Lima').toFormat('yyyy-MM-dd HH:mm') }}` | Timestamp Peru |
| `{{ $if($json.paquete === 'All Access', true, false) }}` | Condicional paquete |
| `{{ $json.body?.customer?.email ?? '' }}` | Email con optional chaining |

## Checklist de creacion de workflow

### Fase 1: Planning
- [ ] Trigger definido (webhook, cron, manual, sub-workflow)
- [ ] Patron seleccionado (A, B, C, D, E)
- [ ] Datos de entrada/salida identificados
- [ ] Integraciones necesarias listadas
- [ ] Error handling disenado

### Fase 2: Implementation
- [ ] Nodos core creados
- [ ] Flujo principal conectado
- [ ] Branch de error conectado (Patron D)
- [ ] Credenciales configuradas en n8n (no hardcodeadas)
- [ ] Code nodes retornan `[{ json: { ... } }]`

### Fase 3: Validation
- [ ] Testear con pinned data (caso exitoso)
- [ ] Testear con pinned data (caso error)
- [ ] Testear idempotencia (si aplica)
- [ ] Verificar expresiones (nombres de nodos case-sensitive)
- [ ] Code nodes no usan `{{ }}` (JavaScript directo)

### Fase 4: Deployment
- [ ] Backup local del workflow en `automations/n8n/`
- [ ] `errorWorkflow` configurado en settings (apunta a workflow de error-handler)
- [ ] `executionTimeout` configurado (60s para webhooks, 3600s para reportes cron)
- [ ] `retryOnFail: true` en nodos que llaman APIs externas (Notion, Sheets, Drive, email)
- [ ] `onError: continueErrorOutput` en nodos no-criticos (Slack, analytics) para no detener flujo principal
- [ ] `saveDataErrorExecution: all` para poder debuggear fallos en produccion
- [ ] Workflow activado
- [ ] Test con caso real (modo sandbox si es pago)
- [ ] Documentar en `automations/README.md`

### Fase 5: Post-Deploy Verification
- [ ] Verificar primeras 3 ejecuciones en produccion (sin errores inesperados)
- [ ] Confirmar que `errorWorkflow` se dispara correctamente (testear con error intencional si es posible)
- [ ] Kill switch documentado: `POST /api/v1/workflows/{id}/deactivate` — saber desactivar rapido si algo sale mal
- [ ] Rollback plan: backup JSON guardado en `automations/n8n/`, saber revertir via `PUT /api/v1/workflows/{id}`
- [ ] Rate limits no excedidos bajo carga real (Google Sheets 300/min, Notion 3/s, ManyChat timeout 10s)

## Gotchas

- **Webhook data bajo `.body`** — datos de webhook externo viven en `$json.body.campo`, NO en `$json.campo`
- **Nombres de nodos case-sensitive** — `$node["Google Sheets"]` no es `$node["google sheets"]`
- **Code nodes NO usan `{{ }}`** — usar JavaScript directo: `$input.first().json.campo`
- **Return obligatorio en Code nodes** — siempre `return [{ json: { ... } }]`
- **Google Sheets delay** — puede tardar 1-2s en reflejar escrituras, esperar antes de leer
- **Notion API rate limits** — 3 requests/second, usar Wait node si hay batch
- **ManyChat External Request timeout** — 10 segundos max, mantener flujos rapidos
- **n8n CE sin Variables** — usar PLACEHOLDER_* o Code node con lookup table

## HTTP Request Avanzado

### Batching nativo
```
HTTP Request node → Add Option → Batching
- batchSize: 10 (items por request)
- batchInterval: 1000 (ms entre batches)
Usar cuando: invitar >10 usuarios a Notion, compartir >10 carpetas Drive
```

### Pagination automatica
```json
// En HTTP Request node → Add Option → Pagination
{
  "paginationMode": "responseContainsNextURL",
  "nextURL": "={{ $response.body.next_cursor }}",
  "completeExpression": "={{ !$response.body.has_more }}"
}
// Para cursor-based (Notion, Stripe):
// paginationMode: "updateAParameterInEachRequest"
// parameterName: "starting_after" | "cursor" | "page_token"
```

### Rate limit monitoring
```javascript
// En Code node post-request — leer headers de rate limit
const remaining = parseInt($input.first().json.headers['x-ratelimit-remaining'] || '100');
const resetAt = parseInt($input.first().json.headers['x-ratelimit-reset'] || '0');
if (remaining < 10) {
  const waitMs = (resetAt * 1000) - Date.now() + 500;
  return [{ json: { ...$input.first().json, _waitMs: Math.max(waitMs, 1000) } }];
}
return $input.all();
// Conectar a Wait node: {{ $json._waitMs }}ms si _waitMs > 0
```

### fullResponse para debug
```json
// En HTTP Request node → Options → Full Response: ON
// Expone: $json.status, $json.headers, $json.body
// Usar en desarrollo para ver headers completos y status codes
```

### Retry directo en nodo
```json
// Settings del nodo HTTP Request
{
  "retryOnFail": true,
  "maxTries": 3,
  "waitBetweenTries": 1000
}
```

## Integracion ManyChat — Schema y Patrones

### Input de ManyChat External Request
```json
{
  "subscriber_id": "12345678",
  "first_name": "Juan",
  "last_name": "Perez",
  "last_input_text": "hola quiero ver el paquete All Access",
  "gender": "male",
  "profile_pic_url": "...",
  "locale": "es_LA",
  "timezone": "America/Lima",
  "custom_fields": {
    "paquete_interes": "All Access",
    "es_cliente": "false"
  }
}
```

### Respuesta esperada para ManyChat (< 10 segundos)
```json
{
  "version": "v2",
  "content": {
    "messages": [
      { "type": "text", "text": "Hola Juan! 👋 ..." },
      { "type": "text", "text": "El paquete All Access incluye..." }
    ],
    "actions": [
      { "action": "set_field_value", "field_name": "es_cliente", "value": "false" }
    ],
    "quick_replies": [
      { "type": "text", "title": "Ver precios 💰", "payload": "PRECIOS" },
      { "type": "text", "title": "Ver ejemplos 📚", "payload": "EJEMPLOS" }
    ]
  }
}
```

### Pattern timeout-safe (CRITICO — ManyChat corta a 10s)
```
Webhook → Extract subscriber_id + last_input_text
  → Google Sheets Lookup (< 1s) — es cliente existente?
  → IF cliente:
    → Build respuesta personalizada (Code node, < 2s)
    → Respond to Webhook (< 3s total)
  → IF no cliente:
    → Build respuesta generica (Code node, < 1s)
    → Respond to Webhook (< 2s total)
    → DESPUES de responder: HTTP Request → registrar lead (async)

NUNCA: llamar LLM sincrono antes de responder
NUNCA: hacer >2 llamadas a APIs externas antes de responder
```

## Integraciones Especificas

### Culqi — Validacion HMAC
```javascript
// Code node — validar firma de webhook Culqi
const crypto = require('crypto');
const rawBody = $input.first().json.rawBody || '';
const signature = $input.first().json.headers['x-culqi-signature'] || '';
const secret = $env.CULQI_WEBHOOK_SECRET;

const computed = crypto
  .createHmac('sha256', secret)
  .update(rawBody, 'utf8')
  .digest('hex');

const isValid = crypto.timingSafeEqual(
  Buffer.from(computed, 'hex'),
  Buffer.from(signature, 'hex')
);
return [{ json: { isValid, rawBody } }];
```

### Notion — Compartir pagina (workaround API)
```javascript
// Notion no tiene endpoint nativo de "share page with email"
// Workaround 1: Crear page con parent = workspace page + cover + invitar via email
// HTTP Request:
// POST https://api.notion.com/v1/pages
// Headers: Authorization: Bearer $env.NOTION_TOKEN, Notion-Version: 2022-06-28
// Body:
{
  "parent": { "page_id": "NOTION_PAGE_ID_DEL_PAQUETE" },
  "properties": {
    "title": [{ "text": { "content": "Acceso " + paquete + " - " + nombre } }]
  }
}
// Workaround 2: Shared link publico (sin autenticacion) — menos seguro
// GET https://api.notion.com/v1/pages/{page_id} → share_url en response
```

### Google Drive — Compartir carpeta
```javascript
// HTTP Request al Google Drive API v3
// POST https://www.googleapis.com/drive/v3/files/{FOLDER_ID}/permissions
// Auth: OAuth2 con scope drive.file o drive
// Body:
{
  "role": "reader",
  "type": "user",
  "emailAddress": email
}
// sendNotificationEmail: false para no enviar email de Drive (manejamos nosotros el email)
```

### Email transaccional — Resend
```javascript
// HTTP Request
// POST https://api.resend.com/emails
// Headers: Authorization: Bearer $env.RESEND_API_KEY
// Body:
{
  "from": "Resummo <noreply@resummo.com>",
  "to": [email],
  "subject": "Tu acceso a Resummo esta listo 🎉",
  "html": htmlContent
}
```

### GA4 — Server-side Purchase Event
```javascript
// HTTP Request
// POST https://www.google-analytics.com/mp/collect?measurement_id=G-XXXXX&api_secret=$env.GA4_API_SECRET
// Body:
{
  "client_id": subscriber_id || transaction_id,
  "events": [{
    "name": "purchase",
    "params": {
      "transaction_id": transaction_id,
      "value": monto,
      "currency": "PEN",
      "items": [{ "item_name": paquete, "price": monto, "quantity": 1 }]
    }
  }]
}
```

### Meta Conversions API — Purchase
```javascript
// HTTP Request
// POST https://graph.facebook.com/v18.0/{PIXEL_ID}/events?access_token=$env.META_CAPI_TOKEN
// Body:
{
  "data": [{
    "event_name": "Purchase",
    "event_time": Math.floor(Date.now() / 1000),
    "event_id": transaction_id,  // Para deduplicacion con pixel browser
    "user_data": {
      "em": [sha256(email.toLowerCase())]  // Email hasheado
    },
    "custom_data": {
      "value": monto,
      "currency": "PEN",
      "content_name": paquete
    },
    "action_source": "website"
  }]
}
```

## Referencias
- `${CLAUDE_SKILL_DIR}/references/code-node-patterns.md` — patrones de Code nodes JS
- `.claude/rules/n8n-patterns.md` — reglas obligatorias de deploy
- `.claude/rules/n8n-ai-agents.md` — patrones de AI Agent node
- `.claude/rules/payment-automation.md` — flujo de pagos
- `.claude/rules/notion-delivery.md` — entrega Notion
