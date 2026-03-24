---
name: content-review
description: Revisar y validar contenido de paquetes Resummo antes de publicar. Cuando Nair suba contenido nuevo o se actualice un paquete
argument-hint: [paquete a revisar — ciencias-basicas, essalud-enam, all-access, encaps]
auto-activate: false
---

# Skill: /content-review

Revisar que el contenido de un paquete de Resummo esta completo, accesible, y consistente con lo que se promete en la landing page.

## Pasos

### 1. Cargar spec del paquete
- Leer `docs/paquetes/[paquete].md` — spec de contenido, precio, que incluye
- Leer `.claude/rules/landing-page.md` — seccion de pricing (que se promete)
- Si no existe spec del paquete: crear una como primer paso

### 2. Verificar contenido vs promesa

| Aspecto | Landing dice | Paquete tiene | Estado |
|---------|-------------|---------------|--------|
| Resumenes [area] | Si | ? | OK/FALTA |
| Flashcards Anki | Si/No | ? | OK/FALTA/N/A |
| Tutorias 1-1 | Si/No | ? | OK/FALTA/N/A |
| Actualizaciones | Si/No | ? | OK/FALTA/N/A |

### 3. Verificar accesibilidad Notion
- La pagina Notion del paquete es compartible?
- Los permisos estan configurados correctamente?
- El contenido se ve bien desde una cuenta guest?
- Las subpaginas son accesibles?

**Nota:** Si no hay Notion MCP conectado, depender de screenshots o verificacion manual de Andre/Nair.

### 4. Verificar accesibilidad Drive (si aplica)
- Solo para paquetes con Anki (All Access, ENCAPS)
- El link de Drive funciona?
- Los archivos Anki se pueden descargar?

### 5. Verificar consistencia con otros paquetes
- Formato similar (headings, organizacion, estilo)
- No hay duplicacion innecesaria entre paquetes
- All Access realmente incluye TODO lo de los otros paquetes

### 6. Verificar capturas de ejemplo
- Hay 2-3 capturas de ejemplo por paquete en `content/testimonios/` o similar?
- Las capturas representan fielmente el contenido?
- Son legibles y profesionales?

### 7. Generar checklist

```
## Content Review — [Paquete] — [Fecha]

### Checklist
- [ ] Contenido completo vs lo prometido en landing
- [ ] Pagina Notion compartible y accesible
- [ ] Link Drive funcional (si aplica)
- [ ] Formato consistente con otros paquetes
- [ ] Capturas de ejemplo disponibles
- [ ] Precio actualizado y consistente

### Hallazgos
| # | Aspecto | Estado | Accion requerida |
|---|---------|--------|-----------------|

### Veredicto
[LISTO PARA PUBLICAR / REQUIERE AJUSTES]
```

## Gotchas
- Claude Code no puede acceder directamente a Notion — depender de Notion MCP si esta conectado, o de screenshots/exports manuales de Nair
- El contenido medico NO se evalua por precision medica — solo por completitud, formato y accesibilidad
- Si el paquete es nuevo: crear spec en `docs/paquetes/` como primer paso

## Reglas
- No evaluar precision del contenido medico — eso es responsabilidad de Nair
- Solo evaluar: completitud, accesibilidad, consistencia, y alineacion con la landing
- Si la spec del paquete no existe: crearla antes de hacer el review
- Reportar todo como checklist accionable
