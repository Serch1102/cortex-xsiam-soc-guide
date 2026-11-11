---
title: Laboratorio guiado de XQL
tags: [xql, laboratorio, soc]
---
# Laboratorio XQL paso a paso

Ejecuta únicamente en un entorno autorizado.

## Ejercicio 1 — Inspeccionar esquema
```xql
dataset = xdr_data
| limit 10
```
¿Qué campos existen y cuáles faltan?

## Ejercicio 2 — Contar procesos por host
```xql
dataset = xdr_data
| filter event_type = PROCESS
| comp count() as total by agent_hostname
| sort desc total
| limit 20
```
**Consulta base**, no validada en vuestro tenant. ¿Qué representa cada fila?

## Ejercicio 3 — Relacionar datos
Construye consultas A y B por separado. Comprueba su cardinalidad. Une con `left join` y explica por qué puede haber más filas que antes.

## Ejercicio 4 — Pensar como un ingeniero de detecciones
Redacta una detección de password spraying: datos, lógica, umbral, supresión, riesgo de falsos positivos y consulta de investigación.

## Criterio de corrección
Se evalúa que puedas explicar qué demuestra la query, qué no demuestra y cuáles son los límites de la fuente.

[Introducción XQL](../01-XQL/01-Introduccion-XQL.md) · [JOIN](../01-XQL/04-JOIN-entre-datasets.md).
