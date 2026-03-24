---
name: iterate-from-dms
description: Analizar DMs reales de Instagram y WhatsApp para diagnosticar bugs y producir ajustes al agente IA de Resummo
argument-hint: [fuente — screenshots, export-manychat, manual]
context: fork
auto-activate: false
---

# Skill: /iterate-from-dms

Leer DMs reales del agente IA, cruzarlas con feedback de Andre/Nair, diagnosticar discrepancias, y producir ajustes quirurgicos al prompt o flows de ManyChat.

## Pasos

### 1. Cargar contexto del agente
- `automations/README.md` — inventario de workflows y flows
- `.claude/rules/manychat-patterns.md` — flujo esperado, personalidad, derivacion
- Prompt actual del agente (si existe en `automations/manychat/`)
- Feedback previo de Andre/Nair (si existe en `docs/discovery/`)

Identificar: flujo esperado, reglas del agente, keywords, triggers de derivacion.

### 2. Obtener DMs (prioridad de fuentes)

| Prioridad | Fuente | Como obtener |
|-----------|--------|--------------|
| A (mayor) | Feedback directo de Andre/Nair | Screenshots + comentarios |
| B | Export de ManyChat | Dashboard → Conversations → Export |
| C | Screenshots manuales | Proporcionados por el usuario |

Si no hay fuente disponible: preguntar al usuario.

### 3. Analizar patrones (clasificar cada conversacion)

Criterios de analisis:
- **Flujo:** Siguio el orden correcto? (saludo → clasificar → recomendar → CTA)
- **Alucinacion:** Invento paquetes, precios, o beneficios?
- **Tono:** Consistente con personalidad definida?
- **Derivacion:** Escalo cuando debia? Escalo de mas?
- **Ejemplos:** Envio capturas cuando las pidieron?
- **CTA:** Llego al link de compra en momento oportuno?
- **Objeciones:** Manejo precio, desconfianza, Notion/Anki correctamente?
- **Post-compra:** Verifico acceso cuando dijeron "ya compre"?

Clasificar: **OK** / **CON_ISSUES** / **CRITICA**

### 4. Cruce con feedback de Andre/Nair

| Tipo | Significado |
|------|------------|
| Confirmado | Reportado por Andre/Nair Y encontrado en DMs |
| No reportado | Encontrado en DMs, Andre/Nair no lo mencionaron |
| Fantasma | Reportado pero NO se reproduce en DMs |

### 5. Diagnostico por causa raiz

| Categoria | Significado | Accion |
|-----------|------------|--------|
| BOT_PROMPT | Instruccion del agente existe pero la ignora | Reforzar en prompt |
| FLOW_MISSING | Flow de ManyChat no existe para este caso | Crear flow nuevo |
| N8N_ERROR | Backend n8n falla o retorna incorrecto | Fix en workflow n8n |
| KEYWORD_GAP | Usuario escribe algo que no matchea ningun keyword | Agregar keyword en ManyChat |
| EDGE_CASE | Situacion no contemplada | Documentar, decidir si agregar o derivar |

### 6. Producir ajustes quirurgicos

Para cada problema:
```
**Problema:** [descripcion breve]
**Evidencia:** [fragmento real del DM]
**Categoria:** [BOT_PROMPT / FLOW_MISSING / N8N_ERROR / KEYWORD_GAP / EDGE_CASE]
**Archivo afectado:** [path exacto]
**Cambio:**
  OLD: [texto/config actual]
  NEW: [cambio minimo propuesto]
**Justificacion:** [por que este cambio resuelve el problema]
**Impacto:** [% conversaciones afectadas estimado]
```

Priorizar por impacto: cambios que afectan >20% de conversaciones van primero.

### 7. Invocar agente reviewer
Antes de presentar cambios al usuario, despachar agente `reviewer` para validar coherencia.

### 8. Deploy solo con aprobacion
- Presentar cambios al usuario
- Esperar aprobacion explicita
- Si son cambios en ManyChat: dar instrucciones paso a paso para ejecutar en la UI
- Si son cambios en n8n: usar script de deploy o REST API

### 9. Reporte final

```
## Reporte de Iteracion — DMs Resummo [fecha]

### Metricas
- Conversaciones analizadas: X
- OK: X | Con issues: X | Criticas: X

### Hallazgos (por prioridad)
[tabla con hallazgos]

### Ajustes propuestos
[tabla con cambios]

### Recomendacion de monitoreo
Verificar en 24-48h que los ajustes mejoraron las metricas.
```

## Gotchas
- ManyChat no tiene API de export de conversaciones tan directa — depender de screenshots y exports manuales
- Instagram DMs pueden incluir audios, stickers, imagenes que el bot no procesa — documentar como limitacion, no como bug
- Flujos de ManyChat se editan en su UI, no via API — los ajustes a flows son instrucciones escritas, no codigo
- No proponer cambios sin evidencia real de un DM
- Feedback de Andre/Nair siempre tiene mayor prioridad que el analisis automatico

## Reglas
- NUNCA reestructurar flujos completos — solo ajustes quirurgicos
- SIEMPRE mostrar evidencia (fragmento real del DM)
- Priorizar por impacto (>20% conversaciones van primero)
- Maximo 30 conversaciones por ejecucion (evitar saturacion)
- Sugerir monitoreo 24-48h despues de cambios
- Deploy solo con aprobacion explicita
