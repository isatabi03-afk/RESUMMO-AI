# Criterios de Analisis y Scoring — DMs Resummo

## Criterios por conversacion

| Criterio | Que evaluar | Clasificacion |
|----------|------------|---------------|
| Flujo | Siguio orden: saludo → clasificar → recomendar → CTA | OK / DESVIADO / ROTO |
| Alucinacion | Invento paquetes, precios, beneficios | OK / DETECTADO |
| Tono | Cercano, estudiantil, emojis moderados | OK / INCONSISTENTE |
| Derivacion | Escalo cuando debia, no escalo de mas | OK / TARDIA / PREMATURA |
| Ejemplos | Envio capturas cuando las pidieron | OK / NO_ENVIO / NO_APLICA |
| CTA | Presento link de compra en momento oportuno | OK / TARDIO / AUSENTE |
| Objeciones | Manejo precio, desconfianza, Notion/Anki | OK / NO_MANEJO / NO_APLICA |
| Post-compra | Verifico acceso cuando dijeron "ya compre" | OK / NO_VERIFICO / NO_APLICA |

## Clasificacion de conversacion

| Nivel | Criterio |
|-------|---------|
| OK | Todos los criterios aplicables en OK |
| CON_ISSUES | 1-2 criterios con problemas menores (tono, CTA tardio) |
| CRITICA | Alucinacion detectada, derivacion rota, o flow completamente desviado |

## Scoring de calidad del agente (0-10)

| Dimension | Peso | Que mide |
|-----------|------|----------|
| Cobertura de flujo | 25% | % de conversaciones que siguen el flujo esperado |
| Anti-alucinacion | 25% | % de conversaciones sin datos inventados |
| Manejo de objeciones | 20% | % de objeciones manejadas correctamente |
| Conversion a CTA | 15% | % de conversaciones que llegan al link de compra |
| Derivacion correcta | 15% | % de derivaciones oportunas y acertadas |

| Score | Interpretacion |
|-------|---------------|
| 0-5 | Insuficiente — requiere cambios urgentes |
| 6-7 | Aceptable — ajustes menores necesarios |
| 8-9 | Bueno — optimizaciones opcionales |
| 10 | Excelente — monitoreo de mantenimiento |

Score < 7.0 → identificar ajustes antes de seguir escalando ads.
