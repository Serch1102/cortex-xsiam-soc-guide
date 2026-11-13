---
title: JOIN avanzado: cardinalidad, identidad y causalidad
aliases: [JOIN y falsos positivos, Cardinalidad XQL]
tags: [xql, join, correlacion, l2, l3]
estado: nota-educativa
actualizado: 2026-11-13
---
# JOIN avanzado: que dos registros coincidan no significa que estén relacionados

## El problema

Quieres unir un login sospechoso de Entra ID con una conexión a una IP remota. Si haces JOIN únicamente por `user_name`, puedes atribuir al login una conexión que ese usuario realizó en otra sesión, desde otro dispositivo o varias horas antes.

## Tres conceptos indispensables

**Clave de unión:** campo o combinación de campos que permite comparar los dos lados, por ejemplo host ID, ID de usuario o ID de sesión.

**Cardinalidad:** cuántas filas de cada lado comparten la misma clave.

**Causalidad:** una acción provoca la otra. **Un JOIN prueba coincidencia según una condición; no prueba causalidad.**

## Cómo se multiplican las filas

| Dataset A: autenticación | Dataset B: acceso a aplicación |
|---|---|
| usuarioA, login 09:00 | usuarioA, acceso 09:10 |
| usuarioA, login 09:05 | usuarioA, acceso 09:12 |
| usuarioA, login 09:08 | usuarioA, acceso 09:30 |

Un `inner join` **solo por usuario** devuelve **3 × 3 = 9 combinaciones**, aunque físicamente haya seis eventos. No son nueve accesos ni nueve incidentes.

**Pregunta de control:** ¿cuántas filas había por clave antes de unir?

## Estrategia segura para correlacionar

1. Ejecuta ambas consultas por separado.
2. Identifica la entidad más estable: ID de cuenta, host ID, ID de sesión o identificador de proceso, si existe.
3. Normaliza el formato de la clave (por ejemplo, mayúsculas o UPN), **sin eliminar distinciones importantes**.
4. Revisa el número de filas por clave y las claves compartidas.
5. Filtra por rangos temporales relevantes en ambas fuentes.
6. Decide si el objetivo es **enriquecer** (`left`) o **exigir coincidencia** (`inner`).
7. Tras unir, exige una relación temporal razonable y, cuando sea posible, una segunda clave.
8. Comprueba varias filas manualmente contra los registros originales.

## Query realista de preparación (consulta base)

~~~xql
dataset = xdr_data
| filter agent_hostname != null and agent_hostname != ""
| comp count() as eventos_por_host by agent_hostname
| sort desc eventos_por_host
| limit 50
~~~

Este patrón permite descubrir **hosts con muchas filas**. Si esos hosts también tienen muchas filas en el otro dataset, un JOIN sin agregación puede crecer muy rápido.

## Plantilla de correlación (PSEUDOCÓDIGO, NO ejecutable)

~~~text
Consulta A:
  seleccionar eventos A de 15 minutos
  preparar clave de usuario + host + tiempo_evento_A

Consulta B:
  seleccionar eventos B de 15 minutos
  preparar clave de usuario + host + tiempo_evento_B

JOIN A y B por clave compuesta
FILTRAR: tiempo_evento_B >= tiempo_evento_A
FILTRAR: diferencia_tiempo <= umbral_temporal
COMPROBAR: número de filas, duplicados y sesiones
~~~

Para hacerlo en XQL real, sustituye los nombres con los **campos que existan** en vuestro dataset y verifica qué expresiones admite la versión instalada.

## Cuándo NO deduplicar

`dedup` puede reducir filas, pero también **borrar evidencias**. Si buscas el último estado de inventario por host, una fila por host puede tener sentido; si buscas todos los procesos relacionados con un ataque, reducir a una fila por usuario destruye parte de la secuencia.

**No utilices `dedup` únicamente para que desaparezca una multiplicación de filas que aún no entiendes.**

## Comparación entre herramientas

| Objetivo | XSIAM | Google SecOps | Sentinel |
|---|---|---|---|
| Agrupar | `comp` | agregación en búsqueda/regla según motor | `summarize` |
| Unir o relacionar | `join` | correlación según funciones del motor o reglas YARA-L | `join` de KQL |
| Validar condición temporal | comprobar y filtrar timestamps de ambos lados | comparación de timestamps/eventos en ventana de regla | condiciones KQL con tiempos |
| Normalizar identidad | campos XDM u origen comprobado | campos UDM | ASIM u origen |

Son **comparaciones de finalidad**, no sintaxis intercambiable.

## Aplicación concreta en SOC

Un `left join` de eventos de endpoint con inventario sirve para **añadir criticidad**. Un `inner join` con una lista de hashes sirve para **quedarse con coincidencias**. Un JOIN de autenticaciones y actividad de red, sin embargo, exige cuidar **sesión, equipo, tiempo y cardinalidad**.

## Fuentes

- [Palo Alto: JOIN XQL](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/join)
- [Palo Alto: buenas prácticas XQL](https://docs-cortex.paloaltonetworks.com/r/Cortex-XDR/Cortex-XDR-3.x-Documentation/XQL-Query-best-practices)
- [Microsoft Sentinel: ASIM](https://learn.microsoft.com/en-us/azure/sentinel/normalization)
- [Google SecOps: UDM](https://docs.cloud.google.com/chronicle/docs/event-processing/udm-overview)

**Antes:** [JOIN básico](04-JOIN-entre-datasets.md) · **Después:** [Tiempos y ventanas](06-Tiempos-y-ventanas.md).
