# Data — Resummo

## Estructura

```
data/
├── analytics/        Exports GA4, Meta Pixel, reportes de conversion
├── ventas/           Exports de Google Sheets de clientes
└── clientes/         Data anonimizada para analisis
```

## Fuentes de data

| Fuente | Que contiene | Como obtener |
|--------|-------------|--------------|
| Google Sheets | Registro de clientes, pagos, entregas | Export CSV manual o via n8n |
| GA4 | Trafico, eventos, conversion | Export desde GA4 dashboard |
| Meta Ads | Impresiones, clicks, CPA, ROAS | Export desde Meta Ads Manager |
| Meta Pixel | Eventos de conversion | Via GA4 o Meta Events Manager |
| ManyChat | Conversaciones, leads, engagement | Export desde ManyChat dashboard |

## Reglas de seguridad
- NUNCA incluir datos personales reales en archivos commiteados
- Anonimizar antes de guardar: reemplazar emails y nombres
- No commitear exports crudos de Google Sheets con emails reales
- Ver `.claude/rules/security.md`

## Reportes
- Ejecutar `/sales-analytics` para generar reportes periodicos
- Guardar en `data/analytics/reporte_[YYYY-MM-DD].md`
