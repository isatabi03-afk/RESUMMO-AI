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

## Referencias
- `${CLAUDE_SKILL_DIR}/references/code-node-patterns.md` — patrones de Code nodes JS
- `.claude/rules/n8n-patterns.md` — reglas obligatorias de deploy
- `.claude/rules/payment-automation.md` — flujo de pagos
- `.claude/rules/notion-delivery.md` — entrega Notion
