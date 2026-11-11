---
title: Filtros y operadores XQL
tags: [xql, filtros, l1]
---
# Filtrar antes de correlacionar

## Situación SOC
Una búsqueda de todos los procesos de una semana puede ser enorme. Si solo investigas un equipo, filtra por él desde el principio.

```xql
dataset = xdr_data
| filter event_type = PROCESS
| filter agent_hostname = "equipo-lab"
| fields _time, agent_hostname, action_process_image_name
| limit 100
```
**Consulta base:** sustituye por host autorizado y confirma campos.

Operadores frecuentes: `=`, `!=`, `in (...)`, `contains`, `and`, `or`. Utiliza paréntesis para expresiones compuestas y evita comparar atributos de distinta naturaleza (por ejemplo, SID frente a nombre visible).

## Vacíos
```xql
| filter agent_hostname != null and agent_hostname != ""
```
`null` y cadena vacía no son equivalentes. Al investigar resultados inesperados, cuenta filas antes/después del filtro.

[COMP](03-Agregaciones-COMP.md) · [Optimización](05-Optimizar-y-depurar.md).
