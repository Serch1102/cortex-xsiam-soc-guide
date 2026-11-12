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

## Qué ocurre a bajo nivel

Cuando un equipo o servicio envía un evento a XSIAM (por ejemplo, mediante un agente, una integración o un colector), el sistema lo recibe y puede aplicar **Parsing Rules durante la ingesta** para interpretar su formato, extraer atributos, establecer marcas temporales y asignarlo a un dataset; el resultado no tiene por qué estar ya normalizado en XDM. Cada registro dispone de campos propios de su origen y puede incluir metadatos del sistema como `_time` (marca temporal del evento), `_insert_time` (momento de inserción), `_vendor` y `_product`. Cuando ejecutamos `dataset = nombre_dataset`, XQL consulta los campos disponibles en ese dataset; al utilizar una consulta sobre el **data model**, las **Data Model Rules** traducen los campos de origen a rutas comunes `xdm.*` según el mapeo definido, lo que permite comparar fuentes de distintos fabricantes sin asumir que sus logs tienen el mismo esquema. **En XSIAM 3.x este mapeo XDM se aplica normalmente en tiempo de consulta**; XSIAM 4.0 introduce *Data Model Acceleration* para procesar ese mapeo durante la ingesta. Por otro lado, un **preset** selecciona datos preparados para una actividad concreta (por ejemplo, autenticación): no significa necesariamente una copia física de los registros. Para investigar un campo ausente hay que comprobar **el evento original, su parsing, el dataset, el mapeo XDM y la consulta utilizada**, en lugar de concluir directamente que no se registró la actividad.

**Referencias técnicas:** [Parsing Rules](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Create-Parsing-Rules) · [Mapeo XDM (MODEL)](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/MODEL) · [Consultas y presets](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/XQL-language-features) · [XSIAM 4.0 y Data Model Acceleration](https://www.paloaltonetworks.com/blog/security-operations/cortex-xsiam-4-0-ai-knows-your-enterprise/).

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
