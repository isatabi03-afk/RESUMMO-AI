# Git Workflow

## Regla principal
NO crear branches por crear. Trabajar en master para cambios menores.
Solo crear branch cuando hay un trigger logico.

## Triggers para crear branch (unicos momentos validos)
1. **Cambio grande multi-archivo** — si el cambio toca >5 archivos o >3 directorios
2. **Experimento** — probar algo que podria no funcionar y necesitarias revertir
3. **Feature nueva de landing** — cambio significativo en estructura o funcionalidad
4. **Trabajo de varios dias** — si el cambio no se termina en una sesion
5. **Riesgo de romper produccion** — cambios a workflows n8n, pasarela de pagos, o entrega Notion

## Que va directo a master (sin branch)
- Ediciones a un solo archivo (README, doc, config)
- Fixes puntuales y typos
- Actualizaciones de documentacion
- Cambios a configuracion de .claude/
- Commits de mantenimiento (chore)
- Ajustes de copy en la landing

## Naming de branches
- `feature/landing-[que]` → ej: feature/landing-pricing-section
- `feature/payments-[que]` → ej: feature/payments-easypay-integration
- `feature/bot-[que]` → ej: feature/bot-manychat-faq-flow
- `fix/[que]` → ej: fix/notion-delivery-timeout
- `chore/[que]` → ej: chore/cleanup-test-data

## Protocolo
1. Antes de pushear a master, confirmar con el usuario si el cambio es grande
2. Si se creo branch, mergear a master solo cuando el usuario apruebe
3. NUNCA force push a master
4. NUNCA borrar branches sin confirmar
