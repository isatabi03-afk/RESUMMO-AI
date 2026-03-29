# N8n AI Agent Node — Patrones de Produccion

## Cuando usar AI Agent node vs alternativas

| Situacion | Usar |
|-----------|------|
| Chatbot con historial de conversacion multi-turno | AI Agent node + memoria |
| Clasificacion de intent + accion variable | AI Agent node (ReAct o Tools) |
| Respuesta simple con templates fijos | Code node + IF (mas rapido, sin LLM) |
| Extraccion de datos estructurados de texto libre | AI Agent (OpenAI Functions) o Code node con regex |
| Logica compleja pero determinista | Code node (no necesitas LLM) |
| Consulta RAG (buscar en base de conocimiento) | AI Agent + Vector Store Tool |

## Arquitecturas y cuando usarlas (Resummo)

### Conversational Agent — Agente de ventas ManyChat
- Mantiene historial de la conversacion
- Responde preguntas sobre paquetes, precios, ejemplos
- Session ID: `subscriber_id` de ManyChat
- Memoria: Postgres en produccion (tabla `n8n_chat_histories`)

### ReAct Agent — Agente con herramientas
- Razona paso a paso: piensa → actua → observa → responde
- Usar cuando el agente necesita tomar decisiones dinamicas
- Ejemplo: verificar si usuario es cliente → responder diferente

### Tools Agent — Agente especializado
- Acceso a herramientas especificas (Google Sheets, HTTP Request, Execute Workflow)
- Mas predecible que ReAct para casos de uso acotados

### Multi-agente — Supervisor + sub-agentes
```
AI Agent Supervisor
  → AI Agent Tool: Ventas (sabe de paquetes y precios)
  → AI Agent Tool: Soporte (sabe de accesos y entregas)
  → HTTP Request Tool: Google Sheets (lookup de clientes)
```
Usar cuando la complejidad requiere especializacion.

## Memoria — Configuracion por entorno

### Desarrollo / Testing
- **Simple Memory**: OK, pero se reinicia con el workflow
- No usar en produccion (pierde contexto entre sesiones)

### Produccion (OBLIGATORIO)
- **Postgres Chat Memory**:
  - Conectar a base de datos Postgres
  - Table Name: `n8n_chat_histories`
  - Session ID: `{{ $json.subscriber_id }}` (ID unico por usuario)
  - Context Window Length: 10 mensajes (balance memoria/costo)
- **Redis Chat Memory** (alternativa de alta performance):
  - Para conversaciones activas de alto volumen
  - TTL: 24h (conversaciones mas largas no son utiles)

### Vector Store como Tool (RAG)
```
Desde n8n v1.74: conectar vector store directamente como tool del agente
Casos de uso Resummo:
  - FAQ: el agente busca respuestas en base de conocimiento de Resummo
  - Contenido de paquetes: el agente sabe que incluye cada paquete
Servicios: Supabase pgvector, Pinecone, Qdrant
```

## Herramientas disponibles para el agente

| Tool | Cuando usar |
|------|-------------|
| HTTP Request Tool | Llamadas a APIs externas (Culqi, Notion, Drive) |
| Code Tool | Logica JS personalizada dentro del agente |
| Execute Workflow Tool | Disparar sub-workflows (notion-delivery, confirmation-sender) |
| Google Sheets Tool | Lookup de clientes sin Code node intermedio |
| Custom n8n Tool | Envolver cualquier nodo n8n como herramienta del agente |

## Timeout y fallback (CRITICO para ManyChat)

ManyChat External Request corta a **10 segundos**. El agente DEBE responder antes.

```
Patron obligatorio para ManyChat:
1. Recibir webhook
2. Respond to Webhook INMEDIATAMENTE con mensaje de "procesando..." (< 500ms)
   NOTA: ManyChat v2 soporta async — verificar si esta disponible
   Si no: procesar todo en < 8s para tener margen de seguridad
3. Procesar con AI Agent
4. Si AI Agent falla: branch de error → respuesta generica
5. Log del error en Google Sheets

Configuracion del AI Agent node:
- maxIterations: 5 (no 10, para no exceder timeout)
- Timeout implicito: disenar para completar en < 7s
- onError: continueErrorOutput → branch fallback
```

## Anti-alucinacion (obligatorio para Resummo)

El agente IA de Resummo NO debe inventar informacion. Configurar el system prompt del AI Agent con estas restricciones:

```
REGLAS CRITICAS:
1. Solo mencionar los 4 paquetes existentes: Ciencias Basicas, EsSalud/ENAM, All Access, ENCAPS (Anki)
2. Solo mencionar precios exactos configurados — NUNCA inventar precios
3. NUNCA prometer beneficios no documentados
4. Si no sabes la respuesta → "Dejame conectarte con Andre o Nair para ayudarte mejor"
5. NUNCA dar consejo medico — "Somos material de estudio, no reemplazamos consulta medica"
6. Si el usuario pregunta sobre otros productos → "Solo tenemos estos 4 paquetes"
```

## Testing de agentes

### Simular conversacion en n8n
```json
// Pinned data para testear el webhook de ManyChat:
{
  "body": {
    "subscriber_id": "TEST_001",
    "first_name": "TestUser",
    "last_input_text": "cuanto cuesta el all access"
  }
}
```

### Casos de test obligatorios
1. **Pregunta de precio** → debe responder con precio correcto, sin inventar
2. **Pedir ejemplos** → debe enviar capturas del paquete o redirigir
3. **Intent de compra** → debe dar CTA claro con link
4. **"Ya compre"** → debe buscar en Sheets y confirmar acceso
5. **Pregunta fuera de scope** → debe derivar a Andre/Nair, no inventar
6. **Timeout simulado** → si el agente tarda > 7s, el fallback debe activarse

## Monitoreo de agentes en produccion

- Loggear en Google Sheets: `session_id`, `intent_detectado`, `duracion_ms`, `derivado_a_humano`, `timestamp`
- Alertar si tasa de derivacion > 30% (el bot no esta resolviendo)
- Alertar si duracion promedio > 6s (riesgo de timeout ManyChat)
- Revision semanal de conversaciones con derivacion a humano → mejorar prompts
