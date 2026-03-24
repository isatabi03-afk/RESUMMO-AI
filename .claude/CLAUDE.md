# CLAUDE.md

Base de conocimiento operativo de Resummo, plataforma de resumenes medicos y flashcards para estudiantes de medicina en Peru. Operado por FIXU AI como CTO as a Service. Contiene landing page, automatizaciones de pago y entrega, agente IA conversacional, y documentacion del proyecto.

## Reglas Criticas

1. **Cambios quirurgicos** — landing, pagos y entrega estan en produccion con clientes reales. No reestructurar sin autorizacion
2. **Verificar antes de deployar** — probar flujo completo pago → entrega antes de pushear a produccion
3. **Documentar decisiones** con razonamiento en `docs/arquitectura/` o en el README correspondiente
4. **Nunca exponer credenciales** — EasyPay/Culqi keys, Notion tokens, Google API keys NUNCA en outputs ni commits

## Equipo

| Persona | Rol | Scope |
|---------|-----|-------|
| Andre Lapeyre | Co-fundador Resummo | Estrategia comercial, ads, pricing, relacion con cliente |
| Inair/Nair Javier | Co-fundador Resummo | Contenido medico, calidad de paquetes, creacion de material |
| Santiago Zavaleta / FIXU AI | CTO as a Service | Toda la columna tech: landing, pagos, automatizacion, agente IA |

## Estructura del Workspace

```
├── landing/              Codigo fuente de resummo.com (Lovable/Next.js)
├── automations/          Workflows N8N, configs ManyChat, scripts de deploy
│   ├── n8n/              JSONs exportados de workflows
│   ├── manychat/         Exports y configs de ManyChat flows
│   └── scripts/          Scripts JS de deploy y utilidades
├── content/              Assets visuales, testimonios, copy, branding
│   ├── testimonios/      Screenshots y videos de clientes reales
│   ├── ads/              Creativos para Meta Ads
│   ├── copy/             Textos de landing, emails, DMs
│   └── branding/         Logo, paleta, tipografia
├── data/                 Analytics, exports de ventas, data de clientes
│   ├── analytics/        Exports GA4, Meta Pixel, reportes
│   ├── ventas/           Exports de ventas, Google Sheets
│   └── clientes/         Data anonimizada de clientes
├── docs/                 Documentacion tecnica y de negocio
│   ├── discovery/        Transcripciones de reuniones Andre+Nair
│   ├── arquitectura/     Diagramas, decisiones tecnicas
│   ├── paquetes/         Spec de cada paquete (contenido, precio, Notion page)
│   └── roadmap/          Roadmap por fases
├── memory/               Estado del proyecto, decisiones, preferencias
└── .claude/              Rules, skills, agents, settings
```

## Stack Tecnologico

| Capa | Servicio | Proposito |
|------|----------|-----------|
| Landing page | Lovable / Next.js | resummo.com — conversion de trafico |
| Dominio | GoDaddy | resummo.com (S/97/anio, incluye email @resummo.com) |
| Pasarela de pagos | EasyPay / Culqi | Cobros en soles (PEN): Yape, Plin, tarjetas |
| Automatizacion | n8n (self-hosted VPS FIXU) | Orquestacion de flujos post-pago |
| Agente IA | ManyChat + n8n | Instagram DM + WhatsApp — ventas y soporte 24/7 |
| Base de clientes | Google Sheets | Registro de compradores y accesos entregados |
| Entrega de producto | Notion API | Compartir paginas de contenido con comprador |
| Flashcards | Google Drive | Distribucion de decks Anki |
| Analytics | GA4 + Meta Pixel | Tracking de conversion y atribucion de ads |
| Ads | Meta Ads (Instagram) | Trafico a landing y CTWA |

## Producto

| Paquete | Target | Contenido |
|---------|--------|-----------|
| Ciencias Basicas | Estudiantes 1er-3er anio | Resumenes Notion de ciclos basicos |
| EsSalud/ENAM | Internos y egresados | Resumenes con enfoque examen nacional + tutorias 1-1 |
| All Access | Todos | Todo el contenido + tutorias 1-1 + actualizaciones |
| ENCAPS (Anki) | Preparando SERUMS 2026 | +3000 flashcards Anki (VILLAPEPAS) |

