---
title: Investigación de PsExec y PSEXESVC
tags: [soc, lateral-movement, procesos]
---
# PsExec y PSEXESVC: ¿administración o ataque?

El servicio `PSEXESVC` puede aparecer al usar PsExec, tanto para administración como para movimiento lateral malicioso.

## Investiga
- Quién inició la autenticación y desde qué host.
- Destino, usuario y privilegios.
- Servicio instalado y binario utilizado.
- Procesos hijos y comandos ejecutados.
- Ventana de mantenimiento y herramienta autorizada.

**No concluyas** «movimiento lateral» exclusivamente por un servicio llamado PSEXESVC.

## Ejercicio
Plantea una hipótesis legítima (despliegue autorizado) y otra maliciosa (ejecución remota no autorizada). Indica qué registros permitirían distinguirlas.

[Flujo SOC](../00-Fundamentos/04-Flujo-investigacion-SOC.md).
