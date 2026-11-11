---
title: Datasets, presets y XDM
tags: [xsiam, xql, fundamentos]
estado: nota-educativa
---
# Datasets, presets y XDM

## La diferencia explicada sin rodeos
- **Dataset:** colección consultable de registros (como una tabla).
- **Preset:** acceso preconfigurado a datos para un propósito de consulta. No es necesariamente otro origen de telemetría.
- **XDM:** modelo común que intenta representar campos y entidades de diferentes proveedores bajo nombres coherentes.
- **Raw log:** registro original de una integración, que puede tener campos propios.

## Ejemplo
Un firewall puede nombrar `src_ip` al origen, otro proveedor `clientIp`. Una normalización coherente permite investigar ambos de forma comparable. No presupongas que todas las fuentes traen el mismo nivel de detalle.

## Primeros pasos
```xql
dataset = xdr_data
| limit 10
```
**Consulta base**: verifica permisos, disponibilidad de `xdr_data` y rango temporal desde XQL Search.

Observa qué campos aparecen realmente. Construye filtros solo con nombres comprobados. Documenta proveedor, evento, campos ausentes y formato de usuario/host/fecha.

## Riesgos
Un campo vacío no demuestra ausencia de actividad. Puede indicar ingesta incompleta, evento sin atributo o parser no mapeado.

**Relacionadas:** [Introducción XQL](../01-XQL/01-Introduccion-XQL.md) · [JOIN](../01-XQL/04-JOIN-entre-datasets.md).
