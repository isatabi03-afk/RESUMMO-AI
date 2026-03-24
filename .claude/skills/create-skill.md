---
name: create-skill
description: Crear skills de Claude Code siguiendo convenciones del workspace Resummo
argument-hint: [descripcion del skill a crear]
auto-activate: false
---

# Skill: /create-skill

Crear nuevos skills siguiendo las convenciones del workspace.

## Tipos de skill
- **Task:** ejecuta acciones con side effects (deploy, test, audit)
- **Research:** investiga y sintetiza (analytics, content-review)
- **Knowledge:** referencia bajo demanda (patterns, checklists)

## Pasos

### 1. Clarificar
- Que problema resuelve?
- Se repite 2+ veces? (si no, no crear skill)
- Verificar que NO duplica funcionalidad de skill existente

### 2. Determinar scope
- Proyecto: `.claude/skills/` (compartido con el workspace)

### 3. Escribir skill con estructura obligatoria

```yaml
---
name: nombre-kebab-case
description: descripcion con trigger phrases (10-15 palabras)
argument-hint: [que recibe como input]
auto-activate: false
context: fork  # opcional, para skills pesados que requieren aislamiento
---

# Skill: /nombre-skill

[Rol statement — una oracion]

## Pasos
[Numerados, concretos, accionables]

## Gotchas
[Errores comunes, trampas, edge cases]

## Reglas
[Max 10-12, no negociables]
```

### 4. Si >200 lineas: convertir a carpeta
```
.claude/skills/nombre-skill/
├── skill.md           ← Solo Claude carga esto al inicio
├── references/        ← Documentacion detallada (cargada bajo demanda)
└── templates/         ← Templates reutilizables
```

### 5. Verificar checklist
- [ ] Frontmatter YAML completo (name, description)
- [ ] Nombre kebab-case
- [ ] Description con trigger phrases
- [ ] Secciones: Pasos + Gotchas + Reglas
- [ ] < 300 lineas
- [ ] No duplica funcionalidad existente
- [ ] Todo en espanol (comunicacion), codigo en ingles

### 6. Registrar en CLAUDE.md
Agregar el skill a la seccion "Skills" de CLAUDE.md

## Gotchas
- Skills >200 lineas: convertir a carpeta con references/
- No crear si el patron ocurrio <2 veces
- `context: fork` aisla del historial — pasar todo el contexto necesario en los pasos
- Claude solo carga `skill.md` o `SKILL.md` automaticamente — los archivos en references/ se cargan bajo demanda

## Reglas
- SKILL.md < 300 lineas
- Kebab-case para nombres
- No duplicar lo que ya existe
- Todo en espanol (comunicacion)
