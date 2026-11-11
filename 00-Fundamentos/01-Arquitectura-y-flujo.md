---
title: Arquitectura y flujo de Cortex XSIAM
tags: [xsiam, fundamentos, l1]
estado: nota-educativa
---
# Cómo encajan las piezas de XSIAM

## El problema
En un SOC tenemos eventos de muchas fuentes: endpoint, firewall, VPN, Active Directory, Entra ID, aplicaciones y servicios cloud. Si investigáramos cada fuente de forma aislada, perderíamos relaciones.

## Modelo mental
```text
Sensores e integraciones
       ↓
Ingesta y normalización
       ↓
Datasets / XDM / presets
       ↓
XQL y detecciones
       ↓
Alertas / Issues / Cases
       ↓
Investigación y respuesta
```

La **telemetría** es el conjunto de hechos observados. Una **detección** interpreta una combinación de esos hechos para destacar un comportamiento que merece investigación. Una **alerta** no prueba por sí sola que exista un incidente.

## Ejemplo SOC
Se observa `powershell.exe` ejecutado por Word. La ejecución es un hecho. Que esa ejecución sea maliciosa es una hipótesis: hay que comprobar línea de comandos, usuario, procesos hijos, destino de red y si hubo bloqueo.

## Qué mirar en la consola
- Fuente original, producto y tipo de evento.
- Nombre del dataset y campos útiles.
- Regla que produjo la alerta.
- Entidades de usuario, host e IP vinculadas.
- Acciones de respuesta disponibles y permisos requeridos.

**Siguiente:** [Datasets, presets y XDM](02-Datasets-Presets-y-XDM.md).
