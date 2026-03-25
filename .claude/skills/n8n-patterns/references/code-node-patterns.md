# Code Nodes JavaScript — Patrones Resummo

## Regla #1: Return format OBLIGATORIO
```javascript
// SIEMPRE retornar este formato
return [{ json: { nombre: 'Juan', email: 'juan@mail.com' } }];

// Para multiples items
return items.map(item => ({
  json: { ...item.json, processed: true }
}));
```

## Acceso a datos

### En modo "Run Once for All Items" (default, 95% de los casos)
```javascript
// Todos los items de entrada
const items = $input.all();

// Primer item
const first = $input.first();
const email = first.json.email;

// De otro nodo (nombre EXACTO, case-sensitive)
const sheetData = $node["Google Sheets"].first().json;
// o con shorthand
const sheetData = $('Google Sheets').first().json;
```

### En modo "Run Once for Each Item" (5% de los casos)
```javascript
// Item actual
const email = $input.item.json.email;
```

## Patron: Validar firma de webhook de pago

```javascript
// Para EasyPay (adaptar segun documentacion real)
const crypto = require('crypto');
const payload = JSON.stringify($input.first().json.body);
const signature = $input.first().json.headers['x-signature'];
const secret = 'PLACEHOLDER_WEBHOOK_SECRET';

const expected = crypto
  .createHmac('sha256', secret)
  .update(payload)
  .digest('hex');

const isValid = signature === expected;

return [{ json: { isValid, payload: isValid ? $input.first().json.body : null } }];
```

## Patron: Lookup table paquete → Notion Page ID

```javascript
const PAQUETES = {
  'ciencias-basicas': {
    notionPageId: 'PLACEHOLDER_NOTION_PAGE_CIENCIAS',
    includeDrive: false,
    driveLink: null
  },
  'essalud-enam': {
    notionPageId: 'PLACEHOLDER_NOTION_PAGE_ENAM',
    includeDrive: false,
    driveLink: null
  },
  'all-access': {
    notionPageId: 'PLACEHOLDER_NOTION_PAGE_ALL',
    includeDrive: true,
    driveLink: 'PLACEHOLDER_DRIVE_LINK_ANKI'
  },
  'encaps': {
    notionPageId: null, // Solo Drive
    includeDrive: true,
    driveLink: 'PLACEHOLDER_DRIVE_LINK_ENCAPS'
  }
};

const paquete = $input.first().json.body.package;
const config = PAQUETES[paquete];

if (!config) {
  return [{ json: { error: true, message: `Paquete desconocido: ${paquete}` } }];
}

return [{ json: { ...config, paquete } }];
```

## Patron: Idempotency check via Google Sheets data

```javascript
// Recibe: transaction_id del pago + rows de Google Sheets
const txId = $input.first().json.body.transaction_id;
const rows = $node["Google Sheets"].all();

const exists = rows.some(row => row.json.transaction_id === txId);

return [{ json: { isDuplicate: exists, transaction_id: txId } }];
```

## Patron: Calcular metricas diarias

```javascript
const rows = $input.all();
const today = DateTime.now().setZone('America/Lima').toFormat('yyyy-MM-dd');

const todayRows = rows.filter(r => r.json.fecha_pago?.startsWith(today));

const ventas = todayRows.length;
const revenue = todayRows.reduce((sum, r) => sum + (parseFloat(r.json.monto) || 0), 0);
const ticketPromedio = ventas > 0 ? (revenue / ventas).toFixed(2) : 0;

const porPaquete = {};
todayRows.forEach(r => {
  const pkg = r.json.paquete || 'desconocido';
  porPaquete[pkg] = (porPaquete[pkg] || 0) + 1;
});

return [{ json: {
  fecha: today,
  ventas,
  revenue,
  ticketPromedio: parseFloat(ticketPromedio),
  porPaquete
}}];
```

## Patron: Build email HTML de confirmacion

```javascript
const { nombre, email, paquete, notionLink, driveLink } = $input.first().json;

const html = `
<div style="font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto;">
  <h1>¡Bienvenido a Resummo, ${nombre}! 🎉</h1>
  <p>Tu compra del paquete <strong>${paquete}</strong> fue exitosa.</p>

  <h2>Accede a tu contenido:</h2>
  <ul>
    ${notionLink ? `<li><a href="${notionLink}">Abrir en Notion</a> — Tus resúmenes</li>` : ''}
    ${driveLink ? `<li><a href="${driveLink}">Descargar Anki</a> — Tus flashcards</li>` : ''}
  </ul>

  <h2>¿Primera vez en Notion?</h2>
  <p>Si es tu primera vez usando Notion, te llegará una invitación al email ${email}.
  Acepta la invitación y tendrás acceso de por vida.</p>

  <h2>¿Necesitas ayuda?</h2>
  <p>Escríbenos por Instagram @resummo.md o por WhatsApp.</p>

  <p>¡Éxito en tus estudios! 💪</p>
  <p>— El equipo de Resummo</p>
</div>
`;

return [{ json: { html, to: email, subject: `¡Bienvenido a Resummo, ${nombre}!` } }];
```

## Patron: Server-side Purchase event (Meta CAPI)

```javascript
const crypto = require('crypto');
const { email, paquete, monto, transaction_id } = $input.first().json;

// Hash email for Meta CAPI (SHA256)
const hashedEmail = crypto.createHash('sha256').update(email.toLowerCase().trim()).digest('hex');

const eventData = {
  data: [{
    event_name: 'Purchase',
    event_time: Math.floor(Date.now() / 1000),
    event_id: transaction_id, // Para deduplicacion con Pixel browser
    action_source: 'website',
    user_data: {
      em: [hashedEmail]
    },
    custom_data: {
      currency: 'PEN',
      value: monto,
      content_name: paquete,
      content_type: 'product'
    }
  }]
};

return [{ json: eventData }];
```

## Top 5 errores en Code nodes

| # | Error | Frecuencia | Fix |
|---|-------|-----------|-----|
| 1 | Missing return o return vacio | 38% | Siempre `return [{ json: {...} }]` |
| 2 | Usar `{{ }}` en Code node | 8% | Usar JS directo: `$input.first().json` |
| 3 | Return sin wrapper `[{ json: }]` | 5% | Envolver en array de objetos json |
| 4 | No validar null/undefined | 5% | Usar `?.` y `??` (optional chaining) |
| 5 | Nombre de nodo incorrecto | 3% | Verificar nombre exacto (case-sensitive) |

## Checklist pre-deploy para Code nodes
- [ ] Return es `[{ json: {...} }]`
- [ ] Null checks con `?.` y `??`
- [ ] NO usa `{{ }}`
- [ ] Modo correcto (All Items por default)
- [ ] Variables de otro nodo con nombre exacto case-sensitive
- [ ] try-catch para HTTP/APIs externas
- [ ] Testeado con pinned data
