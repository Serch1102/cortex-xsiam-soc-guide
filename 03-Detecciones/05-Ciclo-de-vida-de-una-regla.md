---
title: Ciclo de vida de una regla de detección
tags: [xsiam, reglas, detecciones, l2, l3]
estado: revisado-con-fuentes
actualizado: 2026-11-13
---
# Del caso de uso a una regla mantenible

## La idea

Un ingeniero de detecciones no solo escribe una consulta que devuelve eventos. Construye un **control de seguridad** que necesita datos fiables, pruebas, un responsable, un procedimiento operativo y seguimiento.

## Las ocho fases

### 1. Hipótesis de amenaza

Ejemplo: «Una IP realiza intentos fallidos contra muchos usuarios distintos en una ventana de 10 minutos». Define qué comportamiento representa y qué técnica podría describir, **sin asignar ATT&CK por parecido superficial**.

### 2. Requisitos de telemetría

Prepara un contrato de datos:
- Fuente concreta y dataset.
- Campos imprescindibles: origen, identidad, resultado, tiempo y contexto.
- Tipo y formato de cada campo (string, IP, timestamp).
- Cobertura por dispositivos y clases de evento.
- Lagunas aceptadas e integración responsable.

**Si falta un campo esencial, la regla no es válida aunque compile.**

### 3. Query de investigación

Primero inspecciona datos reales autorizados; después construye filtros. La primera versión debe mostrar **evidencia legible** y mantener campos de investigación. Separar investigación de detección permite comprobar si el comportamiento existe en tu entorno.

### 4. Diseño de resultados

Una ejecución puede producir:
- **Una fila por evento:** útil para un comportamiento individual.
- **Una fila por IP o usuario:** útil para un patrón agregado.
- **Una fila por combinación de entidad y ventana:** útil cuando importa el periodo.

El diseño de la entidad determina cuántos Issues pueden generarse. No confundas «500 eventos» con «500 incidentes».

### 5. Pruebas

| Prueba | Lo que responde |
|---|---|
| **Positiva** | ¿Detecta una actividad de laboratorio que debería señalar? |
| **Negativa** | ¿Evita señalar acciones permitidas razonablemente parecidas? |
| **Histórica** | ¿Qué volumen y falsos positivos produciría sobre datos anteriores? |
| **De degradación** | ¿Qué ocurre si falta una fuente o se vacía un campo? |
| **De regresión** | ¿Sigue detectando el caso tras cada cambio? |

> [!note] Una query con cero hits no demuestra que esté bien afinada
> Puede ser excesivamente restrictiva, faltarle datos o usar campos incorrectos.

### 6. Configuración operativa

En una Correlation Rule revisa:
- Real Time frente a Scheduled según stages soportados.
- Frecuencia de ejecución y ventana de datos.
- Acción de resultado y dominio del Issue.
- Severidad, categoría, táctica/técnica MITRE cuando apliquen.
- Campos de salida y contexto para el analista.
- Política de supresión y consulta de *drill-down*.

Si la query usa JOIN o agregaciones, revisa las restricciones de **Real Time** y valora la ejecución **Scheduled**. No copies cron ni campos sin comprobar la versión.

### 7. Puesta en marcha y monitorización

Después de habilitar:
- Confirma **ejecuciones completadas**, no solo que aparece «enabled».
- Comprueba volumen de Issues, duplicados y calidad del contexto.
- Valida que aparecen eventos esperados en pruebas autorizadas.
- Revisa errores por timeout, campo, dependencia o fuente ausente.
- Registra quién aprobó el despliegue.

**Consulta oficial de arranque para auditoría** (inspecciona esquema, no se presuponen nombres de campos):

~~~xql
dataset = correlations_auditing
| limit 20
~~~

La documentación oficial describe en ese dataset ejecuciones con estados como *Initiated*, *Completed* y *Error*, además de reintentos, motivos de error y ventana consultada. El valor de **`_time` en ese dataset es el momento de auditoría**, no necesariamente la hora de ocurrencia de todos los eventos analizados.

### 8. Mantenimiento y retirada

Define responsable y revisión periódica. Ajusta umbrales cuando cambie el entorno y conserva historial de qué motivó el cambio. Retira reglas obsoletas, superpuestas o sin fuente estable; evita acumular contenido que nadie revisa.

## Diferencia entre rol L1, L2 y L3

- **L1:** detecta patrones recurrentes, entrega ejemplos verificables y explica limitaciones.
- **L2:** analiza volumen, causa del ruido, hipótesis alternativas y pruebas de regresión.
- **L3:** define arquitectura de datos, lógica, criterios de calidad y despliegue; la distribución real depende del proyecto.

## Comparación con otras plataformas

| Objetivo | Cortex XSIAM | Google SecOps | Sentinel/Azure |
|---|---|---|---|
| Diseñar lógica | XQL + Detection Rules | UDM Search / YARA-L según función | KQL + Analytics Rules |
| Validar origen | Dataset, XDM y parsing | Raw y UDM | Tabla de Log Analytics y ASIM |
| Monitorizar funcionamiento | Auditoría `correlations_auditing`, salud de fuentes | Estado de reglas e ingesta | Salud de conectores y ejecución de reglas |
| Reducir ruido | Diseño, condiciones, supresión y tuning | Diseño de detección, filtros y ajustes | KQL, configuración de reglas e incidentes |

No son equivalencias exactas de implementación.

## Definición de «regla lista»

- [ ] Hipótesis y fuente documentadas.
- [ ] Campos esenciales verificados y poblados.
- [ ] Queries de prueba y resultados anexados de forma segura.
- [ ] Supresión y severidad justificadas.
- [ ] Runbook para L1/L2 disponible.
- [ ] Revisada por otro analista.
- [ ] Monitorización y responsable asignados.

## Fuentes

- [XSIAM: crear Correlation Rule](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Create-a-correlation-rule)
- [XSIAM: monitorizar reglas](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Monitor-correlation-rules)
- [XSIAM: salud de ingesta](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Creating-correlation-rules-to-monitor-data-ingestion-health)

[Volver al diseño](01-Diseno-de-detecciones.md) · [Supresión y excepciones](06-Supresion-y-excepciones.md).
