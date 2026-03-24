---
name: payment-flow-test
description: Testear flujo completo de pago end-to-end en Resummo. Antes de produccion o despues de cambios en pasarela, webhooks o entrega
argument-hint: [paquete a testear — ej: all-access, ciencias-basicas]
auto-activate: false
---

# Skill: /payment-flow-test

Testear el flujo completo desde click en "Comprar" hasta que el usuario recibe acceso a Notion.

## Pasos

### 1. Verificar modo test
- Confirmar que la pasarela (EasyPay/Culqi) esta en modo sandbox/test
- Identificar tarjetas de prueba disponibles
- NUNCA testear con transacciones reales

### 2. Simular compra
- Seleccionar paquete indicado en la landing
- Click en boton "Comprar" → verificar redireccion a pasarela
- Completar pago con tarjeta de prueba
- Capturar: transaction_id, monto, estado de respuesta

### 3. Verificar webhook → n8n
- Verificar que el webhook de pago llego a n8n
- Verificar que n8n valido la firma/token
- Verificar que el flujo se ejecuto sin errores
- Si no llego: revisar URL del webhook, estado del workflow n8n

### 4. Verificar Google Sheets
- Verificar que se creo un nuevo registro con:
  - nombre, email, paquete, monto, transaction_id correctos
  - fecha_pago con timestamp correcto
  - estado_entrega actualizado

### 5. Verificar entrega Notion
- Verificar que n8n llamo la Notion API
- Verificar que la pagina correcta fue compartida
- Verificar que el email del comprador tiene acceso
- Si falla: revisar Notion integration token, page ID, permisos

### 6. Verificar entrega Drive (si aplica)
- Solo para paquetes con Anki: All Access, ENCAPS
- Verificar que el link de Drive fue compartido o enviado

### 7. Verificar confirmacion
- Verificar que se envio email/WhatsApp de confirmacion
- Verificar que el mensaje incluye instrucciones de acceso
- Verificar que los links en el mensaje funcionan

### 8. Test de idempotencia
- Correr el test una segunda vez con el MISMO transaction_id
- Verificar que NO se duplico el registro en Google Sheets
- Verificar que NO se envio una segunda invitacion a Notion

### 9. Generar reporte

| Paso | Estado | Detalle |
|------|--------|---------|
| Redireccion a pasarela | PASS/FAIL | [detalle] |
| Pago procesado | PASS/FAIL | [detalle] |
| Webhook recibido en n8n | PASS/FAIL | [detalle] |
| Registro en Google Sheets | PASS/FAIL | [detalle] |
| Entrega Notion | PASS/FAIL | [detalle] |
| Entrega Drive | PASS/FAIL/N/A | [detalle] |
| Confirmacion enviada | PASS/FAIL | [detalle] |
| Idempotencia | PASS/FAIL | [detalle] |

**Resultado:** X/8 PASS — [LISTO PARA PRODUCCION / REQUIERE FIXES]

## Gotchas
- EasyPay/Culqi tienen tarjetas de prueba especificas — buscar en su documentacion
- Notion API puede tener rate limits — si falla intermitentemente, puede ser throttling
- El modo test de la pasarela NO envia dinero real — verificar que el monto es correcto en el registro
- Google Sheets puede tener delay de 1-2s — esperar antes de verificar
- Si n8n no esta corriendo: el webhook se pierde — verificar que el workflow esta activo

## Reglas
- SIEMPRE en modo test/sandbox — NUNCA con dinero real durante testing
- Ejecutar para CADA paquete antes de ir a produccion
- Si cualquier paso falla: NO ir a produccion hasta resolver
- Documentar resultado en `docs/arquitectura/test-results-[fecha].md`
- Despues del fix: re-ejecutar el test completo (no solo el paso que fallo)
