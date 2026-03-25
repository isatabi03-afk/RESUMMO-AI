# Estructura y Estandares AMBOSS — Referencia para Resummo

## Que es AMBOSS

Plataforma de educacion medica fundada en Berlin (2012) por Madjid Salimi (medico + ingeniero), Benedikt Hochkirchen, Sievert Weiss y Nawid Salimi. $292M en funding total. 1,400+ articulos, 13,000+ preguntas, 1M+ usuarios en 180+ paises. Co-CEOs: Madjid Salimi y Benedikt Hochkirchen.

## Template estandar de articulo AMBOSS

Cada tema sigue esta estructura canonica (en orden):

| # | Seccion | Que contiene | Obligatoria |
|---|---------|-------------|-------------|
| 1 | **Definicion** | Que es la enfermedad/condicion en 2-3 oraciones | SI |
| 2 | **Epidemiologia** | Incidencia, prevalencia, distribucion por edad/sexo/geografia | SI |
| 3 | **Etiologia** | Causas, factores de riesgo, clasificacion etiologica | SI |
| 4 | **Fisiopatologia** | Mecanismo de la enfermedad, cascada patologica | SI |
| 5 | **Caracteristicas clinicas** | Signos, sintomas, presentacion tipica y atipica | SI |
| 6 | **Diagnostico** | Laboratorio, imagenes, criterios diagnosticos, gold standard | SI |
| 7 | **Tratamiento** | Primera linea, segunda linea, criterios de derivacion | SI |
| 8 | **Complicaciones** | Que pasa si no se trata, complicaciones del tratamiento | Segun tema |
| 9 | **Pronostico** | Expectativa, factores que modifican | Segun tema |

## Proceso editorial AMBOSS

| Aspecto | Estandar AMBOSS |
|---------|----------------|
| Equipo | 80-150+ medicos editores (US-trained, recien egresados de residencia) |
| Revision | Minimo 3 profesionales medicos + 1 copyeditor por entrada |
| Principio | "8 ojos" — cada articulo revisado por al menos 4 personas |
| Fuentes | Solo evidencia de alto grado (ver jerarquia abajo) |
| Politica | NO opiniones de expertos, NO anecdotas clinicas |
| Actualizacion | Continua — equipo dedicado a revisar guidelines nuevas |

## Jerarquia de evidencia (usada por AMBOSS)

1. **Revisiones sistematicas y meta-analisis** (Cochrane, PRISMA)
2. **Ensayos clinicos aleatorizados** (RCTs)
3. **Guias de practica clinica** (AHA, ACC, IDSA, NICE, etc.)
4. **Estudios observacionales** (cohorte, caso-control)
5. **Series de casos y reportes**
6. ~~Opiniones de expertos~~ (AMBOSS no usa esto)

**Para Resummo:** Agregar GPC MINSA Peru y guias de sociedades medicas peruanas al nivel 3.

## Principios de calidad visual AMBOSS

| Feature AMBOSS | Equivalente Notion para Resummo |
|---------------|-------------------------------|
| Color-coded highlights (amarillo) | Callout tipo "tip" — conceptos clave |
| Color-coded highlights (rojo) | Callout tipo "warning" — hallazgos clasicos/alarma |
| Color-coded highlights (azul) | Callout tipo "info" — gold standard diagnostico |
| Pop-up tooltips (hover = definicion) | Links internos entre paginas Notion |
| Cross-linking entre temas | Links bidireccionales en Notion |
| Learning cards integradas | Referencia cruzada con deck Anki (VILLAPEPAS) |
| Tablas comparativas | Tablas nativas de Notion |
| Imagenes con overlays | Imagenes con anotaciones en Notion |

## Lo replicable a costo $0-bajo

1. **Template estandarizado** — aplicar las 9 secciones a cada tema en Notion
2. **Cross-linking** — vincular temas relacionados dentro de Notion
3. **Callouts con color-coding** — usar callouts de Notion para highlights
4. **Vinculacion Anki** — referenciar cards de VILLAPEPAS en cada tema
5. **Fuentes citadas** — agregar referencias con URLs accesibles
6. **Seccion high-yield** — marcar lo que cae en ENAM/EsSalud

## Lo NO replicable (no intentar)

1. **Qbank masivo** (13,000+ preguntas) — VillamedicGroup ya domina esto en Peru
2. **IA clinica** (LiSA) — requiere millones de inversion
3. **Cobertura global multi-idioma** — enfocarse en espanol Peru/Latam
4. **Apps nativas iOS/Android** — Notion + Anki son suficientes por ahora
5. **Contenido para clinicos en ejercicio** — enfocarse en estudiantes y preparacion ENAM
6. **Drug database completa** — fuera del scope de Resummo
7. **Equipo editorial de 80+ medicos** — Nair es el editor; priorizar pocos temas excelentes

## Features avanzadas de AMBOSS (referencia futura)

- **Anki Add-on:** Pop-ups con explicaciones vinculadas a la Library cuando se estudia con Anki
- **Score Predictor:** Estima score USMLE basado en rendimiento en Qbank
- **Study Plans:** Planificador de estudio estructurado por semanas
- **High-Yield Mode:** Filtra contenido por lo mas preguntado en examenes
- **Chrome Extension:** Explicaciones de terminos medicos en cualquier web
- **LiSA 1.0:** Agente RAG clinico — #1 en estudio NOHARM de Harvard-Stanford (11.9 errores severos/100 casos)
