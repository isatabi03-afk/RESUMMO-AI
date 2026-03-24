# Automatizacion de Pagos

## Flujo completo pago → entrega

```
1. Usuario selecciona paquete en landing (resummo.com)
   ↓
2. Redireccion a checkout de EasyPay/Culqi
   ↓
3. Usuario paga (Yape, Plin, tarjeta, transferencia)
   ↓
4. Pasarela envia webhook de confirmacion a n8n
   ↓
5. n8n valida firma/token del webhook
   ↓
6. PARALELO:
   ├─► Registra en Google Sheets (nombre, email, paquete, monto, fecha, transaction_id)
   ├─► Llama Notion API para compartir pagina del paquete con email del comprador
   ├─► Comparte link de Drive si el paquete incluye Anki
   └─► Envia email/WhatsApp de confirmacion con instrucciones de acceso
   ↓
7. Usuario recibe acceso en < 1 minuto
```

## Pasarela de pagos

### Opciones evaluadas
| Pasarela | Comision | Metodos | Notas |
|----------|----------|---------|-------|
| EasyPay | ~5% | Yape, Plin, tarjetas, transferencia | Mas metodos locales |
| Culqi | ~3.99% + IGV | Tarjetas, Yape | Mas establecido |
| Mercado Pago | Variable | Tarjetas, transferencia | Menos popular en Peru |

### Pendiente: Andre/Nair deben crear la cuenta de pasarela

## Webhook de pago — validacion obligatoria
- Verificar firma/token de la pasarela antes de procesar
- Rechazar webhooks sin firma valida
- Log de webhooks recibidos (sin datos sensibles)

## Idempotencia
- Guardar `transaction_id` en Google Sheets
- Antes de procesar: verificar que `transaction_id` no existe ya
- Si existe: no duplicar entrega, loggear como duplicado

## Mapeo paquete → entrega

| Paquete | Notion Page ID | Drive Link | Incluye tutorias |
|---------|---------------|------------|-----------------|
| Ciencias Basicas | [CONFIGURAR] | N/A | No |
| EsSalud/ENAM | [CONFIGURAR] | N/A | Si (pasivas) |
| All Access | [CONFIGURAR] | [CONFIGURAR] | Si (pasivas) |
| ENCAPS (Anki) | N/A | [CONFIGURAR] | No |

## Google Sheets — estructura de registro

| Columna | Tipo | Ejemplo |
|---------|------|---------|
| fecha_pago | datetime | 2026-03-25 14:30 |
| nombre | string | Juan Perez |
| email | string | juan@gmail.com |
| paquete | string | All Access |
| monto | number | 90 |
| transaction_id | string | TXN_abc123 |
| notion_page_shared | boolean | TRUE |
| drive_shared | boolean | TRUE |
| email_enviado | boolean | TRUE |
| estado_entrega | string | completado / error / pendiente |

## Fallbacks
- Si Notion API falla → notificar a Santiago para entrega manual + marcar estado_entrega = "error"
- Si email falla → reintentar 1 vez despues de 5 min via n8n
- Si Google Sheets falla → loggear en n8n y reintentar
- NUNCA dejar un pago sin registrar — si todo falla, notificacion urgente a Santiago

## Montos
- Todos en soles peruanos (PEN)
- No manejar dolares ni conversion de moneda
