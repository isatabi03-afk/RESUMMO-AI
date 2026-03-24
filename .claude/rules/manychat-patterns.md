# Patrones del Agente IA — ManyChat + n8n

## Arquitectura

```
Instagram DM / WhatsApp
       ↓
   ManyChat (frontend conversacional)
       ↓ (External Request / Webhook)
   n8n (backend de logica)
       ↓
   Respuesta al usuario
```

- **ManyChat:** Maneja el flujo conversacional, keywords, quick replies, botones
- **n8n:** Logica compleja, consultas a Google Sheets, generacion de respuestas con LLM
- **Separacion:** ManyChat para flujos simples (FAQ, botones), n8n para logica inteligente

## Personalidad del agente
- Nombre: [PENDIENTE — definir con Andre/Nair]
- Tono: cercano, estudiantil, usa "tu" (no "usted")
- Emojis: moderados (1-2 por mensaje, no saturar)
- Largo de mensaje: maximo 3-4 lineas por burbuja
- Idioma: espanol (Peru)
- NO usa jerga medica compleja — habla como un compa de clase

## Flujo conversacional principal

```
1. Saludo + identificar interes
   "Hola! Gracias por escribir a Resummo.
   ¿Que te gustaria saber?"
   [Boton: Paquetes] [Boton: Ver ejemplos] [Boton: Precios]
   ↓
2. Clasificar interes por paquete
   Segun respuesta → mostrar info del paquete relevante
   ↓
3. Enviar ejemplos (si lo pide — 30% convierte despues de ver ejemplos)
   Capturas pre-cargadas por paquete
   ↓
4. Manejar objeciones
   Precio → comparar con academias
   Desconfianza → testimonios + info de Andre/Nair
   Notion/Anki → explicacion breve
   ↓
5. CTA de compra
   "Listo! Puedes comprar aqui: [link a landing/checkout]"
   ↓
6. Soporte post-compra
   "Ya compre" → verificar en Google Sheets → confirmar acceso
   "No me llego el acceso" → verificar email → escalar si necesario
```

## Keywords de activacion (ManyChat)

| Keyword | Trigger | Accion |
|---------|---------|--------|
| hola, info, quiero | Saludo inicial | Flow principal |
| precio, cuanto, cuesta | Consulta de precio | Mostrar tabla de paquetes |
| ejemplo, muestra, ver | Pedir ejemplos | Enviar capturas del paquete |
| notion, que es | Explicar Notion | Mini-explicacion + GIF |
| anki, flashcards | Explicar Anki | Mini-explicacion |
| comprar, pagar, quiero | Intencion de compra | CTA directo a checkout |
| ayuda, problema | Soporte | Derivar a humano si complejo |
| ya compre, acceso | Post-compra | Verificar estado en Google Sheets |

## Reglas de derivacion a humano

| Trigger | Derivar a | Canal |
|---------|-----------|-------|
| Pregunta sobre contenido medico especifico | Nair | DM directo |
| Problema de pago no resuelto | Santiago/FIXU | WhatsApp |
| Alianza, colaboracion, comercial | Andre | DM directo |
| Bot no entiende despues de 2 intentos | Andre/Nair | DM directo |
| Queja o insatisfaccion | Andre | DM directo |
| Solicitud de reembolso | Andre | DM directo |

## Anti-alucinacion obligatorio
- Solo hablar de los 4 paquetes existentes — NUNCA inventar paquetes
- Solo mencionar precios definidos — NUNCA inventar precios
- Solo prometer acceso de por vida — NUNCA inventar beneficios extra
- Si no sabe la respuesta → "Dejame conectarte con Andre/Nair para que te ayuden"
- NUNCA dar consejo medico — "Somos material de estudio, no reemplazamos consulta medica"

## Horarios
- Bot: 24/7 automatico
- Humanos (Andre/Nair): horario Peru ~9am-10pm (flexible, son estudiantes)
- Si es fuera de horario humano: "Te responderemos apenas podamos, gracias por tu paciencia"

## Integracion ManyChat ↔ n8n
- ManyChat envia External Request a webhook de n8n
- n8n procesa: consulta Google Sheets, genera respuesta, retorna a ManyChat
- Timeout de External Request: configurar 10s max
- Si n8n no responde: ManyChat muestra mensaje generico + opcion de hablar con humano

## Metricas del agente a trackear
- Mensajes recibidos/dia
- % resueltos sin humano
- % que llegan a CTA de compra
- % que piden ejemplos
- Tiempo promedio de conversacion
- Tasa de derivacion a humano
