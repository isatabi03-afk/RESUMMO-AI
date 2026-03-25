# Plan: Estrategia de Escalamiento Resummo → Modelo AMBOSS

> **Fecha:** 2026-03-24
> **Estado:** Pendiente revision por Santiago, Andre y Nair
> **Origen:** Investigacion con 4 agentes (research x3 + sales strategist) analizando AMBOSS como benchmark

## Context

Resummo tiene 161 clientes en 35 dias, S/14,400 revenue, operacion 100% manual con 3 personas. AMBOSS es el benchmark global ($292M funding, 1M+ usuarios, ~$863M valoracion). **No existe NINGUNA plataforma tipo AMBOSS nativa en espanol.** Ventana de oportunidad: 12-18 meses antes de que AMBOSS lance espanol nativo con EUR 240M recien levantados (ya tienen Sales Manager LATAM contratado).

La investigacion revelo que AMBOSS fue **bootstrap puro de 2010 a 2017** (5 anios sin VC), logro 95% market share en Alemania en 1 anio enfocandose en UN solo examen (Staatsexamen), y no internacionalizo hasta tener 100K+ usuarios locales. Este es el playbook a replicar.

---

## 1. Comparativa Resummo vs AMBOSS

| Dimension | AMBOSS | Resummo | Gap | Replicable |
|-----------|--------|---------|-----|------------|
| Contenido | 1,400+ articulos, 13K+ preguntas | ~50-100 temas Notion, 3K+ flashcards Anki | Grande | Si, con pipeline IA+Nair |
| Calidad editorial | 80-150 medicos, principio "8 ojos" | 1 editor (Nair) | Grande | Parcial: IA genera borrador, Nair valida |
| Plataforma | App web/movil custom | Notion + Anki + Google Drive | Grande | Fase 3 (18 meses) |
| AI features | LiSA 1.0 (#1 NOHARM study) | Agente IA ManyChat (ventas/soporte) | Grande | Fase 4 (3+ anios) |
| Pricing | $12.50-19.99/mes (subscription) | S/90 pago unico | Diferente modelo | Migrar en Fase 3 |
| Usuarios | 1M+ en 180 paises | 161 en Peru | Enorme | Crecimiento gradual |
| B2B | 150+ instituciones (NYU, Yale) | 0 | Grande | Fase 4 |
| Idioma espanol | Beta (traduccion DeepL) | Nativo | **Ventaja Resummo** | Ya lo tenemos |
| Guidelines locales | US/EU-centric | GPC MINSA Peru | **Ventaja Resummo** | Ya lo tenemos |
| ENAM/SERUMS | No cubierto | Enfocado | **Ventaja Resummo** | Ya lo tenemos |
| Precio accesible | $150+/anio | S/90 (~$25) pago unico | **Ventaja Resummo** | Mantener |
| Funding | $292M | $0 | Enorme | Fase 4-5 |

**Ventajas competitivas de Resummo:** Contenido nativo espanol, GPC MINSA, enfoque ENAM/SERUMS, precio 10-50x menor, trato personalizado.

---

## 2. Fuentes de Contenido — Pipeline Replicable

### De donde saca AMBOSS su contenido
- Physician authors destilan textbooks (Harrison, Robbins, Guyton) + literatura primaria (PubMed) + guidelines (AHA, ACC, IDSA, NICE)
- **NO usan IA para generar contenido base** — todo humano-escrito
- Proceso: 4 pasos, minimo 3 medicos + 1 copyeditor por articulo
- Actualizacion continua (diaria), no periodica

### Fuentes open-access para Resummo

**Peruanas (prioridad maxima):**
- GPC MINSA Peru → bvs.minsa.gob.pe
- GPC Peru (agregador) → gpc-peru.com
- IETSI EsSalud → ietsi.essalud.gob.pe/gpc-guias-de-practica-clinica/
- Soc. Peruana Medicina Interna → medicinainterna.net.pe
- DIGEMID (petitorio medicamentos) → digemid.minsa.gob.pe
- INEI (epidemiologia) → inei.gob.pe

**Internacionales (complemento):**
- StatPearls (6,000+ articulos peer-reviewed, CC BY-NC-ND) → ncbi.nlm.nih.gov/books/NBK430685/
- PubMed/PMC, Cochrane Library, OPS/OMS
- GuiaSalud Espana → portal.guiasalud.es/gpc/
- MSF Medical Guidelines → medicalguidelines.msf.org

**Imagenes medicas open access:** MedPix (53K+), PEIR (34K+), WebPath (2.7K+ patologia)

**Bancos de preguntas ENAM:** DrQuiz (2K+ gratis), ASPEFAM (examenes oficiales PDF)

### Pipeline de contenido: 1 editor + IA

| Paso | Quien | Tiempo | Detalle |
|------|-------|--------|---------|
| 1. Seleccion | Nair | 5 min | Siguiente tema de lista priorizada ENAM |
| 2. Borrador IA | Claude + StatPearls + GPC MINSA | 15-20 min | Prompt con template Resummo 9 secciones |
| 3. Validacion medica | Nair | 30-45 min | Verificar vs GPC MINSA, adaptar a Peru, agregar perlas ENAM |
| 4. Publicacion | Nair | 5 min | Subir a Notion, cross-link |
| 5. QA periodico | Nair | Mensual | Revisar 10 temas random |

**Produccion estimada:** 5 temas/semana → 25/mes → **200 temas en 8 meses** (cubre ENAM alta frecuencia)

**Priorizacion ENAM 2025** (180 preguntas):
1. Medicina Interna: 40 preguntas (22%) → crear primero
2. Pediatria: 34 (19%) → crear primero
3. Gineco-Obstetricia: 30 (17%)
4. Cirugia General: 30 (17%)
5. Emergencias/UCI: 16 (9%)

---

## 3. Modelo de Monetizacion — Evolucion

### Fase actual (Mes 0-6): Pago unico optimizado
**No migrar a subscription todavia.** ROAS 4.5x funciona, 161 clientes es base muy pequena para subscription.

**Pricing reestructurado (propuesta para Andre):**

| Paquete | Precio sugerido | Logica |
|---------|----------------|--------|
| Ciencias Basicas | S/59 | Entrada accesible |
| EsSalud/ENAM | S/79 | Target con urgencia (examen) |
| ENCAPS (Anki) | S/49 | Complementario |
| **All Access** | **S/129** | Highlight visual, todo incluido |
| All Access + Tutorias (3 sesiones) | S/199 | Decoy: hace S/129 parecer ganga |

### Fase 2 (Mes 6-12): Freemium + upsells
- 5-10 resumenes gratuitos en landing (30% conversion post-ejemplos → multiplicar el funnel)
- "Update Packs" a clientes existentes: S/29-39 por contenido nuevo
- Bundles estacionales pre-ENAM, pre-SERUMS

### Fase 3 (Mes 12-18): Subscription hibrida
- Mensual: S/29/mes
- Semestral: S/19/mes (S/114)
- Anual: S/14/mes (S/168)
- Clientes actuales: acceso grandfathered de por vida

### Fase 4 (Mes 18+): B2B institucional
- Licencias universitarias: S/15-25/estudiante/anio
- Target: universidades privadas Lima (USMP, UPCH, URP, UPC, UCSUR)

---

## 4. Roadmap Completo: Resummo → Unicornio

### Fase 0 — Genesis (actual, Mar 2026)
- **Estado:** 161 clientes, S/14,400 revenue, operacion manual
- **Validado:** Product-market fit (ROAS 4.5x, conversion 30% post-ejemplos)

### Fase 1 — Automatizacion (Abr-Sep 2026, 6 meses)
| Area | Que hacer |
|------|-----------|
| Producto | Landing con checkout, pasarela EasyPay/Culqi, entrega automatica Notion |
| Contenido | 50 temas nuevos con pipeline IA+Nair (Medicina Interna + Pediatria) |
| Tecnologia | n8n webhooks pago→entrega, agente IA ManyChat mejorado |
| Marketing | Escalar ads S/3,200→S/15,000, programa embajadores (10 universidades) |
| Revenue target | S/50,000-80,000 acumulado |
| Clientes target | 500-800 |
| Equipo | 3 personas (actual) |

### Fase 2 — Dominio Peru (Oct 2026-Sep 2027, 12 meses)
| Area | Que hacer |
|------|-----------|
| Producto | 200 temas (cobertura ENAM completa), Qbank 500 preguntas, freemium |
| Contenido | Contratar 1-2 medicos freelance part-time |
| Tecnologia | SEO optimizado, WhatsApp communities, referral system |
| Marketing | Content marketing (YouTube/TikTok), embajadores 20+ universidades |
| Revenue target | S/300,000-500,000/anio |
| Clientes target | 3,000-5,000 |
| Equipo | 5-7 personas |
| Expansion | Primeros pasos Colombia y Ecuador (contenido adaptado) |

### Fase 3 — Plataforma Propia (Oct 2027-Mar 2029, 18 meses)
| Area | Que hacer |
|------|-----------|
| Producto | Plataforma web/movil propia (dejar Notion), subscription model |
| Stack | Next.js + React Native + Supabase + PostgreSQL |
| MVP estimado | $26,000-57,000 USD |
| Contenido | 500+ temas, Qbank 2,000+ preguntas, study plans |
| Marketing | 4+ paises Latam activos |
| Revenue target | $500K-1M ARR |
| Clientes target | 10,000-20,000 |
| Funding | Pre-seed $300-500K |

### Fase 4 — B2B + AI (2029-2031, 2 anios)
| Area | Que hacer |
|------|-----------|
| Producto | B2B institucional, AI tutor (RAG sobre contenido propio), analytics |
| Contenido | 1,000+ temas, Qbank 5,000+, cobertura de 6+ examenes nacionales Latam |
| Revenue target | $3-8M ARR |
| Clientes target | 50,000+ usuarios |
| Funding | Seed $1-3M |
| Equipo | 20-30 personas |

### Fase 5 — Camino a Unicornio (2031-2033)
| Area | Que hacer |
|------|-----------|
| Producto | Dominancia Latam, USMLE en espanol, partnerships farmaceuticas |
| Revenue target | $50M+ ARR |
| Usuarios | 500K+ |
| Funding | Series A $10-30M |
| Equipo | 100+ |

---

## 5. Canales de Adquisicion — Priorizacion

| # | Canal | Cuando | CPA esperado | Prioridad |
|---|-------|--------|-------------|-----------|
| 1 | Meta Ads (Instagram) | Ahora | S/20 | MANTENER + escalar |
| 2 | Landing page SEO | Mes 1-2 | S/0 organico | CRITICO |
| 3 | Referral program | Mes 2-3 | S/10 | ALTO |
| 4 | Embajadores universitarios | Mes 3-6 | S/10-15 comision | ALTO |
| 5 | WhatsApp communities | Mes 3-4 | S/0 | ALTO |
| 6 | TikTok/Reels medical | Mes 4-6 | S/5-10 | MEDIO |
| 7 | YouTube tutoriales | Mes 6-9 | S/0 organico | MEDIO |
| 8 | B2B universidades | Mes 12+ | Variable | FUTURO |

**Programa embajadores:** 1 estudiante por universidad, comision S/10-15/venta, kit de contenido. 20 embajadores x 5 ventas/mes = 100 ventas/mes sin ads.

---

## 6. Fundraising — Cuando y Con Quien

**Fondos que invierten en edtech Latam:**
- Kaszek (pre-seed a Series A, Latam)
- IGNIA (seed, Mexico/Latam)
- ALLVP (seed, Mexico/Latam)
- Magma Partners (pre-seed, Chile/Latam)
- Owl Ventures (edtech global)
- Reach Capital (edtech)
- Platanus Ventures (pre-seed, Chile/Latam)

**Metricas para pre-seed (mes 6-9):**
- $50K+ ARR
- 1,000+ usuarios activos
- Crecimiento MoM 15-20%
- Retencion >60% a 90 dias

---

## 7. Estacionalidad Peru

| Periodo | Evento | Paquete a promover |
|---------|--------|--------------------|
| Ene-Feb | Inicio ciclo | Ciencias Basicas (early bird) |
| Mar-Abr | Parciales | All Access |
| Junio | ENAM | EsSalud/ENAM (urgencia, sin descuento) |
| Jul-Ago | Medio anio | ENCAPS bundle |
| Sep | SERUMS convocatoria | ENCAPS + EsSalud |
| Nov-Dic | Finales | All Access (Black Friday 15-20% off) |

---

## 8. Riesgos Principales

| Riesgo | Probabilidad | Mitigacion |
|--------|-------------|-----------|
| AMBOSS lanza espanol nativo (12-18 meses) | Alta | Velocidad: consolidar base + marca. GPC MINSA/ENAM que AMBOSS no tendra. Precio 10x menor. |
| VillamedicGroup lanza formato digital accesible | Media | UX superior (Notion+Anki vs PDFs). Precio 20-50x menor. |
| Pago unico limita LTV (S/90 terminal) | Alta | Migrar a subscription Fase 3. Mientras: upsells + referidos. |
| Nair como unico editor (cuello de botella) | Alta | Pipeline IA reduce carga 60%. Contratar 1-2 freelance en Fase 2. |
| CPA sube al escalar ads | Alta | Diversificar: <40% clientes por paid ads para mes 12. |
| Andre/Nair no completan blockers | Media | Deadline 7 dias, alternativa Culqi self-service. |

---

## 9. Proximos Pasos Inmediatos

| # | Accion | Responsable | Deadline |
|---|--------|-------------|----------|
| 1 | Andre/Nair crean cuenta pasarela (EasyPay/Culqi) | Andre | 31 Mar 2026 |
| 2 | Confirmar precios exactos de los 4 paquetes | Andre | 31 Mar 2026 |
| 3 | Obtener 2-3 capturas ejemplo por paquete | Nair | 31 Mar 2026 |
| 4 | Obtener 3+ testimonios clientes | Andre | 31 Mar 2026 |
| 5 | Export Google Sheets ventas (anonimizado) para data granular | Andre | 31 Mar 2026 |
| 6 | Crear 3 temas piloto con pipeline IA+Nair (1 Med Interna, 1 Pediatria, 1 GO) | Nair + Santiago | 7 Abr 2026 |

**Sin los pasos 1-4, todo el roadmap se retrasa.** La Fase 1 (landing + pagos automatizados) es el blocker de todo lo demas.

---

## Division de responsabilidades (PENDIENTE revisar)

> Santiago, Andre y Nair deben revisar este roadmap y asignar:
> - Lo que hace Santiago/FIXU (tech, automatizacion, landing)
> - Lo que hace Nair (contenido medico, validacion, Notion)
> - Lo que hace Andre (comercial, pricing, ads, partnerships)
> - Lo que es viable con recursos actuales vs requiere inversion

---

## Sources de la investigacion

- AMBOSS Founding Story — amboss.com/int/about
- AMBOSS Our Journey — careers.amboss.com/our-journey/
- AMBOSS Series C EUR 240M — eu-startups.com/2025/03/amboss-raises-e240-million
- AMBOSS Sales Manager LATAM — careers.amboss.com/jobs/job-4194ac/
- AMBOSS Content Policy — support.amboss.com/hc/en-us/articles/6365572887188
- AMBOSS AI Principles — amboss.com/us/ai-principles
- StatPearls — ncbi.nlm.nih.gov/books/NBK430685/
- GPC Peru — gpc-peru.com
- IETSI EsSalud GPC — ietsi.essalud.gob.pe/gpc-guias-de-practica-clinica/
- ASPEFAM ENAM — aspefam.org.pe/enam/
- TodoParaENAM — todoparaenam.com/temas-del-examen-enam-2025/
- Peru EdTech Epicenter — ruta-startup.com/2024/02/21/peru-has-become-the-epicenter-of-edtech-in-latin-america/
