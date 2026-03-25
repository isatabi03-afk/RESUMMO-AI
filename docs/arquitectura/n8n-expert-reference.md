# n8n Expert Reference — Resummo

Base de conocimiento tecnico exhaustivo de n8n para construir y operar workflows de pago, entrega Notion, agente IA y reportes automaticos.

**Fuentes:** OpenAPI spec oficial (GitHub n8n-io/n8n), documentacion n8n, comunidad n8n, articulos tecnicos especializados.

---

## 1. Arquitectura y Conceptos Fundamentales

### Que es n8n
- Plataforma de automatizacion de workflows fair-code con 400+ integraciones
- Escrita en TypeScript, Node.js
- Self-hosteable (Docker, npm, VPS) o cloud (app.n8n.cloud)
- 180K+ stars en GitHub, 56K+ forks
- Soporta JavaScript y Python en Code nodes
- Capacidades nativas de IA (LangChain)

### Arquitectura interna
- **Monorepo** con paquetes: `cli`, `core`, `workflow`, `nodes-base`, `editor-ui`
- Controladores REST: `WorkflowsController`, `ExecutionsController`, `CredentialsController`
- Servicios: `WorkflowService`, `CredentialService` manejan logica de negocio
- Base de datos: SQLite (default), PostgreSQL, MySQL
- Cola de ejecucion: main mode (default) o queue mode (Redis + workers)

### Modelo de datos
- **Workflow**: coleccion de nodos conectados con settings y metadata
- **Node**: unidad de ejecucion con tipo, parametros, credenciales y posicion
- **Connection**: enlace entre nodos (source → target via main/ai outputs)
- **Execution**: instancia de ejecucion de un workflow con datos de entrada/salida
- **Credential**: secreto encriptado reutilizable entre nodos

---

## 2. REST API Publica (OpenAPI 3.0)

### Autenticacion
```
Header: X-N8N-API-KEY: <api-key>
Base URL: /api/v1
```
Todas las requests requieren API key en header `X-N8N-API-KEY`.

### Recursos disponibles
| Recurso | Descripcion |
|---------|-------------|
| Workflow | CRUD de workflows, activar/desactivar |
| Execution | Listar, obtener, eliminar, reintentar, detener ejecuciones |
| Credential | CRUD de credenciales (secrets no se exponen en GET) |
| Tags | CRUD de tags para organizar workflows |
| Variables | CRUD de variables globales (requiere licencia en CE) |
| User | Listar usuarios, actualizar roles |
| Audit | Auditoria de seguridad |
| SourceControl | Pull de cambios de source control |
| DataTable | CRUD de tablas de datos y sus filas |
| Projects | Gestion de proyectos y usuarios |
| Discover | Descubrimiento de capacidades API |

### Endpoints de Workflows

| Metodo | Path | Descripcion |
|--------|------|-------------|
| `GET` | `/workflows` | Listar todos los workflows. Filtros: `active` (bool), `tags` (string), `name` (string), `projectId`, `excludePinnedData` (bool). Paginacion: `limit`, `cursor` |
| `POST` | `/workflows` | Crear workflow. Body: objeto workflow (name, nodes, connections, settings requeridos) |
| `GET` | `/workflows/{id}` | Obtener workflow por ID. Query: `excludePinnedData` |
| `PUT` | `/workflows/{id}` | Actualizar workflow completo. Si esta publicado, se re-publica automaticamente |
| `DELETE` | `/workflows/{id}` | Eliminar workflow |
| `POST` | `/workflows/{id}/activate` | Publicar/activar workflow. Body opcional: `versionId`, `name`, `description` |
| `POST` | `/workflows/{id}/deactivate` | Desactivar workflow |
| `PUT` | `/workflows/{id}/tags` | Actualizar tags del workflow |
| `PUT` | `/workflows/{id}/transfer` | Transferir workflow a otro proyecto |
| `GET` | `/workflows/{id}/{versionId}` | Obtener version especifica |

### Endpoints de Executions

| Metodo | Path | Descripcion |
|--------|------|-------------|
| `GET` | `/executions` | Listar ejecuciones. Filtros: `status` (canceled/crashed/error/new/running/success/unknown/waiting), `workflowId`, `projectId`. Paginacion: `limit`, `cursor` |
| `GET` | `/executions/{id}` | Obtener ejecucion por ID. Query: `includeData` |
| `DELETE` | `/executions/{id}` | Eliminar ejecucion |
| `POST` | `/executions/{id}/retry` | Reintentar ejecucion. Body: `loadWorkflow` (bool) — si true, usa la version actual del workflow en vez de la guardada al momento de la ejecucion |
| `POST` | `/executions/{id}/stop` | Detener ejecucion en curso |
| `POST` | `/executions/stop` | Detener multiples ejecuciones |
| `PUT` | `/executions/{id}/tags` | Actualizar tags de ejecucion |

