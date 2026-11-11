---
title: JOIN entre datasets en XQL
aliases: [JOIN XQL, Unir datasets]
tags: [xsiam, xql, join, l2, l3]
estado: nota-educativa
---
# JOIN entre datasets: teoría y práctica

## El problema operativo
Hay un evento en una fuente y contexto en otra: por ejemplo, un inicio de sesión y los metadatos de un usuario. Queremos reunirlos **sin ocultar eventos ni multiplicar filas de forma engañosa**.

`join` combina dos resultados a partir de una condición común. No implica por sí solo relación temporal o causal.

## Tipos
| Tipo | Devuelve | Para qué sirve |
|---|---|---|
| `inner` | Solo coincidencias en ambos lados | Eventos que aparecen en una lista de interés |
| `left` | Todas las filas izquierdas y coincidencias derechas | Enriquecer sin perder eventos |
| `right` | Todas las filas derechas y coincidencias izquierdas | Similar a invertir las fuentes y usar `left` |

## Sintaxis
```xql
dataset = xdr_data
| filter event_type = PROCESS
| fields _time, agent_hostname, action_process_image_name
| join conflict_strategy = left type = left (
    dataset = xdr_data
    | filter event_type = NETWORK
    | comp count() as total_red by agent_hostname
    | fields agent_hostname as host_red, total_red
) as red red.host_red = agent_hostname
| fields _time, agent_hostname, action_process_image_name, total_red
| sort desc _time
| limit 30
```
**Consulta educativa: no verificada en ningún tenant.** Filtra una ventana pequeña y comprueba que `NETWORK`, `PROCESS` y los campos están disponibles.

Esta consulta combina **procesos por host** con el **total de eventos de red del mismo host**. **No** demuestra que esos procesos fueran responsables de esas conexiones.

## Conflictos de nombre
`conflict_strategy = left` prioriza campos del lado principal; `right`, del lado de la subquery; `both` conserva ambos y puede renombrar duplicados. Durante depuración es útil renombrar explícitamente las claves.

## El problema muchos-a-muchos
Si un usuario tiene 3 registros en A y 4 en B, unir por usuario puede devolver **12 combinaciones**. No son 12 incidentes. Agrega o deduplica solo cuando la pregunta de investigación lo permita.

## Método de trabajo
1. Ejecuta A y mide el número de filas por clave.
2. Ejecuta B y comprueba los campos.
3. Filtra claves nulas/vacías; normaliza mayúsculas con `lowercase()` si hace falta.
4. Decide si quieres conservar todo A (`left`) o solo coincidencias (`inner`).
5. Agrega B si solo necesitas un resumen por clave.
6. Ejecuta `join`, revisa duplicados y ordena **después** del join.
7. Compara cifras y valida manualmente algunas filas.

## Plantilla (pseudocódigo, NO ejecutable)
```text
dataset = <dataset_A>
| filter <clave_A> != null
| alter clave_normalizada = lowercase(<clave_A>)
| join type = left (
    dataset = <dataset_B>
    | alter clave_B_normalizada = lowercase(<clave_B>)
    | comp count() as repeticiones by clave_B_normalizada
) as b b.clave_B_normalizada = clave_normalizada
```

## ¿JOIN o IN?
Si solo verificas que un valor está en una lista, `in` puede ser suficiente. Si necesitas **columnas adicionales**, `join` es apropiado.

## Regla de correlación
Si una detección necesita `join`, estudia una **Scheduled Correlation Rule**; las restricciones de Real Time Correlation Rules pueden impedirlo. Valida el comportamiento en la versión concreta.

## Referencias
- [JOIN — documentación oficial](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/join)
- [Buenas prácticas XQL](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/XQL-Query-best-practices)
- [Correlation Rules](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Create-a-correlation-rule)

[Siguiente: optimización](05-Optimizar-y-depurar.md).
