---
title: Triage de alertas y evaluación de evidencias
aliases: [Cómo analizar una alerta, Triage SOC]
tags: [soc, xsiam, investigacion, l1, l2]
estado: revisado-con-fuentes
actualizado: 2026-11-13
---
# Triage en XSIAM: del aviso a una conclusión verificable

## El problema

Llega una alerta «PowerShell sospechoso». El L1 debe decidir si está ante actividad legítima, una detección que merece seguimiento o un incidente que exige escalar. **El nombre de la regla no es la evidencia**: es la razón por la que el motor ha señalado un evento.

## Analogía sencilla

Una alarma de incendios detecta humo; no determina por sí sola si hay un incendio, vapor de cocina o una prueba de mantenimiento. El SOC debe **inspeccionar el contexto, comprobar la señal y actuar según el riesgo**.

## Los cinco bloques de una investigación

### 1. Qué ha detectado la regla

Identifica la **condición exacta** que se cumplió, su fuente y gravedad. Comprueba qué evento la disparó y qué campos están vacíos o no disponibles.

Ejemplo:
- **Hecho:** el sensor observó un proceso llamado `powershell.exe`.
- **Hecho:** el padre informado es `winword.exe`, si la fuente registra esa relación.
- **Hipótesis:** un documento ejecutó código no autorizado.
- **No demostrado:** éxito de una descarga o ejecución de malware.

### 2. Quién, dónde y cuándo

Reúne:
- Host e identidad (ID estable cuando exista).
- Sesión y tipo de inicio de sesión.
- Hora del evento y posibles retrasos de ingesta.
- Origen remoto, VPN/proxy y acción de los controles.
- Contexto de usuario: cuenta humana, servicio, administrador o aplicación.

### 3. Qué pasó antes y después

Reconstruye una secuencia limitada y comprobable:
1. Evento inicial (documento/proceso/URL/login).
2. Acción posterior (proceso hijo, servicio, archivo, acceso de red).
3. Resultado (permitido, bloqueado, fallido o desconocido).
4. Persistencia o repetición, si la telemetría lo permite.

**Importante:** una alerta de «conexión bloqueada» no demuestra que los datos hayan salido; un proceso lanzado no demuestra que el script haya finalizado.

### 4. Qué hipótesis alternativas existen

| Indicio | Explicación potencialmente legítima | Qué comprobar |
|---|---|---|
| Servicio remoto instalado | Administración IT | Cambio autorizado, cuenta, host origen |
| PowerShell desde Office | Automatización o macro | Documento, firma, línea de comandos y cadena de ejecución |
| Login desde ubicación extraña | VPN/proxy | IP efectiva, sesión, método MFA |
| Muchos fallos desde una IP | NAT, aplicación mal configurada | Usuarios afectados, aplicación, patrón temporal |

### 5. Conclusión y siguiente acción

Una buena conclusión separa:
- **Observado:** evidencia concreta respaldada por logs.
- **Interpretación:** por qué encaja o no con la hipótesis de amenaza.
- **Limitaciones:** qué fuentes no estaban disponibles o no lo permiten determinar.
- **Acción:** ampliar investigación, escalar, cerrar conforme a procedimiento o contener con autorización.

## Dónde buscar contexto en XSIAM

La investigación puede apoyarse en el detalle del **Issue**, su asociación a un **Case** cuando proceda, **Causality View**, cronología, entidades, eventos contribuyentes y búsquedas XQL. La disponibilidad de vistas y acciones puede depender de licencia, versión, fuente y **RBAC**. No todos los tipos de Issue ofrecen idéntica profundidad de causalidad.

### Consulta inicial de exploración

~~~xql
dataset = xdr_data
| fields _time, agent_hostname, event_type, event_sub_type
| sort desc _time
| limit 50
~~~

**Consulta base:** localiza primero el evento o entidad de interés. Luego filtra por host, usuario o identificador de proceso con los campos reales. No uses esta consulta general como prueba de la actividad maliciosa.

## Comparación con Google SecOps y Sentinel

- **Google SecOps:** los pivotes investigativos utilizan los campos UDM como `principal`, `target` y `metadata`; valora qué actor representa cada campo.
- **Microsoft Sentinel:** suele usarse la relación incidente/alerta con búsqueda KQL sobre tablas como `SecurityEvent`, `SigninLogs` o eventos de Defender, si se ingieren.
- **XSIAM:** la vista de causalidad puede reunir procesos y red de una entidad; su utilidad depende del tipo de evento y la cobertura real. **La representación visual también debe contrastarse con hechos.**

## Plantilla de cierre sintética

> Se identifica una ejecución de PowerShell iniciada por un proceso de ofimática en un host de laboratorio. La fuente muestra la creación del proceso, pero no confirma por sí sola la ejecución completa del script. No se ha observado actividad adicional en las fuentes consultadas durante el intervalo analizado. Se recomienda revisar la disponibilidad de telemetría de red y las actividades autorizadas antes de establecer la clasificación definitiva.

## Checklist del analista

- [ ] He distinguido regla, evento de origen e hipótesis.
- [ ] He comprobado tiempo, usuario, host y alcance.
- [ ] He corroborado el resultado: intento frente a ejecución exitosa.
- [ ] He buscado eventos anteriores/posteriores.
- [ ] He evaluado posibilidades legítimas.
- [ ] He declarado las lagunas de visibilidad.
- [ ] He registrado una decisión reproducible.

## Fuentes

- [XSIAM: investigar Issues](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Investigate-issues)
- [XSIAM: vistas de investigación y Cases](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-Documentation/Alert-side-panel)
- [Google SecOps: estructura UDM](https://docs.cloud.google.com/chronicle/docs/event-processing/udm-overview)

**Relacionadas:** [Flujo general](../00-Fundamentos/04-Flujo-investigacion-SOC.md) · [Calidad de ingesta](../00-Fundamentos/05-Visibilidad-y-calidad-de-ingesta.md).