### Endpoints de Credentials

| Metodo | Path | Descripcion |
|--------|------|-------------|
| `GET` | `/credentials` | Listar credenciales (solo owner/admin). Los secrets NO se incluyen |
| `POST` | `/credentials` | Crear credencial. Body: tipo y datos del credential |
| `PATCH` | `/credentials/{id}` | Actualizar credencial (solo el owner puede) |
| `DELETE` | `/credentials/{id}` | Eliminar credencial |
| `GET` | `/credentials/schema/{credentialTypeName}` | Obtener schema de un tipo de credencial |
| `PUT` | `/credentials/{id}/transfer` | Transferir credencial a otro proyecto |

### Endpoints de Variables

| Metodo | Path | Descripcion |
|--------|------|-------------|
| `GET` | `/variables` | Listar variables. Filtros: `projectId`, `state` |
| `POST` | `/variables` | Crear variable |
| `GET/PUT/DELETE` | `/variables/{id}` | Operaciones por ID |

### Endpoints de Tags

| Metodo | Path | Descripcion |
|--------|------|-------------|
| `GET` | `/tags` | Listar tags |
| `POST` | `/tags` | Crear tag |
| `GET/PUT/DELETE` | `/tags/{id}` | Operaciones por ID |

### Otros endpoints
- `POST /audit` — Ejecutar auditoria de seguridad
- `POST /source-control/pull` — Pull de source control
- `GET/POST /data-tables` — CRUD de data tables
- `GET/POST /projects` — CRUD de proyectos

---

## 3. Schema de Workflow (JSON)

### Estructura completa de un workflow
```json
{
  "id": "2tUt1wbLX592XDdX",
  "name": "Payment Webhook",
  "active": true,
  "nodes": [
    {
      "id": "uuid-del-nodo",
      "name": "Webhook",
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2,
      "position": [-100, 80],
      "parameters": {
        "path": "payment-webhook",
        "httpMethod": "POST",
        "responseMode": "responseNode",
        "options": {
          "rawBody": true
        }
      },
      "credentials": {},
      "webhookId": "unique-webhook-id",
      "retryOnFail": false,
      "maxTries": 3,
      "waitBetweenTries": 1000,
      "continueOnFail": false,
      "onError": "stopWorkflow",
      "executeOnce": false,
      "alwaysOutputData": false,
      "disabled": false
    }
  ],
  "connections": {
    "Webhook": {
      "main": [
        [{ "node": "Validate Signature", "type": "main", "index": 0 }]
      ]
    }
  },
  "settings": {
    "saveExecutionProgress": true,
    "saveDataErrorExecution": "all",
    "saveDataSuccessExecution": "all",
    "executionTimeout": 3600,
    "errorWorkflow": "VzqKEW0ShTXA5vPj",
    "timezone": "America/Lima",
    "executionOrder": "v1",
    "callerPolicy": "workflowsFromSameOwner",
    "availableInMCP": false
  },
  "staticData": { "lastId": 1 },
  "pinData": null,
  "tags": []
}
```

### Propiedades del Node
| Propiedad | Tipo | Descripcion |
|-----------|------|-------------|
| `id` | string (UUID) | Identificador unico del nodo |
| `name` | string | Nombre visible del nodo |
| `type` | string | Tipo de nodo (ej: `n8n-nodes-base.webhook`) |
| `typeVersion` | number | Version del tipo de nodo |
| `position` | [x, y] | Posicion en el canvas |
| `parameters` | object | Configuracion especifica del nodo |
| `credentials` | object | Referencias a credenciales `{ tipo: { id, name } }` |
| `retryOnFail` | boolean | Reintentar si falla |
| `maxTries` | number | Maximo intentos de reintento |
| `waitBetweenTries` | number | Milisegundos entre reintentos |
| `onError` | string | `stopWorkflow` o `continueRegularOutput` o `continueErrorOutput` |
| `executeOnce` | boolean | Ejecutar solo una vez (no por cada item) |
| `disabled` | boolean | Nodo deshabilitado |

### Workflow Settings
| Setting | Tipo | Descripcion |
|---------|------|-------------|
| `errorWorkflow` | string | ID del workflow que maneja errores |
| `executionTimeout` | number | Timeout en segundos (max 3600) |
| `saveDataErrorExecution` | `all`/`none` | Guardar datos de ejecuciones con error |
| `saveDataSuccessExecution` | `all`/`none` | Guardar datos de ejecuciones exitosas |
| `timezone` | string | Zona horaria (ej: `America/Lima`) |
| `callerPolicy` | enum | Quien puede llamar este workflow via Execute Workflow |
| `availableInMCP` | boolean | Exponer via Model Context Protocol |

