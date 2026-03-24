# Entrega Automatica de Acceso Notion

## Concepto
Cada paquete de Resummo es una pagina Notion con subpaginas de contenido medico.
La entrega = compartir esa pagina con el email del comprador via Notion API.

## Notion API — operaciones de entrega

### Compartir pagina con usuario
```
POST https://api.notion.com/v1/pages
Headers:
  Authorization: Bearer {NOTION_INTEGRATION_TOKEN}
  Notion-Version: 2022-06-28
  Content-Type: application/json
```

**Nota:** La API de Notion no tiene un endpoint directo de "share page with email".
Opciones de implementacion:
1. **Notion invite via email** — agregar miembro al workspace con acceso a pagina especifica
2. **Crear pagina duplicada** — duplicar template y agregar como guest
3. **Shared link** — generar link publico con acceso de lectura

**Decision pendiente:** Evaluar con agente research cual opcion es viable. Andre/Nair actualmente invitan manualmente via email — replicar ese flujo via API.

## Mapeo paquete → pagina Notion

| Paquete | Notion Page ID | Tipo de acceso |
|---------|---------------|----------------|
| Ciencias Basicas | [PENDIENTE] | Lectura |
| EsSalud/ENAM | [PENDIENTE] | Lectura |
| All Access | [PENDIENTE] | Lectura (todo el workspace) |
| ENCAPS (Anki) | N/A — entrega via Google Drive | N/A |

## Cuenta Notion
- Plan: Notion Partners ($20/mes)
- Permite invitar miembros como guests
- Limite de guests: verificar segun plan actual

## Google Drive — entrega Anki
- Paquetes que incluyen Anki: All Access, ENCAPS
- Entrega: compartir carpeta de Drive con email del comprador
- API: Google Drive API v3 — `POST /files/{fileId}/permissions`
- Alternativa simple: enviar link directo de Drive en el email de bienvenida

## Validacion post-entrega
- Verificar que el email es valido antes de intentar compartir
- Si el email no es cuenta Notion → el usuario recibe invitacion para crear cuenta
- Marcar `notion_page_shared = TRUE` en Google Sheets solo despues de confirmacion de API
- Si falla: marcar `estado_entrega = "error"` y notificar

## Edge cases
- **Email invalido** → no intentar compartir, marcar error, notificar
- **Pagina ya compartida** → verificar antes de compartir, no duplicar invitacion
- **Compra duplicada** → idempotencia via transaction_id (ver payment-automation.md)
- **Usuario quiere cambiar email** → proceso manual via Andre/Nair
- **Acceso de por vida** → no hay logica de expiracion, el acceso permanece indefinidamente
