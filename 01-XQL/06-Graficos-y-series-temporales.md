---
title: Gráficos y series temporales en XQL
aliases: [Visualizaciones XQL, Graficas XSIAM, DNS por hora]
tags: [xsiam, xql, bin, comp, visualizacion, dns, soc, l1, l2]
estado: nota-educativa
actualizado: 2026-11-23
---

# Gráficos y series temporales en XQL: de un total a una línea temporal

> [!important] Datos de ejemplo
> Todas las consultas utilizan el dominio **ficticio** `portal.lab.example`. No contienen dominios, cuentas, endpoints, direcciones IP, comandos ni identificadores reales de clientes. **Consultas base no probadas en el tenant**: comprobar disponibilidad de `xdr_data`, los campos y la ventana de tiempo.

## Situación SOC: «sé cuántas consultas DNS hay, pero no cuándo ocurrieron»

Esta consulta agrupa consultas DNS por la línea de comandos del proceso que las originó:

~~~xql
config case_sensitive = false
| dataset = xdr_data
| filter dns_query_name contains "portal.lab.example"
| comp count() as hits by causality_actor_process_command_line
| sort desc hits
~~~

**Limitación:** devuelve una fila por comando y un total acumulado para el rango seleccionado en XQL Search. Al aplicar `comp`, se pierden los eventos individuales y el campo de tiempo deja de estar disponible si no participa en la agrupación. No sirve directamente para una curva de evolución temporal.

## Concepto: `_time` + `bin` + `comp` + `view graph`

- `_time`: marca temporal del evento utilizada para el eje horizontal (X).
- `bin _time span = 1h`: redondea/agrupa las marcas temporales en intervalos de una hora.
- `comp count() as hits by _time`: cuenta los registros de cada intervalo y devuelve una fila por hora.
- `sort asc _time`: ordena cronológicamente.
- `view graph type = line xaxis = _time yaxis = hits`: solicita una gráfica de líneas con tiempo en X y número de eventos en Y.

> [!note] Diferencia fundamental
> La consulta sin `_time` responde **«cuántos por proceso»**. La consulta con `bin` y `comp ... by _time` responde **«cuándo y cuántos en cada intervalo»**. El gráfico es una representación de esas filas agregadas, no una búsqueda adicional.

## 1. Consultas DNS por hora: línea temporal

~~~xql
config case_sensitive = false
| dataset = xdr_data
| filter dns_query_name contains "portal.lab.example"
| bin _time span = 1h
| comp count() as hits by _time
| sort asc _time
| view graph type = line xaxis = _time yaxis = hits
~~~

**Interpretación SOC:** un pico de `hits` señala una concentración de eventos DNS en una hora. Puede deberse a un servicio legítimo, retries, procesos automatizados o actividad no deseada. Por sí mismo, **no prueba DNS tunneling, C2 ni exfiltración**.

## 2. Consultas DNS por hora, separadas por comando de proceso

~~~xql
config case_sensitive = false
| dataset = xdr_data
| filter dns_query_name contains "portal.lab.example"
| bin _time span = 1h
| comp count() as hits by _time, causality_actor_process_command_line
| sort asc _time
| view graph type = line xaxis = _time yaxis = hits series = causality_actor_process_command_line
~~~

Cada valor distinto de `causality_actor_process_command_line` genera una serie. Ayuda a identificar **qué procesos producen los picos**, pero las líneas de comandos pueden variar mucho o incluir parámetros extensos: la cardinalidad elevada vuelve ilegible la visualización. Además, los eventos sin campo de proceso pueden quedar agrupados como valores vacíos o nulos según la presentación.

> [!warning] No asumir que todo DNS tiene un proceso correlacionado
> Si la fuente no rellena `causality_actor_process_command_line`, la serie puede no resultar útil. Inspecciona primero los eventos y prueba un campo más estable, como el nombre del proceso, **solo si existe en ese dataset y aparece poblado**.

## 3. Más detalle o una visión por días

Cambia únicamente el intervalo de `bin`. Por ejemplo, **cada 15 minutos**:

~~~xql
config case_sensitive = false
| dataset = xdr_data
| filter dns_query_name contains "portal.lab.example"
| bin _time span = 15m
| comp count() as hits by _time
| sort asc _time
| view graph type = line xaxis = _time yaxis = hits
~~~

