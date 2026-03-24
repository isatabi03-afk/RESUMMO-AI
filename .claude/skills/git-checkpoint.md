---
name: git-checkpoint
description: Verificar estado git del repo Resummo. Que esta sin commitear/pushear antes de cerrar sesion
auto-activate: false
---

# Skill: /git-checkpoint

Verificar estado git del repositorio Resummo y ofrecer acciones.

## Pasos

### 1. Verificar repo Resummo
```bash
git status --short
git log origin/master..HEAD --oneline
```

### 2. Generar reporte

| Repo | Sin commitear | Sin pushear | Accion sugerida |
|------|--------------|-------------|-----------------|

### 3. Ofrecer ejecutar acciones
- Si hay cambios sin commitear: proponer commit con mensaje descriptivo
- Si hay commits sin pushear: proponer push (NUNCA push sin confirmacion)
- Si todo limpio: confirmar "Todo al dia"

## Reglas
- NUNCA commitear .env, credenciales, node_modules/, .next/, .git/
- Mensaje de commit: `tipo(scope): descripcion breve`
  - tipos: feat, fix, chore, docs, style, refactor
  - scopes: landing, payments, bot, n8n, analytics, docs
- NUNCA push sin confirmacion explicita del usuario
- Si todo limpio: confirmar brevemente y no agregar ruido