---

## 4. Webhook Node — Configuracion y Seguridad

### Configuracion basica
| Parametro | Opciones | Descripcion |
|-----------|----------|-------------|
| `httpMethod` | GET, POST, PUT, DELETE, PATCH, HEAD | Metodo HTTP aceptado |
| `path` | string | Path del webhook (ej: `payment-webhook`) |
| `responseMode` | `onReceived`, `lastNode`, `responseNode` | Cuando responder |
| `authentication` | None, Basic, Header, JWT | Metodo de autenticacion |
| `options.rawBody` | boolean | Habilitar acceso al body crudo (OBLIGATORIO para validacion de firmas) |

### URL del webhook
- **Produccion:** `https://tu-dominio.com/webhook/payment-webhook`
- **Test:** `https://tu-dominio.com/webhook-test/payment-webhook`

### Metodos de autenticacion

**1. Sin autenticacion** — Solo para desarrollo
**2. Basic Auth** — `Authorization: Basic <base64(user:pass)>`. Solo sobre HTTPS.
**3. Header Auth** — Header personalizado como `X-API-Key: token-value`. Recomendado.
**4. JWT Auth** — Tokens firmados con expiracion y claims personalizados.

### Validacion HMAC de firmas (patron para pasarelas de pago)

**Caso Stripe (patron replicable para Culqi/EasyPay):**
```javascript
// Code Node despues del Webhook
const crypto = require('crypto');
const secret = $env.PAYMENT_SIGNING_SECRET;  // Nunca hardcodear
const rawBody = $json.rawBody;
const signature = $json.headers['stripe-signature'];

// Extraer timestamp y firma
const timestamp = signature.split(',')[0].split('=')[1];
const receivedSig = signature.split(',')[1].split('=')[1];

// Reconstruir payload firmado
const signedPayload = `${timestamp}.${rawBody}`;
const expectedSig = crypto
  .createHmac('sha256', secret)
  .update(signedPayload)
  .digest('hex');

if (expectedSig !== receivedSig) {
  throw new Error('Firma invalida - webhook rechazado');
}

return { json: { verified: true, data: JSON.parse(rawBody) } };
```

**Patron generico HMAC-SHA256 (para Culqi/EasyPay):**
```javascript
const crypto = require('crypto');
const secret = $env.WEBHOOK_SECRET;
const rawBody = $input.item.json.rawBody;
const receivedSignature = $input.item.json.headers['x-signature'];

const calculatedSignature = crypto
  .createHmac('sha256', secret)
  .update(rawBody)
  .digest('hex');

if (calculatedSignature !== receivedSignature) {
  throw new Error('Webhook signature validation failed');
}

return [{ json: { valid: true, payload: JSON.parse(rawBody) } }];
```

### Configuracion CORS para webhooks publicos
```json
{
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Headers": "Content-Type, Authorization",
  "Access-Control-Allow-Methods": "POST, GET, OPTIONS"
}
```

### REST API via Webhooks
- Habilitar "Allow Multiple HTTP Methods" para escuchar GET, POST, PUT, DELETE
- Cada metodo produce output separado para ruteo condicional
- Usar path parameters y Switch/IF nodes para comportamiento REST

---

## 5. HTTP Request Node — Llamadas a APIs externas

### Configuracion
| Parametro | Opciones | Descripcion |
|-----------|----------|-------------|
| Method | GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS | Metodo HTTP |
| URL | string | URL del endpoint |
| Authentication | None, Predefined Credential, Generic | Autenticacion |
| Send Body | boolean | Incluir body en la request |
| Body Content Type | JSON, Form URL Encoded, Form Data, Raw, Binary | Formato del body |
| Send Headers | boolean | Headers personalizados |
| Send Query Parameters | boolean | Query params |
| Response Format | JSON, Text, Binary, File | Formato de respuesta |

### Autenticacion con credenciales predefinidas
Para Notion, Google Sheets, etc. se usa "Predefined Credential Type" que inyecta automaticamente el token de autenticacion.

### Retry automtico
- Configurar `retryOnFail: true` en el nodo
- `maxTries`: numero de reintentos (ej: 3)
- `waitBetweenTries`: milisegundos entre reintentos (ej: 1000)

### Llamada a Notion API (compartir pagina)
```
POST https://api.notion.com/v1/pages
Headers:
  Authorization: Bearer {NOTION_TOKEN}
  Notion-Version: 2022-06-28
  Content-Type: application/json
Body (JSON):
  {
    "parent": { "page_id": "PAGE_ID_DEL_PAQUETE" },
    "properties": { ... }
  }
```