Para una tendencia de varios días, cambia `15m` por `1d`. Los intervalos de `5m`, `15m`, `1h` y `1d` son ejemplos útiles; escoge una granularidad coherente con el rango temporal consultado.

## 4. Gráfico de columnas, sin modificar la agregación

~~~xql
config case_sensitive = false
| dataset = xdr_data
| filter dns_query_name contains "portal.lab.example"
| bin _time span = 1h
| comp count() as hits by _time
| sort asc _time
| view graph type = column xaxis = _time yaxis = hits
~~~

Las columnas suelen ser más legibles para comparar ventanas discretas. La línea suele ser mejor para observar tendencias.

## Usarlo desde XQL Search (sin `view graph`)

Puedes terminar la consulta en `sort asc _time`, ejecutarla y abrir **Query Results → Chart Editor**; selecciona **Line** o **Column**, eje X `_time`, eje Y `hits` y, si agrupaste por proceso, utiliza el campo de proceso como **Series**. Incluir `view graph` en la query permite conservar las preferencias de visualización al guardarla.

## Qué mirar a bajo nivel

1. **Ventana temporal:** la gráfica depende del selector de tiempo de XQL Search; no interpreta períodos fuera de él.
2. **Pérdida de detalle:** `comp` resume datos. Para inspeccionar un pico, vuelve a la consulta de eventos sin agregar y limita las fechas.
3. **Ceros ausentes:** una hora sin registros puede no generar una fila con `hits=0`. Un hueco en la gráfica no siempre equivale a un cero explícito.
4. **Contar eventos no siempre es contar resoluciones únicas:** la telemetría puede registrar retries, eventos duplicados o varias consultas similares.
5. **Filtro de coincidencia parcial:** `contains` encuentra cadenas que incluyen el texto, no solo el dominio exacto. Usa filtros más restrictivos si el objetivo es exclusivamente un FQDN.
6. **Coste y legibilidad:** empieza con rango corto y gráfico de una serie; después añade el proceso. Evita publicar líneas de comandos auténticas en capturas o repositorios compartidos.

## Comparaciones útiles con otros SIEM

| Intención | XQL (Cortex XSIAM) | Google SecOps / YARA-L | Sentinel / KQL |
|---|---|---|---|
| Agrupar por tiempo | `bin _time span = 1h` | Agrupación temporal en el contexto de la consulta/regla, según funcionalidad | `bin(TimeGenerated, 1h)` |
| Contar | `comp count() as hits by _time` | Agregaciones y contadores según búsqueda o regla | `summarize hits=count() by bin(TimeGenerated, 1h)` |
| Graficar | `view graph ...` o Chart Editor | Visualizaciones de la interfaz | `render timechart` |

Son **analogías funcionales**, no traducciones 1:1: cambian el esquema, el lenguaje y las capacidades.

## Práctica guiada

Usando un dominio de laboratorio autorizado, responde: **¿en qué intervalo aparecen más consultas DNS y qué proceso está asociado al pico?** Primero ejecuta la consulta por hora y localiza el máximo; después utiliza la versión por proceso y revisa los eventos sin agregar de esa ventana. Contrasta con la actividad esperada del host antes de atribuir intencionalidad.

## Problemas frecuentes

- **No aparece el tiempo:** lo perdiste en `comp`. Añade `bin _time` y agrupa también por `_time`.
- **El gráfico no aparece:** ejecuta primero sin `view graph`, comprueba que hay filas y que `hits` es numérico; después prueba Chart Editor.
- **No hay resultados:** verifica el dominio ficticio, el rango temporal y que `dns_query_name` exista y esté poblado.
- **Demasiadas series:** utiliza primero el total por hora o agrupa por un identificador de proceso más estable y disponible.

## Referencias

- [Palo Alto Networks: Graph query results (Cortex XSIAM)](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Graph-query-results)
- [Palo Alto Networks: Translate to XQL (ejemplo de `bin`)](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/Translate-to-XQL)
- [Palo Alto Networks: XQL Query best practices](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/XQL-Query-best-practices)

## Relacionado

- [Agregaciones COMP](03-Agregaciones-COMP.md)
- [Filtros y operadores](02-Filtros-y-operadores.md)
- [Optimización](05-Optimizar-y-depurar.md)
- [Mapa XQL](00-MOC-XQL.md)
