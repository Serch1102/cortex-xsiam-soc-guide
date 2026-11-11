---
title: Introducción a XQL para analistas
tags: [xql, fundamentos, l1]
estado: nota-educativa
---
# Primeras consultas XQL

XQL es el lenguaje de consulta de Cortex. Permite seleccionar el conjunto de registros, filtrarlos, transformarlos, agruparlos y presentar campos relevantes.

## Consulta base
```xql
dataset = xdr_data
| limit 10
```

Después, al identificar campos reales de tu entorno:
```xql
dataset = xdr_data
| filter event_type = PROCESS
| fields _time, agent_hostname, action_process_image_name
| sort desc _time
| limit 50
```
**Consulta base educativa:** comprueba `event_type` y nombres de campo en la fuente.

## Cómo leerla
- `dataset`: de dónde salen las filas.
- `filter`: qué registros conservar.
- `fields`: qué columnas presentar.
- `sort`: orden.
- `limit`: máximo de filas devueltas; no sustituye a filtrar adecuadamente.

Práctica: añade un filtro por host de laboratorio, ejecuta y explica qué tipos de eventos quedan fuera. [Siguiente: filtros](02-Filtros-y-operadores.md).