### Llamada a Google Sheets API
```
POST https://sheets.googleapis.com/v4/spreadsheets/{spreadsheetId}/values/{range}:append
Headers:
  Authorization: Bearer {GOOGLE_TOKEN}
Body:
  {
    "values": [["fecha", "nombre", "email", "paquete", "monto", "txn_id"]]
  }
```

### Llamada a Culqi API (verificar pago)
```
GET https://api.culqi.com/v2/charges/{charge_id}
Headers:
  Authorization: Bearer {CULQI_SECRET_KEY}
```

---

## 6. Nodos de Integracion Clave para Resummo

### 6.1 Notion Node

**Triggers disponibles:**
- Page added to database (polling)
- Page updated in database (polling)

**Operaciones por recurso:**

| Recurso | Acciones |
|---------|----------|
| Database | Query, Get, Get Many, Search by title |
| Page | Create, Get, Archive, Search, Update properties |
| Block | Append (paragraphs, headings, toggles, to-dos, bullets, images), Get children |
| User | Get, Get All (solo paid members, no guests) |

**Limitaciones importantes para Resummo:**
- La Notion API NO tiene endpoint nativo de "share page with email"
- Los guests solo se pueden consultar si tienes su user ID
- Para compartir paginas: usar HTTP Request node con la API de Notion directamente
- Opcion viable: invitar como guest al workspace, luego dar acceso a pagina especifica
- La propiedad Person solo acepta Users e Integrations como IDs, no emails arbitrarios

**Workarounds para compartir contenido:**
1. Generar links publicos con acceso de lectura (menos seguro)
2. Usar HTTP Request con PATCH para modificar permisos (mas control)
3. Duplicar pagina template y agregar como guest (recomendado)

**Credenciales:**
- Crear integracion interna en Notion → obtener Integration Token
- Compartir paginas/databases con la integracion manualmente en Notion
- El nodo maneja autenticacion automaticamente

### 6.2 Google Sheets Node

**Operaciones de documento:**
- Create spreadsheet
- Delete spreadsheet

**Operaciones de sheet:**
| Operacion | Descripcion | Parametros clave |
|-----------|-------------|-----------------|
| Append Row | Agregar fila al final | Mapping mode (auto/manual/none) |
| Append or Update Row | Agregar o actualizar si existe | Key column para match |
| Update Row | Modificar fila existente | Row index o key column |
| Get Row(s) | Obtener filas con filtros | Range (A1 notation), filtros |
| Delete Row | Eliminar fila | Row index |
| Clear Sheet | Limpiar datos (preserva estructura) | - |
| Create Sheet | Crear nueva hoja | - |
| Delete Sheet | Eliminar hoja | - |

**Modos de mapeo de datos:**
1. **Automatic**: n8n intenta mapear JSON keys a headers — riesgoso en produccion
2. **Manual**: mapeo explicito campo por campo — **recomendado para produccion**
3. **None**: sin mapeo

**Patron Upsert (Update or Insert):**
```
Get Rows (buscar por email) → IF (existe?)
  → TRUE: Update Row
  → FALSE: Append Row
```

**Rate limiting:**
- Google Sheets: 300 requests/minuto
- Usar Split In Batches + Wait nodes para operaciones masivas

**Credenciales:**
- OAuth2 con Google Cloud Console
- Service Account key para server-to-server
- Configurar redirect URI y scopes correctamente

### 6.3 WhatsApp Business Cloud Node

**Operaciones:**
- Send Message (texto, template, media)
- Send and Wait for Response (pausa workflow hasta respuesta)
- Upload/Download/Delete media

**Credenciales:**
- Meta for Developers → App Dashboard → WhatsApp → API Setup
- Generar access token permanente
- Configurar numero de telefono de negocio

**Comun issue:** Error "Invalid request body format" al usar Send Template si los datos no coinciden con el formato del template.

### 6.4 Send Email Node (SMTP)

**Configuracion Gmail:**
| Parametro | Valor |
|-----------|-------|
| Host | smtp.gmail.com |
| Port | 465 (SSL) o 587 (TLS) |
| User | email@gmail.com |
| Password | App password (2FA requerido) |
| SSL/TLS | Activado |

**Operaciones:**
- Send Email
- Send and Wait for Response

### 6.5 Stripe Node (patron para payment gateways)

**Trigger events disponibles:**
- payment_intent.succeeded
- charge.failed
- checkout.session.completed
- customer.created
- invoice.paid
- dispute.created

