---
title: Flujo de investigación SOC
tags: [soc, investigacion, l1, l2]
---
# De una alerta a una conclusión defendible

## Secuencia práctica
1. **Identificar**: regla, hora, host, usuario, origen/destino, severidad.
2. **Validar telemetría**: qué fuente generó el dato, si hay lagunas y qué representa.
3. **Correlacionar**: eventos anteriores y posteriores, procesos y cuentas relacionadas.
4. **Contextualizar**: actividades autorizadas, administración, automatizaciones, activos críticos.
5. **Clasificar**: verdadero positivo, falso positivo o información insuficiente, según procedimiento.
6. **Actuar o escalar**: contención únicamente dentro de permisos y proceso.
7. **Documentar**: hechos, hipótesis, búsquedas, limitaciones, decisión y seguimiento.

## Ejemplo de buena conclusión
> Se observa la creación de un servicio en un host de laboratorio. Esta evidencia por sí sola no permite determinar si el origen es legítimo o malicioso. Se recomienda revisar actor, binario, firma, ruta y actividad posterior.

Evita frases como «equipo comprometido» cuando solo se ha detectado una ejecución aislada.

[Checklist](../06-Laboratorios/02-Checklist-investigacion.md)
