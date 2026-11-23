---
title: Agregaciones COMP en XQL
tags: [xql, comp, l2]
---
# COMP: responder cuántos, dónde y con qué frecuencia

`comp` agrupa eventos y calcula métricas. Es útil para responder «¿cuántas veces aparece esto por host?».

```xql
dataset = xdr_data
| filter event_type = PROCESS
| filter agent_hostname != null
| comp count() as total_eventos by agent_hostname
| sort desc total_eventos
| limit 30
```
**Consulta base:** los campos y valores dependen del tenant.

## Interpretación
El resultado es **una fila por host**, no una fila por evento. Si haces `JOIN` de este resultado contra otro dataset, recuerda que se ha perdido el detalle de cada proceso.

## Evitar falsas conclusiones
Muchos procesos no significa actividad maliciosa. Compara con la línea base, horarios, cambios autorizados y presencia en múltiples equipos.

## Series temporales y gráficos

Si necesitas conocer **cuándo** se producen los eventos, añade una dimensión temporal: `bin _time span = 1h` seguido de `comp count() as hits by _time`. Consulta [Gráficos y series temporales (caso DNS)](06-Graficos-y-series-temporales.md) para ver líneas, columnas y series separadas por proceso, sin datos reales de clientes.

[JOIN](04-JOIN-entre-datasets.md).