**Operaciones:**
- Get Balance
- Create Charge
- Create/Get/Update/Delete Customer
- Create Payment Intent
- 20+ acciones disponibles

**Patron replicable para Culqi:**
Culqi no tiene nodo nativo en n8n → usar Webhook + HTTP Request:
1. Webhook recibe IPN de Culqi
2. Code node valida firma HMAC
3. HTTP Request verifica el cargo con Culqi API
4. Google Sheets registra la transaccion
5. Notion/Drive comparte contenido
6. Email/WhatsApp confirma al comprador

---

## 7. Expresiones y Variables Built-in

### Variables de acceso a datos

| Variable | Descripcion | Ejemplo |
|----------|-------------|---------|
| `$json` | JSON del item actual (shorthand de `$input.item.json`) | `$json.email` |
| `$json.fieldName` | Acceder propiedad especifica | `$json.body.transaction_id` |
| `$input.item.json` | Forma completa de acceder al item | `$input.item.json.amount` |
| `$input.all()` | Todos los items del input | `$input.all()` |
| `$input.first()` | Primer item del input | `$input.first().json` |
| `$input.last()` | Ultimo item del input | `$input.last().json` |
| `$binary` | Datos binarios del item actual | `$binary.data` |
| `$itemIndex` | Indice del item actual (0-based) | `$itemIndex` |
| `$runIndex` | Indice de la ejecucion actual | `$runIndex` |

### Referenciar otros nodos

| Expresion | Descripcion |
|-----------|-------------|
| `$("NodeName").first()` | Primer item del nodo especificado |
| `$("NodeName").all()` | Todos los items del nodo |
| `$("NodeName").last()` | Ultimo item del nodo |
| `$("NodeName").item` | Item vinculado del nodo (item linking) |
| `$("NodeName").first().json.email` | Campo especifico del primer item |
| `$('Google Sheets').first().json.email` | Email de Google Sheets |
| `$prevNode.name` | Nombre del nodo anterior |

### Variables de metadata

| Variable | Descripcion |
|----------|-------------|
| `$execution.id` | ID de la ejecucion actual |
| `$execution.resumeUrl` | URL para reanudar ejecucion pausada |
| `$workflow.id` | ID del workflow |
| `$workflow.name` | Nombre del workflow |
| `$workflow.active` | Si el workflow esta activo |
| `$env.VARIABLE_NAME` | Variable de entorno del sistema |
| `$vars.variableName` | Variables de n8n (requiere licencia en CE) |

### Fecha y hora

| Expresion | Resultado |
|-----------|-----------|
| `$now` | Timestamp actual |
| `$today` | Hoy a medianoche |
| `$now.toFormat("yyyy-MM-dd")` | Formato personalizado |
| `$now.toFormat("yyyy-MM-dd HH:mm")` | Fecha y hora |
| `$now.plus({days: 7})` | Sumar 7 dias |
| `$now.minus({hours: 2})` | Restar 2 horas |
| `$json.date.toDateTime()` | Convertir string a DateTime |
| `.year`, `.month`, `.day` | Extraer partes de fecha |

### Condicionales

| Expresion | Descripcion |
|-----------|-------------|
| `condition ? "yes" : "no"` | Operador ternario |
| `value ?? "default"` | Nullish coalescing (null/undefined) |
| `value \|\| "fallback"` | OR logico |
| `$if(condition, "true", "false")` | Helper de n8n |
| `$json?.user?.email` | Optional chaining |

### Strings

| Metodo | Uso |
|--------|-----|
| `.toUpperCase()` / `.toLowerCase()` | Conversion de caso |
| `.trim()` | Remover espacios |
| `.includes("search")` | Buscar substring |
| `.split(delimiter)` | Dividir en array |
| `.replace(old, new)` | Reemplazar texto |
| `.extractEmail()` | Extraer emails (n8n nativo) |

### Arrays

| Metodo | Uso |
|--------|-----|
| `.length` | Tamano del array |
| `.map(x => x.name)` | Transformar elementos |
| `.filter(x => x > 5)` | Filtrar por condicion |
| `.reduce((acc, val) => acc + val, 0)` | Agregar/acumular |
| `[...new Set(array)]` | Valores unicos |
| `.sort((a, b) => a - b)` | Ordenar |

### Objetos

| Metodo | Uso |
|--------|-----|
| `Object.keys(obj)` | Nombres de propiedades |
| `Object.values(obj)` | Valores |
| `Object.entries(obj)` | Pares key-value |
| `obj.hasOwnProperty(key)` | Verificar existencia |

### JMESPath
```
$jmespath(data, 'query')  // Consultar JSON con JMESPath
```

