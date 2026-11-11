---
title: Investigación de procesos
tags: [soc, endpoint, l1]
---
# ¿Quién ejecutó qué? Procesos y relación padre-hijo

## Ejemplo
Se detecta `wscript.exe` ejecutando un archivo `.js` desde una carpeta temporal. Necesitamos saber **qué proceso lanzó wscript**, con qué argumentos, con qué usuario y qué ocurrió después.

## Comprobar
- Árbol de procesos y procesos padre/hijo.
- Línea de comandos completa.
- Firma, hash y ruta.
- Usuario y contexto de ejecución.
- Conexiones posteriores y escritura de archivos.
- Si el sensor observó el intento o si el proceso realmente avanzó.

**Advertencia:** que un sensor informe de una invocación no demuestra necesariamente ejecución completa del script.

## Consulta de exploración
```xql
dataset = xdr_data
| filter event_type = PROCESS
| fields _time, agent_hostname, actor_process_image_name, action_process_image_name
| sort desc _time
| limit 100
```
**Consulta base adaptable**: confirma el esquema y después añade filtro por host o proceso de interés.

[Flujo SOC](../00-Fundamentos/04-Flujo-investigacion-SOC.md).
