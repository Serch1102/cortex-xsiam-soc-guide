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

## Analogía y equivalencias con Google SecOps y Microsoft Sentinel

Imagina que investigamos autenticaciones de Windows y de un proveedor de identidad cloud. **El raw log es el mensaje original**, un **dataset** permite consultarlo dentro de XSIAM, el **preset** facilita acceder a un subconjunto útil para la investigación y **XDM** aporta nombres y estructuras comunes para correlacionar fuentes heterogéneas.

Si ya conoces otras plataformas SIEM, estos paralelismos ayudan a situar los conceptos:

| Cortex XSIAM | Google SecOps (Chronicle) | Microsoft Sentinel / Azure |
|---|---|---|
| **Raw log** | Raw log original antes o junto a la representación UDM | Evento recibido en una tabla de Log Analytics |
| **Dataset** | Registros consultables en Google SecOps (raw y/o normalizados en UDM; **no hay equivalencia 1:1 con el dataset XQL**) | Tabla del workspace de Log Analytics, como `SecurityEvent` o `SigninLogs` |
| **Preset** | Búsqueda preparada o reutilizable (**analogía funcional, no componente equivalente**) | Consulta KQL guardada o función de consulta (**analogía funcional**) |
| **XDM** | **UDM (Unified Data Model)** | **ASIM (Advanced Security Information Model)** |
| **XQL** | Búsquedas UDM para investigar; **YARA-L** para reglas de detección | **KQL** para consultar datos y definir lógica de reglas analíticas |

### Diferencia técnica que sí importa

Los tres sistemas buscan hacer comparables eventos de proveedores distintos, **pero no normalizan necesariamente en el mismo momento**. Google SecOps utiliza parsers que convierten registros originales a **UDM** como parte de la canalización de ingesta, o puede recibir eventos previamente normalizados. En Microsoft Sentinel, **ASIM** puede normalizar mediante funciones/parsers **KQL al consultar** y también existen tablas ASIM con **normalización durante la ingesta**. En XSIAM, el comportamiento depende de la versión y de la configuración de parsing y mapeo XDM (véase el apartado anterior). Por eso, **UDM, ASIM y XDM persiguen fines similares, pero no son implementaciones intercambiables**.

### Aplicación SOC

Si una búsqueda de autenticaciones muestra campos vacíos en XSIAM, no basta con asumir que «faltan logs». Contrasta **el evento original, la extracción de campos y la normalización**, del mismo modo que en Google SecOps revisarías el *parser* y el mapeo UDM y en Sentinel comprobarías la tabla de Log Analytics y el parser ASIM correspondiente. Una capa de normalización no puede recuperar información que la fuente nunca envió.

**Fuentes:** [Google SecOps — UDM](https://docs.cloud.google.com/chronicle/docs/event-processing/udm-overview) · [Google SecOps — parsing](https://docs.cloud.google.com/chronicle/docs/event-processing/parsing-overview) · [Microsoft Sentinel — parsers ASIM](https://learn.microsoft.com/en-us/azure/sentinel/normalization-parsers-overview) · [Microsoft Sentinel — normalización durante la ingesta y consulta](https://learn.microsoft.com/en-us/azure/sentinel/sentinel-solution-quality-guidance).

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
