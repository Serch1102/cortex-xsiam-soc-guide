---
title: Tiempos y ventanas en XQL y las detecciones
aliases: [Timeframe XQL, Event time vs ingestion time]
tags: [xql, xsiam, tiempo, detecciones]
estado: nota-educativa
actualizado: 2026-11-13
---
# Marcas temporales: cuándo ocurrió, cuándo llegó y cuándo se detectó

## El problema real

Un usuario realiza una acción a las 10:01; el servicio envía el registro a las 10:04 y la regla se ejecuta a las 10:10. Si confundes esos tres momentos puedes perder eventos, duplicar alertas o reconstruir mal la cronología.

## Una analogía útil

Un paquete se **envía**, llega al **almacén** y se **procesa**. Son tres horas distintas. Los registros también tienen diferentes tiempos.

## Qué relojes distinguir

| Reloj | Significado | Duda SOC que resuelve |
|---|---|---|
| **Tiempo de evento** | Momento en que la fuente dice que ocurrió la acción | ¿Cuándo actuó la cuenta? |
| **Tiempo de ingesta/inserción** | Momento en que la plataforma recibe o incorpora el registro | ¿Hay retraso de colección? |
| **Tiempo de ejecución de la regla** | Momento en que se evalúa la detección | ¿Por qué la alerta apareció más tarde? |
| **Ventana de consulta** | Intervalo de eventos que analiza la búsqueda | ¿Qué datos podían entrar en el resultado? |

En XSIAM, `_time` suele representar la marca temporal consultable del evento, pero **verifica su origen exacto en la fuente y dataset**. No des por hecho que todos los datasets disponen de los mismos metadatos de ingesta ni que están en el mismo formato.

## Consulta de inspección segura

~~~xql
dataset = xdr_data
| fields _time, _vendor, _product
| sort desc _time
| limit 30
~~~

**Consulta base:** confirma primero que el dataset y los metadatos están accesibles. La selección de un rango temporal en XQL Search determina la población consultada.

Si buscas retraso de recepción, **añade únicamente el campo de tiempo de ingesta que hayas confirmado en ese dataset**; no copies un nombre genérico sin comprobarlo.

## Diferencia entre programación y ventana

Una regla *Scheduled* tiene dos decisiones independientes:

1. **Frecuencia de ejecución:** cada cuánto se lanza la query.
2. **Query time frame:** qué intervalo de datos examina cada ejecución.

Ejemplo razonado:
- Ejecución a las 10:00, 10:10 y 10:20.
- Ventana de consulta de 20 minutos.
- La ejecución de las 10:10 examina parte del mismo intervalo que la de las 10:00.

Esto crea **solapamiento**. Puede ser útil ante retrasos de ingesta, pero también puede hacer que una misma evidencia sea evaluada varias veces. La estrategia para evitar Issues duplicados debe definirse en la lógica, el agrupado y/o la supresión.

> [!warning] La supresión no recupera eventos perdidos
> Si un log llega cuando ya pasó la ventana de consulta, configurar supresión no lo hace visible retroactivamente. Es necesario revisar la política temporal y la frescura de datos.

## Razonamiento de correlación temporal

Si deseas detectar «login sospechoso seguido de descarga»:

- Asegúrate de que ambos tiempos se han interpretado con la misma zona horaria.
- Correlaciona por **identidad estable**, no solo por nombre visible o una IP que represente un proxy.
- Verifica que el segundo evento ocurrió después del primero.
- Impón un intervalo máximo coherente con la hipótesis.
- Comprueba si hay varias sesiones o usuarios detrás de la misma dirección de salida.
- Conserva el identificador de sesión o dispositivo cuando esté disponible.

**Sin condición temporal, un JOIN por usuario puede vincular acciones de horas o días distintos.**

## Google SecOps y Sentinel

- **Google SecOps (UDM):** `metadata.event_timestamp` identifica cuándo ocurrió el evento. Google también documenta campos como `metadata.collected_timestamp`; no son intercambiables.
- **Sentinel:** `TimeGenerated` es el referente habitual del evento. La función `ingestion_time()` permite examinar recepción para algunas tablas; **no funciona directamente sobre los parsers unificadores ASIM**, según la documentación de limitaciones.
- **XSIAM:** cruza el tiempo del evento con el momento de ejecución y el estado de ingesta para interpretar posibles retrasos.

## Ejercicio

Dibuja una línea temporal con tres eventos sintéticos:
- 10:01: autenticación.
- 10:05: transferencia.
- 10:11: el SIEM recibe la autenticación.

¿Qué habría visto una regla ejecutada a las 10:10? ¿Cambiaría la respuesta si el sistema consultara por tiempo de evento o de llegada?

## Fuentes oficiales

- [XSIAM: auditoría de reglas de correlación y tiempos](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Monitor-correlation-rules)
- [Google SecOps: UDM y marcas temporales](https://docs.cloud.google.com/chronicle/docs/unified-data-model/udm-usage)
- [Sentinel: limitaciones de ASIM y tiempo de ingesta](https://learn.microsoft.com/en-us/azure/sentinel/normalization-known-issues)

**Relacionadas:** [Calidad de ingesta](../00-Fundamentos/05-Visibilidad-y-calidad-de-ingesta.md) · [JOIN y causalidad](07-JOIN-y-correlaciones-engañosas.md).
