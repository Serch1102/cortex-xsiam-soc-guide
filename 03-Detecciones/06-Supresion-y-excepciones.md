---
title: Supresión, excepciones y reducción de falsos positivos
aliases: [Suppression XSIAM, Exclusions]
tags: [xsiam, tuning, reglas, soc]
estado: nota-educativa
actualizado: 2026-11-13
---
# Supresión y excepciones: reducir ruido sin perder seguridad

## El problema

Una regla detecta actividad repetida desde la misma IP durante una hora. ¿Debemos generar 200 Issues iguales? Seguramente no. Pero si ocultamos **todas** las IP durante esa hora, podríamos perder incidentes diferentes.

## Tres mecanismos distintos

| Mecanismo | Qué modifica | Riesgo principal |
|---|---|---|
| **Afinar la condición** | Qué eventos cumplen la detección | Excluir ataques similares si se estrecha demasiado |
| **Supresión** | Cuántos Issues repetidos se generan durante un periodo | Ocultar repeticiones significativas o entidades diferentes |
| **Excepción/exclusión** | Qué actividad deja de evaluarse, alertarse o tratarse según configuración | Crear un punto ciego permanente |

**No son sinónimos.** La supresión suele controlar la generación de Issues repetidos sin cambiar necesariamente los logs originales; una excepción puede modificar el comportamiento de la detección o de una política, según el mecanismo concreto.

## Clave de supresión: ejemplos

| Caso | Clave razonable para estudiar | Riesgo al elegir mal |
|---|---|---|
| Password spraying | IP origen + regla, quizá contexto de aplicación | Suprimir solo por regla oculta fuentes diferentes |
| Proceso anómalo | Host + proceso/hash cuando los datos estén disponibles | Un nombre común de ejecutable mezcla conductas |
| Exfiltración hipotética | Usuario + aplicación + destino | Un solo usuario puede tener sesiones distintas |
| Error de integración | Identificador del conector/dispositivo | La supresión global oculta fallos de otros conectores |

La clave correcta depende del nivel de entidad que el analista debe distinguir, no de cuántos campos podemos seleccionar.

## Supresión por campos en Correlation Rules

La documentación oficial indica que pueden seleccionarse campos del resultado XQL como clave de supresión, además de una duración. Si **no seleccionas campos**, la supresión puede limitar la generación a **un solo Issue durante ese periodo para la regla**, sin distinguir entidades. Si seleccionas demasiados campos volátiles (timestamp exacto, identificador único por evento), quizá casi ningún resultado repita la misma clave y acabes generando ruido.

### Ejemplo conceptual

~~~text
Regla: muchos fallos de autenticación
Resultados:
IP-A, 15 usuarios, 10:00
IP-A, 16 usuarios, 10:10
IP-B, 12 usuarios, 10:10

Supresión global sin campos → puede ocultar también IP-B.
Supresión por IP → IP-A repite; IP-B sigue siendo entidad diferente.
Supresión por IP + timestamp exacto → no se suprime casi nada.
~~~

Esto explica **por qué la supresión debe diseñarse junto con la salida de la query**.

## ¿Cuándo solicitar una excepción?

Antes de aprobarla, demuestra:
1. Actividad legítima reproducible y con responsable conocido.
2. Identidad y ámbito exactos: cuenta, host, proceso firmado, ruta o acción.
3. Cuánto durará la excepción y quién la revisará.
4. Qué riesgo residual queda y qué controles siguen dando visibilidad.
5. Que hay alternativa a la exclusión global (filtros más específicos, desduplicación o enrichment).

## Falso positivo vs falso negativo

- **Falso positivo:** se señala actividad que no cumple la hipótesis maliciosa en el contexto real.
- **Falso negativo:** una actividad que la regla debería detectar queda sin detectar.
- Reducir falsos positivos sacrificando demasiada cobertura puede aumentar falsos negativos.

## Ejercicio de criterio

Un administrador hace accesos remotos legítimos desde un servidor de gestión. La regla los detecta.

- **Mala solución:** excluir todas las cuentas de administración.
- **Mejor planteamiento:** verificar origen, herramienta, identidad, ventana autorizada y actividad concreta; plantear una condición específica y una revisión periódica.

No decidas una excepción solo porque el cliente diga «es normal»: pide la característica comprobable que distingue esa actividad de la amenaza.

## Comparaciones

- **Google SecOps:** filtros en lógica de reglas e investigación ayudan a reducir ruido; la implementación concreta de exclusiones depende de la detección.
- **Sentinel:** condiciones de KQL, frecuencia y configuración de reglas, agrupación de alertas y automatizaciones afectan al ruido, pero no son equivalentes directos a un campo de supresión XSIAM.
- **XSIAM:** prioriza el diseño de entidad + la lógica XQL + supresión por campos + evaluación de excepciones.

## Checklist de revisión antes de aplicar cambios

- [ ] Está documentada la actividad legítima.
- [ ] Se conserva la cobertura de otras entidades.
- [ ] He medido volumen antes/después.
- [ ] He preparado una prueba maliciosa positiva.
- [ ] He documentado responsable, duración y revisión.
- [ ] La modificación tiene aprobación y puede revertirse.

## Fuentes oficiales

- [XSIAM: crear Correlation Rule](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Create-a-correlation-rule)
- [XSIAM: auditoría de supresión](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Monitor-correlation-rules)

[Validación y tuning](04-Validacion-y-tuning.md) · [Ciclo de vida](05-Ciclo-de-vida-de-una-regla.md).
