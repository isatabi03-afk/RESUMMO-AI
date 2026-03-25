# Template de Contenido Resummo — Inspirado en AMBOSS, Adaptado a Peru

## Instrucciones de uso

Este template es el formato estandar para CADA tema medico en Resummo. Adaptado de la estructura AMBOSS pero con:
- Epidemiologia Peru/Latam primero
- Tratamiento basado en GPC MINSA cuando disponible
- Seccion high-yield para ENAM/EsSalud
- Flashcards Anki derivadas del contenido
- Formato Notion-ready con callouts

## Template (copiar para cada tema nuevo)

```markdown
# [Nombre del Tema]

> **Categoria:** [Ciencias Basicas / Medicina Interna / Cirugia / Pediatria / GO / Salud Publica]
> **Paquete(s):** [Ciencias Basicas / EsSalud-ENAM / All Access]
> **Relevancia ENAM:** [Alta / Media / Baja]
> **Ultima actualizacion:** [YYYY-MM-DD]
> **Validado por:** [Nair / Pendiente validacion]
> **Estado:** [Borrador / Revisado / Publicado]

---

## Definicion

[Definicion concisa en 2-3 oraciones. Que es, como se clasifica.]

---

## Epidemiologia

> Datos Peru/Latam primero. Si no hay datos locales, usar datos globales y marcarlo.

- **Peru:** [dato con fuente]
- **Latam:** [dato con fuente]
- **Global:** [dato con fuente]
- **Grupos de riesgo:** [edad, sexo, factores]

---

## Etiologia

[Causas principales organizadas por frecuencia o clasificacion]

| Tipo | Causas |
|------|--------|
| Mas frecuente | [causa] |
| Otras | [causas] |
| Factores de riesgo | [factores] |

---

## Fisiopatologia

[Mecanismo de la enfermedad en 3-5 oraciones]

> CONCEPTO CLAVE: [el mecanismo central que explica toda la clinica]

[Diagrama de flujo si aplica: causa → mecanismo → consecuencia clinica]

---

## Caracteristicas clinicas

**Presentacion tipica:**
- [signo/sintoma 1]
- [signo/sintoma 2]
- [signo/sintoma 3]

> HALLAZGO CLASICO: [el dato patognomonico o mas preguntado en examen]

**Signos de alarma:**
- [signo que indica gravedad o complicacion]

---

## Diagnostico

| Estudio | Hallazgo esperado | Cuando pedirlo |
|---------|------------------|----------------|
| [lab/imagen 1] | [resultado] | [indicacion] |
| [lab/imagen 2] | [resultado] | [indicacion] |

> GOLD STANDARD: [metodo diagnostico definitivo]

**Criterios diagnosticos:** [si existen criterios formales, listarlos]

**Diagnostico diferencial:**
- [dx diferencial 1] — [como diferenciarlo]
- [dx diferencial 2] — [como diferenciarlo]

---

## Tratamiento

> Basado en GPC MINSA Peru cuando disponible. Si no hay GPC MINSA, usar guia internacional y marcarlo.

**Primera linea:** [tratamiento + dosis si relevante]
**Segunda linea:** [alternativa]
**En caso de emergencia:** [conducta inmediata si aplica]

| Escenario | Tratamiento | Fuente |
|-----------|------------|--------|
| Leve | [tx] | [GPC MINSA / guia] |
| Moderado | [tx] | [GPC MINSA / guia] |
| Severo | [tx] | [GPC MINSA / guia] |

**Criterios de derivacion/referencia:**
- [cuando referir a especialista o nivel superior]

---

## Complicaciones

- [complicacion 1] — [frecuencia, manejo]
- [complicacion 2] — [frecuencia, manejo]

---

## Pronostico

[Expectativa general, factores que modifican pronostico]

---

## Para ENAM / EsSalud (High-Yield)

> Los 3-5 puntos que MAS caen en examen sobre este tema:

1. [punto high-yield 1]
2. [punto high-yield 2]
3. [punto high-yield 3]
4. [punto high-yield 4 — si aplica]
5. [punto high-yield 5 — si aplica]

---

## Temas relacionados

- [[Tema relacionado 1]] — [relacion]
- [[Tema relacionado 2]] — [relacion]
- [[Tema relacionado 3]] — [relacion]

---

## Flashcards sugeridas (Anki)

| Pregunta | Respuesta |
|----------|-----------|
| [pregunta 1 — estilo examen] | [respuesta concisa] |
| [pregunta 2 — concepto clave] | [respuesta concisa] |
| [pregunta 3 — hallazgo clasico] | [respuesta concisa] |
| [pregunta 4 — tratamiento] | [respuesta concisa] |
| [pregunta 5 — diagnostico] | [respuesta concisa] |

---

## Fuentes

1. [Fuente 1 — con URL si disponible]
2. [Fuente 2 — con URL si disponible]
3. [Fuente 3 — con URL si disponible]
```

## Notas para el creador de contenido (Nair)

### Callouts de Notion — como usarlos

| Callout | Icono | Uso | Ejemplo |
|---------|-------|-----|---------|
| Concepto clave | Bombilla | Lo que explica el mecanismo central | "La HTA es el factor de riesgo #1 de ACV" |
| Hallazgo clasico | Triangulo alerta | Dato patognomonico o mas preguntado | "Soplo en maquinaria = PCA" |
| Gold standard | Microscopio | Metodo diagnostico definitivo | "Biopsia renal = gold standard en GN" |
| Tip de examen | Estrella | Mnemonico o truco para recordar | "MUDPILES = causas de acidosis metabolica AG+" |

### Secciones opcionales

- **Fisiopatologia:** puede ser breve en temas quirurgicos donde la clinica importa mas
- **Epidemiologia Peru:** si no hay datos peruanos, usar datos OPS Americas y marcar "[datos regionales, no Peru]"
- **Complicaciones/Pronostico:** pueden fusionarse en temas simples
- **Flashcards:** minimo 3, maximo 8 por tema

### Reglas de redaccion

1. Maximo 3-4 lineas por parrafo — no muros de texto
2. Tablas > parrafos para comparaciones
3. Listas con bullets > parrafos para enumeraciones
4. Negrita para terminos clave, no para enfasis general
5. Links internos entre temas de Resummo (cross-linking)
6. Todo verificable con fuente — si no hay fuente, marcar [NAIR: verificar]
