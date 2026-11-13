---
title: Visibilidad y calidad de la ingesta
aliases: [¿Faltan logs?, Salud de fuentes]
tags: [xsiam, ingesta, telemetria, soc, l2]
estado: revisado-con-fuentes
actualizado: 2026-11-13
---
# Visibilidad y calidad de ingesta: ¿podemos confiar en los datos?

## El problema en un SOC

Una detección de password spraying no genera ninguna alerta. ¿Significa que no hubo ataques? **No**. Podría significar que el proveedor de identidad dejó de enviar eventos, que el parser dejó de reconocer un cambio de formato, que faltan permisos o que la regla consulta el dataset equivocado. Una búsqueda sin resultados demuestra únicamente que **esa búsqueda, con esas condiciones y esa visibilidad, no encontró registros**.

## Explicación sencilla

Piensa en una cámara de seguridad: antes de asegurar que nadie pasó por una puerta, debes comprobar que la cámara **estaba encendida, apuntaba a la puerta y grababa en ese horario**. Con el SIEM sucede igual: antes de confiar en una detección, hay que validar la cobertura de la fuente.

## Funcionamiento técnico: cuatro capas distintas

| Capa | Pregunta | Ejemplo de fallo |
|---|---|---|
| **Origen y colección** | ¿El sistema está generando y enviando eventos? | Un firewall pierde conectividad con el colector |
| **Recepción e ingesta** | ¿Los eventos entran en XSIAM a tiempo? | Hay retrasos, desconexión o caída de volumen |
| **Parsing y normalización** | ¿Se extraen y mapean los campos correctos? | El fabricante cambia la estructura del log y desaparece usuario/IP del campo normalizado |
| **Consulta y permisos** | ¿Estamos buscando donde corresponde? | Se filtra otro dataset, periodo o ámbito de acceso |

**Un log recibido no equivale a un log correctamente normalizado.** Y un log normalizado no garantiza que contenga todos los atributos necesarios para el caso de uso.

## Qué comprobar paso a paso

1. **Confirmar fuente esperada.** ¿Qué sistema debe emitir el evento? ¿Qué tipo de evento concreta la hipótesis? No presupongas que una integración incluye todas las categorías.
2. **Comprobar recepción reciente.** Identifica proveedor, producto, colector, dataset y último evento útil; compara con días y franjas similares.
3. **Comprobar frescura.** Distingue momento de ocurrencia del evento y momento de recepción/inserción: son dos relojes diferentes.
4. **Comparar original con normalizado.** Revisa si el dato ya faltaba en origen o se perdió al transformarlo.
5. **Medir cobertura.** No basta una fila: ¿todos los dispositivos esperados están reportando? ¿Todas las clases de evento necesarias?
6. **Escalar al responsable adecuado.** Colección/conector, parser/XDM, permisos o lógica de detección: la investigación cambia según la capa fallida.

## Ejemplo de consulta de exploración

La documentación oficial de XSIAM muestra el preset **metrics_view** para métricas de ingesta. Una consulta de inspección inicial (verifica disponibilidad en el tenant):

~~~xql
preset = metrics_view
| limit 20
~~~

Para buscar anomalías **no basta con contar eventos presentes en el dataset de eventos**: si un dispositivo deja de emitir, puede no aparecer ninguna fila con la que compararlo. Necesitas una línea base o un inventario de fuentes esperadas; la documentación oficial incluye reglas que comparan métricas de ingesta con un lookup de dispositivos.

**Ejemplo conceptual, no query lista para producción:**

~~~text
Inventario esperado de 20 firewalls
               ↓
Métricas de ingesta de los últimos 20 minutos
               ↓
Comparar por ID de dispositivo
               ↓
Detectar dispositivos sin eventos / caída de volumen
               ↓
Crear Issue del dominio Health con contexto
~~~

## Caso práctico

Una regla debería detectar modificaciones sensibles en directorio, pero un servidor no envía esos eventos desde hace 2 horas.

- Hecho 1: existen registros de ese servidor hasta cierta hora.
- Hecho 2: no se observan registros posteriores en la fuente consultada.
- Hipótesis A: pérdida de conectividad.
- Hipótesis B: desactivación de auditoría.
- Hipótesis C: cambio de parser.
- **Conclusión válida:** hay una brecha de visibilidad que debe investigarse; no se puede afirmar cuál es la causa sin más comprobaciones.

## Comparación con otras herramientas

- **Google SecOps:** los registros originales y los eventos **UDM** están relacionados por el parsing. Si faltan campos en UDM, comprueba primero el original y el parser. Campos como `metadata.event_timestamp` describen cuándo ocurrió el evento.
- **Sentinel/Azure:** comprueba la tabla de Log Analytics, **TimeGenerated**, el conector y, si usas **ASIM**, el parser correspondiente. Los parsers ASIM pueden operar durante la consulta, así que un campo vacío en una vista normalizada no implica necesariamente que esté vacío en la tabla original.
- **XSIAM:** distingue ingesta, Parsing Rules, Data Model Rules/XDM, datasets/presets y permisos.

## Preguntas para un L1 / L2 / L3

- **L1:** ¿qué fuente ha originado el registro? ¿En qué periodo y con qué visibilidad lo consulté?
- **L2:** ¿puedo demostrar qué fuentes faltan, cuánto dura el problema y qué detecciones dependen de ellas?
- **L3:** ¿cómo monitorizamos la salud de la fuente y qué prueba regresiva valida cambios de parser/integración?

## Errores a evitar

- Concluir «sin actividad» a partir de «sin resultados».
- Confundir la ausencia de una fila con una fila cuyo contador vale cero.
- Crear una regla sobre un campo sin medir cuántos eventos lo tienen poblado.
- Usar un periodo de 5 minutos para una integración que llega con retrasos conocidos.

## Fuentes oficiales

- [XSIAM: reglas para monitorizar ingesta](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Creating-correlation-rules-to-monitor-data-ingestion-health)
- [XSIAM: investigar Health Issues](https://docs-cortex.paloaltonetworks.com/r/Cortex-XSIAM/Cortex-XSIAM-3.x-Documentation/Investigate-and-resolve-health-issues)
- [Google SecOps: parsers](https://docs.cloud.google.com/chronicle/docs/event-processing/parsing-overview)
- [Sentinel: ASIM](https://learn.microsoft.com/en-us/azure/sentinel/normalization-parsers-overview)

**Continúa con:** [Marcas temporales y ventanas](../01-XQL/06-Tiempos-y-ventanas.md) · [Checklist de investigación](../06-Laboratorios/02-Checklist-investigacion.md).
