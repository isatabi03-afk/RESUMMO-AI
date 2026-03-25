---
name: amboss-benchmark
description: Auditar y mejorar contenido Resummo usando AMBOSS como gold standard. Evaluar calidad, identificar gaps, crear contenido nuevo con template estandarizado
argument-hint: [modo — audit, create, gaps, evaluate] [tema o paquete]
context: fork
auto-activate: false
---

# Skill: /amboss-benchmark

Evaluar y mejorar el contenido medico de Resummo usando la metodologia y estructura de AMBOSS como benchmark, adaptado a la realidad Peru/Latam y al formato Notion+Anki.

## Modos de operacion

Invocar con: `/amboss-benchmark [modo] [tema-o-paquete]`

| Modo | Input | Output |
|------|-------|--------|
| `audit` | Nombre de paquete | Reporte de auditoria con score /10 |
| `create` | Nombre de tema medico | Borrador Notion-ready con template estandar |
| `gaps` | Paquete o temario (ENAM/SERUMS) | Analisis de cobertura con gaps priorizados |
| `evaluate` | Tema medico especifico | Evaluacion granular + mejoras priorizadas |

---

## Pasos por modo

### Modo 1: `audit [paquete]`

Auditar contenido existente de un paquete contra estandar AMBOSS.

1. **Cargar referencias**
   - Leer `references/amboss-structure.md` — template gold standard
   - Leer `references/content-template.md` — template adaptado Resummo

2. **Obtener contenido actual**
   - Usar Notion MCP si disponible (`notion-search` por nombre del paquete)
   - Si no hay MCP: pedir screenshots o exports al usuario

3. **Evaluar cada tema** contra 8 dimensiones:
   - Estructura (template estandar) — 20%
   - Profundidad clinica — 20%
   - Fuentes citadas — 15%
   - Cross-linking — 10%
   - Callouts/destacados — 10%
   - Vinculacion Anki — 5%
   - Actualizacion — 10%
   - Relevancia ENAM/SERUMS — 10%

4. **Generar reporte** usando `templates/audit-report.md`
5. **Producir Top 5 mejoras** priorizadas por impacto

### Modo 2: `create [tema]`

Crear contenido nuevo siguiendo template estandar.

1. **Cargar referencias**
   - Leer `references/content-template.md` — template a seguir
   - Leer `references/peru-latam-context.md` — fuentes disponibles

2. **Investigar el tema**
   - WebSearch: buscar en PubMed, GPC MINSA, Cochrane
   - Priorizar fuentes peruanas/latam sobre globales
   - Buscar datos epidemiologicos Peru si existen

3. **Generar borrador** siguiendo template completo:
   - Definicion > Epidemiologia (Peru/Latam) > Etiologia > Fisiopatologia
   - Clinica > Diagnostico > Tratamiento (GPC MINSA) > Complicaciones > Pronostico
   - Seccion "Para ENAM/EsSalud" con 3-5 puntos high-yield
   - Seccion "Temas relacionados" con cross-links
   - Seccion "Flashcards sugeridas" con 3-5 cards

4. **Marcar todo** lo que requiere validacion medica con `[NAIR: validar]`
5. **Listar fuentes** con URLs accesibles

### Modo 3: `gaps [paquete-o-temario]`

Identificar gaps de cobertura vs temarios oficiales.

1. **Cargar referencias**
   - Leer `references/peru-latam-context.md` — temarios ENAM/SERUMS

2. **Obtener lista de temas actuales**
   - Notion MCP: buscar paginas del paquete
   - Si no hay MCP: pedir lista al usuario

3. **Obtener temario oficial**
   - WebSearch: temario ENAM vigente, SERUMS, curricula basica
   - Cruzar con temas actuales de Resummo

4. **Clasificar gaps:**
   - **CRITICO** — alta frecuencia en examen, NO cubierto
   - **IMPORTANTE** — frecuencia media, NO cubierto
   - **COMPLEMENTARIO** — frecuencia baja o profundizacion

5. **Generar reporte** usando `templates/gap-analysis.md`
6. **Recomendar orden** de creacion por impacto/esfuerzo

### Modo 4: `evaluate [tema]`

Evaluar calidad de un tema especifico en profundidad.

1. **Cargar referencias** (amboss-structure + content-template)

2. **Obtener contenido del tema**
   - Notion MCP o screenshots del usuario

3. **Comparar seccion por seccion:**
   - Para cada seccion del template: EXISTE_COMPLETA / EXISTE_INCOMPLETA / FALTA / NO_APLICA

4. **Buscar fuentes** accesibles para las secciones faltantes/incompletas

5. **Producir evaluacion:**
   - Score /10 por dimension (tabla detallada)
   - Comparativa: "AMBOSS cubriria este tema asi: [descripcion]"
   - Lista de mejoras priorizadas con fuentes sugeridas

6. **Output accionable** para Nair: que mejorar, en que orden, con que fuentes

---

## Gotchas

- Claude NO puede verificar precision medica — siempre marcar `[NAIR: validar]` en contenido generado
- AMBOSS tiene 80-150 editores medicos; Resummo tiene 1 (Nair) — ajustar expectativas de profundidad y volumen
- Los temarios ENAM cambian anualmente — verificar version vigente antes de analisis de gaps
- No todas las secciones del template aplican a cada tema (ej: epidemiologia Peru puede no existir para condiciones raras)
- Notion MCP puede no estar conectado — tener fallback de screenshots/exports manuales
- Las GPC MINSA no cubren todas las patologias — usar guias internacionales como complemento
- Datos epidemiologicos Peru son escasos para muchas condiciones — marcar cuando se usan datos regionales/globales

## Reglas

1. NUNCA presentar contenido generado como "validado medicamente" — siempre requiere revision de Nair
2. Fuentes permitidas: Cochrane, PubMed, GPC MINSA, Harrison, Robbins, Guyton, OPS/OMS, sociedades medicas peruanas
3. Epidemiologia: priorizar datos Peru/Latam sobre datos globales cuando existan
4. Template adaptado a Resummo, no copia literal de AMBOSS — Resummo tiene su identidad
5. Scoring honesto — si el contenido es 4/10, decir 4/10
6. Gaps priorizados por frecuencia en examen, no por "completitud academica"
7. Output siempre accionable — no diagnosticar sin proponer solucion concreta
8. Flashcards Anki derivadas del contenido del tema, no inventadas independientemente
9. Cross-linking: sugerir vinculos entre temas relacionados dentro de Resummo
10. NO replicar funcionalidad de `/content-review` — este skill evalua calidad y estructura medica, no accesibilidad ni empaquetado
11. Maximo 1 tema por ejecucion en modo `create` y `evaluate`
12. Siempre incluir URLs accesibles en fuentes (no paywalled cuando sea posible)