---

## 8. Code Node — Patrones Avanzados

### Modos de ejecucion

**Run Once for All Items (default):**
```javascript
// Se ejecuta una vez con todos los items
const allItems = $input.all();
const results = allItems.map(item => ({
  json: {
    ...item.json,
    processed: true
  }
}));
return results;
```

**Run Once for Each Item:**
```javascript
// Se ejecuta por cada item individualmente
const item = $input.item.json;
return {
  ...item,
  processed: true,
  timestamp: new Date().toISOString()
};
```

### Hacer HTTP requests dentro del Code Node
```javascript
const response = await this.helpers.httpRequest({
  method: 'GET',
  url: `https://api.example.com/resource/${$json.id}`,
  json: true,
  headers: {
    'Authorization': `Bearer ${$env.API_TOKEN}`
  }
});

return [{ json: response }];
```

### getWorkflowStaticData — Persistencia entre ejecuciones
```javascript
// Obtener datos estaticos globales (compartidos entre todos los nodos)
const staticData = $getWorkflowStaticData('global');

// Obtener datos estaticos del nodo (solo este nodo)
const nodeData = $getWorkflowStaticData('node');

// Leer y escribir
const lastProcessedId = staticData.lastId || 0;
staticData.lastId = currentId;  // Se persiste automaticamente

// Usar para circuit breaker
staticData.apiFailures = (staticData.apiFailures || 0) + 1;
staticData.lastFailureTime = Date.now();
```

**IMPORTANTE:** Static Data solo persiste en ejecuciones de produccion (triggers, webhooks). NO persiste en ejecuciones manuales. El workflow debe estar activado.

### Crear archivos binarios
```javascript
const htmlContent = `<html><body>Reporte generado</body></html>`;
const binaryData = await this.helpers.prepareBinaryData(
  Buffer.from(htmlContent, 'utf8'),
  'report.html',
  'text/html'
);

return [{
  json: { reportGenerated: true },
  binary: { data: binaryData }
}];
```

### Patron de agregacion/reporte
```javascript
const allItems = $input.all();
const orders = allItems.map(item => item.json);

const totalRevenue = orders.reduce(
  (sum, order) => sum + order.price * order.quantity, 0
);

const byPackage = orders.reduce((report, order) => {
  const pkg = order.paquete;
  if (!report[pkg]) report[pkg] = { total: 0, count: 0 };
  report[pkg].total += order.monto;
  report[pkg].count += 1;
  return report;
}, {});

return [{
  json: {
    totalRevenue: parseFloat(totalRevenue.toFixed(2)),
    byPackage,
    generatedAt: new Date().toISOString()
  }
}];
```

### Error handling en Code Node
```javascript
try {
  const response = await this.helpers.httpRequest({
    method: 'POST',
    url: 'https://api.notion.com/v1/pages',
    headers: {
      'Authorization': `Bearer ${$env.NOTION_TOKEN}`,
      'Notion-Version': '2022-06-28'
    },
    body: { parent: { page_id: $json.pageId } },
    json: true
  });
  return [{ json: { success: true, data: response } }];
} catch (error) {
  console.error('Notion API failed:', error.message);
  return [{ json: {
    success: false,
    error: error.message,
    statusCode: error.statusCode || 500
  }}];
}
```

---

## 9. Error Handling — Patrones y Best Practices

### 9.1 Error Workflow (configuracion global)

En Workflow Settings → `errorWorkflow`: apuntar al ID del workflow que contiene un Error Trigger node.

El Error Trigger recibe:
- Nombre del workflow que fallo
- Nodo que fallo
- Mensaje de error
- ID de ejecucion
- Timestamp

**Ejemplo de Error Workflow:**
```
Error Trigger → Slack/Email Alert → Log a Google Sheets → PagerDuty (si critico)
```

### 9.2 Retry on Fail (por nodo)

| Propiedad | Valor | Descripcion |
|-----------|-------|-------------|
| `retryOnFail` | true | Habilitar reintentos |
| `maxTries` | 3-5 | Numero maximo de intentos |
| `waitBetweenTries` | 1000-5000 | Milisegundos entre intentos |

### 9.3 onError (por nodo)

| Valor | Comportamiento |
|-------|---------------|
| `stopWorkflow` | Detiene el workflow completo (default) |
| `continueRegularOutput` | Continua por la salida regular (ignora error) |
| `continueErrorOutput` | Continua por la salida de error (rama separada) |

### 9.4 Exponential Backoff con Jitter
```javascript
// En Code Node antes de llamada API
const staticData = $getWorkflowStaticData('global');
const retryCount = staticData.retryCount || 0;

