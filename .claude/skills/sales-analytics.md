---
name: sales-analytics
description: Calcular metricas de venta y conversion de Resummo. Para reportes semanales, analisis de CPA, o cuando Andre pida numeros
argument-hint: [periodo — semana, mes, o rango de fechas YYYY-MM-DD]
auto-activate: false
---

# Skill: /sales-analytics

Calcular metricas de venta, conversion y ROI de Resummo para un periodo especifico.

## Pasos

### 1. Obtener data de ventas
- Leer Google Sheets de clientes (via export CSV en `data/ventas/` o via n8n)
- Filtrar por periodo indicado
- Si no hay export reciente: solicitar al usuario que exporte desde Google Sheets

### 2. Calcular metricas core

| Metrica | Formula |
|---------|---------|
| Ventas totales (unidades) | COUNT registros en periodo |
| Revenue total (S/) | SUM montos en periodo |
| Ticket promedio | Revenue / Ventas |
| Ventas por paquete | GROUP BY paquete |
| Clientes/dia promedio | Ventas / dias en periodo |

### 3. Calcular metricas de ads (si hay data)
- Leer data de Meta Ads en `data/analytics/` o solicitar al usuario
- CPA = Gasto en ads / Clientes adquiridos
- ROAS = Revenue / Gasto en ads
- CTR = Clicks / Impresiones

### 4. Calcular metricas de funnel (si hay data GA4)
- Trafico landing (sessions)
- Conversion rate = Compras / Sessions
- Click-to-checkout rate = Checkouts iniciados / Clicks en CTA
- Checkout completion rate = Compras / Checkouts iniciados

### 5. Comparar contra periodo anterior
- Calcular delta (%) para cada metrica
- Identificar tendencias (crecimiento, estancamiento, caida)

### 6. Generar reporte

```
## Reporte de Ventas — Resummo [periodo]

### Metricas principales
| Metrica | Valor | vs periodo anterior |
|---------|-------|-------------------|
| Ventas | X unidades | +X% |
| Revenue | S/ X | +X% |
| Ticket promedio | S/ X | +X% |
| CPA | S/ X | +X% |

### Ventas por paquete
| Paquete | Unidades | Revenue | % del total |
|---------|----------|---------|-------------|

### Insights
- [hallazgo accionable 1]
- [hallazgo accionable 2]

### Recomendaciones
1. [accion — impacto esperado]
```

### 7. Guardar reporte
Guardar en `data/analytics/reporte_[YYYY-MM-DD].md`

## Gotchas
- Google Sheets puede tener formatos inconsistentes si Andre/Nair editan manualmente — normalizar antes de calcular
- Si no hay data de ads: omitir metricas de CPA/ROAS, no inventar
- Si no hay data de GA4: omitir metricas de funnel, no inventar
- Estacionalidad: comparar periodos equivalentes (no comparar semana de examen con semana normal)

## Reglas
- Solo usar datos reales — NUNCA inventar o estimar sin data
- Si faltan datos: indicar "Data no disponible" y sugerir como obtenerla
- Montos siempre en soles (PEN)
- Redondear a 2 decimales
- Incluir siempre comparacion contra periodo anterior cuando sea posible