- **Modelo:** Pago unico, acceso de por vida
- **Ticket promedio:** S/90
- **Paquete mas vendido:** All Access (constante todo el anio)
- **Entrega:** Acceso automatico a Notion tras confirmacion de pago + link Drive para Anki

## Metricas de referencia (Mar 2026)

| Metrica | Valor |
|---------|-------|
| Clientes totales | 161 |
| Dias activos vendiendo | ~35 |
| DMs/dia | 50-60 |
| Conversion DM → Venta | 5-10% |
| Conversion "pide ejemplos" → Venta | 30% |
| CPA (Meta Ads) | S/20 |
| Inversion total ads | S/3,200 |
| Revenue total | S/14,400 |
| Seguidores Instagram | ~767 |

## Convenciones y reglas detalladas

Ver `.claude/rules/` — fuente de verdad para:
- `git-workflow.md` — Branches, naming, triggers
- `security.md` — Credenciales, produccion
- `workspace-structure.md` — Proteccion de estructura
- `landing-page.md` — Convenciones de la landing page
- `payment-automation.md` — Flujo de pagos automatizado
- `notion-delivery.md` — Entrega automatica de acceso Notion
- `manychat-patterns.md` — Patrones del agente IA en Instagram/WhatsApp
- `n8n-patterns.md` — Patrones de workflows n8n
- `analytics-tracking.md` — GA4 + Meta Pixel

## Skills

- `/git-checkpoint` — Verificar que esta sin commitear/pushear
- `/create-skill` — Crear nuevos skills siguiendo convenciones
- `/landing-audit` — Auditar landing page: performance, SEO, conversion, mobile
- `/payment-flow-test` — Testear flujo completo de pago end-to-end
- `/iterate-from-dms` — DMs reales de Instagram → bugs → ajustes al agente IA
- `/sales-analytics` — Metricas de venta, conversion, CPA, LTV
- `/content-review` — Revisar y validar contenido de paquetes antes de publicar

## Ciclo de verificacion

1. Leer antes de modificar (README + docs relevantes)
2. Modificar quirurgicamente (un cambio a la vez)
3. Despachar reviewer antes de deploy de agente IA
4. Probar flujo completo (pago → entrega) antes de ir a produccion
5. Deploy solo con aprobacion explicita
6. Si hay error: documentar como rule para no repetir

## Dominios de sesion

Un dominio por sesion. /clear al cambiar de dominio. No editar el mismo archivo desde 2 sesiones.

| Dominio | Leer al iniciar | Scope |
|---------|----------------|-------|
| Landing | `landing/README.md` + rules landing | Frontend, conversion, SEO |
| Pagos | `docs/arquitectura/` + rules payment | Pasarela, webhooks, entrega |
| Agente IA | `automations/README.md` + rules manychat | ManyChat, n8n, prompt del bot |
| Analytics | `data/analytics/` + rules tracking | GA4, Meta Pixel, metricas |
| Estrategia | `docs/roadmap/` + memory | Roadmap, pricing, expansion |

## Git discipline

Ejecutar `/git-checkpoint` al inicio y al final de cada sesion. Si han pasado mas de 45 minutos de trabajo sin commit, sugerir proactivamente hacer checkpoint.

## Estado actual (Mar 2026)

Fase 0 — Setup inicial. 161 clientes adquiridos manualmente via Instagram DMs en ~35 dias. Operacion 100% manual. Prioridad: Fase 1 (landing + pasarela + automatizacion de entrega). Andre y Nair pendientes de: crear cuenta EasyPay/Culqi, definir precios exactos, preparar capturas de ejemplo, conseguir testimonios. Roadmap completo en `docs/roadmap/`.

## Context management

- Al hacer /compact, especificar: "Preservar: fase actual, cambios pendientes, decisiones tomadas"
- Sesiones optimas < 45 min. Despues de 45 min sugerir /compact o cerrar sesion
- Respuestas sucintas — accion primero, explicacion solo si necesaria