if (retryCount > 0) {
  const baseDelay = Math.pow(2, retryCount) * 1000; // 1s, 2s, 4s, 8s...
  const jitter = baseDelay * (0.8 + Math.random() * 0.4); // +-20%
  await new Promise(resolve => setTimeout(resolve, jitter));
}

try {
  const result = await this.helpers.httpRequest({ /* ... */ });
  staticData.retryCount = 0; // Reset on success
  return [{ json: result }];
} catch (error) {
  staticData.retryCount = retryCount + 1;
  if (retryCount >= 5) {
    staticData.retryCount = 0;
    throw new Error(`Max retries exceeded: ${error.message}`);
  }
  throw error; // Will trigger retry
}
```

### 9.5 Circuit Breaker
```javascript
const staticData = $getWorkflowStaticData('global');
const failures = staticData.apiFailures || 0;
const lastFailure = staticData.lastFailureTime || 0;
const cooldown = 60000; // 60 segundos

// Si el circuito esta abierto, derivar a fallback
if (failures >= 5 && Date.now() - lastFailure < cooldown) {
  return [{ json: {
    queued: true,
    reason: 'circuit_open',
    message: 'API temporalmente no disponible, cola de espera activada'
  }}];
}

try {
  const result = await this.helpers.httpRequest({ /* ... */ });
  // Reset en exito
  staticData.apiFailures = 0;
  return [{ json: result }];
} catch (error) {
  staticData.apiFailures = failures + 1;
  staticData.lastFailureTime = Date.now();
  throw error;
}
```

### 9.6 Dead Letter Queue (DLQ)
```
Webhook → Validate Data → IF (valid?)
  → TRUE: Process normally
  → FALSE:
    → Log to Google Sheets ("DLQ" sheet)
    → Alert via Slack/Email
    → Continue (no crash)
```

### 9.7 Idempotencia
```javascript
// Verificar transaction_id antes de procesar
const txnId = $json.body.transaction_id;
const existingRows = $('Google Sheets - Check').all();
const isDuplicate = existingRows.some(
  row => row.json.transaction_id === txnId
);

if (isDuplicate) {
  return [{ json: {
    status: 'duplicate',
    transaction_id: txnId,
    message: 'Transaction already processed'
  }}];
}

return [{ json: { status: 'new', data: $json.body } }];
```

### 9.8 Compensacion/Rollback
Cuando un paso del flujo falla despues de pasos exitosos:
1. Guardar IDs/referencias de cada paso completado
2. Al fallar: ejecutar compensaciones en orden inverso
3. Ejemplo: pago procesado pero Notion fallo → marcar estado_entrega = "error", notificar, no revertir pago

### 9.9 Best Practices Consolidadas

1. **Siempre** habilitar retryOnFail para nodos que llaman APIs externas
2. **Siempre** configurar errorWorkflow para workflows criticos (pagos, entrega)
3. **Nunca** dejar un pago sin registrar — fallback de notificacion urgente
4. Usar `continueErrorOutput` para manejar errores sin detener el workflow
5. Clasificar errores: 5xx/timeout → retry, 4xx → revisar datos, 401 → refrescar credenciales
6. Log TODOS los errores con suficiente contexto (workflow ID, node, input, timestamp)
7. Testear escenarios de error intencionalmente antes de produccion
8. Monitorear metricas: tasa de error por workflow, tiempo de ejecucion, reintentos

---

## 10. Patrones Especificos para Resummo

### 10.1 Flujo de Pago Completo
```
Webhook (POST /payment-webhook)
  → Code: Validar firma HMAC
  → IF: Firma valida?
    → FALSE: Log Invalid + Alert Santiago + Respond 401
    → TRUE:
      → Code: Extraer datos (email, paquete, monto, txn_id)
      → Google Sheets: Check idempotencia (buscar txn_id)
      → IF: Duplicado?
        → TRUE: Log + Respond 200 (idempotente)
        → FALSE (paralelo):
          ├─ Google Sheets: Append Row (registro de venta)
          ├─ HTTP Request: Notion API (compartir pagina)
          ├─ HTTP Request: Google Drive API (compartir Anki si aplica)
          └─ Send Email: Confirmacion al comprador
      → Code: Verificar resultados
      → IF: Algun error?
        → TRUE: Alert Santiago + marcar estado_entrega = "error"
        → FALSE: marcar estado_entrega = "completado"
      → Respond to Webhook: 200 OK
```

### 10.2 Backend del Agente IA (ManyChat)
```
Webhook (POST /manychat-backend)
  → Code: Extraer datos del usuario (user_id, message, platform)
  → Google Sheets: Buscar si es cliente existente
  → IF: Cliente existente?
    → TRUE: Respuesta personalizada
    → FALSE:
      → Google Sheets: Registrar como lead
      → Respuesta generica
  → Code: Generar respuesta (o llamar LLM via HTTP Request)
  → Respond to Webhook: JSON con respuesta para ManyChat
```

### 10.3 Reporte Diario
```
Schedule Trigger (8am Peru / America/Lima)
  → Google Sheets: Get Rows (ventas del dia anterior)
  → Code: Calcular metricas (total ventas, por paquete, revenue)
  → Send Email / WhatsApp: Resumen a Andre/Santiago
```

### 10.4 Entrega Notion (Sub-workflow)
```
Execute Workflow Trigger (llamado por payment-webhook)
  → Input: email, paquete, nombre
  → Switch: Por paquete
    → Ciencias Basicas: HTTP Request → Notion API (share page CB_ID)
    → EsSalud/ENAM: HTTP Request → Notion API (share page ENAM_ID)
    → All Access: HTTP Request → Notion API (share all pages) + Drive API
    → ENCAPS: HTTP Request → Drive API (share Anki folder)
  → Code: Verificar respuesta de API
  → Return: { success: true/false, error: message }
```

---

## 11. Deploy via REST API — Protocolo Seguro

### Paso 1: Backup del workflow actual
```bash
curl -X GET "https://n8n.tuservidor.com/api/v1/workflows/{id}" \
  -H "X-N8N-API-KEY: $N8N_API_KEY" \
  -H "Content-Type: application/json" \
  > backup_workflow_{id}_$(date +%Y%m%d).json
```

### Paso 2: Actualizar workflow
```bash
curl -X PUT "https://n8n.tuservidor.com/api/v1/workflows/{id}" \
  -H "X-N8N-API-KEY: $N8N_API_KEY" \
  -H "Content-Type: application/json" \
  -d @workflow_updated.json
```

### Paso 3: Activar workflow
```bash
curl -X POST "https://n8n.tuservidor.com/api/v1/workflows/{id}/activate" \
  -H "X-N8N-API-KEY: $N8N_API_KEY"
```

### Paso 4: Verificar estado
```bash
curl -X GET "https://n8n.tuservidor.com/api/v1/workflows/{id}" \
  -H "X-N8N-API-KEY: $N8N_API_KEY" | jq '.active'
```

### Paso 5: Testear con pinned data o request real
```bash
curl -X POST "https://n8n.tuservidor.com/webhook/payment-webhook" \
  -H "Content-Type: application/json" \
  -d '{"test": true, "transaction_id": "TEST_001"}'
```

---

## 12. Limitaciones de n8n Community Edition

| Limitacion | Impacto | Workaround |
|------------|---------|------------|
| No soporta `$vars` (Variables nativas) | No se puede usar `$vars.MI_VARIABLE` | Usar `$env.VARIABLE` via variables de entorno del servidor |
| Sin SSO/SAML | Autenticacion basica | OK para equipo pequeno (Resummo) |
| Sin audit log nativo | No hay registro de cambios | Log manual en docs/ |
| Sin environments (staging/prod) | Un solo entorno | Usar instancias separadas si es necesario |
| Sin version history UI | No hay rollback visual | Backups JSON manuales en automations/n8n/ |
| Queue mode requiere Redis | Ejecucion concurrente limitada | Main mode OK para volumen actual de Resummo |

---

## Fuentes

- [GitHub n8n-io/n8n](https://github.com/n8n-io/n8n) — OpenAPI spec, schemas, codigo fuente
- [n8n Docs](https://docs.n8n.io/) — Documentacion oficial
- [n8n API Reference](https://docs.n8n.io/api/api-reference/)
- [n8n Expressions Cheat Sheet](https://n8narena.com/guides/n8n-expression-cheatsheet/)
- [n8n Code Node Guide](https://www.aifire.co/p/supercharge-your-n8n-workflows-the-complete-code-node-guide)
- [Webhook Security Guide](https://automategeniushub.com/mastering-the-n8n-webhook-node-part-b/)
- [Error Handling Patterns](https://www.pagelines.com/blog/n8n-error-handling-patterns)
- [Advanced Error Recovery](https://www.wednesday.is/writing-articles/advanced-n8n-error-handling-and-recovery-strategies)
- [Google Sheets Integration Guide](https://automategeniushub.com/ultimate-guide-to-n8n-google-sheets-integration/)
- [Notion n8n Nodes Overview](https://www.simonesmerilli.com/business/notion-n8n-nodes)
- [WhatsApp Business Cloud Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.whatsapp/)
- [Send Email Node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.sendemail/)
- [Stripe Node](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.stripe/)
